# Windows Recon: SMB Nmap Scripts
Target machine : demo.ine.local  

NOTE
- SMB : Server Management Block
- File sharing protocol used mainly on Windows systems
- It lets users access shared folder, files, printers
- Works over TCP port 445 (sometimes 139)

#### 1.Identify SMB Protocol Dialects
SMB protocol dialects = **version** of the SMB protocol
```bash
nmap --script smb-protocols -p 445,139 demo.ine.local
```
Output
```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-01 11:20 IST
Nmap scan report for demo.ine.local (10.0.29.147)
Host is up (0.0028s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2:0:2
|     2:1:0
|     3:0:0
|_    3:0:2

Nmap done: 1 IP address (1 host up) scanned in 5.19 seconds
```
List shared folders
*-L*  : lists shares
*-N* : means "no password" for anonymous access
```bash
smbclient -U administrator -L //demo.ine.local/ 
```
Output
```bash
Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C               Disk      
        C$              Disk      Default share
        D$              Disk      Default share
        Documents       Disk      
        Downloads       Disk      
        IPC$            IPC       Remote IPC
        print$          Disk      Printer Drivers
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to demo.ine.local failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
