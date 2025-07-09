# 🔍 Vulnerability Scanning with Metasploit Framework (MSF)

## 🌟 Objective
Explore the process of performing vulnerability scans using **Metasploit Framework**.

---

## 💻 Lab Environment
- Target: **Metasploitable3**
- Attacker: On **same local network**

---

## 🛠️ Vulnerability Scanning
- Scan a system for known vulnerabilities  
- Check if the discovered vulnerabilities are **exploitable**

---

## ⚙️ Step-by-Step Demo

### 🔎 Step 1: Identify Target IP
Run a ping scan to find live hosts:
```bash
sudo nmap -sn 10.10.10.1/24
```
To check your attacking IP:
```bash
ip a s
```

---

### 🧰 Step 2: Start Metasploit + Setup
1. Ensure PostgreSQL service is running  
2. Start Metasploit console:
```bash
msfconsole
```
3. Confirm DB connection:
```bash
db_status
```
Expected output:
```
Connected to msf. Connection type: postgresql.
```
4. Set target IP globally:
```bash
setg RHOSTS 10.10.10.4
setg RHOST 10.10.10.4
```
5. Create and switch to workspace:
```bash
workspace -a MS3
```
6. Perform full scan (SYN, service & OS detection):
```bash
db_nmap -sS -sV -O 10.10.10.4
```
7. Check OS info:
```bash
hosts
```
8. View discovered services:
```bash
services
```

---

### 🪨 Step 3: Search & Use Exploits
1. Search for available exploits:
```bash
search type:exploit name:Microsoft IIS
```
2. Use desired module:
```bash
use <module_path>
```
3. Set payload (if needed):
```bash
set payload windows/meterpreter/reverse_tcp
```
4. View module info:
```bash
info
```
5. Check required options:
```bash
show options
```
6. Analyze vulnerabilities:
```bash
analyze
```
7. Detect known vulnerable services:
```bash
vulns
```

---

## 🛠️ Using Built-in Exploit Search (SearchSploit)

### 🔎 Step 1: Search Exploits
```bash
searchsploit "Microsoft Windows SMB"
searchsploit "Microsoft Windows SMB" | grep -e "Metasploit"
```

### ⚠️ Before Running:
Check required settings:
```bash
show options
```

| Field     | Meaning                           |
|-----------|-----------------------------------|
| RHOSTS / RHOST | Target IP(s)                    |
| LHOST / LPORT | Local listener IP and port      |
| SESSION    | Which session to use              |

### ▶️ Step 2: Run Module
```bash
run
```
Sample output:
```bash
[+] 10.10.10.4:445 - Host is likely VULNERABLE to MS17-010!
```

---

## 🔌 Plugin: `db_autopwn`

### ⚙️ Step 1: Setup
1. Find **metasploit-autopwn** plugin on GitHub  
2. Download/clone it to local machine  
3. Move to MSF plugin directory:
```bash
cd Downloads/
sudo mv db_autopwn.rb <path_to_msf_plugins>
```

### 🥈 Step 2: Load Plugin in MSF
```bash
msf6> load db_autopwn
```

### 🔄 Commands:
```bash
db_autopwn
db_autopwn -p -t
db_autopwn -p -t -PI 445
```
> `-p`: match modules by open ports  
> `-t`: show all matching exploit modules  
> `-PI`: specify ports

> ⚠️ Errors may show — it's okay to ignore for now.

---

## 🧪 Test Your Knowledge

1. **Q**: Which command imports Nmap scan results into MSF?  
   **A**: `db_nmap`

2. **Q**: Primary purpose of vulnerability scanning in MSF?  
   **A**: To identify & exploit matching modules

3. **Q**: What does the SMB MS17-010 module check?  
   **A**: Common SMB misconfigurations

4. **Q**: Which plugin matches exploits to open ports?  
   **A**: `db_autopwn`

5. **Q**: What should you confirm before exploiting?  
   **A**: Target's service version info

---

## 📝 Memo / Notes

### 🔢 Info Needed for Exploitation:
- Service version
- OS type
- Open ports

### 📂 PostgreSQL (Used by MSF)
- Open-source RDBMS
- Used by Metasploit to store:
  - Scan results
  - Loot (e.g., credentials)
  - Exploit/session history

### 🔍 Identifying Real MSF Modules
Valid if listed with:
- `exploit/...`
- `auxiliary/...`
- `post/...`
- `scanner/...`
- `payload/...`

### 🔹 `db_autopwn` Flags:
- `-p` : Select modules based on open ports  
- `-t` : Show all matching modules  
- `-PI`: Specify ports
