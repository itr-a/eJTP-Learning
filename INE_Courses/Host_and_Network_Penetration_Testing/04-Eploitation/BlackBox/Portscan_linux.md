# Black Box Port Scanning & Enumeration - Linux

Nmap target, save output (nmap_10k.txt)
```bash
└─# nmap -sV -p1-1000 10.0.21.162 -oN nmap_10k.txt 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-28 11:51 IST
Nmap scan report for demo.ine.local (10.0.21.162)
Host is up (0.0026s latency).
Not shown: 988 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
23/tcp  open  telnet      Linux telnetd
25/tcp  open  smtp        Postfix smtpd
51/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp  open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp open  rpcbind     2 (RPC #100000)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp open  exec?
513/tcp open  login?
514/tcp open  tcpwrapped
Service Info: Host:  metasploitable.localdomain; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 64.15 seconds
```

Netcat if ports are open (the ports with "?")
```bash
└─# nc -nv 10.0.21.162 512
(UNKNOWN) [10.0.21.162] 512 (exec) open
ls
^C

┌──(root㉿INE)-[~]
└─# nc -nv 10.0.21.162 513
(UNKNOWN) [10.0.21.162] 513 (login) open
^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^C

┌──(root㉿INE)-[~]
└─# nc -nv 10.0.21.162 1524
(UNKNOWN) [10.0.21.162] 1524 (ingreslock) open
root@d1d6a9361621:/# 
```