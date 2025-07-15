# Enumeration CTF 1

**Step 0**: Preparation
```bash
nmap -sV -O targt.ine.local
```

**Flag 1**: There is a samba share that allows anonymous access. Wonder what's in there!

**Flag 2**: One of the samba users have a bad password. Their private share with the same name as their username is at risk!

**Step 1: Find Samba users with smb module**
![Modules](/../../../Screenshots/Enum_CTF_f2_modules.png)

**Set uername file and password file**
![2 user show options](.png)

#
