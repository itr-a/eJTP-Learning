# Vulnerability Analysis: EthernalBlue
### Exploiting Windows MS17-010 SMB Vulnerability (EthernalBlue)
- Vulnerability in Windows SMB v1 (pre-2017)
- Can be detected using:
    nmap -p 445 --script smb-vuln-ms17-010 target
- Can exploit via Metasploit: exploit/windows/smb/ms17_010_eternalblue
- Can also use AutoBlue from GitHub (advanced/custom control)

---
- Environment and Tools  
AutoBlue-MS17-010  
Target system : Windows Server 2008 R2

```bash
sudo nmap -p 444 -O <target IP>
```
```bash
sudo nmap -sv -p --script=smb-vuln-ms17-010 <target IP>
```
**Perform exploit without Metasploit**  
Follow the instruction on Github


