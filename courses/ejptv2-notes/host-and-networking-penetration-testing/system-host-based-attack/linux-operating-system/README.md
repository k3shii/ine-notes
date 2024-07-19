---
cover: ../../../../../.gitbook/assets/Top-5-Linux-distros-in-2023.webp
coverY: 36
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

# 🐧 Linux Operating System

## Linux Vulnerabilities <a href="#linux-vulnerabilities" id="linux-vulnerabilities"></a>

[**GNU/Linux**](https://www.gnu.org/home.en.html) is a free and open source operating system, combination of the Linux kernel and the GNU toolkit software collection, developed by Richard Stallman.

* Linux kernel is the core of the O.S.
* Linux distributions are variants of the same O.S.
* Typically deployed as a **server** O.S.
  * Linux server services and protocols can provide with an access vector that an attacker can use

<table><thead><tr><th width="183">Protocol/Service</th><th width="141">Ports</th><th>Purpose</th></tr></thead><tbody><tr><td><a href="https://httpd.apache.org/"><strong>Apache Web Server</strong></a></td><td>TCP <code>80</code>/<code>443</code></td><td>Open source cross-platform web server</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Secure_Shell"><strong>SSH</strong></a> (Secure Shell)</td><td>TCP <code>22</code></td><td>Cryptographic remote access protocol, used for operating network services securely over an unsecured network. Secure successor of <code>telnet</code></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/File_Transfer_Protocol"><strong>FTP</strong></a> (File Transfer Protocol)</td><td>TCP <code>21</code></td><td>Communication protocol used for file sharing between a server and a client, over TCP</td></tr><tr><td><a href="https://www.samba.org/samba/what_is_samba.html"><strong>SAMBA</strong></a></td><td>TCP <code>445</code></td><td>Open source implementation of the <code>SMB</code> protocol. Enabled Unix machines to communicate with Windows machines in a network</td></tr></tbody></table>

## Linux Exploitation <a href="#linux-exploitation" id="linux-exploitation"></a>

### Apache - ShellShock <a href="#apache-shellshock" id="apache-shellshock"></a>

* [CVE-2014-6271](https://nvd.nist.gov/vuln/detail/CVE-2014-6271)

> GNU Bash through 4.3 processes trailing strings after function definitions in the values of environment variables, which allows remote attackers to _**execute arbitrary code** via a crafted environment, as demonstrated by vectors involving the ForceCommand feature in **OpenSSH** sshd, the mod\_cgi and mod\_cgid modules in the **Apache HTTP Server**, scripts executed by unspecified DHCP clients, and other situations in which setting the environment occurs across a privilege boundary from Bash execution_, aka **ShellShock**.
>
> * `Bash` shell since v.1.3
> * The `Bash` mistakenly exectutes trailing commands after a series of characters
> * Apache web servers that run CGI or `.sh` scripts are also vulnerable

🗒️ [**Bash**](https://www.gnu.org/software/bash/) - \*_Nix_ shell part of the GNU project and default shell for most Linux distros.

🗒️ **CGI** (**C**ommond **G**ateway **I**nterface) - Apache executes arbitrary commands on the Linux system and the output displayed on the web server.

**ShellShock Exploitation**

1. Locate a script or input vector (legitimate Apache CGI scripts) to communicate with Bash
2. Input special characters within the HTTP headers (e.g. `user-agent`)
3. When CGI is executed, the web server will run it with Bash in a new process

The exploitation can be done manually and automatically.

### FTP <a href="#ftp" id="ftp"></a>

🗒️ [**FTP**](https://en.wikipedia.org/wiki/File\_Transfer\_Protocol) (File Transfer Protocol) - facilitate file sharing between a server and clients. Used for transfering files to and from a web server (`e.g.` CPanel or FTP credentials).

* Port: **`21`** (TCP) - default
* _User Authentication_ - `username` & `password`
  * _**anonymous**_ access may be configured on FTP - no credentials needed

#### **FTP Exploitation**

1. Credentials can be **brute-forced** on the FTP server
2. Exploit inherent vulnerability within FTP service

### SSH <a href="#ssh" id="ssh"></a>

🗒️ [**SSH**](https://en.wikipedia.org/wiki/Secure\_Shell) (**S**ecure **Sh**ell) - cryptographic remote administration protocol, typically used for servers remote access

* Port: **`22`** (TCP) - default
* SSH Authentication:
  * _User Authentication_ - `username` & `password`
  * `Key` based, 2 key pairs (**public** and **private** keys) - no username and password

#### **SSH Exploitation**

1. Credentials can be **brute-forced** on the SSH
2. With SSH legitimate credentials the attacker gain access to a full shell, with the utilized user account's privileges

### SAMBA <a href="#samba" id="samba"></a>

🗒️ [**SAMBA**](https://www.samba.org/samba/what\_is\_samba.html) - network file sharing protocol, for file and peripherals sharing on a LAN. It is the Linux implementation of SMB

* Port: **`445`** (TCP).&#x20;
* Port: `139` (Originally ran on top of NetBIOS )
* Not pre-packed, not a common running service
* _User Authentication_ - `username` & `password`

#### **SAMBA Exploitation**

1. Credentials can be **brute-forced**
2. Use SMBMap or `smbclient` to retrieve information





