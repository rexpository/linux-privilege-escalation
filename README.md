<p align="left"><img src="https://static1.makeuseofimages.com/wordpress/wp-content/uploads/2022/03/sudo-command-unsplash.jpg" width="60%" height="60%"></p>

# Linux Privilege Escalation
Bash script to check and exploit the CVE-2022-0847 Linux "Dirty Pipe" vulnerability

## About this Proof of Concept
This script allows an unprivileged user on a vulnerable system to do the following:
- Modify/overwrite *read-only* files like /etc/passwd.
- Obtain an elevated shell

This repo contains 2 exploits:
### Exploit 1: 
Replaces the root password with the password "piped" and backups the original /etc/passwd file under /tmp/passwd.bak. Then, the exploit provides you with access to an elevated root shell and restores the original passwd file when you exit the shell.
### Exploit 2:
Injects and overwrites data in read-only SUID process memory that run as root.

# Usage
Clone this repository and change working direcotry
```
git clone https://github.com/rexpository/linux-privilege-escalation.git
cd linux-privilege-escalation
```
Check if the current target system is vulnerable
```
./check.sh
```
Install GCC to compile the exploit
```
sudo apt-get install gcc
```
Compile and run the bashscript to automate the compilation of both exploits
```
chmod +x compile.sh
./compile.sh
```
Run your desired exploit binary
```
./exploit-1
```
or
```
./exploit-2 /usr/bin/sudo
```
![Demo-1](./Demo-1.png)
![Demo-2](./Demo-2.png)

# Technical Details
This vulnerability resides in the *pipe* tool used for unidirectional communication between processes, hence the name *"Dirty Pipe"*. 

An unprivileged local user could exploit this vulnerability to overwrite supposedly read-only files in the Linux kernel and as such, escalate their privileges on the system. 

This vulnerabilty occurs due to the usage of partially uninitialized memory of the *pipe* buffer structure during its construction. A lack of zero initialization of the new structures's member results in a stale value of *flags*, which can be abused by anattacker to gain write acces to pages in the cache even if they originally were marked with a *read-only* attribute. 

# Mitigations
To ensure that your infrastructure is protected against this and similar threats:
- Apply all relevant security updates once they are available. To patch CVE-2022-0847, update your Linux systems to version 5.16.11, 5.15.25, and 5.10.102 or newer. 
- Use a security solution that provides patch management and endpoint protection.
- Use the latest Threat Intelligence information to stay aware of actual TTPs used by threat actors. 

# Credits
- Credits to Max Kellermann <max.kellermann@ionos.com>
- Check out the official disclosure here: https://dirtypipe.cm4all.com/
