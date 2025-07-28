# Banner Grabbing

Banner grabbing with Nmap
```bash
└─# nmap -sV --script=banner 192.63.111.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-28 06:22 IST
Nmap scan report for demo.ine.local (192.63.111.3)
Host is up (0.000023s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
|_banner: SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
MAC Address: 02:42:C0:3F:6F:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.36 seconds
```

Banner grabbing with Netcat
```bash
└─# nc 192.63.111.3 22                                                    
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
```

Why these information important
```bash
└─# searchsploit openssh 7.2
---------------------------------------- ---------------------------------
 Exploit Title                          |  Path
---------------------------------------- ---------------------------------
OpenSSH 2.3 < 7.7 - Username Enumeratio | linux/remote/45210.py
OpenSSH 2.3 < 7.7 - Username Enumeratio | linux/remote/45233.py
OpenSSH 7.2 - Denial of Service         | linux/dos/40888.py
OpenSSH 7.2p1 - (Authenticated) xauth C | multiple/remote/39569.py
OpenSSH 7.2p2 - Username Enumeration    | linux/remote/40136.py
OpenSSH < 7.4 - 'UsePrivilegeSeparation | linux/local/40962.txt
OpenSSH < 7.4 - agent Protocol Arbitrar | linux/remote/40963.txt
OpenSSH < 7.7 - User Enumeration (2)    | linux/remote/45939.py
OpenSSHd 7.2p2 - Username Enumeration   | linux/remote/40113.txt
---------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```