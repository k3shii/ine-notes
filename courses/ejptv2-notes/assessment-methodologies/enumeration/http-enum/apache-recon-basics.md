# Apache Recon: Basics

### Overview

> In this challenge we will look at the basics of Apache server reconnaissance. Please start the lab and answer the following questions:
>
> **Questions**
>
> 1. Which web server software is running on the target server and also find out the version using nmap.
> 2. Which web server software is running on the target server and also find out the version using suitable metasploit module.
> 3. Check what web app is hosted on the web server using curl command.
> 4. Check what web app is hosted on the web server using wget command.
> 5. Check what web app is hosted on the web server using browsh CLI based browser.
> 6. Check what web app is hosted on the web server using lynx CLI based browser.
> 7. Perform bruteforce on webserver directories and list the names of directories found. Use brute\_dirs metasploit module.
> 8. Use the directory buster (dirb) with tool/usr/share/metasploit-framework/data/wordlists/directory.txt dictionary to check if any directory is present in the root folder of the web server. List the names of found directories.
> 9. Which bot is specifically banned from accessing a specific directory?
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



> 🔬 [Apache Recon: Basics](https://www.attackdefense.com/challengedetails?cid=538)
>
> * Target IP: `192.49.123.3`
> * Enumeration of an `Apache` HTTP server

```bash
root@INE:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
207487: eth0@if207488: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:12 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.18/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
207490: eth1@if207491: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:31:7b:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.49.123.2/24 brd 192.49.123.255 scope global eth1
       valid_lft forever preferred_lft forever
       
root@INE:~# nmap -sn 192.49.123.0/24
Starting Nmap 7.92 ( https://nmap.org ) at 2024-06-27 15:56 IST
Nmap scan report for linux (192.49.123.1)
Host is up (0.000055s latency).
MAC Address: 02:42:4C:DA:06:15 (Unknown)
Nmap scan report for 7vg4yznoiozme6agvob0k.temp-network_a-49-123 (192.49.123.3)
Host is up (0.000018s latency).
MAC Address: 02:42:C0:31:7B:03 (Unknown)
Nmap scan report for INE (192.49.123.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 3.30 seconds
```

> 1. Which web server software is running on the target server and also find out the version using nmap.

Answer: `Apache httpd 2.4.18`

```bash
root@INE:~# nmap -sV -script banner 192.49.123.3
Starting Nmap 7.92 ( https://nmap.org ) at 2024-06-27 16:01 IST
Nmap scan report for 7vg4yznoiozme6agvob0k.temp-network_a-49-123 (192.49.123.3)
Host is up (0.0000090s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
MAC Address: 02:42:C0:31:7B:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.56 seconds

```

> 2. Which web server software is running on the target server and also find out the version using suitable metasploit module.

Answer: `Apache/2.4.18 (Ubuntu)`

```bash
msf6 > use auxiliary/scanner/http/http_version 
msf6 auxiliary(scanner/http/http_version) > options

Module options (auxiliary/scanner/http/http_version):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   THREADS  1                yes       The number of concurrent threads (max one per host)
   VHOST                     no        HTTP server virtual host


msf6 auxiliary(scanner/http/http_version) > set RHOSTS 192.49.123.3
RHOSTS => 192.49.123.3
msf6 auxiliary(scanner/http/http_version) > options

Module options (auxiliary/scanner/http/http_version):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS   192.49.123.3     yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   THREADS  1                yes       The number of concurrent threads (max one per host)
   VHOST                     no        HTTP server virtual host

msf6 auxiliary(scanner/http/http_version) > exploit

[+] 192.49.123.3:80 Apache/2.4.18 (Ubuntu)
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```



### curl

> 3. Check what web app is hosted on the web server using curl command.

Answer: `Apache Ubuntu Default Page`

```bash
root@INE:~# curl http://192.49.123.3
```

<figure><img src="../../../../../.gitbook/assets/image (30) (1) (1).png" alt=""><figcaption></figcaption></figure>



### wget

> 4. Check what web app is hosted on the web server using wget command.

Answer: `Apache Ubuntu Default Page`

```bash
root@INE:~# wget "http://192.49.123.3/index"
```

<figure><img src="../../../../../.gitbook/assets/image (31) (1) (1).png" alt=""><figcaption></figcaption></figure>

> 5. Check what web app is hosted on the web server using browsh CLI based browser.

Answer: `Apache Ubuntu Default Page`

```bash
root@INE:~# browsh --startup-url 192.49.123.3
```

<figure><img src="../../../../../.gitbook/assets/image (32) (1) (1).png" alt=""><figcaption></figcaption></figure>



### lynx

> 6. Check what web app is hosted on the web server using lynx CLI based browser.

Answer: `Apache Ubuntu Default Page`

```bash
root@INE:~# lynx http://192.49.123.3
```

<figure><img src="../../../../../.gitbook/assets/image (34) (1) (1).png" alt=""><figcaption></figcaption></figure>

> 7. Perform bruteforce on webserver directories and list the names of directories found. Use brute\_dirs metasploit module.

Answer: `dir`, `poc` directories found.

```bash
msf6 > use auxiliary/scanner/http/brute_dirs 
msf6 auxiliary(scanner/http/brute_dirs) > options

Module options (auxiliary/scanner/http/brute_dirs):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   DELAY    0                yes       The delay between connections, per thread, in milliseconds
   FORMAT   a,aa,aaa         yes       The expected directory format (a alpha, d digit, A upperalpha)
   JITTER   0                yes       The delay jitter factor (maximum value by which to +/- DELAY) in milliseconds.
   PATH     /                yes       The path to identify directories
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   THREADS  1                yes       The number of concurrent threads (max one per host)
   TIMEOUT  20               yes       The socket connect/read timeout in seconds
   VHOST                     no        HTTP server virtual host

msf6 auxiliary(scanner/http/brute_dirs) > set RHOSTS 192.49.123.3
RHOSTS => 192.49.123.3
msf6 auxiliary(scanner/http/brute_dirs) > exploit

[*] Using code '404' as not found.
[+] Found http://192.49.123.3:80/dir/ 200
[+] Found http://192.49.123.3:80/src/ 200
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

<figure><img src="../../../../../.gitbook/assets/image (35) (1) (1).png" alt=""><figcaption></figcaption></figure>

> 8. Use the directory buster (dirb) with tool/usr/share/metasploit-framework/data/wordlists/directory.txt dictionary to check if any directory is present in the root folder of the web server. List the names of found directories.

Answer: `dir`, `poc` directories found.

```bash
root@INE:~# dirb http://192.49.123.3 /usr/share/metasploit-framework/data/wordlists/directory.txt 

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Thu Jun 27 16:26:15 2024
URL_BASE: http://192.49.123.3/
WORDLIST_FILES: /usr/share/metasploit-framework/data/wordlists/directory.txt

-----------------

GENERATED WORDS: 24                                                            

---- Scanning URL: http://192.49.123.3/ ----
+ http://192.49.123.3//data (CODE:301|SIZE:311)                                                                                                                                            
+ http://192.49.123.3//dir (CODE:301|SIZE:310)                                                                                                                                             
                                                                                                                                                                                           
-----------------
END_TIME: Thu Jun 27 16:26:15 2024
DOWNLOADED: 24 - FOUND: 2

```

> 9. Which bot is specifically banned from accessing a specific directory?

Answer: `BadBot`

```bash
msf6 > use auxiliary/scanner/http/robots_txt 
msf6 auxiliary(scanner/http/robots_txt) > options

Module options (auxiliary/scanner/http/robots_txt):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   PATH     /                yes       The test path to find robots.txt file
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   THREADS  1                yes       The number of concurrent threads (max one per host)
   VHOST                     no        HTTP server virtual host

msf6 auxiliary(scanner/http/robots_txt) > set RHOSTS 192.49.123.3
RHOSTS => 192.49.123.3
msf6 auxiliary(scanner/http/robots_txt) > exploit

[*] [192.49.123.3] /robots.txt found
[+] Contents of Robots.txt:
User-agent: *
Disallow: /cgi-bin/
Disallow: Disallow: /junk/

User-agent: BadBot
Disallow: /no-badbot-dir/

[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```





