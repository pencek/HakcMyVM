# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-16 02:50 EST
Nmap scan report for 192.168.21.5
Host is up (0.00050s latency).
MAC Address: 08:00:27:61:49:1F (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.7
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.70 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.5
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-16 02:51 EST
Nmap scan report for 192.168.21.5
Host is up (0.00024s latency).
Not shown: 65521 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
21/tcp    open  ftp           vsftpd 3.0.5
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.21.7
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-rw-r--    1 1002     1002      2349914 Aug 30  2023 CodeShield_pitch_deck.pdf
| -rw-rw-r--    1 1003     1003        67520 Aug 28  2023 Information_Security_Policy.pdf
|_-rw-rw-r--    1 1004     1004       226435 Aug 28  2023 The_2023_weak_password_report.pdf
22/tcp    open  ssh           OpenSSH 6.0p1 Debian 4+deb7u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 32:14:67:32:02:7a:b6:e4:7f:a7:22:0b:02:fd:ee:07 (RSA)
|   256 34:e4:d0:5d:bd:bc:9e:3f:4c:f9:1e:7d:3c:60:ce:6e (ECDSA)
|_  256 ef:3c:ff:f9:9a:a3:aa:7d:5a:82:73:b9:8c:b8:97:04 (ED25519)
25/tcp    open  smtp          Postfix smtpd
|_smtp-commands: SMTP: EHLO 521 5.5.1 Protocol error\x0D
80/tcp    open  http          nginx
|_http-title: Did not follow redirect to https://192.168.21.5/
110/tcp   open  pop3          Dovecot pop3d
|_pop3-capabilities: CAPA SASL TOP PIPELINING STLS RESP-CODES AUTH-RESP-CODE UIDL
| ssl-cert: Subject: commonName=mail.codeshield.hmv/organizationName=mail.codeshield.hmv/stateOrProvinceName=GuangDong/countryName=CN
| Not valid before: 2023-08-26T09:34:43
|_Not valid after:  2033-08-23T09:34:43
|_ssl-date: TLS randomness does not represent time
143/tcp   open  imap          Dovecot imapd (Ubuntu)
|_imap-capabilities: listed post-login ENABLE capabilities have more SASL-IR Pre-login LOGIN-REFERRALS ID IMAP4rev1 OK LOGINDISABLEDA0001 IDLE LITERAL+ STARTTLS
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mail.codeshield.hmv/organizationName=mail.codeshield.hmv/stateOrProvinceName=GuangDong/countryName=CN
| Not valid before: 2023-08-26T09:34:43
|_Not valid after:  2033-08-23T09:34:43
443/tcp   open  ssl/http      nginx
| ssl-cert: Subject: commonName=mail.codeshield.hmv/organizationName=mail.codeshield.hmv/stateOrProvinceName=GuangDong/countryName=CN
| Not valid before: 2023-08-26T09:34:43
|_Not valid after:  2033-08-23T09:34:43
| http-robots.txt: 1 disallowed entry 
|_/
|_ssl-date: TLS randomness does not represent time
|_http-title: CodeShield - Home
465/tcp   open  ssl/smtp      Postfix smtpd
| ssl-cert: Subject: commonName=mail.codeshield.hmv/organizationName=mail.codeshield.hmv/stateOrProvinceName=GuangDong/countryName=CN
| Not valid before: 2023-08-26T09:34:43
|_Not valid after:  2033-08-23T09:34:43
|_smtp-commands: mail.codeshield.hmv, PIPELINING, SIZE 15728640, ETRN, AUTH PLAIN LOGIN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, CHUNKING
|_ssl-date: TLS randomness does not represent time
587/tcp   open  smtp          Postfix smtpd
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mail.codeshield.hmv/organizationName=mail.codeshield.hmv/stateOrProvinceName=GuangDong/countryName=CN
| Not valid before: 2023-08-26T09:34:43
|_Not valid after:  2033-08-23T09:34:43
|_smtp-commands: mail.codeshield.hmv, PIPELINING, SIZE 15728640, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, CHUNKING
993/tcp   open  imaps?
| ssl-cert: Subject: commonName=mail.codeshield.hmv/organizationName=mail.codeshield.hmv/stateOrProvinceName=GuangDong/countryName=CN
| Not valid before: 2023-08-26T09:34:43
|_Not valid after:  2033-08-23T09:34:43
|_ssl-date: TLS randomness does not represent time
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3s?
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mail.codeshield.hmv/organizationName=mail.codeshield.hmv/stateOrProvinceName=GuangDong/countryName=CN
| Not valid before: 2023-08-26T09:34:43
|_Not valid after:  2033-08-23T09:34:43
2222/tcp  open  ssh           OpenSSH 6.0p1 Debian 4+deb7u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 32:14:67:32:02:7a:b6:e4:7f:a7:22:0b:02:fd:ee:07 (RSA)
|   256 34:e4:d0:5d:bd:bc:9e:3f:4c:f9:1e:7d:3c:60:ce:6e (ECDSA)
|_  256 ef:3c:ff:f9:9a:a3:aa:7d:5a:82:73:b9:8c:b8:97:04 (ED25519)
3389/tcp  open  ms-wbt-server Microsoft Terminal Service
22222/tcp open  ssh           OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 2a:49:28:84:25:99:62:e8:29:68:88:d6:36:be:8e:d6 (ECDSA)
|_  256 20:9f:5b:3f:52:eb:a9:60:27:39:3b:e7:d8:17:8d:70 (ED25519)
MAC Address: 08:00:27:61:49:1F (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=2/16%OT=21%CT=1%CU=39767%PV=Y%DS=1%DC=D%G=Y%M=080027%T
OS:M=6992CC9B%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=10A%TI=Z%CI=Z%II=I
OS:%TS=A)SEQ(SP=101%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=104%GCD=1%ISR=
OS:10E%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=FB%GCD=1%ISR=106%TI=Z%CI=Z%II=I%TS=A)SEQ(
OS:SP=FD%GCD=1%ISR=104%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M5B4ST11NW7%O2=M5B4ST11NW
OS:7%O3=M5B4NNT11NW7%O4=M5B4ST11NW7%O5=M5B4ST11NW7%O6=M5B4ST11)WIN(W1=FE88%
OS:W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=81%W=FAF0%O=M5B4N
OS:NSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=81%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=
OS:Y%DF=Y%T=81%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=81%W=0%S=Z%A=S+%F=A
OS:R%O=%RD=0%Q=)T6(R=Y%DF=Y%T=81%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=8
OS:1%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=81%IPL=164%UN=0%RIPL=G%RID=
OS:G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=81%CD=S)

Network Distance: 1 hop
Service Info: Hosts: -mail.codeshield.hmv,  mail.codeshield.hmv; OSs: Unix, Linux, Windows; CPE: cpe:/o:linux:linux_kernel, cpe:/o:microsoft:windows

TRACEROUTE
HOP RTT     ADDRESS
1   0.24 ms 192.168.21.5

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.16 seconds
```

# 漏洞利用

21端口，可以匿名登录，吧文件都下载下来

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.21.5 
Connected to 192.168.21.5.
220 (vsFTPd 3.0.5)
Name (192.168.21.5:kali): Anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||25459|)
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      2349914 Aug 30  2023 CodeShield_pitch_deck.pdf
-rw-rw-r--    1 1003     1003        67520 Aug 28  2023 Information_Security_Policy.pdf
-rw-rw-r--    1 1004     1004       226435 Aug 28  2023 The_2023_weak_password_report.pdf
226 Directory send OK.
```

其中发现了，一个域名：j.carlson@codeshield.hmv，添加一下

```
