# About this Project

The pen-testing environment was set through a hypervisor inside a Windows Virtual Machine running in Azure Lab Services.  All the processes of Offense, Defense, and Forensic were developed individually. The final PowerPoint presentation and subsequent exposition were worked as a group.

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

    vboxmanage dhcpserver add --network=pentest --server-ip=192.168.1.1 --lower-ip=192.168.1.100 --upper-ip=192.168.1.120 --netmask=255.255.255.0 --enable 
    
### **Optional** 

To set the machines with the same IP address as this exercise:

- Raven 1: 192.168.1.110 
- Raven 2: 192.168.1.115

Modify the next payloads and run them in the same place path the DHCP server was set.

Raven 1: `vboxmanage dhcpserver modify --network=Raven --mac-address=080027BBBDB1 --fixed-address=192.168.1.110`

Raven 2: `vboxmanage dhcpserver modify --network=Raven --mac-address=080027FABB89 --fixed-address=192.168.1.115`

Search for the Mac address of the each Raven machine in their respective setting on virtual box and then proceed to modify the Mac address of the sript  with the ones of your machines. (--mac-address="Raven Machine Mac Address)
