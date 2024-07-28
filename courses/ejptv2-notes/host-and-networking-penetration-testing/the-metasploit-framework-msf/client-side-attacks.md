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

