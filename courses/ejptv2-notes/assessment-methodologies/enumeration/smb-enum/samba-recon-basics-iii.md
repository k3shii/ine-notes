# Samba Recon: Basics III

### Overview

> In this challenge, we will look at the basics of SMB server reconnaissance. Please start the lab and answer the following questions:
>
> Questions
>
> 1. List all available shares on the samba server using Nmap script.
> 2. List all available shares on the samba server using smb\_enumshares Metasploit module.
> 3. List all available shares on the samba server using enum4Linux.
> 4. List all available shares on the samba server using smbclient.
> 5. Find domain groups that exist on the samba server by using enum4Linux.
> 6. Find domain groups that exist on the samba server by using rpcclient.
> 7. Is samba server configured for printing?
> 8. How many directories are present inside share “public”?
> 9. Fetch the flag from samba server.
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



Target: `192.180.88.3`

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
207000: eth0@if207001: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:0a brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.10/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
207003: eth1@if207004: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:b4:58:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.180.88.2/24 brd 192.180.88.255 scope global eth1
       valid_lft forever preferred_lft forever
root@attackdefense:~# nmap 192.180.88.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 13:22 UTC
Nmap scan report for syn-192-180-088-001.res.spectrum.com (192.180.88.1)
Host is up (0.0000070s latency).
Not shown: 997 closed ports
PORT    STATE    SERVICE
22/tcp  open     ssh
80/tcp  filtered http
443/tcp filtered https
MAC Address: 02:42:41:B8:4D:34 (Unknown)

Nmap scan report for target-1 (192.180.88.3)
Host is up (0.0000090s latency).
Not shown: 998 closed ports
PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:B4:58:03 (Unknown)

Nmap scan report for attackdefense.com (192.180.88.2)
Host is up (0.0000040s latency).
All 1000 scanned ports on attackdefense.com (192.180.88.2) are closed

Nmap done: 256 IP addresses (3 hosts up) scanned in 3.45 seconds
```

> 1. List all available shares on the samba server using Nmap script.

Answer: `IPC$, aisha, emma, everyone, john, public`

```bash
root@attackdefense:~# nmap --script smb-enum-shares -p445 192.180.88.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 13:24 UTC
Nmap scan report for target-1 (192.180.88.3)
Host is up (0.000041s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:B4:58:03 (Unknown)

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\192.180.88.3\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (samba.recon.lab)
|     Users: 1
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\192.180.88.3\aisha: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\samba\aisha
|     Anonymous access: <none>
|     Current user access: <none>
|   \\192.180.88.3\emma: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\samba\emma
|     Anonymous access: <none>
|     Current user access: <none>
|   \\192.180.88.3\everyone: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\samba\everyone
|     Anonymous access: <none>
|     Current user access: <none>
|   \\192.180.88.3\john: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\samba\john
|     Anonymous access: <none>
|     Current user access: <none>
|   \\192.180.88.3\public: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\samba\public
|     Anonymous access: READ/WRITE
|_    Current user access: READ/WRITE

Nmap done: 1 IP address (1 host up) scanned in 0.78 seconds
```

> 2. List all available shares on the samba server using `smb_enumshares` Metasploit module.

Answer: `public, john, aisha, emma, everyone, IPC$`

```bash
root@attackdefense:~# msfconsole

msf5 > use auxiliary/scanner/smb/smb_enumshares 
msf5 auxiliary(scanner/smb/smb_enumshares) > options

Module options (auxiliary/scanner/smb/smb_enumshares):

   Name            Current Setting  Required  Description
   ----            ---------------  --------  -----------
   LogSpider       3                no        0 = disabled, 1 = CSV, 2 = table (txt), 3 = one liner (txt) (Accepted: 0, 1, 2, 3)
   MaxDepth        999              yes       Max number of subdirectories to spider
   RHOSTS                           yes       The target address range or CIDR identifier
   SMBDomain       .                no        The Windows domain to use for authentication
   SMBPass                          no        The password for the specified username
   SMBUser                          no        The username to authenticate as
   ShowFiles       false            yes       Show detailed information when spidering
   SpiderProfiles  true             no        Spider only user profiles when share = C$
   SpiderShares    false            no        Spider shares recursively
   THREADS         1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb_enumshares) > set RHOSTS 192.180.88.3
RHOSTS => 192.180.88.3
msf5 auxiliary(scanner/smb/smb_enumshares) > exploit

[+] 192.180.88.3:139      - public - (DS) 
[+] 192.180.88.3:139      - john - (DS) 
[+] 192.180.88.3:139      - aisha - (DS) 
[+] 192.180.88.3:139      - emma - (DS) 
[+] 192.180.88.3:139      - everyone - (DS) 
[+] 192.180.88.3:139      - IPC$ - (I) IPC Service (samba.recon.lab)
[*] 192.180.88.3:         - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 3. List all available shares on the samba server using enum4Linux.

Answer: `public, john, aisha, emma, everyone, IPC$`

```bash
Usage: ./enum4linux.pl [options] ip

Options are (like "enum"):
    -U        get userlist
    -M        get machine list*
    -S        get sharelist
    -P        get password policy information
    -G        get group and member list
    -d        be detailed, applies to -U and -S
    -u user   specify username to use (default "")  
    -p pass   specify password to use (default "")  
```

