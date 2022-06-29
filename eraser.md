The pen-testing environment was set through a hypervisor inside a Windows Virtual Machine running in Azure Lab Services. 

All the processes of Offense, Defense, and Forensic were developed individually. 

The final PowerPoint presentation and subsequent exposition were worked as a group.

This Pen-testing environment can be recreated in Virtual Box using the following tools:
- [Kali Linux ova file](https://kali.download/virtual-images/kali-2022.2/kali-linux-2022.2-virtualbox-amd64.ova)

- [Raven 1 ova-file](https://download.vulnhub.com/raven/Raven.ova)

- [Raven 2 ova-file](https://download.vulnhub.com/raven/Raven2.ova)

Create a DHCP server in Virtual Box
Note: the links lead to the official's sites of kali Linux and VulnHub

Setting the Pentest Lab walkthrough:
After downloading the three ova files(Virtual Machines) upload them into Virtual Box:
File / Import Appliance / (Browse for the iso file)

After the previous step, Change the network setting of each VM to the same internal network and set the name of the network as "pentest: (all lowercase)

To set the DHCP Server

Open the command Prompt of your machine and go to Virtual Box path.

For windows type: 

    cd /Program Files/Oracle/VirtualBox

Then, input

    vboxmanage dhcpserver add --network=Raven --server-ip=192.168.1.1 --lower-ip=192.168.1.100 --upper-ip=192.168.1.120 --netmask=255.255.255.0 --enable 
