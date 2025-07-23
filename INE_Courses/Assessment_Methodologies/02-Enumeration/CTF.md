# Enumeration CTF 1

**Step 0**: Preparation
```bash
nmap -sV -O targt.ine.local
```

**Flag 1**: There is a samba share that allows anonymous access. Wonder what's in there!
```bash
└─# nmap -sV -O 192.157.100.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 18:25 IST
Nmap scan report for target.ine.local (192.157.100.3)
Host is up (0.000060s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 02:42:C0:9D:64:03 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

msf6 > use auxiliary/scanner/smb/smb_login 
[*] New in Metasploit 6.4 - The CreateSession option within this module can open an interactive session

msf6 auxiliary(scanner/smb/smb_login) > smbclient //target.ine.local/IPC$ -U ""%""


└─# nmap -p 445,139 --script smb-protocols 192.157.100.3                  
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 19:15 IST
Nmap scan report for target.ine.local (192.157.100.3)
Host is up (0.000040s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:9D:64:03 (Unknown)

Host script results:
| smb-protocols: 
|   dialects: 
|     2:0:2
|     2:1:0
|     3:0:0
|     3:0:2
|_    3:1:1


└─# smbclient -L 192.157.100.3 -N                                         

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        IPC$            IPC       IPC Service (target server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
Protocol negotiation to server 192.157.100.3 (for a protocol between LANMAN1 and NT1) failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available


msf6 auxiliary(scanner/smb/smb_login) > use auxiliary/scanner/smb/smb_enumshares 
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf6 auxiliary(scanner/smb/smb_enumshares) > show options

msf6 auxiliary(scanner/smb/smb_enumshares) > set RHOSTS 192.157.100.3
RHOSTS => 192.157.100.3
msf6 auxiliary(scanner/smb/smb_enumshares) > 
msf6 auxiliary(scanner/smb/smb_enumshares) > run

[-] 192.157.100.3:139 - Login Failed: Unable to negotiate SMB1 with the remote host: Expecting SMB1 protocol with command=114, got SMB1 protocol with command=114, Status: (0x00000000) STATUS_SUCCESS: The operation completed successfully.
[!] 192.157.100.3:445 - peer_native_os is only available with SMB1 (current version: SMB3)
[!] 192.157.100.3:445 - peer_native_lm is only available with SMB1 (current version: SMB3)
[+] 192.157.100.3:445 - print$ - (DISK) Printer Drivers
[+] 192.157.100.3:445 - IPC$ - (IPC|SPECIAL) IPC Service (target server (Samba, Ubuntu))
[*] 192.157.100.3: - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed





```

**Flag 2**: One of the samba users have a bad password. Their private share with the same name as their username is at risk!

**Step 1: Find Samba users with smb module**
![Modules](/../../../Screenshots/Enum_CTF_f2_modules.png)

**Set uername file and password file**
![2 user show options](.png)

#
