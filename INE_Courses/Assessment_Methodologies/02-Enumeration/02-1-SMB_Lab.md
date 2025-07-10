# SMB Enumeration Lab

### Objective: Answer the following questions

**0. Preparation**
```bash
# target IP = won IP +1 (last octed: 3)
ifconfig
```

**1. Find the default tcp port by smbd**
```bash
nmap -sV -O -Pn <target IP> 

# Output
~~~
PORT    STATE SERVICE     VERSION
<ANSWER_1>/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: <ANSWER_3>)
<ANSWER_1>/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: <ANSWER_3>)
~~~
Service Info: Host: <ANSWER_6>
```

**2. Find the default udp ports used by nmbd**
```bash
# UDP port scap = -sU
nmap -sU -F -T4 <taraget IP>
nmap -sU --top-ports 25 <domain>

# Otput for the first command
PORT    STATE SERVICE
<ANSWER_2>/udp open  netbios-ns

# Output for the second command
PORT      STATE         SERVICE
53/udp    open|filtered domain
67/udp    open|filtered dhcps
68/udp    closed        dhcpc
69/udp    open|filtered tftp
111/udp   open|filtered rpcbind
123/udp   closed        ntp
135/udp   closed        msrpc
<ANSWER_1>/udp   open          netbios-ns
<ANSWER_1>/udp   open|filtered netbios-dgm
139/udp   closed        netbios-ssn
161/udp   closed        snmp
162/udp   open|filtered snmptrap
445/udp   closed        microsoft-ds
500/udp   open|filtered isakmp
514/udp   closed        syslog
520/udp   open|filtered route
631/udp   open|filtered ipp
998/udp   closed        puparp
1434/udp  open|filtered ms-sql-m
1701/udp  closed        L2TP
1900/udp  open|filtered upnp
4500/udp  closed        nat-t-ike
5353/udp  closed        zeroconf
49152/udp closed        unknown
49154/udp open|filtered unknown
MAC Address: 02:42:C0:3D:DE:03 (Unknown)
```

**3. What is the workgroup name of the samba server**
```bash
nmap -sV -p 445 <target>

# Output
PORT    STATE SERVICE     VERSION
<ANSWER_1>/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: <ANSWER_3>)
MAC Address: 02:42:C0:3D:DE:03 (Unknown)
Service Info: Host: <ANSWER_6>

```

**4. Find the exact version of samba server by using appropriate nmap script**
```bash
nmap --script smb-os-discovery.nse -p 445 <domain>

# Output
PORT    STATE SERVICE
<ANSWER_1>/tcp open  microsoft-ds
MAC Address: 02:42:C0:3D:DE:03 (Unknown)

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba <ANSWER_4>)
|   Computer name: demo
|   NetBIOS computer name: <ANSWER_6>\x00
|   Domain name: ine.local
|   FQDN: demo.ine.local
|_  System time: 2025-07-10T05:49:30+00:00
```

**5. Find the exact version of samba server by using smb_version metasploit**
```bash
# Start PostgreSQL and Metasploit console
service postgresql start && msfconsole
search type:auxiliary name:smb
use 38
set RHOSTS <target IP>
run

# Output
[*] 192.61.222.3:445      - SMB Detected (versions:1, 2, 3) (preferred dialect:SMB 3.1.1) (compression capabilities:) (encryption capabilities:AES-128-CCM) (signatures:optional) (guid:{626d6173-2d61-6572-636f-6e0000000000}) (authentication domain:SAMBA-RECON)
[*] 192.61.222.3:445      -   Host could not be identified: Windows 6.1 (Samba <ANSWER_5>)
[*] 192.61.222.3:         - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

**6. What is the NetBIOS computer name of samba server? Use appropriate nmap scripts.**
```bash
nmap --script smb-os-discovery.nse -p 445 <domain>

# Output
PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:3D:DE:03 (Unknown)
|_  System time: 2025-07-10T05:49:30+00:00
```

**7. Find the NetBIOS computer name of samba server using nmblookup**
```bash
nmblookup -A <target IP>

# Output
Looking up status of 192.61.222.3
        SAMBA-RECON     <00> -         H <ACTIVE> 
        SAMBA-RECON     <03> -         H <ACTIVE> 
        <ANSWER_7>     <20> -         H <ACTIVE> 
        ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE> 
        RECONLABS       <00> - <GROUP> H <ACTIVE> 
        RECONLABS       <1d> -         H <ACTIVE> 
        RECONLABS       <1e> - <GROUP> H <ACTIVE> 

        MAC Address = 00-00-00-00-00-00
```

**8. Using smbclient determine whether anonymous connection (null session) is allowed on the samba server or not.**
```bash
smbclient -L <target IP> -N

# Output
Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      
        john            Disk      
        aisha           Disk      
        emma            Disk      
        everyone        Disk      
        IPC$            IPC       IPC Service (samba.recon.lab)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        RECONLABS            SAMBA-RECON
```

**9. Using rpcclient determine whether anonymous connection (null session) is allowed on the samba server or not.**
```bash
rpcclient -U "" -N <target IP>

# Output
rpcclient $>
```