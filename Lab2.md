# Assessment Methodologies: Footprinting and Scanning CTF 1 
#### Flag 1: The server proudly announces its identity in every response. Look closely; you might find something unusual.
This strongly hints at a **Server banner** which is often found in HTTP response headers. <br>
HTTP pages don't always contain the same body content - **but headers are return on every requests**.
```bash
# Check HTTP Header!
curl -I target.ine.local

# Output
HTTP/1.1 200 OK
Server: Werkzeug/3.0.6 Python/3.10.12
Date: Mon, 30 Jun 2025 05:36:58 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 2557
Server: FLAG1_blahblahblah
Connection: close
```

