# Windows Recon: IIS

### Overview

> A Kali GUI machine and a target machine running an IIS service are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target).&#x20;
>
> Your task and objective are to fingerprint the service using the tools available on the Kali machine (WhatWeb, Dirb, Browsh) and extract target server information.
>
> Instructions:
>
> * Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.
> * The IP address of the target machine is mentioned in the file “/root/Desktop/target”
> * Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1



> 🔬 [Windows Recon: IIS](https://attackdefense.com/challengedetails?cid=2311)
>
> * Target IP: `10.0.22.67`
> * Enumeration of an `IIS` HTTP server, without the usage of a browser

```bash
root@attackdefense:~# nmap 10.0.22.67
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-27 14:06 IST
Nmap scan report for 10.0.22.67
Host is up (0.0023s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3306/tcp open  mysql
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 5.62 seconds
root@attackdefense:~# nmap -sV -O 10.0.22.67
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-27 14:07 IST
Nmap scan report for 10.0.22.67
Host is up (0.0023s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3306/tcp open  mysql         MySQL (unauthorized)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.91%E=4%D=6/27%OT=80%CT=1%CU=39805%PV=Y%DS=3%DC=I%G=Y%TM=667D24D
OS:5%P=x86_64-pc-linux-gnu)SEQ(SP=106%GCD=1%ISR=10B%TI=I%CI=I%II=I%SS=S%TS=
OS:U)OPS(O1=M546NW8NNS%O2=M546NW8NNS%O3=M546NW8%O4=M546NW8NNS%O5=M546NW8NNS
OS:%O6=M546NNS)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)ECN(R=Y%
OS:DF=Y%T=7F%W=FFFF%O=M546NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=7F%S=O%A=S+%F=AS%RD=
OS:0%Q=)T2(R=Y%DF=Y%T=7F%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%DF=Y%T=7F%W=0%S
OS:=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=7F%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=
OS:Y%DF=Y%T=7F%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=7F%W=0%S=A%A=O%F=
OS:R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=7F%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T
OS:=7F%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=7F%CD=
OS:Z)

Network Distance: 3 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.57 seconds

```



### whatweb

```bash
root@attackdefense:~# whatweb 10.0.22.67
Ignoring eventmachine-1.3.0.dev.1 because its extensions are not built. Try: gem pristine eventmachine --version 1.3.0.dev.1
Ignoring fxruby-1.6.29 because its extensions are not built. Try: gem pristine fxruby --version 1.6.29
http://10.0.22.67 [302 Found] ASP_NET[4.0.30319], Cookies[ASP.NET_SessionId,Server], Country[RESERVED][ZZ], HTTPServer[Microsoft-IIS/10.0], HttpOnly[ASP.NET_SessionId], IP[10.0.22.67], Microsoft-IIS[10.0], RedirectLocation[/Default.aspx], Title[Object moved], X-Powered-By[ASP.NET], X-XSS-Protection[0]
http://10.0.22.67/Default.aspx [302 Found] ASP_NET[4.0.30319], Cookies[ASP.NET_SessionId,Server], Country[RESERVED][ZZ], HTTPServer[Microsoft-IIS/10.0], HttpOnly[ASP.NET_SessionId], IP[10.0.22.67], Microsoft-IIS[10.0], RedirectLocation[/Default.aspx], Title[Object moved], X-Powered-By[ASP.NET], X-XSS-Protection[0]

```

> 📌
>
> * IIS Server version is `10.0`
> * ASP.NET version is `4.0.30319`
> * XSS Protection is `off`(`0`)
> * Default page of the target web app is `/Default.aspx`



### httpie

> **`httpie`** - CLI, cURL-like tool for humans. Run with **`http`**

```bash
root@attackdefense:~# http 10.0.22.67
HTTP/1.1 302 Found
Cache-Control: private
Content-Length: 130
Content-Type: text/html; charset=utf-8
Date: Thu, 27 Jun 2024 08:47:35 GMT
Location: /Default.aspx
Server: Microsoft-IIS/10.0
Set-Cookie: ASP.NET_SessionId=3tmin25bbk1xpdmam3kdmjp4; path=/; HttpOnly; SameSite=Lax
Set-Cookie: Server=RE9UTkVUR09BVA==; path=/
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
X-XSS-Protection: 0

<html><head><title>Object moved</title></head><body>
<h2>Object moved to <a href="/Default.aspx">here</a>.</h2>
</body></html>

```



### dirb

> [**`dirb`**](https://dirb.sourceforge.net/about.html) - a Web content scanner. It _launches a `dictionary based attack` against a web server, looking for existing Web Objects and analyzing the response_. It comes with a set of preconfigured _attack wordlists_.

* This utility is useful when we don’t have a browser i.e Firefox, Chrome, etc. to access the target application and we have to use the terminal to access the web application.
* Try to browse to the found directories to find out if access is granted

```bash
root@attackdefense:~# dirb http://10.0.22.67

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Thu Jun 27 14:10:12 2024
URL_BASE: http://10.0.22.67/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.0.22.67/ ----
==> DIRECTORY: http://10.0.22.67/app_themes/                                                                          
==> DIRECTORY: http://10.0.22.67/aspnet_client/                                                                       
==> DIRECTORY: http://10.0.22.67/configuration/                                                                       
==> DIRECTORY: http://10.0.22.67/content/                                                                             
==> DIRECTORY: http://10.0.22.67/Content/                                                                             
==> DIRECTORY: http://10.0.22.67/downloads/                                                                           
==> DIRECTORY: http://10.0.22.67/Downloads/                                                                           
==> DIRECTORY: http://10.0.22.67/resources/                                                                           
==> DIRECTORY: http://10.0.22.67/Resources/                                                                           
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/ ----
==> DIRECTORY: http://10.0.22.67/app_themes/default/                                                                  
==> DIRECTORY: http://10.0.22.67/app_themes/Default/                                                                  
                                                                                                                      
---- Entering directory: http://10.0.22.67/aspnet_client/ ----
==> DIRECTORY: http://10.0.22.67/aspnet_client/system_web/                                                            
                                                                                                                      
---- Entering directory: http://10.0.22.67/configuration/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/content/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/Content/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/downloads/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/Downloads/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/resources/ ----
==> DIRECTORY: http://10.0.22.67/resources/images/                                                                    
==> DIRECTORY: http://10.0.22.67/resources/Images/                                                                    
                                                                                                                      
---- Entering directory: http://10.0.22.67/Resources/ ----
==> DIRECTORY: http://10.0.22.67/Resources/images/                                                                    
==> DIRECTORY: http://10.0.22.67/Resources/Images/                                                                    
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/default/ ----
==> DIRECTORY: http://10.0.22.67/app_themes/default/images/                                                           
==> DIRECTORY: http://10.0.22.67/app_themes/default/Images/                                                           
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/Default/ ----
==> DIRECTORY: http://10.0.22.67/app_themes/Default/images/                                                           
==> DIRECTORY: http://10.0.22.67/app_themes/Default/Images/                                                           
                                                                                                                      
---- Entering directory: http://10.0.22.67/aspnet_client/system_web/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/resources/images/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/resources/Images/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/Resources/images/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/Resources/Images/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/default/images/ ----
==> DIRECTORY: http://10.0.22.67/app_themes/default/images/extras/                                                    
==> DIRECTORY: http://10.0.22.67/app_themes/default/images/listing/                                                   
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/default/Images/ ----
==> DIRECTORY: http://10.0.22.67/app_themes/default/Images/extras/                                                    
==> DIRECTORY: http://10.0.22.67/app_themes/default/Images/listing/                                                   
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/Default/images/ ----
==> DIRECTORY: http://10.0.22.67/app_themes/Default/images/extras/                                                    
==> DIRECTORY: http://10.0.22.67/app_themes/Default/images/listing/                                                   
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/Default/Images/ ----
==> DIRECTORY: http://10.0.22.67/app_themes/Default/Images/extras/                                                    
==> DIRECTORY: http://10.0.22.67/app_themes/Default/Images/listing/                                                   
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/default/images/extras/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/default/images/listing/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/default/Images/extras/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/default/Images/listing/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/Default/images/extras/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/Default/images/listing/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/Default/Images/extras/ ----
                                                                                                                      
---- Entering directory: http://10.0.22.67/app_themes/Default/Images/listing/ ----
                                                                                                                      
-----------------
END_TIME: Thu Jun 27 14:16:22 2024
DOWNLOADED: 133748 - FOUND: 0

```



### browsh

**`browsh`** - _A fully interactive, real-time, and modern text-based browser rendered to TTYs and browsers_. It's used when only command line is available or now browser is installed.

```bash
root@attackdefense:~# browsh -h
Usage of browsh:
      --debug                  Log to ./debug.log
      --firefox.path string    Path to Firefox executable (default "firefox")
      --firefox.use-existing   Whether Browsh should launch Firefox or not
      --firefox.with-gui       Don't use headless Firefox
      --http-server-mode       Run as an HTTP service
      --monochrome             Start browsh in monochrome mode
      --startup-url string     URL to launch at startup (default "https://www.brow.sh")
      --time-limit int         Kill Browsh after the specified number of seconds
      --version                Output current Browsh version
pflag: help requested

root@attackdefense:~# browsh --startup-url http://10.0.22.67/Default.aspx
```

<figure><img src="../../../../../.gitbook/assets/image (27) (1) (1).png" alt=""><figcaption><p>Browsh</p></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (28) (1) (1).png" alt=""><figcaption><p>Normal Broswer</p></figcaption></figure>

📌 The target application is `WebGoat.net`







