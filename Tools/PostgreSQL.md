# PostgreSQL
> A open-source database system with *advanced features*

#### Why is PostgreSQL showing up in your hacking video?
Because the **Metasploit Framework** uses PostgreSQL as its **default database backend**.  
That means:  
- All my scanned hosts
- Open ports
- Vulnerabilities I found
- Sessions
- Credentials I captured

```bash
service postgresql start && msfconsole
```
`servic postgresql start`\
 Start the **PostgreSQL detabase service**\
 `&&`\
  This means "only do the next command **if** the first one works successfully"\
 `msfconsole`\
  This opens the **Metasploit Framework console**\
   Use it to load exploits, run scans, and test vulnerabilities\

   
