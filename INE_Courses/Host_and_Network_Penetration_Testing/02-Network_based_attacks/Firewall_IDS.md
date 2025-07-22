# Firewall Detection & IDS Evasion

Firewall Detection
```bash
# Host discovery and port scan
nmap -sn <target IP>
nmap -Pn -sS -F <target IP>

# If it says
Not shown: 92 closed ports -> ports not open
Not shown: 92 filtered ports -> firewall

# Confirming
nmap -Pn -sA -p 445,3389 <target IP>
# This will output
PORT, STSTE and SERVICE of the port
STATE: unfiltered -> no firewall
```


`-Pn` Host discovery\
`-sS` Syn scan\
`-sV` Service version detection\
`-F` Fast port scan (100 most commonly used ports)\
`-f` Fragment the packets
```bash
nmap -Pn -sS -sV -F -f
```

Decoy
`-D`
```bash
nmap -Pn -sS -sV -F -f -D <decoy IP(s) separate with ,>
```