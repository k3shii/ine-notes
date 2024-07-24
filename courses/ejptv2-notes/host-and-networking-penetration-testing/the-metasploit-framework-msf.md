# The Metasploit Framework (MSF)

> **⚡ Prerequisites**
>
> * Basic familiarity with Linux & Windows
> * Basic familiarity with TCP & UDP protocols
>
> **📕 Learning Objectives**
>
> * Understand, install, configure and use **Metasploit Framework**
> * Perform **info-gathering**, **enumeration**, **exploitation**, **post exploitation** with Metasploit
>
> **🔬 Training list - PentesterAcademy/INE Labs**
>
> `subscription required`
>
> * [Metasploit Auxiliary modules](https://www.attackdefense.com/listingnoauth?labtype=metasploit\&subtype=metasploit-auxiliary)
> * [MITRE ATT\&CK Linux Discovery](https://attackdefense.com/listing?labtype=mitre\&subtype=mitre-discovery)
> * [Metasploit Meterpreter](https://attackdefense.com/listing?labtype=metasploit\&subtype=metasploit-meterpreter)
> * [Linux Vulnerable Servers Exploitation - Metasploit](https://www.attackdefense.com/listingnoauth?labtype=metasploit\&subtype=metasploit-linux-exploitation)
> * [Linux Exploitation - Metasploit](https://www.attackdefense.com/listingnoauth?labtype=linux-security-exploitation\&subtype=linux-security-exploitation-metasploit)
> * [Linux Post Modules - Metasploit](https://www.attackdefense.com/listingnoauth?labtype=linux-security-post-exploitation\&subtype=linux-security-post-exploitation-metasploit)
> * [Win Basic Exploitation - Metasploit](https://www.attackdefense.com/listingnoauth?labtype=windows-exploitation\&subtype=windows-exploitation-basics)
> * [Win Pentesting Basic Exploitation](https://attackdefense.com/listing?labtype=windows-exploitation\&subtype=windows-exploitation-pentesting)
> * [Win Apps Exploits - Metasploit](https://www.attackdefense.com/listingnoauth?labtype=metasploit\&subtype=metasploit-windows-apps-exploits)
> * [Win Post Exploitation - Metasploit](https://attackdefense.com/listing?labtype=windows-post-exploitation\&subtype=windows-post-exploitation-metasploit)
> * [Win Maintaining Access](https://attackdefense.com/listing?labtype=windows-maintaining-access\&subtype=windows-maintaining-access-basics)

## MSF Introduction

🗒️ The [**Metasploit Framework**](https://www.metasploit.com/) (**MSF**) is an open-source pentesting and exploit development platform, used to write, test and execute exploit code.

* Provides automation of the penetration testing life cycle (specially exploitation and post-exploitation)
* Used to develop and test exploits
* Has a world database and public tested exploits
* It is modular, new modules can be added and integrated
* [Pre-installed in Kali Linux](https://www.kali.org/tools/metasploit-framework/)
* It is [open-source](https://github.com/rapid7/metasploit-framework)
* Founded by H.D. Moore in 2003 (developed in Perl), Written in Ruby in 2007, acquired by [Rapid7](https://www.rapid7.com/) in 2009, released as Metasploit v6.0 in 2020
* **Metasploit Framework** is the Community Edition
* Metasploit Pro & Express are Commercial versions

> 📌 Check the [Metasploit Unleashed – Free Ethical Hacking Course by OffSec](https://www.offsec.com/metasploit-unleashed/)

### Terminology

<table><thead><tr><th width="176">Term</th><th>Description</th></tr></thead><tbody><tr><td><strong>Interface</strong></td><td>Methods of interacting with the Metasploit Framework (<code>msfconsole</code>, Metasploit cmd)</td></tr><tr><td><strong>Module</strong></td><td>Pieces of code that perform a particular task (an exploit)</td></tr><tr><td><strong>Vulnerability</strong></td><td>Exploitable flaw or weakness in a computer system or network</td></tr><tr><td><strong>Exploit</strong></td><td>Code/Module used to take advantage of a vulnerability</td></tr><tr><td><strong>Payload</strong></td><td>Piece of code delivered to the target by an exploit (execute arbitrary commands or provide remote access)</td></tr><tr><td><strong>Listener</strong></td><td>Utility that listens for an incoming connection from a target</td></tr></tbody></table>

> 📌 **Exploit** is launched (takes advantage of the vulnerability) ➡️ **Payload** dropped (executes a reverse shell command) ➡️ Connects back to the **Listener**

### Interfaces

🗒️ **Metasploit Framework Console** ([**MSFconsole**](https://www.offsec.com/metasploit-unleashed/msfconsole/)) - an all in one interface that provides with access to all the functionality of the MSF.

<figure><img src="../../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

🗒️ **Metasploit Framework Command Line Interface** ([**MSFcli**](https://www.offsec.com/metasploit-unleashed/msfcli/)) - a command line utility used to facilitate the creation of automation scripts that utilize Metasploit modules.

* Discontinued in 2015, MSFconsole can be used with the same functionality of _redirecting output from other tools into `msfcli` and vice versa._

🗒️ **Metasploit Community Edition GUI** - a web based GUI front-end of the MSF.

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

🗒️ [**Armitage**](https://www.kali.org/tools/armitage/) - a free Java based GUI front-end cyber attack management tool for the MSF.

* Visualizes targets and simplifies network discovery
* Recommends exploits
* Exposes the advanced capabilities of the MSF

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

### [Architecture](https://www.offsec.com/metasploit-unleashed/metasploit-architecture/)

<figure><img src="../../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

🗒️ A **module** is the piece of code that can be utilized and executed by the MSF.

The MSF **libraries** (Rex, Core, Base) allow to extend and initiate functionality, facilitating the execution of modules without having to write additional code.

### [Modules](https://www.offsec.com/metasploit-unleashed/modules-and-locations/)

<table><thead><tr><th width="200">MSF Module</th><th>Description</th></tr></thead><tbody><tr><td><strong>Exploit</strong></td><td>Used to take advantage of a vulnerability, usually paired with a payload</td></tr><tr><td><strong>Payload</strong></td><td>Code delivered and remotely executed on the target <em>after successful exploitation</em> - <strong>e.g.</strong> a reverse shell that initiates a connection</td></tr><tr><td><strong>Encoder</strong></td><td>Used to encode payloads in order to avoid Anti Virus detection - <strong>e.g.</strong> <a href="https://www.mandiant.com/resources/blog/shikata-ga-nai-encoder-still-going-strong">shikata_ga_nai</a> encoding scheme</td></tr><tr><td><strong>NOPS</strong></td><td>Keep the payload sizes consistent across exploit attempts and ensure the <em>stability of a payload</em> on the target system</td></tr><tr><td><strong>Auxiliary</strong></td><td>Is not paired with a payload, used to perform additional functionality - <strong>e.g.</strong> port scanners, fuzzers, sniffers, etc</td></tr></tbody></table>

### [Payload Types](https://www.offsec.com/metasploit-unleashed/payloads/)

**Payloads** are created at runtime from various components. Depending on the target system and infrastructure, there are two types of payloads that can be used:

* **Non-Staged Payload** - sent to the target system as is, along with the exploit
* **Staged Payload** - sent to the target in two parts:
  * the **stager** (first part) establish a stable communication channel between the attacker and target. It contains a payload, the stage, that initiates a reverse connection back to the attacker
  * the **stage** (second part) is downloaded by the stager and executed
    * executes arbitrary commands on the target
    * provides a reverse shell or Meterpreter session

🗒️ The [**Meterpreter**](https://www.offsec.com/metasploit-unleashed/about-meterpreter/) is an advanced multi-functional payload executed by in memory DLL injection stagers on the target system.

* Communicates over the stager socket
* Provides an interactive command interpreter on the target system

### [File System](https://www.offsec.com/metasploit-unleashed/filesystem-and-libraries/)

```bash
ls /usr/share/metasploit-framework
```

* MSF filesystem is intuitive and organized by directories.
* Modules are stored under:
  * `/usr/share/metasploit-framework/modules/`
  * `~/.msf4/modules` - user specified modules

### Pentesting with MSF

🗒️ [**PTES**](http://www.pentest-standard.org/index.php/Main\_Page) (**P**enetration **T**esting **E**xecution **S**tandard) is a methodology that contains 7 main sections, defined by the standard as a comprehensive basis for penetration testing execution.

* can be adopted as a roadmap for Metasploit integration and understanding of the phases of a penetration test.

> The various phases involved in a typical pentest should be:
>
> 📌 **Pre-Engagement Interactions**
>
> ⬇️
>
> 📌 **Information Gathering**
>
> ⬇️
>
> 📌 **Enumeration**
>
> * Threat Modeling
> * Vulnerability Analysis
>
> ⬇️
>
> 📌 **Exploitation**
>
> * Identify Vulnerable Services
> * Prepare Exploit Code
> * Gaining Access
> * Bypass AV detection
> * Pivoting
>
> ⬇️
>
> 📌 **Post Exploitation**
>
> * Privilege Escalation
> * Maintaining Persistent Access
> * Clearing Tracks
>
> ⬇️
>
> 📌 **Reporting**

<figure><img src="../../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

<table><thead><tr><th width="275" align="center">Pentesting Phase</th><th>MSF Implementation</th></tr></thead><tbody><tr><td align="center"><strong>Information Gathering &#x26; Enumeration</strong></td><td>Auxiliary Modules, <code>nmap</code> reports</td></tr><tr><td align="center"><strong>Vulnerability Scanning</strong></td><td>Auxiliary Modules, <code>nessus</code> reports</td></tr><tr><td align="center"><strong>Exploitation</strong></td><td>Exploit Modules &#x26; Payloads</td></tr><tr><td align="center"><strong>Post Exploitation</strong></td><td>Meterpreter</td></tr><tr><td align="center"><strong>Privilege Escalation</strong></td><td>Post Exploitation Modules, Meterpreter</td></tr><tr><td align="center"><strong>Maintaining Persistent Access</strong></td><td>Post Exploitation Modules, Persistence</td></tr></tbody></table>

## Metasploit Fundamentals

### [Database](https://www.offsec.com/metasploit-unleashed/using-databases/)

🗒️ The **Metasploit Framework Database** (**msfdb**) contains all the data used with MSF like assessments and scans data, etc.

* Uses PostgreSQL as the primary database - `postgresql` service must be running
* Facilitates the import and storage of scan results (from Nmap, Nessus, other tools)

### [MSF Kali Configuration](https://www.kali.org/docs/tools/starting-metasploit-framework-in-kali/)

* Use APT package manager on Kali Linux (or on Debian-based distros)

```bash
sudo apt update && sudo apt install metasploit-framework -y
```

* Enable `postgresql` at boot, start the service and initialize MSF database

```bash
sudo systemctl enable postgresql
sudo systemctl restart postgresql
sudo msfdb init
```

* Run **`msfconsole`** to start the Metasploit Framework Console

```bash
msfconsole
```

* Check the db connection is on in the `msfconsole`

```bash
db_status
```

> 📌 Check this article by StationX ➡️ [How to Use Metasploit in Kali Linux + Metasploitable3](https://www.stationx.net/how-to-use-metasploit-in-kali-linux/) which will cover:
>
> * Deploying a Kali Linux virtual machine with Metasploit pre-installed
> * Setting up a target in a virtual lab, Metasploitable3, with Vagrant
> * A sample walkthrough against a vulnerable MySQL Server
> * Frequently Asked Questions (FAQ)

### [MSFConsole](https://www.offsec.com/metasploit-unleashed/msfconsole/)

🗒️ The **Metasploit Framework Console** (**msfconsole**) is an all-in-one interface and centralized console that allows access to all of the MSF options and features.

* It is launched by running the `msfconsole` command

```bash
msfconsole
```

* Run it in quiet mode without the banner with

```bash
msfconsole -q
```

### Module Variables

An MSF module requires additional information that can be configured through the use of MSF **variables**, both _local_ or _global_ variables, called **`options`** inside the msfconsole.

**Variables e.g.** (they are based on the selected module):

* `LHOST` - attacker's IP address
* `LPORT` - attacker's port number (receive reverse connection)
* `RHOST` - target's IP address
* `RHOSTS` - multiple targets/networks IP addresses
* `RPORT` - target port number

### Useful Commands

* Run `msfconsole` and check these useful commands:

```bash
help
version

show -h
show all
show exploits

search <STRING>
use <MODULE_NAME>
set <OPTION>
run
execute # same as run

sessions
connect
```

### **Port Scan Example**

```bash
search portscan
use auxiliary/scanner/portscan/tcp
show options
set RHOSTS <TARGET_IP>
set PORTS 1-1000
run
# CTRL+C to cancel the running process
back
```

### **CVE Exploits Example**

```bash
search cve:2017 type:exploit platform:windows
```

### **Payload Options Example**

```bash
search eternalblue
use 0
# specify the identifier
set payload <PAYLOAD_NAME>
set RHOSTS <TARGET_IP>
run
# or
exploit
```

### [Workspaces](https://docs.rapid7.com/metasploit/managing-workspaces/)

🗒️ Metasploit **Workspaces** allows to manage and organize the hosts, data, scans and activities stored in the `msfdb`.

* Import, manipulate, export data
* Create, manage, switch between workspaces
* Sort and organize the assessments of the penetration test

> 📌 _It's recommended to create a new workspace for each engagement._

```bash
msfconsole -q
db_status
	[*] Connected to msf. Connection type: postgresql.
```

```bash
workspace -h
```

```bash
workspace
# current working workspace
	* default
```

* **Create** a new workspace

```bash
workspace -a Test
```

* **Change** workspace

```bash
workspace <WORKSPACE_NAME>
```

```bash
workspace -a INE
```

* **Delete** a workspace

```bash
workspace -d Test
```





