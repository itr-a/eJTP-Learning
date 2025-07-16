# What is EthernalBlue

**EternalBlue** is a super-powerful Windows exploit that takes advantage of a vulnerability in SMBv1 (Server Message Block version 1).

- Discovered by the NSA (U.S. spy agency)
- Leaked by a hacker group called Shadow Brokers in 2017
- Used in real-world attacks like WannaCry ransomware and NotPetya

### How it works (simply)
1. A Windows machine has SMBv1 enabled (usually on port 445).
2. That version has a bug in how it handles certain network packets.
3. EternalBlue sends a special packet that confuses SMB and causes a crash.
4. During the crash, the attacker injects code into the memory.
5. The attacker now controls the machine remotely.

### Vulnerable systems
Windows XP\
Windows 7\
Windows Server 2003 / 2008\
Unpatched Windows 10 (early versions)