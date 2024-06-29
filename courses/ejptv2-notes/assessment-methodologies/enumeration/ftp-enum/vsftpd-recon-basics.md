# VSFTPD Recon: Basics

### Overview

> In this challenge we will look at the basics of VSFTPD server reconnaissance.  Please start the lab and answer the following questions:
>
> Questions
>
> 1. Find the version of vsftpd server.
> 2. Check whether anonymous login is allowed on the ftp server using nmap script.
> 3. Fetch the flag from FTP server.
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



> 🔬 [VSFTPD Recon: Basics](https://attackdefense.com/challengedetails?cid=519)
>
> * Target IP: `192.217.80.3`
> * Enumeration of [vsftpd](https://security.appspot.com/vsftpd.html) server

```bash
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
232532: eth0@if232533: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:10 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.16/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
232535: eth1@if232536: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:d9:50:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.217.80.2/24 brd 192.217.80.255 scope global eth1
       valid_lft forever preferred_lft forever
       
root@attackdefense:~# nmap -sn 192.217.80.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 04:38 UTC
Nmap scan report for linux (192.217.80.1)
Host is up (0.000063s latency).
MAC Address: 02:42:00:44:0E:A4 (Unknown)
Nmap scan report for target-1 (192.217.80.3)
Host is up (0.000013s latency).
MAC Address: 02:42:C0:D9:50:03 (Unknown)
Nmap scan report for attackdefense.com (192.217.80.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.00 seconds
```

> 1. Find the version of vsftpd server.

Answer: `vsftpd 3.0.3`

```
root@attackdefense:~# nmap 192.217.80.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 04:38 UTC
Nmap scan report for target-1 (192.217.80.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:C0:D9:50:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.20 seconds
root@attackdefense:~# nmap -sV 192.217.80.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 04:39 UTC
Nmap scan report for target-1 (192.217.80.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
MAC Address: 02:42:C0:D9:50:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.51 seconds
```

> 2. Check whether anonymous login is allowed on the ftp server using nmap script.

Answer: `Allowed, | ftp-anon: Anonymous FTP login allowed (FTP code 230)`

```bash
root@attackdefense:~# nmap --script ftp-anon -p21 192.217.80.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 04:40 UTC
Nmap scan report for target-1 (192.217.80.3)
Host is up (0.000051s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Dec 18  2018 flag
|_drwxr-xr-x    2 ftp      ftp          4096 Dec 18  2018 pub
MAC Address: 02:42:C0:D9:50:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.37 seconds
```

> 3. Fetch the flag from FTP server.

```bash
root@attackdefense:~# ftp 192.217.80.3
Connected to 192.217.80.3.
220 (vsFTPd 3.0.3)
Name (192.217.80.3:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.

ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Dec 18  2018 flag
drwxr-xr-x    2 ftp      ftp          4096 Dec 18  2018 pub
226 Directory send OK.

ftp> get flag
local: flag remote: flag
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for flag (33 bytes).
226 Transfer complete.
33 bytes received in 0.00 secs (309.8708 kB/s)

ftp> bye
221 Goodbye.

root@attackdefense:~# ls
README  flag  tools  wordlists

root@attackdefense:~# cat flag 
4267bdfbff77d7c2635e4572519a8b9c
```

<details>

<summary>Flag</summary>

`4267bdfbff77d7c2635e4572519a8b9c`

</details>