```bash
root@attackdefense:~# enum4linux -S 192.180.88.3
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jun 26 13:27:41 2024

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 192.180.88.3
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ==================================================== 
|    Enumerating Workgroup/Domain on 192.180.88.3    |
 ==================================================== 
[+] Got domain/workgroup name: RECONLABS

 ===================================== 
|    Session Check on 192.180.88.3    |
 ===================================== 
[+] Server 192.180.88.3 allows sessions using username '', password ''

 =========================================== 
|    Getting domain SID for 192.180.88.3    |
 =========================================== 
Domain Name: RECONLABS
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ========================================= 
|    Share Enumeration on 192.180.88.3    |
 ========================================= 

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

[+] Attempting to map shares on 192.180.88.3
//192.180.88.3/public   Mapping: OK, Listing: OK
//192.180.88.3/john     Mapping: DENIED, Listing: N/A
//192.180.88.3/aisha    Mapping: DENIED, Listing: N/A
//192.180.88.3/emma     Mapping: DENIED, Listing: N/A
//192.180.88.3/everyone Mapping: DENIED, Listing: N/A
//192.180.88.3/IPC$     [E] Can't understand response:
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*
enum4linux complete on Wed Jun 26 13:27:41 2024
```

> 4. List all available shares on the samba server using `smbclient`.

Answer: `public, john, aisha, emma, everyone, IPC$`

```bash
root@attackdefense:~# smbclient -L 192.180.88.3 -N 

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

> 5. Find domain groups that exist on the samba server by using enum4Linux.

Answer: `Maintainer, Reserved`

```bash
root@attackdefense:~# enum4linux -G 192.180.88.3 
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jun 26 13:31:38 2024

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 192.180.88.3
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ==================================================== 
|    Enumerating Workgroup/Domain on 192.180.88.3    |
 ==================================================== 
[+] Got domain/workgroup name: RECONLABS

 ===================================== 
|    Session Check on 192.180.88.3    |
 ===================================== 
[+] Server 192.180.88.3 allows sessions using username '', password ''

 =========================================== 
|    Getting domain SID for 192.180.88.3    |
 =========================================== 
Domain Name: RECONLABS
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ============================== 
|    Groups on 192.180.88.3    |
 ============================== 

[+] Getting builtin groups:

[+] Getting builtin group memberships:

[+] Getting local groups:
group:[Testing] rid:[0x3f0]

[+] Getting local group memberships:

[+] Getting domain groups:
group:[Maintainer] rid:[0x3ee]
group:[Reserved] rid:[0x3ef]

[+] Getting domain group memberships:
enum4linux complete on Wed Jun 26 13:31:38 2024
```

> 6. Find domain groups that exist on the samba server by using `rpcclient`.

Answer: `Maintainer, Reserved`

```bash
rpcclient $> enumdomgroups
group:[Maintainer] rid:[0x3ee]
group:[Reserved] rid:[0x3ef]
```

> 7. Is samba server configured for printing?

Answer: `No, result shows "No printers returned."`

```bash
root@attackdefense:~# enum4linux -i 192.180.88.3              
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jun 26 13:36:21 2024

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 192.180.88.3
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ==================================================== 
|    Enumerating Workgroup/Domain on 192.180.88.3    |
 ==================================================== 
[+] Got domain/workgroup name: RECONLABS

 ===================================== 
|    Session Check on 192.180.88.3    |
 ===================================== 
[+] Server 192.180.88.3 allows sessions using username '', password ''

 =========================================== 
|    Getting domain SID for 192.180.88.3    |
 =========================================== 
Domain Name: RECONLABS
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ============================================= 
|    Getting printer info for 192.180.88.3    |
 ============================================= 
No printers returned.


enum4linux complete on Wed Jun 26 13:36:22 2024

```

> 8. How many directories are present inside share “public”?

Answer: `2. /dev and /secret`

```bash
root@attackdefense:~# smbclient //192.180.88.3/public -N
Try "help" to get a list of possible commands.
smb: \> ?
?              allinfo        altname        archive        backup         
blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             
l              mask           md             mget           mkdir          
more           mput           newer          notify         open           
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir    
posix_unlink   posix_whoami   print          prompt         put            
pwd            q              queue          quit           readlink       
rd             recurse        reget          rename         reput          
rm             rmdir          showacls       setea          setmode        
scopy          stat           symlink        tar            tarmode        
timeout        translate      unlock         volume         vuid           
wdel           logon          listconnect    showconnect    tcon           
tdis           tid            utimes         logoff         ..             
!              
smb: \> 
```

```bash
smb: \> ls
  .                                   D        0  Wed Jun 26 13:24:24 2024
  ..                                  D        0  Tue Nov 27 13:36:13 2018
  dev                                 D        0  Tue Nov 27 13:36:13 2018
  secret                              D        0  Tue Nov 27 13:36:13 2018

                1981084628 blocks of size 1024. 166052312 blocks available
```

> 9. Fetch the flag from samba server.

```bash
smb: \> cd secret
smb: \secret\> ls
  .                                   D        0  Tue Nov 27 13:36:13 2018
  ..                                  D        0  Wed Jun 26 13:24:24 2024
  flag                                N       33  Tue Nov 27 13:36:13 2018

                1981084628 blocks of size 1024. 166073360 blocks available
smb: \secret\> get flag
getting file \secret\flag of size 33 as flag (32.2 KiloBytes/sec) (average 32.2 KiloBytes/sec)

smb: \secret\> exit

root@attackdefense:~# ls
README  enumdomgroups  flag  tools  wordlists

root@attackdefense:~# cat flag
03ddb97933e716f5057a18632badb3b4
```

<details>

<summary>Flag</summary>

`03ddb97933e716f5057a18632badb3b4`

</details>

