# Web Server Enumeration

**Step 0**: Preparation
```bash
# The next Ip of own Ip is target IP
ifconfig

# Start Metasploit and create workspace
service postgresql start && msfconsole
workspace -a <workspace name>

# Set target IP grobally
setg RHOSTS <target IP>
setg RHOST <target IP>
```

**Step 1**: Gather information
```bash
# Find module for http
search type:auxiliary name:http

# http version detection
# http header detection for potion
# Search robots module to find sensitive directory
# Show page with crul
# directory,file directory brute force before username and passwrd brute force