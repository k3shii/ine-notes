# SSH Recon: Basic

### Overview

> In this challenge we will look at the basics of SSH server reconnaissance. Please start the lab and answer the following questions:
>
> Questions
>
> 1. What is the version of SSH server.
> 2. Fetch the banner using netcat and check the version of SSH server.
> 3. Fetch pre-login SSH banner.
> 4. How many “encryption\_algorithms” are supported by the SSH server.
> 5. What is the ssh-rsa host key being used by the SSH server.
> 6. Which authentication method is being used by the SSH server for user “student”.
> 7. Which authentication method is being used by the SSH server for user “admin”.
> 8. Fetch the flag from /home/student/FLAG by using nmap ssh-run script.
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1

### Task

> ### Pre-requisites
>
> 1. Basic familiarity with TCP & UDP.
>
> ### Requirements
>
> This task does not have any requirements.



> 🔬 [SSH Recon: Basic](https://attackdefense.com/challengedetails?cid=526)
>
> * Target IP: `192.127.171.3`
> * Enumeration of [OpenSSH](https://www.openssh.com/) server

```bash
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
207266: eth0@if207267: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:12 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.18/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
207269: eth1@if207270: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:7f:ab:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.127.171.2/24 brd 192.127.171.255 scope global eth1
       valid_lft forever preferred_lft forever
       
root@attackdefense:~# nmap -sn 192.127.171.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 06:01 UTC
Nmap scan report for linux (192.127.171.1)
Host is up (0.000057s latency).
MAC Address: 02:42:5E:79:1B:58 (Unknown)
Nmap scan report for target-1 (192.127.171.3)
Host is up (0.000024s latency).
MAC Address: 02:42:C0:7F:AB:03 (Unknown)
Nmap scan report for attackdefense.com (192.127.171.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.02 seconds
root@attackdefense:~# nmap 192.127.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 06:01 UTC
Nmap scan report for target-1 (192.127.171.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 02:42:C0:7F:AB:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.22 seconds
```

> 1. What is the version of SSH server.

Answer: `OpenSSH 7.2p2 Ubuntu 4ubuntu2.6`

```bash
root@attackdefense:~# nmap -sV 192.127.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 06:02 UTC
Nmap scan report for target-1 (192.127.171.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
MAC Address: 02:42:C0:7F:AB:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.49 seconds
```

### nc

> 2. Fetch the banner using netcat and check the version of SSH server.

```bash
root@attackdefense:~# nc 192.127.171.3 22
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6

Protocol mismatch.
```

### ssh

> 3. Fetch pre-login SSH banner.

Answer: `Welcome to attack defense ssh recon lab!!`

```bash
root@attackdefense:~# ssh root@192.127.171.3
The authenticity of host '192.127.171.3 (192.127.171.3)' can't be established.
ECDSA key fingerprint is SHA256:dxlBXgBb0Iv5/LmemZ2Eikb5+GLl9CSLf/B854fUeV8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.127.171.3' (ECDSA) to the list of known hosts.
Welcome to attack defense ssh recon lab!!
root@192.127.171.3's password: 
```

> 4. How many `“encryption_algorithms”` are supported by the SSH server.

Answer: `6`

```bash
root@attackdefense:~# nmap --script ssh2-enum-algos 192.127.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 06:06 UTC
Nmap scan report for target-1 (192.127.171.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
| ssh2-enum-algos: 
|   kex_algorithms: (6)
|       curve25519-sha256@libssh.org
|       ecdh-sha2-nistp256
|       ecdh-sha2-nistp384
|       ecdh-sha2-nistp521
|       diffie-hellman-group-exchange-sha256
|       diffie-hellman-group14-sha1
|   server_host_key_algorithms: (5)
|       ssh-rsa
|       rsa-sha2-512
|       rsa-sha2-256
|       ecdsa-sha2-nistp256
|       ssh-ed25519
|   encryption_algorithms: (6)
|       chacha20-poly1305@openssh.com
|       aes128-ctr
|       aes192-ctr
|       aes256-ctr
|       aes128-gcm@openssh.com
|       aes256-gcm@openssh.com
|   mac_algorithms: (10)
|       umac-64-etm@openssh.com
|       umac-128-etm@openssh.com
|       hmac-sha2-256-etm@openssh.com
|       hmac-sha2-512-etm@openssh.com
|       hmac-sha1-etm@openssh.com
|       umac-64@openssh.com
|       umac-128@openssh.com
|       hmac-sha2-256
|       hmac-sha2-512
|       hmac-sha1
|   compression_algorithms: (2)
|       none
|_      zlib@openssh.com
MAC Address: 02:42:C0:7F:AB:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.38 seconds
```

> 5. What is the ssh-rsa host key being used by the SSH server.

Answer:&#x20;

```bash
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1fkJK7F8yxf3vewEcLYHljBnKTAiRqzFxkFo6lqyew73ATL2Abyh6at/oOmBSlPI90rtAMA6jQGJ+0HlHgf7mkjz5+CBo9j2VPu1bejYtcxpqpHcL5Bp12wgey1zup74fgd+yOzILjtgbnDOw1+HSkXqN79d+4BnK0QF6T9YnkHvBhZyjzIDmjonDy92yVBAIoB6Rdp0w7nzFz3aN9gzB5MW/nSmgc4qp7R6xtzGaqZKp1H3W3McZO3RELjGzvHOdRkAKL7n2kyVAraSUrR0Oo5m5e/sXrITYi9y0X6p2PTUfYiYvgkv/3xUF+5YDDA33AJvv8BblnRcRRZ74BxaD
```

```basic
root@attackdefense:~# nmap --script ssh-hostkey --script-args ssh_hostkey=full 192.127.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 06:08 UTC
Nmap scan report for target-1 (192.127.171.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
| ssh-hostkey: 
|   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1fkJK7F8yxf3vewEcLYHljBnKTAiRqzFxkFo6lqyew73ATL2Abyh6at/oOmBSlPI90rtAMA6jQGJ+0HlHgf7mkjz5+CBo9j2VPu1bejYtcxpqpHcL5Bp12wgey1zup74fgd+yOzILjtgbnDOw1+HSkXqN79d+4BnK0QF6T9YnkHvBhZyjzIDmjonDy92yVBAIoB6Rdp0w7nzFz3aN9gzB5MW/nSmgc4qp7R6xtzGaqZKp1H3W3McZO3RELjGzvHOdRkAKL7n2kyVAraSUrR0Oo5m5e/sXrITYi9y0X6p2PTUfYiYvgkv/3xUF+5YDDA33AJvv8BblnRcRRZ74BxaD
|   ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBB0cJ/kSOXBWVIBA2QH4UB6r7nFL5l7FwHubbSZ9dIs2JSmn/oIgvvQvxmI5YJxkdxRkQlF01KLDmVgESYXyDT4=
|_  ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKuZlCFfTgeaMC79zla20ZM2q64mjqWhKPw/2UzyQ2W/
MAC Address: 02:42:C0:7F:AB:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
```

> 6. Which authentication method is being used by the SSH server for user “student”.

Answer: `none_auth`

```bash
root@attackdefense:~# nmap -p 22 --script ssh-auth-methods --script-args="ssh.user=student" 192.127.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 06:11 UTC
Nmap scan report for target-1 (192.127.171.3)
Host is up (0.000052s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-auth-methods: 
|_  Supported authentication methods: none_auth
MAC Address: 02:42:C0:7F:AB:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.40 seconds
```

> 7. Which authentication method is being used by the SSH server for user “admin”.

Answer: `publickey, password`

```bash
root@attackdefense:~# nmap -p 22 --script ssh-auth-methods --script-args="ssh.user=admin" 192.127.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 06:11 UTC
Nmap scan report for target-1 (192.127.171.3)
Host is up (0.000046s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
MAC Address: 02:42:C0:7F:AB:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 2.45 seconds
```

> 8. Fetch the flag from `/home/student/FLAG` by using nmap ssh-run script.

```bash
root@attackdefense:~# nmap --script ssh-run --script-args="ssh-run.cmd=cat /home/student/FLAG, ssh-run.username=student, ssh-run.password=" -p22 192.127.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 06:22 UTC
NSE: [ssh-run] Authenticated
NSE: [ssh-run] Running command: cat /home/student/FLAG
NSE: [ssh-run] Output of command: e1e3c0c9d409f594afdb18fe9ce0ffec

Nmap scan report for target-1 (192.127.171.3)
Host is up (0.000040s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-run: 
|   output: 
|_    e1e3c0c9d409f594afdb18fe9ce0ffec\x0D
MAC Address: 02:42:C0:7F:AB:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.39 seconds
```

```bash
// the FLAG is inside "student" if connect using ssh

root@attackdefense:~# ssh student@192.127.171.3
Welcome to attack defense ssh recon lab!!
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 5.4.0-152-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: Thu Jun 27 06:22:17 2024 from 192.127.171.2
student@victim-1:~$ ls
FLAG
student@victim-1:~$ cat FLAG
e1e3c0c9d409f594afdb18fe9ce0ffec
```

<details>

<summary>Flag</summary>

`e1e3c0c9d409f594afdb18fe9ce0ffec`

</details>









