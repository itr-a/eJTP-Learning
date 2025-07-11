# Web Server Enumeration Lab

### Objective: Run the following auxiliary modules against the target (victim-1):

**Preparation**
```bash
nmap victim-1

# IP address of victim-1
192.36.149.3
```

**auxiliary/scanner/http/apache_userdir_enum**
```bash
# This module determines if usernames are valid on a server running Apache with the UserDir directive enabled. 

# Output
[*] http://192.36.149.3/~ - Trying UserDir: ''
[*] http://192.36.149.3/ - Apache UserDir: '' not found
[*] http://192.36.149.3/~4Dgifts - Trying UserDir: '4Dgifts'
[*] http://192.36.149.3/ - Apache UserDir: '4Dgifts' not found
[*] http://192.36.149.3/~abrt - Trying UserDir: 'abrt'
[*] http://192.36.149.3/ - Apache UserDir: 'abrt' not found
[*] http://192.36.149.3/~adm - Trying UserDir: 'adm'
[*] http://192.36.149.3/ - Apache UserDir: 'adm' not found
[*] http://192.36.149.3/~admin - Trying UserDir: 'admin'
[*] http://192.36.149.3/ - Apache UserDir: 'admin' not found
[*] http://192.36.149.3/~administrator - Trying UserDir: 'administrator'
[*] http://192.36.149.3/ - Apache UserDir: 'administrator' not found
[*] http://192.36.149.3/~anon - Trying UserDir: 'anon'
[*] http://192.36.149.3/ - Apache UserDir: 'anon' not found
[*] http://192.36.149.3/~_apt - Trying UserDir: '_apt'
[+] http://192.36.149.3/ - Apache UserDir: '_apt' found 
```

**auxiliary/scanner/http/brute_dirs**
```bash
# Brute Fource directories
run

# Output
[*] Using code '404' as not found.
[+] Found http://192.36.149.3:80/doc/ 200
[+] Found http://192.36.149.3:80/pro/ 200
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

**auxiliary/scanner/http/dir_scanner**
```bash
# This module scans one or more web servers for interesting directories that can be futrher exploited
run

# Output
[*] Detecting error code
[*] Using code '404' as not found for 192.36.149.3
[+] Found http://192.36.149.3:80/cgi-bin/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/data/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/downloads/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/doc/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/icons/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/manual/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/secure/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/users/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/uploads/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/view/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/webadmin/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/web_app/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/webmail/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/webdb/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/webdav/ 404 (192.36.149.3)
[+] Found http://192.36.149.3:80/~nobody/ 404 (192.36.149.3)
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

# `404`: The browser successfully connected to the server, but the specific page or file the user was trying to access doesn't exist or has moved
```

**auxiliary/scanner/http/dir_listing**
```bash
# Identifies directory listing vulnerabilities in a given directory path
run

# Output
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

# ! Had to set RHOSTS = victim-1
```

**auxiliary/scanner/http/http_put**
```bash
# This module can abuse misconfigured web servers to upload and delete web content via PUT and DELETE HTTP requests.
run

# Output
[-] 192.36.149.3: File doesn't seem to exist. The upload probably failed
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

# ! Had to set RHOSTS victim-1
# ! DICTIONARY /usr/share/metasploit-framework/data/wordlists/directory.txt
```

**auxiliary/scanner/http/files_dir**
```bash
#identifies the existence of interesting files in a given directory path
run

# Output
[*] Using code '404' as not found for files with extension .null
[*] Using code '404' as not found for files with extension .backup
[+] Found http://192.36.149.3:80/file.backup 200
[*] Using code '404' as not found for files with extension .bak
[*] Using code '404' as not found for files with extension .c
[+] Found http://192.36.149.3:80/code.c 200
[*] Using code '404' as not found for files with extension .cfg
[+] Found http://192.36.149.3:80/code.cfg 200
[*] Using code '404' as not found for files with extension .class
~~~
```

**auxiliary/scanner/http/http_login**
```bash
# Attempts to authenticate to an HTTP service
run

# Output
[-] http://192.36.149.3:80 No URI found that asks for HTTP authentication
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

# ! Had to set AUTH_URI /secure/
```

**auxiliary/scanner/http/http_header**
```bash
# Shows HTTP Headers returned by the scanned systems
run

# Output
+] 192.36.149.3:80      : CONTENT-TYPE: text/html
[+] 192.36.149.3:80      : LAST-MODIFIED: Wed, 28 Aug 2024 08:56:57 GMT
[+] 192.36.149.3:80      : SERVER: Apache/2.4.18 (Ubuntu)
[+] 192.36.149.3:80      : detected 3 headers
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

**auxiliary/scanner/http/http_version**
```bash
run

# Output
[+] 192.36.149.3:80 Apache/2.4.18 (Ubuntu)
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

**auxiliary/scanner/http/robots_txt**
```bash
run

# Output
~~~
User-agent: *
# Directories
Disallow: /data
Disallow: /secure
~~~
```