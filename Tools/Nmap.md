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
