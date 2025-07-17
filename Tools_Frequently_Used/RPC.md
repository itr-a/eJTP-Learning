# RPC
smbclient \\\\target.ine.local\\IPC$ -U josh

smbclient -U josh \\\\target.ine.local\\josh
 cat flag2.txt                                                                                                                                          
FLAG2{ae657b0b74704803966effda77507457}

Psst! I heard there is an FTP service running. Find it and check the banner.
msf6 auxiliary(scanner/smb/smb_login) > smbclient -L //target.ine.local -U josh
[*] exec: smbclient -L //target.ine.local -U josh

Password for [WORKGROUP\josh]:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        IPC$            IPC       IPC Service (target server (Samba, Ubuntu))
