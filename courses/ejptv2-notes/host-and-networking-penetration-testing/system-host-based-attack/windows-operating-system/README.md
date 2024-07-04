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

<figure><img src="../../../../../.gitbook/assets/image (9).png" alt=""><figcaption><p>davtest</p></figcaption></figure>

> [**`cadaver`**](https://www.kali.org/tools/cadaver/) - supports file _upload, download, on-screen display, in-place editing, namespace operations (move/copy), collection creation and deletion, property manipulation, and resource locking_. Pre-installed on Kali Linux and Parrot OS.

```bash
cadaver [OPTIONS] http://hostname[:port]/path
```

<figure><img src="../../../../../.gitbook/assets/image (10).png" alt=""><figcaption><p>cadaver</p></figcaption></figure>

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

<figure><img src="../../../../../.gitbook/assets/image (8).png" alt=""><figcaption><p>SMB Authentication</p></figcaption></figure>

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

#### BlueKeep

> BlueKeep (CVE-2019-0708) - RDP vulnerability in Windows that could potentially allow attackers to remotely execute arbitrary code and gain access to Windows system and consequently the network that the target system is a part of.

* _The attacker can remotely execute arbitrary code by gaining access to a chunk of kernel memory, without authentication._
* BlueKeep **PoC**'s (**P**roof **o**f **C**oncepts) and exploits could be **malicious** in nature, use only verified exploit code and modules.











