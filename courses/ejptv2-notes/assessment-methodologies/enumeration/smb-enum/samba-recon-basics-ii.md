# Samba Recon: Basics II

### Overview

> In this challenge we will look at the basics of SMB server reconnaissance. Please start the lab and answer the following questions:
>
> Questions
>
> 1. Find the OS version of samba server using rpcclient.
> 2. Find the OS version of samba server using enum4Linux.
> 3. Find the server description of samba server using smbclient.
> 4. Is NT LM 0.12 (SMBv1) dialects supported by the samba server? Use appropriate nmap script.
> 5. Is SMB2 protocol supported by the samba server? Use smb2 metasploit module.
> 6. List all users that exists on the samba server  using appropriate nmap script.
> 7. List all users that exists on the samba server  using smb\_enumusers metasploit modules.
> 8. List all users that exists on the samba server  using enum4Linux.
> 9. List all users that exists on the samba server  using rpcclient.
> 10. Find SID of user “admin” using rpcclient.
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



Target: `192.151.57.3`

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
232257: eth0@if232258: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:0e brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.14/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
232260: eth1@if232261: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:97:39:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.151.57.2/24 brd 192.151.57.255 scope global eth1
       valid_lft forever preferred_lft forever

root@attackdefense:~# nmap 192.151.57.3        
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 12:18 UTC
Nmap scan report for target-1 (192.151.57.3)
Host is up (0.0000090s latency).
Not shown: 998 closed ports
PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:97:39:03 (Unknown)
```

> 1. Find the OS version of samba server using rpcclient.

Answer: `6.1`

```bash
root@attackdefense:~# rpcclient -U "" -N 192.151.57.3
rpcclient $> srvinfo
        SAMBA-RECON    Wk Sv PrQ Unx NT SNT samba.recon.lab
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03
```

> 2. Find the OS version of samba server using enum4Linux.

Answer: `6.1`

```bash
root@attackdefense:~# enum4linux -o 192.151.57.3
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jun 26 12:13:37 2024

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 192.151.57.3
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ==================================================== 
|    Enumerating Workgroup/Domain on 192.151.57.3    |
 ==================================================== 
[+] Got domain/workgroup name: RECONLABS

 ===================================== 
|    Session Check on 192.151.57.3    |
 ===================================== 
[+] Server 192.151.57.3 allows sessions using username '', password ''

 =========================================== 
|    Getting domain SID for 192.151.57.3    |
 =========================================== 
Domain Name: RECONLABS
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ====================================== 
|    OS information on 192.151.57.3    |
 ====================================== 
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for 192.151.57.3 from smbclient: 
[+] Got OS info for 192.151.57.3 from srvinfo:
        SAMBA-RECON    Wk Sv PrQ Unx NT SNT samba.recon.lab
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03
enum4linux complete on Wed Jun 26 12:13:37 2024

```

> 3. Find the server description of samba server using smbclient.

Answer: `samba.recon.lab`

```bash
root@attackdefense:~# smbclient -L 192.151.57.3 -N

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      
        john            Disk      
        aisha           Disk      
        emma            Disk      
        everyone        Disk      
        IPC$            IPC       IPC Service (samba.recon.lab)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        RECONLABS            SAMBA-RECON
```

> 4. Is NT LM 0.12 (SMBv1) dialects supported by the samba server? Use appropriate nmap script.

Answer: `Yes, it supported.`

```bash
root@attackdefense:~# nmap --script smb-protocols -p139,445 192.151.57.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 12:20 UTC
Nmap scan report for target-1 (192.151.57.3)
Host is up (0.000030s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:97:39:03 (Unknown)

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2.02
|     2.10
|     3.00
|     3.02
|_    3.11

Nmap done: 1 IP address (1 host up) scanned in 0.40 seconds
```

> 5. Is SMB2 protocol supported by the samba server? Use smb2 metasploit module.

Answer: `Yes, because it shows supports SMB 2.`

```bash
root@attackdefense:~# msfconsole
msf5 > use auxiliary/scanner/smb/smb2
msf5 auxiliary(scanner/smb/smb2) > options

Module options (auxiliary/scanner/smb/smb2):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   RHOSTS                    yes       The target address range or CIDR identifier
   RPORT    445              yes       The target port (TCP)
   THREADS  1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb2) > set RHOST 192.151.57.3
RHOST => 192.151.57.3
msf5 auxiliary(scanner/smb/smb2) > options

Module options (auxiliary/scanner/smb/smb2):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   RHOSTS   192.151.57.3     yes       The target address range or CIDR identifier
   RPORT    445              yes       The target port (TCP)
   THREADS  1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb2) > run

[+] 192.151.57.3:445      - 192.151.57.3 supports SMB 2 [dialect 255.2] and has been online for 3712188 hours
[*] 192.151.57.3:445      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 6. List all users that exists on the samba server using appropriate nmap script.

Answer: `admin, aisha, elie, emma, john, shawn`

