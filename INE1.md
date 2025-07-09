# Assessment Methodologies: Footprinting and Scanning CTF 1 
Target machine : **http;//target.ine.local**<br>
Tools : Nmap, FTP, MySQL

#### Flag 1: The server proudly announces its identity in every response. Look closely; you might find something unusual.
This strongly hints at a **Server banner** which is often found in HTTP response headers. <br>
HTTP pages don't always contain the same body content - **but headers are return on every requests**.
```bash
# Check HTTP Header!
curl -I http://target.ine.local

# Output
HTTP/1.1 200 OK
Server: Werkzeug/3.0.6 Python/3.10.12
Date: Mon, 30 Jun 2025 05:36:58 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 2557
Server: FLAG1_xxxxxxxxxxxxxxxxxxxx <- flag!
Connection: close
```
#### Flag 2: The gatekeeper's instructions often reveal what should remain unseen. Don't forget to read between the lines.
-> robots.txt
```bash
curl http://target.ine.local/robts.txt
```
Output
```bash
User-agent: googlebot 
Disallow: /photos

User-agent: *
Disallow: /secret-info/
Disallow: /data/
```
What this means...<br>
Don't allow googlebot to crawl /photos,<br>
Don't allow **any** bots to crawl /secret-info/ and /data/

Check what's inside the /secret-info/
```bash
curl http://target.ine.local/secret-info/
```
Output
```bash
["flag.txt"]
```
Now check flag.txt
```bash
curl http://target.ine.local/secret-info/flag.txt
```
Output
```bash
FLAG2_xxxxxxxxxxxxxxxxxxxxxxx
```
####  Flag 3: Anonymous access sometimes leads to forgotten treasures. Connect and explore the directory; you might stumble upon something valuable.
-> FTP
Because "Anonymous access" is very commonly associated with **FTP servers**<br>
Also, it's the most classic and immediately testable option!
```bash
# No "http://" because FTP is File Transfer "Protocol" so no need to specify
ftp tatget.ine.local
```
Output
```bash
Connected to target.ine.local.
220 (vsFTPd 3.0.5)
Name (target.ine.local:root):
```
Try common user name and password for FTP<br>
username : anonymous<br>
password : anonymous@ or just hit Enter (different this time)
```bash
Name (target.ine.local:root): anonymous
331 Please specify the password.
Password:
```
Output
```bash
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> .
```
```bash
# Check what's in the directory
ftp>ls
```
Output<br>
Found **creds.txt** and **flag.txt**
```bash
229 Entering Extended Passive Mode (|||22529|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              22 Oct 28  2024 creds.txt
-rw-r--r--    1 0        0              39 Jun 30 05:35 flag.txt
226 Directory send OK.
```
Downloaded both with `get`  
(because I didn't want to login again if there was no flag in flag.txt)
```bash
ftp> get creds.txt
local: creds.txt remote: creds.txt
229 Entering Extended Passive Mode (|||33561|)
150 Opening BINARY mode data connection for creds.txt (22 bytes).
100% |**************************************************************************************************************|    22      262.00 KiB/s    00:00 ETA
226 Transfer complete.
22 bytes received in 00:00 (32.25 KiB/s)
ftp> get flag.txt
local: flag.txt remote: flag.txt
229 Entering Extended Passive Mode (|||49717|)
150 Opening BINARY mode data connection for flag.txt (39 bytes).
100% |**************************************************************************************************************|    39      656.65 KiB/s    00:00 ETA
226 Transfer complete.
39 bytes received in 00:00 (82.43 KiB/s)
ftp>exit
```
Open file
```bash
ls                                                                                                                                                     
creds.txt  Desktop  Documents  Downloads  flag.txt  Music  Pictures  Public  Templates  thinclient_drives  Videos

cat flag.txt                                                                                                                                           
FLAG3_xxxxxxxxxxxxxxx
```
#### Flag 4: A well-named database can be quite revealing. Peek at the configurations to discover the hidden treasure.
Try MySQL when **database** mentioned  
Check if port 3306 is open (default MySQL port)  
(No "http://" because nmap works *port level* not URL level)
```bash
nmap -p 3306 target.ine.local
```
Output
```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-30 14:16 IST
Nmap scan report for target.ine.local (192.103.140.3)
Host is up (0.000058s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
MAC Address: 02:42:C0:67:8C:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.10 seconds
```
Username and password to login is in **creds.txt**
```bash
cat creds.txt
db_admin:password@123
```
Connect to MySQL  
`-u`: username  
`-h`: host  
`-p`: tell mysql to ask for a password
```bash
mysql -u db_admin -h target.ine.local -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 28
Server version: 8.0.39-0ubuntu0.22.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Support MariaDB developers by giving a star at https://github.com/MariaDB/server
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
List database
```bash
MySQL [(none)]> show databases;
+----------------------------------------+
| Database                               |
+----------------------------------------+
| FLAG4_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX   |
| information_schema                     |
| mysql                                  |
| performance_schema                     |
| sys                                    |
+----------------------------------------+
5 rows in set (0.002 sec)
```

Some commands to explore database
```bash
# Show all the databases
show databases;
```
```bash
# Choose a database
use example_db;
```
```bash
# Show tables
show tables;
```
