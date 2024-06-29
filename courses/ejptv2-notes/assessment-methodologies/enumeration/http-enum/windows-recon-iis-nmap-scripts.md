# Windows Recon: IIS: Nmap Scripts

### Overview

> A Kali GUI machine and a target machine running an IIS service are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target).&#x20;
>
> Your task is to fingerprint the service using the tools available on the Kali machine and run Nmap scripts to enumerate the Windows target machine IIS service.
>
> Objective:&#x20;
>
> 1. Identify IIS Server
> 2. Get webserver header details
> 3. Enumerated HTTP methods
> 4. Detect WebDAV configuration - etc.
>
> Instructions:
>
> * Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.
> * The IP address of the target machine is mentioned in the file “/root/Desktop/target”
> * Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1



> 🔬 [Windows Recon: IIS: Nmap Scripts](https://attackdefense.com/challengedetails?cid=2312)
>
> * Target IP: `10.0.19.201`
> * Enumeration of an `IIS` HTTP server using `nmap` scripts

```bash
root@attackdefense:~# nmap 10.0.19.201
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-27 14:54 IST
Nmap scan report for 10.0.19.201
Host is up (0.0020s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3306/tcp open  mysql
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 1.80 seconds
```

> We have discovered that multiple ports are open. We will be focusing on port 80 where the IIS server is running.
>
> 📌 Potentially interesting folders are `content`, `downloads`, `webdav`.

```bash
root@attackdefense:~# nmap -sV --script http-enum -p80 10.0.19.201
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-27 14:57 IST
Nmap scan report for 10.0.19.201
Host is up (0.0029s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
| http-enum: 
|   /content/: Potentially interesting folder
|   /downloads/: Potentially interesting folder
|_  /webdav/: Potentially interesting folder
|_http-server-header: Microsoft-IIS/10.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.75 seconds

```

<figure><img src="../../../../../.gitbook/assets/image (29).png" alt=""><figcaption><p>nmap http-enum</p></figcaption></figure>

> Running Header script to get the IIS server header information.

> 📌
>
> * IIS Server version is `10.0`
> * ASP.NET version is `4.0.30319`
> * XSS Protection is `off`(`0`)
> * Default page of the target web app is `/Default.aspx`

```bash
root@attackdefense:~# nmap -sV --script http-headers -p80 10.0.19.201
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-27 14:59 IST
Nmap scan report for 10.0.19.201
Host is up (0.0022s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
| http-headers: 
|   Cache-Control: private
|   Content-Type: text/html; charset=utf-8
|   Location: /Default.aspx
|   Server: Microsoft-IIS/10.0
|   Set-Cookie: ASP.NET_SessionId=dwg0spd3px3kp00wxo0nsrx5; path=/; HttpOnly; SameSite=Lax
|   X-AspNet-Version: 4.0.30319
|   Set-Cookie: Server=RE9UTkVUR09BVA==; path=/
|   X-XSS-Protection: 0
|   X-Powered-By: ASP.NET
|   Date: Thu, 27 Jun 2024 09:29:38 GMT
|   Connection: close
|   Content-Length: 130
|   
|_  (Request type: GET)
|_http-server-header: Microsoft-IIS/10.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.85 seconds

```

> Running http-methods script on /webdav path to discover all allowed methods.

> 📌 Enumerated supported HTTP methods are `OPTIONS`, `TRACE`, `GET`, `HEAD`, `POST`, `COPY`, `PROPFIND`, `DELETE`, `MOVE`, `PROPPATCH`, `MKCOL`, `LOCK`, `UNLOCK`, `PUT`

```bash
root@attackdefense:~# nmap --script http-methods --script-args http-methods.url-path=/webdav 10.0.19.201
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-27 15:03 IST
Nmap scan report for 10.0.19.201
Host is up (0.0021s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE
80/tcp   open  http
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|   Potentially risky methods: TRACE COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|_  Path tested: /webdav
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3306/tcp open  mysql
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 5.81 seconds
```

> Running webdav scan Nmap script to identify WebDAV installations the script uses the OPTIONS and PROPFIND methods to detect it.

```bash
root@attackdefense:~# nmap --script http-webdav-scan --script-args http-methods.url-path=/webdav  10.0.19.201
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-27 15:05 IST
Nmap scan report for 10.0.19.201
Host is up (0.0021s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE
80/tcp   open  http
| http-webdav-scan: 
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, LOCK, UNLOCK
|   WebDAV type: Unknown
|   Server Type: Microsoft-IIS/10.0
|_  Server Date: Thu, 27 Jun 2024 09:35:28 GMT
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3306/tcp open  mysql
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 2.92 seconds

```

















