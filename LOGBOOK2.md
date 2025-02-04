
# Trabalho realizado nas Semanas #2 e #3

# Log4Shell (CVE-2021-44228)

## Identification
- Log4Shell (CVE-2021-44228) affects Apache Log4j 2.x versions before 2.15.0.  
- Impacts Java-based applications across cloud services, enterprise software and custom applications.  
- Affects systems running major operating systems like Windows, Linux and macOS.  
- Can exploit vulnerable servers and embedded devices with Java-based components.  

## Cataloging
- First reported by Alibaba's cloud security team in November 2021.  
- Public disclosure on December 9, 2021, classified as critical with a CVSS score of 10.0.  
- Patches released urgently by Apache, recognized in multiple bug bounty programs.  
- Requires immediate mitigation, such as updates or disabling vulnerable Log4j features.  

## Exploit
- Allows Remote Code Execution (RCE) via JNDI injection in vulnerable Log4j instances.  
- Automated tools and Metasploit modules were quickly developed for exploitation.  
- Exploit automation via public PoC scripts enabled mass scanning and attacks.  
- Exploits can be delivered through simple web requests, requiring no authentication.  

## Attacks
- The vulnerability has been used in various attacks, including ransomware, botnets and cryptocurrency mining operations.
- Following the disclosure of the vulnerability, numerous major corporations, government entities and cloud providers were targeted.
- Attackers have used Log4Shell for lateral movement within networks, gaining higher privileges and extracting sensitive data.
- Reports indicated that some attacks affected critical infrastructure, raising significant concerns about global cyber security. 

## Bibliography 
1. NVD – National Vulnerability Database, "CVE-2021-44228 Detail"  
   [https://nvd.nist.gov/vuln/detail/CVE-2021-44228](https://nvd.nist.gov/vuln/detail/CVE-2021-44228)  
2. Wired, "The Log4j Flaw Could Affect the Entire Internet"  
   [https://www.wired.com/story/log4j-flaw-hacking-internet/](https://www.wired.com/story/log4j-flaw-hacking-internet/)  
