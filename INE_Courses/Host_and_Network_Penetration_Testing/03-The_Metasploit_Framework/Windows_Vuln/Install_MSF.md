# MSF Instllation


### 1. Update repository and install latest MSF
```bash
sudo apt-get && sudo apt-get nstall metasploit-framework -y
```

### 2. Enable postgresql
```bash
# Enable
sudo systemctl enable postgresql
# Start postgresql
sudo systemctl start postgresql
# Check if it's started
sudo systemctl status postgresql
```

### 3. Initialize database
```bash
# Initialize
sudo msfdb init
# Check status
sudo msfdb status
```

### 4. Start MSF console
```bash
msfconsole
```