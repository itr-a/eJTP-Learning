# Frequently Exploited Windows Services
#### Objective
Understanding of :
- Windows servises
- How they work
- Potential vulnerabilities

|Protocol/Service|Ports|Purpose|
|---|---|---|
|Microsoft IIS (Internet Information Services)|TCP ports 80/443|Web server software developed by Microsoft that runs on Windows|
|WebDAV (Web Distributed Authoring & Versioning)|TCP ports 80/443|HTTP extension that allows clients to update, delete and copy files on a web server. Typically see it enabled on a Microsoft IIS|
|SMB/CIFS (Server Message Block Protocol)|TCP port 445|SMB: network file sharing protocol (on a LAN)|
|RDP (Remote Desktop Protocol)|TCP port 3389|Graphical user interface remote access protocol. (Disabled by default)
|WinRM (Windows Remote Management Protocol)|TCP port 5986|Can be used to facilitate remote access with Windows systems

#### Test your knowledge
Q:1  WecDAV is mainly used to   
A: Update, delete, move and copy files on a web server

Q:2  Which protocol allows for graphical user interface remote access on Windows systems  
A: RDP

Q3:  Which natice Windows sevice runs on TCP port 80 or port 443 if SSL is configured  
A:  IIS

Q4:  What is the primary fnction of the SMB protocol in Windows  
A:  To share files and peripherals over a network

Q5:  What does WinRM stand for and what port does it typically use with SSL?  
A:  Windows Remote Management, port 5986

Q6:  When would the RDP service be enabled by default on a Windows system  
A:  It is not enabled by default
