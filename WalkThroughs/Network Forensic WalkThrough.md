# **Forensic Walkthrough**

## Overview
The Security team has requested to monitor live traffic on the wire to detect any abnormalities not reflected in the alerting system. They have evidence that people are misusing the network. Specifically, they've received tips about:

- Time thieves
- Infected Machine
- Illegal Downloads.

You must report your findings to the SOC manager and the engineering manager with appropriate analysis.

---

Tools: 

- Wireshark
- Tcpreplay

File: Use the next command to download the file

		curl -L -o pcap.pcap http://tinyurl.com/yaajh8o8

##### Note: Wireshark and tcpreplay were used to create the file. Nevertheless, the file can be downloaded from a third-party link. It is strongly recommended to use a sandbox for this task as you will be dealing with live malware.

---

## **Walkthrough**

- ## Time Thieves

	---
	### Context: 

	At least two users on the network have been wasting time on YouTube. Usually, It wouldn't pay much mind to this behavior, but it seems these people have created their own web server on the corporate network. So far, Security knows the following about these time thieves:

	- They have set up an Active Directory network.
	- They are constantly watching videos on YouTube.
	- Their IP addresses are somewhere in the range 10.6.12.0/24.

	---

	1. DOMAIN NAME OF USER CUSTOM SITE

		wireshark query: `Ip.addr ==10.6.12/24 and bootp`

		![2](/Resources/Images/3/2.png)
		
		**Domain name of the user custom site:** `frank-n-ted.com`

	---

	2. DOMAIN CONTROLLER IP ADDRESS
		
		**Ip address of domain controller:** `10.6.12.12`

		######  Note: obtained from the previous query (look picture)

	---

	3. DOWNLOADED FILE

		- From the Menu: `Statistics / HTTP / Requests`

		- Display filter: `ip.addr == 10.6.12.203`

		![3](/Resources/Images/3/3.png)

		**Downloaded file name:** `june11.dll`

	---

	4. ANALIZE WITH TOTAL VIRUS

		- Download the file: `file /export objects / http /`

		- Text filter: `june11`

		- `Save`

		![4](/Resources/Images/3/4.png)

		- Go to virustotal.com

		- Upload file

		![5](/Resources/Images/3/5.png)

		**Malware is a** `Trojan`

---

- ## vulnerable Windows Machine

	---

	### Context: 
		
	The Security team received reports of an infected Windows host on the network. They know the following:

	- Machines in network live in the range 172.16.4.0/24.
	- The domain mind-hammer.net is associated with the infected computer.
	- The DC for this network lives at 172.16.4.4 and is named Mind-Hammer-DC.
	- The network has standard gateway and broadcast addresses.

	---

	1. HOST NAME

		- From the Menu: `view / name resolution / (check) Resolve Network Address`

		- In Search bar: `Ip.addr == 172.16.4.0/24 and nbns`

		- In the middle wireshark screen: `NetBios Name Service / Additional records / Name`

		![6](/Resources/Images/3/6.png)

		**Host name:** `ROTTERDAM-PC`

	---

	2. IP ADDRESS

		- Form the Menu: `view / name resolution / (uncheck) Resolve Network Address`

		![7](/Resources/Images/3/7.png)

		**Ip Address** `172.16.4.205`

	---

	3. WINDOWS USER NAME

		- In Search bar: `Ip.addr == 172.16.4.205 kerberos.CNameString`

		- In the middle wireshark screen: `Kerberos / as-req / req-body / cname / CNameString`

		- Right click on `CNameString field` and `apply as a column`

		- *Search for username with the new column*

		![8](/Resources/Images/3/8.png)

		**Windows User Name:** `matthijs.derives`

	---

	4. IP ADDRESSES

		From Search: `Ip.addr == 172.16.4.205 and http.request.method ==”POST”`

		![9](/Resources/Images/3/9.png)

		**Ip addresses:** `172.16.4.205 - 31.7.62.214 - 185.243.115.84`

	---

	5. BACKGROUNG

		- In the search bar: `Ip.addr == 172.16.4.205 and and ip.addr == 185.243.115.84 and http.request.method == “POST”`

		![10](/Resources/Images/3/10.png)

		- *Copy the name of the file in the info column*

		- On the menu: `File / Export Objects / http`

		- *Paste the name of the file in the filter*

		![11](/Resources/Images/3/11.png)

		- *Save it and open it*

		![12](/Resources/Images/3/12.png)

---

- ## Illegal Downloads

	---

	### Context: 
		
	There is an informed that some users are torrenting on the network. The Security team does not forbid the use of torrents for legitimate purposes, such as downloading operating systems. However, they have a strict policy against copyright infringement.

	IT shared the following about the torrent activity:

	- The machines using torrents live in the range 10.0.0.0/24 and are clients of an AD domain.
	- The DC of this domain lives at 10.0.0.2 and is named DogOfTheYear-DC.
	- The DC is associated with the domain dogoftheyear.net.
	
	Isolate torrent traffic and identified.

	---

	1. MAC ADDRESS 

		- In the search: `ip.addr == 10.0.0.201`

		- If the destination address is the mac address to look for

		- In the middle wireshark screen: `Ethernet II / Destination / Address`

		![13](/Resources/Images/3/13.png)

		**Mac Address:** `00:16:17:18:66:c8`

	---

	2. WINDOWS USER NAME

		- In the search: `Ip.addr == 10.0.0.201  kerberos.CNameString`

		- In the middle wireshark screen: `Kerberos / as-req / req-body / cname / CNameString`

		- *Right click on CNameString field and apply as a column*

		- *Search for username with the new column*

		![14](/Resources/Images/3/14.png)

		**Windows User Name:** `elmer.blanco`

	---

	3. OS VERSION 

		- From search: `Ip.addr == 10.0.0.201 and http.request.method == “GET”`

		- In the middle wireshark screen: `Hypertext Transfer Protocol / User Agent`

		![15](/Resources/Images/3/15.png)

		**OS version:** `Windows 64 NT 10.0`

	---

	4. TORRENT DOWNLOADED

		- From the Menu: Statistics / HTTP /  Requests

		- Filter: ip.addr == 10.0.0.201

		![16](/Resources/Images/3/16.png)

		**Torrent:** `Betty_Boop_Rhythm_on_the_Reservation.avi.torrent`