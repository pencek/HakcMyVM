# 信息搜集
主机发现

```
┌──(kali㉿kali)-[~]
└─$ sudo arp-scan -l 
192.168.21.3    08:00:27:b1:a0:3f       (Unknown)

```
端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-08 23:10 EST
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.00047s latency).
Not shown: 65532 closed tcp ports (reset)
PORT    STATE SERVICE
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 08:00:27:B1:A0:3F (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.98 seconds
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-08 23:12 EST
Warning: 192.168.21.3 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.0012s latency).
Not shown: 65456 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
PORT    STATE SERVICE
137/udp open  netbios-ns
MAC Address: 08:00:27:B1:A0:3F (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 73.05 seconds
┌──(kali㉿kali)-[~]
└─$ nmap -sV -O -p80,139,445 192.168.21.3 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-08 23:11 EST
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.00023s latency).

PORT    STATE SERVICE     VERSION
80/tcp  open  http        Apache httpd 2.4.38 ((Debian))
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
MAC Address: 08:00:27:B1:A0:3F (Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: Host: CROSSROADS

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.71 seconds
┌──(kali㉿kali)-[~]
└─$ nmap -sU -sV -O -p137 192.168.21.3        
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-08 23:14 EST
Nmap scan report for 192.168.21.3 (192.168.21.3)
Host is up (0.00033s latency).

PORT    STATE SERVICE    VERSION
137/udp open  netbios-ns Samba nmbd netbios-ns (workgroup: WORKGROUP)
MAC Address: 08:00:27:B1:A0:3F (Oracle VirtualBox virtual NIC)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop
Service Info: Host: CROSSROADS

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.80 seconds

```
# 漏洞利用
先看一下80端口

![输入图片说明](image/Crossroads/1.png)

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.3 -w /usr/share/wordlists/dirb/big.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.3
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htpasswd            (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/robots.txt           (Status: 200) [Size: 42]
/server-status        (Status: 403) [Size: 277]
Progress: 20469 / 20470 (100.00%)
===============================================================
Finished
===============================================================

```
/robots.txt