```bash
root@attackdefense:~# nmap --script smb-enum-users -p445 192.151.57.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 12:25 UTC
Nmap scan report for target-1 (192.151.57.3)
Host is up (0.000045s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:97:39:03 (Unknown)

Host script results:
| smb-enum-users: 
|   SAMBA-RECON\admin (RID: 1005)
|     Full name:   
|     Description: 
|     Flags:       Normal user account
|   SAMBA-RECON\aisha (RID: 1004)
|     Full name:   
|     Description: 
|     Flags:       Normal user account
|   SAMBA-RECON\elie (RID: 1002)
|     Full name:   
|     Description: 
|     Flags:       Normal user account
|   SAMBA-RECON\emma (RID: 1003)
|     Full name:   
|     Description: 
|     Flags:       Normal user account
|   SAMBA-RECON\john (RID: 1000)
|     Full name:   
|     Description: 
|     Flags:       Normal user account
|   SAMBA-RECON\shawn (RID: 1001)
|     Full name:   
|     Description: 
|_    Flags:       Normal user account

Nmap done: 1 IP address (1 host up) scanned in 0.42 seconds
```

> 7. List all users that exists on the samba server using smb\_enumusers metasploit modules

Answer: `[ john, elie, aisha, shawn, emma, admin ]`

```bash
root@attackdefense:~# msfconsole
msf5 > use auxiliary/scanner/smb/smb_enumusers
msf5 auxiliary(scanner/smb/smb_enumusers) > options

Module options (auxiliary/scanner/smb/smb_enumusers):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   RHOSTS                      yes       The target address range or CIDR identifier
   SMBDomain  .                no        The Windows domain to use for authentication
   SMBPass                     no        The password for the specified username
   SMBUser                     no        The username to authenticate as
   THREADS    1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb_enumusers) > set RHOST 192.151.57.3
RHOST => 192.151.57.3
msf5 auxiliary(scanner/smb/smb_enumusers) > run

[+] 192.151.57.3:139      - SAMBA-RECON [ john, elie, aisha, shawn, emma, admin ] ( LockoutTries=0 PasswordMin=5 )
[*] 192.151.57.3:         - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 8. List all users that exists on the samba server using enum4Linux.

Answer:&#x20;

```bash
index: 0x1 RID: 0x3e8 acb: 0x00000010 Account: john     Name:   Desc: 
index: 0x2 RID: 0x3ea acb: 0x00000010 Account: elie     Name:   Desc: 
index: 0x3 RID: 0x3ec acb: 0x00000010 Account: aisha    Name:   Desc: 
index: 0x4 RID: 0x3e9 acb: 0x00000010 Account: shawn    Name:   Desc: 
index: 0x5 RID: 0x3eb acb: 0x00000010 Account: emma     Name:   Desc: 
index: 0x6 RID: 0x3ed acb: 0x00000010 Account: admin    Name:   Desc: 
```

```bash
root@attackdefense:~# enum4linux -U 192.151.57.3
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jun 26 12:28:59 2024

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 192.151.57.3
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ==================================================== 
|    Enumerating Workgroup/Domain on 192.151.57.3    |
 ==================================================== 
[+] Got domain/workgroup name: RECONLABS

 ===================================== 
|    Session Check on 192.151.57.3    |
 ===================================== 
[+] Server 192.151.57.3 allows sessions using username '', password ''

 =========================================== 
|    Getting domain SID for 192.151.57.3    |
 =========================================== 
Domain Name: RECONLABS
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ============================= 
|    Users on 192.151.57.3    |
 ============================= 
index: 0x1 RID: 0x3e8 acb: 0x00000010 Account: john     Name:   Desc: 
index: 0x2 RID: 0x3ea acb: 0x00000010 Account: elie     Name:   Desc: 
index: 0x3 RID: 0x3ec acb: 0x00000010 Account: aisha    Name:   Desc: 
index: 0x4 RID: 0x3e9 acb: 0x00000010 Account: shawn    Name:   Desc: 
index: 0x5 RID: 0x3eb acb: 0x00000010 Account: emma     Name:   Desc: 
index: 0x6 RID: 0x3ed acb: 0x00000010 Account: admin    Name:   Desc: 

user:[john] rid:[0x3e8]
user:[elie] rid:[0x3ea]
user:[aisha] rid:[0x3ec]
user:[shawn] rid:[0x3e9]
user:[emma] rid:[0x3eb]
user:[admin] rid:[0x3ed]
enum4linux complete on Wed Jun 26 12:28:59 2024
```

> 9. List all users that exists on the samba server using rpcclient.

```bash
rpcclient $> enumdomusers
user:[john] rid:[0x3e8]
user:[elie] rid:[0x3ea]
user:[aisha] rid:[0x3ec]
user:[shawn] rid:[0x3e9]
user:[emma] rid:[0x3eb]
user:[admin] rid:[0x3ed]
```

> 10. Find SID of user “admin” using rpcclient.

```bash
rpcclient $> lookupnames admin
admin S-1-5-21-4056189605-2085045094-1961111545-1005 (User: 1)
```

<details>

<summary>Flag</summary>

`S-1-5-21-4056189605-2085045094-1961111545-1005`

</details>





