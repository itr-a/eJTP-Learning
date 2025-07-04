# 👁️‍🗨️ Nmap

`-sS`  
- Identifying open ports  
- TCP SYN Scan
- Default and most popular scan
```bash
+------------+                                               +------------+
|            |  ------SYN (Request port 22 connection)---->  |            |
|            |  <-----SYN/ACK (It's open!)-----------------  |            |
|            |  ------RST (No, forget it)----------------->  |            |
+------------+                                               +------------+
     Atk                                                         target
```

`-sn`
- Ping scan
- Useful when only want to discover live devices on the network
- Identifying target IP

---

`-sC`
- Run default scripts
- With `-sV` give a lot of detailed info quickly. This combo is often used in early-stage recon.

---

`-A`
- `sC`+`sV`

---
#### --script 
`--script=http-enum`
- Nmap NSE (Nmap Scripting Engine) script used to enumerate common web application paths on an HTTP server.
- Checks for the presentce for commnly known web paths such as:
  /admin
  /login
  /uploads etc
- It does not brute-force — instead, it uses a predefined list of common paths to quickly detect known pages or services.
- Sample output:
```bash
PORT   STATE SERVICE
80/tcp open  http
| http-enum:
|   /admin/ => 403 Forbidden
|   /login/ => 200 OK
|   /phpmyadmin/ => 200 OK
|   /wordpress/ => 301 Moved Permanently
|_  /uploads/ => 200 OK
```
