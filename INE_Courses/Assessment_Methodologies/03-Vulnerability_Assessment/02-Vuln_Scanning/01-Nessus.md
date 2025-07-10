# Vulnerability Scanning WIth Nessus
#### Object
- Configure Nessus on Kali Linux
- Perform a vulnerabirity scan
- Export/Inport scan result into the Metasploit


#### What is Nessus?
Is a vulnerability scanner\
It helps you **find weaknesses** in systems, like:
- Open ports
- Weak passwords
- Outdated software
- Known exploits

**Step 1**: Preparation\
Download Nessus from\
Install
```bash
~/Downloads$ chmod +x Nessus-<package name>
~/Downloads$ sudo dpkg -i Nessus-<package name>
```
Start Nessus
```bash
sudo sustemctl start nessusd.service
```
Search IP and port appeared after installing\
From advanced setting, access Nessus screen\

**Step 2**: Export results
Choose Basic Network Scan -> make a new scan -> **Save & Launch**\
**Export**
> Advanced!
> 1. On **vulnerability** tab, try filtering result by **Metasploit Framework**\
> Click one of them!\
> CVSS (Common Vulnerability Scoring System : 10 is the highest)
> 2. Filter result by **Severity** equal to **High**


**Step 3**: Use the results!
Sart Metasploit from Terminal
```bash
service postgresql start && msfconsole
```
Create new workspace
```bash
workspace -a <workspace name>
```
Import the data downloaded
```bash
db_import <directory example: /home/..>
```
Search module
> CVE (Common Vulnerabilities and Exposures)\
> A catalog number for a known security vulnerabilities
```bash
msf6> search cve:2017 name:smb
```
Use exploit, run after checked options
