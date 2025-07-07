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
#### Commands
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

`migrate <PID>`\
- Meterpreter command
- Moces active Meterpreter session **from the current process** to **another process** running on the target system
- <PID> : Process ID
Why migrate?\
- Some processes are more stable or have higher privileges (like explorer.exe or services.exe).
- If your current session is running inside a short-lived or restricted process, migrating helps you keep control longer or get better permissions.
- It can help avoid crashing the exploited process and losing your session.
How does it work technically?
-Your Meterpreter code injects itself into the memory space of the new target process.
- Then it starts running from there.
- This lets your session survive if the original exploited process exits.
