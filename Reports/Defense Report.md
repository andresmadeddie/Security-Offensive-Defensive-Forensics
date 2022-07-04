# **Blue Team: Summary of Operations**

### Table of Contents

- Network Topology
- Description of Targets
- Monitoring the Targets
- Suggestions for Going Further

***

## Network Topology

![1](/Resources/Images/4/1.png)

The following machines were identified on the network:

- ### Target 1

    Operating System: Linux (Debian)

    Purpose: Vulnerable machine 1

    IP Address: 192.168.1.110

- ### Target 2 

    Operating System:  Linux (Debian)

    Purpose: Vulnerable machine 2

    IP Address: 192.168.1.115

- ### Capstone

    Operating System: Linux (Ubuntu)

    Purpose: Testing Alerts

    IP Address: 192.168.1.105

- ### ELK

    Operating System: Linux (Ubuntu)

    Purpose: Log management

    IP Address: 192.168.31.100

- ### Kali Linux

    Operating System: Linux

    Purpose: Attack Machine

    IP Address: 192.168.1.90

- ### Hypervisor

    Operating System: Windows

    Purpose: Gateway

    IP Address: 192.168.1.1

***

## Description of Targets

Attacks were detected on IP address 192.168.1.110 (Target1), and  IP address 192.168.115 (Target2).

Target1 is an Apache web server with SSH enabled, so ports 80 and 22 are possible gates of entry for attackers. 

Target 2 is a copy of Target 1 but with an increased security. So it has the same points for entry through port 22 and 80.

***

## Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

- ### Alert 1: Excessive HTTP Errors

    It is implemented as follows:

    - **Metric:** Packetbeat: 'WHEN count() GROUPED OVER top 5 'http.response.status_code' IS ABOVE 400 FOR THE LAST 5 minutes'.

    - **Threshold:** When a grouped status code with values over 400 are in the top 5 within 5 minutes.

    - **Vulnerability Mitigated:** Detects Brute Force Attack and Enumeration of the web server, but it will not stop the attack.

    - **Reliability:** High. The alert detects enumeration and brute force attacks on the web server. Because error codes are over 400, the threshold makes unlikely the existence of false positives since all successful responses will be filtered out.

    This graphics shows an enumeration of the webserver (in red) and several successful requests of the site (in green).

    ![2](/Resources/Images/4/2.png)

- ### Alert 2: HTTP Request Size Monitor

    It  is implemented as follows:

    - **Metric:** Packetbeat: 'WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute'.

    - **Threshold:** More than 3500 bytes in a minute.

    ***Vulnerability Mitigated:** Detects illegal downloads or uploads and code injections. The alert will detect this events but will not stop them.

    **Reliability:** Medium. A large amount of legitimate http requests could lead to false positives.

    ![3](/Resources/Images/4/3.png)

- ### Alert 3: CPU Usage Monitor

    It is implemented as follows:

    - **Metric:** Metricbeat: 'WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.5 FOR THE LAST 5 minutes'.

    - **Threshold:** When the CPU usage goes over 0.5 in five minutes

    - **Vulnerability Mitigated:**  Will detect DOS attacks or malware using the CPU resources as the use increases the CPU usage over the 50% for more than five consecutives minutes. The alert only detects and will not prevent these events.

    - **Reliability:** HIGH and MEDIUM. If statistical CPU usage is usually lower than 50%, this rule would effectively detect a Denial Of Service attack. Malware could be detected if the malware activity increases more than 50% of the CPU usage in five consecutive minutes. A stealthy malware could use low CPU resources and be undetected. Likewise, false positives could exist as legitimate software increases CPU usage.

    This graphics shows the activity of a malware in the system.

    ![4](/Resources/Images/4/4.png)

***

## Suggestions for Going Further

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:
 
### CWE-200: Exposure of Sensitive Information to an Unauthorized Actor

- **Hardening:** Web Server / Wordpress

    The Site should be sanitized of sensitive information, and its design should be modified using security guidelines.

- **Why It Works:**

    Sensitive data will not be exposed to any authorized personnel or malicious actors. Example: The flag1 will be taken out from the file service.html.

### CWE-521: Weak Password Requirements (Brute Force Attacks/Enumeration)

- **Hardening:** Web Server / Wordpress

    - Implement a policy to require strong passwords(minimum 8 characters long, at least one capital, lowercase, number and symbol). 
    
    - Implement Accounts Lockout policies to define account lockout threshold and duration.

    - Implement Multi Factor authentication (MFA). Conditional access policies can be implemented to check if users log on with MFA in order to grant access to the resources(files and folders).

- **Why It Works:**

    - A strong password policy will prevent guessing the password and will difficult any brute force attack.

    - Account lockout policies help to mitigate or slow down brute force attacks and enumeration.

    - MFA and a conditional access scheme would prevent unauthorized personnel from gaining access to sensitive files like wp-admin or wp-login.php even if they got a password.

### CVE-2016-10033 Remote Code Execution Vulnerability in PHPMailer

- **Hardening:** Upgrade PHPMailer version to 6.6.3 and maintain automatics updates.

- **Why It Works:**

    Companies are continually monitoring and patching their systems against vulnerabilities. Maintaining up to date with patches prevents attackers to exploit a well-known vulnerability.
