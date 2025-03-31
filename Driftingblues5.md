# 信息搜集

主机发现

```
# sudo arp-scan -l
192.168.21.9 08:00:27:fd:23:2d PCS Systemtechnik GmbH
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.9
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-08 03:42 EST
Nmap scan report for 192.168.21.9 (192.168.21.9)
Host is up (0.00036s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:FD:23:2D (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.73 seconds

┌──(kali㉿kali)-[~]
└─$ nmap -sT -sV -O -p22,80 192.168.21.9  
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-08 03:43 EST
Nmap scan report for 192.168.21.9 (192.168.21.9)
Host is up (0.00029s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
MAC Address: 08:00:27:FD:23:2D (Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.08 seconds
```
# 漏洞利用

先看一下80端口