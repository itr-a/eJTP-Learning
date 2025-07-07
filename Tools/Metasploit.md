# 🔷 Metasploit

#### How it works
```bash
        ┌────────────────────┐
        │   Your Machine     │
        │ (Attacker / Kali)  │
        └──────┬─────────────┘
               │
               │ 1️⃣ Scan target for open ports & services
               │    e.g. Nmap, db_nmap
               ▼
        ┌────────────────────┐
        │  Target Machine     │
        │ (Server, Windows, etc)│
        └──────┬─────────────┘
               │
               │ 2️⃣ Find vulnerability
               │    e.g. SMB, MySQL, HTTP service, etc.
               ▼
        ┌──────────────────────────┐
        │  Metasploit Exploit Module│
        └──────┬────────────────────┘
               │
               │ 3️⃣ Set payload (reverse shell)
               │    + Options (RHOST, LHOST)
               ▼
        ┌──────────────────────────┐
        │    Exploit is launched!  │
        └──────────┬───────────────┘
                   │
            🎉 SUCCESS! Exploited!
                   │
                   ▼
        ┌──────────────────────────┐
        │ Meterpreter Session Open │
        │ (You’re in the system!)  │
        └──────┬───────────────────┘
               │
        ┌──────▼────────────┐
        │    You Can Now:   │
        │------------------│
        │ ✔ run shell       │
        │ ✔ browse files    │
        │ ✔ dump creds      │
        │ ✔ escalate privs  │
        │ ✔ scan LAN        │
        └───────────────────┘
```

`msfconsole`
- Start Metasploit console
  
`db_status`
- Confirm DB connection
  
`set/setg`
- Local/Global setting

`RHOSTS/RHOST`
- set target IP(s)

`workspace -a ----`
- Create and switch to workspace named ----

`hosts`
- Check OS info

`services`
- View discovered services

`info`
- View module info

`show options`
- Check required options

`analyze`
- Analyze vulnerabilities

