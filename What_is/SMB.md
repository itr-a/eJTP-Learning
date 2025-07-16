# What is SMB

**SMB** = **Server Message Block**

It's a Windows protocol that allows devices to:
- Share files
- Access printers
- Manage network shares
- Send Windows-specific commands remotely

> Think of SMB like the "remote file system and device control" for Windows computers.

It runs over:
TCP port 445 (modern versions)
TCP port 139 (NetBIOS/legacy)

| Tool         | Purpose                             |
| ------------ | ----------------------------------- |
| `smbclient`  | Like FTP for SMB shares             |
| `enum4linux` | Extract users, shares, OS info      |
| `smbmap`     | Find readable/writable shares       |
| `nmap`       | Scan for SMB versions & vulns       |
| `msfconsole` | Run SMB exploits (like EternalBlue) |
