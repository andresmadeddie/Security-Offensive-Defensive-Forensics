# Security Offensive Defensive Forensics

# About this Project

> Note: The pen-testing environment was set through a hypervisor inside a Windows Virtual Machine running in Azure Lab Services.  All the processes of Offense, Defense, and Forensic were developed individually. The final presentation and subsequent exposition were worked as a group.

***

## Table of Contents

1. Content of the project: Enumeration of documents in this repository

2. Tools used in the project

3. The Narrative behind the project

4. Recreate the PenTest Lab

***

## 1. Content of the project

- The project consists of three parts:

    - Defensive Security: Implement alerts and thresholds in Kibana.
    - Offensive Security: Assess a vulnerable VM and verify that the Kibana rules work as expected.
    - Network Forensics: Analyze live malicious traffic

- Descriptions of the folders and files

    - **[PRESENTATION File](Presentation.pdf):** It is the summary of the project focusing on a Red Team perspective.    

    - **[REPORTS Folder](/Reports/):** It holds the reports of the defensive, offensive, and forensic analyses. 

    - **[WALKTHROUGH Folder](/WalkThroughs/):** It is a detailed step-by-step to discover and exploit the vulnerabilities of targe1 and target2. 

***

## 2. Tools used in the project

|  |  |  |  |
| --- | --- | --- | --- |
|- Wireshark |  - Tcpreplay | - Kali Linux | - Q shell |
| - Namp | - Nikto | - Gobuster | - FireFox Browser | 
|- Searchsploit | - Bash Script | - NetCat | - Wpscan |
| - Google Chrome Browser | - Google Developers Tool | - SSH | - Bash Commands|
| - Mysql | - John the Ripper | - Python Script | - Kibana

***

## 3. The Narrative behind the project

You are working as a Security Engineer for X-CORP, supporting the SOC infrastructure. SOC analysts have noticed some discrepancies with the alerts in the Kibana system, so the manager asked the Security Engineering team to investigate.

First, your team needs to set up new alerts in Kibana. Penetration testing will then be performed to test alerts for vulnerabilities. Finally, the security team requested live traffic monitoring on the cable to detect any anomalies not reflected in the alert system. They have evidence that there are people misusing the network. Specifically, they have received reports on:

- Time thieves
- Infected Machine
- Illegal Downloads.

You should report your findings to the SOC manager and engineering manager with the appropriate analysis.

***

## 4. Recreate the PenTest Lab

This Pen-testing environment can be recreated in Virtual Box using the following tools:

- [Kali Linux .ova file](https://kali.download/virtual-images/kali-2022.2/kali-linux-2022.2-virtualbox-amd64.ova)

- [Raven 1 .ova file](https://download.vulnhub.com/raven/Raven.ova)

- [Raven 2 .ova file](https://download.vulnhub.com/raven/Raven2.ova)

- A DHCP server in Virtual Box

#### Note: the links download the files from the official's sites of kali Linux and VulnHub

---

## Pentest Lab creation Walkthrough

### **Upload files to Virtual Box**

After downloading the three ova files(Virtual Machines) upload them into Virtual Box.
Steps to upload them from the Virtual Box Menu

1. File
2. Import Appliance
3. (Browse for the .ova file)

### **Change Network Settings**

Change setting of each VM to have the same internal network. Call the network "pentest" (all lowercase. This is only important for the payload to set the DHCP server)

### **DHCP Server**

Open the command Prompt of your machine and go to the Virtual Box path.

For windows type: 

    cd /Program Files/Oracle/VirtualBox

Then, type:

    vboxmanage dhcpserver add --network=pentest --server-ip=192.168.1.1 --lower-ip=192.168.1.80 --upper-ip=192.168.1.120 --netmask=255.255.255.0 --enable 
    
### **Optional** 

To set the machines with the same IP address as this exercise:

- target1: 192.168.1.110 
- target2: 192.168.1.115
- kali: 192.168.90

Modify the next payloads and run them in the same place path the DHCP server was set.

target1: `vboxmanage dhcpserver modify --network=Raven --mac-address=080027BBBDB1 --fixed-address=192.168.1.110`

target2: `vboxmanage dhcpserver modify --network=Raven --mac-address=080027FABB89 --fixed-address=192.168.1.115`

kali: `vboxmanage dhcpserver modify --network=Raven --mac-address=080027DB966A --fixed-address=192.168.1.90`

> Search for the **Mac address** of each machine in their respective setting on the virtual box and then proceed to modify the Mac address on the script with the ones of your machines. (--mac-address="Raven Machine Mac Address)