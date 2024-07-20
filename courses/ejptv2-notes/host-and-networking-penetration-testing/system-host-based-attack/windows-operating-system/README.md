---
cover: ../../../../../.gitbook/assets/8.png
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 💻 Windows Operating System

## Windows Vulnerabilities

[Windows O.S.](https://www.microsoft.com/en-us/windows) is a prime target for attackers given the threat surface and its popularity.

Most of the Windows vulnerabilities **exploits** are publicly available, making them simple to use.

* Threat surface is fragmented, depending on the Win O.S. version.
* The older the O.S. version, the more vulnerable to attacks.
* All of Windows operating systems share a similarity according to the development model.
  * `C` programming language - leads to buffer overflows, arbitrary code execution, etc
  * No default security practices applied - must be sistematically handled by the company
  * Patching by Microsoft is not immediate, or versions are out of support/patching
* To name a few, Windows `XP`, `7`, `Server 2008` and Server 2012, are still used by many companies and are _**largerly vulnerable**_, leaving the systems open to new attack vectors.
  * Cross platform vulnerabilities, `e.g.` SQL injections, cross-site scripting (on IIS web servers)
* Physical attacks, `e.g.` malicious USB drives, theft, etc

### Types of Windows Vulnerabilities <a href="#vulns-types" id="vulns-types"></a>

<table><thead><tr><th width="196" align="center">Vulnerability</th><th>Description</th></tr></thead><tbody><tr><td align="center"><strong><code>Information Disclosure</code></strong></td><td>Allows an attacker to access confidential data</td></tr><tr><td align="center"><strong><code>Buffer Overflows</code></strong></td><td>Programming error that allows an attacker to write data to a buffer and overrun the allocated buffer, therefore writing malicious data to allocated memory addresses</td></tr><tr><td align="center"><strong><code>Remote Code Execution (RCE)</code></strong></td><td>Allows an attacker to remotely execute code on the target</td></tr><tr><td align="center"><strong><code>Privilege Escalation</code></strong></td><td>Allows an attacker to elevate their privileges after initial compromise</td></tr><tr><td align="center"><strong><code>Denial of Service (DoS)</code></strong></td><td>Allows an attacker to flood a target consuming its resources (CPU, RAM, Network ...), interrupting the system's normal functioning, resulting in denial of service to other users</td></tr></tbody></table>

***

## Windows Exploitation

Windows has various standard native services and protocols configured or not on a host. When active, they provide an attacker with an **access vector**.

<table><thead><tr><th width="249">Protocol/Service</th><th width="183">Ports</th><th>Purpose</th></tr></thead><tbody><tr><td><a href="https://www.iis.net/">Microsoft <strong>IIS</strong></a> (<strong>I</strong>nternet <strong>I</strong>nformation <strong>S</strong>ervices)</td><td>TCP <code>80</code>/<code>443</code></td><td>Microsoft Web server for Windows, hosting web applications</td></tr><tr><td><a href="https://learn.microsoft.com/en-us/windows/win32/webdav/webdav-portal"><strong>WebDAV</strong></a> (<strong>W</strong>eb <strong>D</strong>istributed <strong>A</strong>uthoring &#x26; <strong>V</strong>ersioning)</td><td>TCP <code>80</code>/<code>443</code></td><td>HTTP extension that allows clients to copy, move, delete and update files on a web server. Used to enable a web server to act as a <em>file server</em></td></tr><tr><td><a href="https://learn.microsoft.com/en-us/windows-server/storage/file-server/file-server-smb-overview"><strong>SMB</strong></a>/CIFS (<strong>S</strong>erver <strong>M</strong>essage <strong>B</strong>lock)</td><td>TCP <code>445</code> / on top of NetBios <code>137-139</code></td><td>Network file and peripherals sharing  protocol, between computers on a local network (LAN)</td></tr><tr><td><a href="https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol"><strong>RDP</strong></a> (<strong>R</strong>emote <strong>D</strong>esktop <strong>P</strong>rotocol)</td><td>TCP <code>3389</code></td><td>GUI remote access protocol used to remotely authenticate and interact with Windows <em>(Disabled by default)</em></td></tr><tr><td><a href="https://learn.microsoft.com/en-us/windows/win32/winrm/portal"><strong>WinRM</strong></a> (<strong>W</strong>indows <strong>R</strong>emote <strong>M</strong>anagement <strong>P</strong>rotocol)</td><td>TCP <code>5986</code>/<code>443</code></td><td>Used to facilitate remote access with Windows systems, execute remote commands</td></tr></tbody></table>



### IIS WebDAV <a href="#iis-webdav" id="iis-webdav"></a>

🗒️ [Microsoft **IIS**](https://www.iis.net/) (**I**nternet **I**nformation **S**ervices) - a Microsoft proprietary extensible web server developed for use with Windows.

* Ports: **`80`** (no certificate), **`443`** (with SSL Certificate)
* Host websites and web applications
* Administrative GUI for IIS management
* Static and dynamic web pages, developed in `ASP.NET` and `PHP`
* Supported file extensions: `.asp`, `.aspx`, `.config`, `.php`

🗒️ [**WebDAV**](https://learn.microsoft.com/en-us/windows/win32/webdav/webdav-portal) (**W**eb **D**istributed **A**uthoring & **V**ersioning) - a set of HTTP protocol extentions used by users to manage file on remote web servers.

* Web server as `File server`
* Run on top of Apache or IIS - ports `80`/`443`
* Credentials `username` and `password` are necessary for connection the WebDAV Server

#### WebDAV Exploitation

1. Check _if WebDAV is configured_ to run on the IIS web server.
2. **Brute-force attack** on the WebDAV server - _identify legitimate credentials_.
3. Use the obtained credentials to _authenticate with the WebDAV_ and upload malicious code, like an `.asp` **payload**, used to execute arbitrary commands or obtain **reverse shell** on the target.

#### **Tools**

> [**`davtest`**](https://www.kali.org/tools/davtest) - scanner tool used to _scan, authenticate and exploit a WebDAV server, by uploading test executable files which allow for command execution on the target._ Pre-installed on Kali Linux and Parrot OS.

```bash
davtest -url <url> [options]
```

<figure><img src="../../../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>davtest</p></figcaption></figure>

> [**`cadaver`**](https://www.kali.org/tools/cadaver/) - supports file _upload, download, on-screen display, in-place editing, namespace operations (move/copy), collection creation and deletion, property manipulation, and resource locking_. Pre-installed on Kali Linux and Parrot OS.

```bash
cadaver [OPTIONS] http://hostname[:port]/path
```

<figure><img src="../../../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>cadaver</p></figcaption></figure>

### SMB <a href="#smb" id="smb"></a>

🗒️ [**SMB**](https://learn.microsoft.com/en-us/windows-server/storage/file-server/file-server-smb-overview) (**S**erver **M**essage **B**lock) - a network file sharing protocol, used for files and peripherals sharing, on Windows

* Ports: **`445`** (TCP), **`139`** (NetBIOS)
* Two levels of authentication to access a share:
  * _User Authentication_ - `username` & `password`
  * _Share Authentication_ - `password`
  * both utilize a challenge response authentication system

🗒️ **SAMBA** is the open source _Linux_ SMB

* it allows Windows systems to access Linux shares

#### **SMB Authentication**

<figure><img src="../../../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>SMB Authentication</p></figcaption></figure>

1. Auth request from the client to the server
2. The server request the client to encrypt string with user's hash
3. The client sends the encrypted string to the server
4. The server checks the actual string value of that users matches the client's one, and grant access. It doesn't match access is denied

#### **PsExec**

> [**`psexec`**](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec) - a light-weight telnet-replacement that lets you execute processes on remote systems, complete with full interactivity for console applications, using any user's credentials

* PsExec authentication is performed via SMB
* Run arbitrary commands or a remote command prompt
* Commands are sent via **`CMD`** (without a GUI like `RDP`)
* Legitimate user account and passwords/hashes are necessary to gain Windows target access

#### **PsExec Exploitation**

1. Leverage various techniques, `e.g.` **SMB login brute-force** attack.
2. Narrow down the attack to only common Win user accounts, `e.g.` **Administrator**.
3. Use the obtained credentials to authenticate via **`PsExec`** and execute system commands or get a reverse shell.

#### MS17-010 Eternal Blue

> [`EternalBlue (MS17-010/CVE-2017-0144)`](https://learn.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010) - A collection of Windows vulnerabilities and exploits that allow remote code execution and system access.

* Developed by the NSA and Leaked by the Shadow Brokers in 2017.
* Exploits a vulnerability in the Windows `SMBv1` protocol by sending specially crafted packets to execute arbitrary commands.
* Used in the `WannaCry` ransomware attack on June 27, 2017.
* Microsoft released a patch in March 2017, but many systems remain unpatched.
* Contains auxiliary and exploit modules to check for and exploit vulnerable systems, providing a privileged `meterpreter` session.
* Possible with publicly available exploit code.

This vulnerability affects multiple versions of Windows:

* Windows Vista
* Windows 7
* Windows Server 2008
* Windows 8.1
* Windows Server 2012
* Windows 10
* Windows Server 2016

#### Tools

> [`AutoBlue-MS17-010`](https://github.com/3ndG4me/AutoBlue-MS17-010) - exploit code or a collection of scripts that used to exploit the vulnerability manually

### RDP <a href="#rdp" id="rdp"></a>

🗒️ [**RDP**](https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol) (**R**emote **D**esktop **P**rotocol) - Microsoft proprietary GUI remote access protocol used to remotely connect with Windows.

* Ports: **`3389`** (TCP) or any other port
* _User Authentication_ - `username` & `password`
* An RDP Client is used to connect to the target

#### BlueKeep Exploitation

> [BlueKeep (CVE-2019-0708)](https://nvd.nist.gov/vuln/detail/cve-2019-0708) - RDP vulnerability in Windows that could potentially allow attackers to remotely execute arbitrary code and gain access to Windows system and consequently the network that the target system is a part of.

* _The attacker can remotely execute arbitrary code by gaining access to a chunk of kernel memory, without authentication._
* BlueKeep **PoC**'s (**P**roof **o**f **C**oncepts) and exploits could be **malicious** in nature, use only verified exploit code and modules.

This vulnerability affects multiple versions of Windows:

* XP
* Vista
* Windows 7
* Windows Server 2008 & R2

### WinRM <a href="#winrm" id="winrm"></a>

🗒️ [**WinRM**](https://learn.microsoft.com/en-us/windows/win32/winrm/portal) (**W**indows **R**emote **M**anagement **P**rotocol) - a protocol used to facilitate remote access with Windows systems over HTTP(S).

* Ports: `5986` - `5986 (HTTPS)` (TCP)
* Not configured by default
* Used by system administrator to:
  * remotely access, interact and execute commands on Windows hosts on a LAN
  * remotely manage and configure Windows systems
* Various form of authentication are used for access control and security

#### **WinRM Exploitation**

> [**`crackmapexec`**](https://www.kali.org/tools/crackmapexec/) - a python script, _a swiss army knife for pentesting Windows/Active Directory environments. From enumerating logged on users and spidering SMB shares to executing psexec style attacks, auto-injecting Mimikatz/Shellcode/DLL’s into memory using Powershell, dumping the NTDS.dit and more._
>
> Can be utilized for brute-force WinRM to find legitimate credentials.

<figure><img src="../../../../../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

> [**`evil-winrm`**](https://www.kali.org/tools/evil-winrm/) - a Ruby script used to obtain a command shell session on a target system

<figure><img src="../../../../../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

***

## Windows Privileges Escalation

🗒️ **Privilege Escalation** (_privesc_) is the process of exploiting vulnerabilities to escalate/elevate privileges from one user to a user with administrative or root access.

* it is an important part of the Penetration testing process, specially after gaining initial foothold
* the better the privesc is, the better the Pentest will be

### Win Kernel Exploits <a href="#win-kernel-exploits" id="win-kernel-exploits"></a>

{% hint style="danger" %}
Targeting Kernel space memory and apps can cause system crashes, data loss, etc
{% endhint %}

🗒️ The [kernel](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/windows-kernel-mode-kernel-library) of an operating system is a computer program that implements the core functionality of an OS. and has control over every system resource and hardware.

The kernel facilitates the communication between hardware and software layers.

[Windows NT](https://en.wikipedia.org/wiki/Windows\_NT) is the Microsoft Windows kernel and consists of two modes of operation

* User Mode - end-user programs with limited access to system resources
* Kernel Mode - unlimited access to system resources and functionality

An attacker can get shell code execution with the highest privileges by targeting vulnerabilities in the Windows kernel.

The **Windows Kernel Exploitation** process will be different, depending on the attacked system. It consists of:

1. Identifying kernel vulnerabilities (via automation scripts)
2. Downloading, compiling and transferring kernel exploits onto the target system, based on the target Windows version

> [**Windows-Exploit-Suggester**](https://github.com/AonCyberLabs/Windows-Exploit-Suggester) - a python tool that _compares a targets patch levels against the Microsoft vulnerability database in order to detect potential missing patches on the target. It notifies the user if there are public exploits and Metasploit modules available for the missing bulletins._

```bash
./windows-exploit-suggester.py --database YYYY-MM-DD-mssb.xlsx --systeminfo win7sp1-systeminfo.txt
```

> [**windows-kernel-exploits**](https://github.com/SecWiki/windows-kernel-exploits) - a Github collection of Windows Kernel Exploits sorted by CVE

<figure><img src="../../../../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

### UAC Bypass <a href="#uac-bypass" id="uac-bypass"></a>

🗒️ [**UAC**](https://learn.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-overview) (**U**ser **A**ccount **C**ontrol) - a Windows security feature used to prevent unauthorized changes to the operating system. Exception of cases when an administrator has deliberately granted administrator-level access to the system, _UAC ensures that programs and processes always operate in the security context of a **non-administrator account**_.

* It requires approval from a user that is part of the administrators group
* On modern versions of Windows, since Win Vista
* A **consent form** appears if the user is already a local administrator and he opens an app with `Run as administrator`
* A standard account instead, will be prompted with a **credential prompt**, to enter an administrator's credentials
* Depending on the type of access to the Windows system, **attacks can bypass UAC**, in order to execute malicious programs.
  * A **local administrators group** user account is necessary

UAC has _integrity levels_ ranging from Low to High.

* The bypass tools depend on the Windows release and the UAC integrity level.

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>default integrity level</p></figcaption></figure>

{% hint style="info" %}
It is hard to bypass the UAC when the integrity level is high
{% endhint %}

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Access Token Impersonation <a href="#access-token-impersonation" id="access-token-impersonation"></a>

🗒️ [**Access Tokens**](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-tokens) - objects that describe the security context of a process or a thread. A token includes the identity and privileges of the user account associated with the process.

* Created and managed by the **LSASS** (**L**ocal **S**ecurity **A**uthority **S**ubsystem **S**ervice)
* Generated by the `winlogon.exe` process at every user successful log on
* Every process executed by this user, has a copy of this **access token** (that is attached to the `userinit.exe` process)

**Security levels** are used to determine the token assigned privileges:

* `Impersonate-level` - non-interactive login on Windows (services, domain logons)
  * _can be used to impersonate a token **on the local system**_
* `Delegate-level` - interactive login on Windows (traditional login, RDP)
  * _can be used to impersonate a token **on any system**_ ❗

**Windows Privileges** determine what the user can or can't do.

For a successful **impersonation attack**, the following privileges are required:

* `SeAssignPrimaryToken` - allows a user to impersonate tokens
* `SeCreateToken` - allows a user to create an arbitrary token with administrative privileges
* `SeImpersonatePrivilege` - allows a user to impersonate a token, creating a process under the security context of another (privileged) user

> **`incognito`** meterpreter module - allows to list available tokens and to impersonate user tokens after exploitation

***

## File System - Alternate Data Streams <a href="#file-system-alternate-data-streams" id="file-system-alternate-data-streams"></a>

🗒️ [**ADS**](https://www.malwarebytes.com/blog/news/2015/07/introduction-to-alternate-data-streams) (**A**lternate **D**ata **S**treams) are a _file attribute only found on the `NTFS` file system_ that allow files to contain more than one stream of data. They were originally designed to provide compatibility with files in the MacOS file system and have been around since Windows NTFS was introduced.

* Any file created have 2 different streams:
  * **data stream** - contains the data of the file
  * **resource stream** - contains the `metadata` of the file (data of the data)
* With ADS, _malicious code can be hidden in legitimate files in order to evade detection_ by basic signature Antiviruses
  * the payload is stored in the metadata of the file.

***

## Windows Credential Dumping <a href="#windows-credential-dumping" id="windows-credential-dumping"></a>

🗒️ [**SAM**](https://www.windows-active-directory.com/windows-security-account-manager.html) (**S**ecurity **A**ccounts **M**anager) is a database file stored within `C:\Windows\System32\config`. It is used to _authenticate local and remote users and uses cryptographic measures to prevent unauthenticated users from accessing the system_. On a Domain Controller, it simply stores passwords hashes in `HKEY_LOCAL_MACHINE\SAM`.

* All the hashed user account passwords are stored in the SAM database
* SAM db file cannot be copied while the O.S. is running
* SAM db is encrypted with a `SysKey`

🗒️ _**Hashing**_ - the process of transforming any given piece of data into another value, using a **hash function** to generate the new value according to a algorithm.

* the result is called **hash/hash value**

Storing passwords locally is a big security risk, specially if stored unencrypted and in _clear-text_ strings.

* **`LM`** and **`NTLM`** are two types of hashes, utilized in versions up to Windows Server 2003
* **`NTLM`** only is used from Windows Vista onwards

🗒️ [**LSA**](https://learn.microsoft.com/en-us/windows/win32/secauthn/lsa-authentication) (**L**ocal **S**ecurity **A**uthority) - the central component of the Windows security subsystem, responsible for enforcing the security policy of the system, `e.g.` authentication, credentials verification, etc.

The Windows NT Kernel keeps the SAM database file locked.

* An attacker utilize _in-memory_ attack techniques and hash dumping tools to interact with the LSASS process

❗ _Elevated privileges are required for LSASS process interaction._

### Password Hashes <a href="#password-hashes" id="password-hashes"></a>

🗒️ **`LM`** - default hashing algorithm implemented in Windows prior to NT4.0

* outdated and weak protocol, easily crackable
* disabled by default since Windows Vista/Server 2008

🗒️ **`NTLM`** (`NTHash`) - a collection of authentication protocols and the currently used algorithm for _storing passwords on modern Windows systems_.

* Algorithm - the password is encrypted using the **`MD4`** hashing algorithm and the original password is disposed of
  * No split of the hash
  * It is case sensitive
  * Allows symbols and unicode chars
  * NTLMv1, NTLMv2 - _challenge response protocols used for authentication in Windows environments_
* NTLM (NT) hashes do not have password salts - _**can be cracked through a brute-force / dictionary attacks**_.

### Passwords Configuration Files <a href="#passwords-configuration-files" id="passwords-configuration-files"></a>

Windows configuration files can contain stored passwords, `e.g.` in the _Unattended Windows Setup_ utility, used to mass deploy Windows on systems.

* The configuration file can contain specific configurations and user account credentials
* An attacker can find the configuration file left on the target after installation

The utility typically utilizes those [files](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/update-windows-settings-and-scripts-create-your-own-answer-file-sxs?view=windows-11):

`C:\Windows\Panther\Unattend.xml`

`C:\Windows\Panther\Autounattend.xml`

* The stored passwords might be encoded in **`base64`** (easily decodable).

### Dumping Hashes with Mimikatz <a href="#dumping-hashes-with-mimikatz" id="dumping-hashes-with-mimikatz"></a>

> [**Mimikatz**](https://www.kali.org/tools/mimikatz/) - a tool that allows the extraction of clear-text passwords, hashes, PIN code and Kerberos tickets from memory.
>
> * _perform pass-the-hash, pass-the-ticket attacks, or build Golden tickets_
> * extract hashes from the `lsass.exe` process memory
> * **requires elevated privileges** (Administrator/SYSTEM)
> * pre-packet on Kali Linux and Parrot OS
> * **`Kiwi`** - `meterpreter` extension for hashes dumping from memory

***

### Pass-The-Hash <a href="#pass-the-hash" id="pass-the-hash"></a>

🗒️ [**Pass-the-hash**](https://www.crowdstrike.com/cybersecurity-101/pass-the-hash/) (**PtH**) is an exploitation technique that involves harvesting NTLM hashes and reusing them to authenticate with the target legitimately.

* It allows legitimate access to the target system, without exploitation
* Administrator user's NTLM hash comes useful after a service is being patched or disabled and can no longer be exploited







