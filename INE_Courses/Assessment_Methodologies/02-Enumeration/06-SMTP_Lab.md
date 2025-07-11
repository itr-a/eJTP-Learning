# SMTP Lab

### Objective: Answer the following questions:

**What is the SMTP server name and banner**
```bash

# Banner grabber (Failed?)
msf6 auxiliary(scanner/smtp/smtp_version) > run

[+] 192.142.167.3:25      - 192.142.167.3:25 SMTP 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.\x0d\x0a
[*] 192.142.167.3:25      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

# Banner! (but not didn't notice "Postfix" was server name! Also got domain name unexpectedly...)
msf6 auxiliary(scanner/smtp/smtp_enum) > run

[*] 192.142.167.3:25      - 192.142.167.3:25  Banner: 220 openmailbox.xyz ESMTP  Postfix: Welcome to our mail server.
[+] 192.142.167.3:25      - 192.142.167.3:25 Users found: , _apt, admin, administrator, backup, bin, daemon, games, gnats, irc, list, lp, mail, man, news, nobody, postfix, postmaster, proxy, sync, sys, uucp, www-data
[*] 192.142.167.3:25      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```


> Answer
```bash

nmap -sV -script banner demo.ine.local

Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-11 10:22 IST
Nmap scan report for demo.ine.local (192.142.167.3)
Host is up (0.000027s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_banner: 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
MAC Address: 02:42:C0:8E:A7:03 (Unknown)
Service Info: Host:  openmailbox.xyz

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.40 seconds
```


**Connect to SMTP service using netcat and retrieve the hostname of the server (domain name)**
> Answer
```bash
nc demo.ine.local 25

220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
```

**Does user “admin” exist on the server machine? Connect to SMTP service using netcat and check manually**
> Answer: Yes
```bash
nc demo.ine.local 25


220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
VRFY admin@openmailbox.xyz
252 2.0.0 admin@openmailbox.xyz
```

**Does user “commander” exist on the server machine? Connect to SMTP service using netcat and check manually**
>Answer: No
```bash
VRFY commander@openmailbox.xyz

550 5.1.1 <commander@openmailbox.xyz>: Recipient address rejected: User unknown in local recipient table
```


**What commands can be used to check the supported commands/capabilities? Connect to SMTP service using telnet and check**
> HELO attacker.xyz
> EHLO attacker.xyz
```bash
telnet demo.ine.local 25
HELO attacker.xyz

250 openmailbox.xyz

EHLO attacker.xyz

EHLO attacker.xyz
250-openmailbox.xyz
250-PIPELINING
250-SIZE 10240000
250-VRFY
250-ETRN
250-STARTTLS
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250 SMTPUTF8
```

**How many of the common usernames present in the dictionary /usr/share/commix/src/txt/usernames.txt exist on the server. Use smtp-user-enum tool for this task**
> 8
```bash
smtp-user-enum -U /usr/share/commix/src/txt/usernames.txt -t demo.ine.local

~~~
######## Scan started at Fri Jul 11 12:29:00 2025 #########
 existse.local: admin
 existse.local: administrator
 existse.local: mail
 existse.local: postmaster
 existse.local: root
 existse.local: sales
 existse.local: support
demo.ine.local: www-data exists
######## Scan completed at Fri Jul 11 12:29:01 2025 #########
```


**How many common usernames present in the dictionary /usr/share/metasploit-framework/data/wordlists/unix_users.txt exist on the server. Use suitable metasploit module for this task**
> 22 usernames
```bash
# Enumeration
msf6 auxiliary(scanner/smtp/smtp_enum) > run

[*] 192.142.167.3:25      - 192.142.167.3:25  Banner: 220 openmailbox.xyz ESMTP  Postfix: Welcome to our mail server.
[+] 192.142.167.3:25      - 192.142.167.3:25 Users found: , _apt, admin, administrator, backup, bin, daemon, games, gnats, irc, list, lp, mail, man, news, nobody, postfix, postmaster, proxy, sync, sys, uucp, www-data
[*] 192.142.167.3:25      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

**Connect to SMTP service using telnet and send a fake mail to root user**
```bash
telnet demo.ine.local 25
HELO attacker.xyz
mail from: admin@attacker.xyz
rcpt to:root@openmailbox.xyz
data
Subject: Hi Root
Hello,
This is a fake mail sent using telnet command.
From,
Admin
.

250 2.0.0 Ok: queued as B40457CADE0
```

**Send a fake mail to root user using sendemail command**
```bash
sendemail -f admin@attacker.xyz -t root@openmailbox.xyz -s demo.ine.local -u Fakemail -m "Hi root, a fake from admin" -o tls=no

Jul 11 12:37:55 ine sendemail[5224]: Email was sent successfully!
```


### Note
`Banner grabbing`\
Banner grabbing is a technique used to gain information about a computer system on a network and the services running on its open ports. 
```bash
nc www.targethost.com 80
```