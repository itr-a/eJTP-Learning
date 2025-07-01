## Windows Recon: SMB Nmap Scripts
Target machine : demo.ine.local  

NOTE
- SMB : Server Management Block
- File sharing protocol used mainly on Windows systems
- It lets users access shared folder, files, printers
- Works over TCP port 445 (sometimes 139)

#### 1.Identify SMB Protocol Dialects
```bash
nmap - 445,139 target.ine.local demo.ine.local
```
Output
```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-01 07:16 IST
Failed to resolve "-".
Bare '-': did you put a space between '--'?
Failed to resolve "445,139".
Failed to resolve "target.ine.local".
Nmap scan report for demo.ine.local (10.0.27.155)
Host is up (0.0023s latency).
Not shown: 992 closed tcp ports (reset)
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.44 seconds
```
List shared folders
*-L*  : lists shares
*-N* : means "no password" for anonymous access
```bash
smbclient -L //demo.ine.local/ -N
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
