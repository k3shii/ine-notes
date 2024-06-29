---
description: Linux SMB (smbd) enumeration
---

# Samba Recon: Basics

### Task

> In this challenge we will look at the basics of SMB server reconnaissance. Please start the lab and answer the following questions:
>
> Questions
>
> 1. Find the default tcp ports used by smbd.
> 2. Find the default udp ports used by nmbd.
> 3. What is the workgroup name of samba server?
> 4. Find the exact version of samba server by using appropriate nmap script.
> 5. Find the exact version of samba server by using smb\_version metasploit module.
> 6. What is the NetBIOS computer name of samba server? Use appropriate nmap scripts.
> 7. Find the NetBIOS computer name of samba server using nmblookup
> 8. Using smbclient determine whether anonymous connection (null session)  is allowed on the samba server or not.
> 9. Using rpcclient determine whether anonymous connection (null session) is allowed on the samba server or not.
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



Target: `192.40.195.3`

* Linux SMB (**`smbd`**) enumeration
* Check for local Ethernet IP address
* Use `nmap` host discovery to scan host within the subnet to find the target machines IP address

> Find the default tdp ports used by nmbd. (139, 445)

```bash
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
232202: eth0@if232203: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:0a brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.10/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
232205: eth1@if232206: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:28:c3:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.40.195.2/24 brd 192.40.195.255 scope global eth1
       valid_lft forever preferred_lft forever
       
root@attackdefense:~# nmap 192.40.195.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 10:21 UTC
Nmap scan report for linux (192.40.195.1)
Host is up (0.0000080s latency).
Not shown: 997 closed ports
PORT    STATE    SERVICE
22/tcp  open     ssh
80/tcp  filtered http
443/tcp filtered https
MAC Address: 02:42:8E:C4:3B:3A (Unknown)

Nmap scan report for target-1 (192.40.195.3)
Host is up (0.0000090s latency).
Not shown: 998 closed ports
PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:28:C3:03 (Unknown)

Nmap scan report for attackdefense.com (192.40.195.2)
Host is up (0.0000040s latency).
All 1000 scanned ports on attackdefense.com (192.40.195.2) are closed

Nmap done: 256 IP addresses (3 hosts up) scanned in 3.49 seconds
```

* Use `--top-ports` with udp scan to scan the common udp ports
  * `--top-ports`: Scan most common ports

> Find the default tdp ports used by nmbd. (137, 138)

```bash
root@attackdefense:~# nmap -sU --top-ports 25 192.40.195.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 10:24 UTC
Nmap scan report for target-1 (192.40.195.3)
Host is up (0.000079s latency).

PORT      STATE         SERVICE
53/udp    closed        domain
67/udp    closed        dhcps
68/udp    closed        dhcpc
69/udp    closed        tftp
111/udp   closed        rpcbind
123/udp   closed        ntp
135/udp   closed        msrpc
137/udp   open          netbios-ns
138/udp   open|filtered netbios-dgm
139/udp   closed        netbios-ssn
161/udp   closed        snmp
162/udp   closed        snmptrap
445/udp   closed        microsoft-ds
500/udp   closed        isakmp
514/udp   closed        syslog
520/udp   closed        route
631/udp   closed        ipp
998/udp   closed        puparp
1434/udp  closed        ms-sql-m
1701/udp  closed        L2TP
1900/udp  closed        upnp
4500/udp  closed        nat-t-ike
5353/udp  closed        zeroconf
49152/udp closed        unknown
49154/udp closed        unknown
MAC Address: 02:42:C0:28:C3:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 22.79 seconds
```

* What is the workgroup name of samba server?

> ```bash
> RECONLABS
> ```

```bash
root@attackdefense:~# nmap -sV -p139,445 192.40.195.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 10:27 UTC
Nmap scan report for target-1 (192.40.195.3)
Host is up (0.000042s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: RECONLABS)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: RECONLABS)
MAC Address: 02:42:C0:28:C3:03 (Unknown)
Service Info: Host: SAMBA-RECON

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.50 seconds
```

* Find the exact version of samba server by using appropriate nmap script.

> ```bash
> Samba 4.3.11-Ubuntu
> ```

```bash
root@attackdefense:~# nmap --script smb-os-discovery -p445 192.40.195.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 10:30 UTC
Nmap scan report for target-1 (192.40.195.3)
Host is up (0.000038s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:28:C3:03 (Unknown)

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: victim-1
|   NetBIOS computer name: SAMBA-RECON\x00
|   Domain name: \x00
|   FQDN: victim-1
|_  System time: 2024-06-26T10:30:34+00:00

Nmap done: 1 IP address (1 host up) scanned in 0.39 seconds
```



### Metasploit

* Find the exact version of samba server by using smb\_version metasploit module.

> ```bash
> Samba 4.3.11-Ubuntu
> ```

```bash
root@attackdefense:~# msfconsole
msf5 > use auxiliary/scanner/smb/smb_version
msf5 auxiliary(scanner/smb/smb_version) > options

Module options (auxiliary/scanner/smb/smb_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   RHOSTS                      yes       The target address range or CIDR identifier
   SMBDomain  .                no        The Windows domain to use for authentication
   SMBPass                     no        The password for the specified username
   SMBUser                     no        The username to authenticate as
   THREADS    1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb_version) > set RHOSTS 192.40.195.3
RHOSTS => 192.40.195.3
msf5 auxiliary(scanner/smb/smb_version) > options

Module options (auxiliary/scanner/smb/smb_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   RHOSTS     192.40.195.3     yes       The target address range or CIDR identifier
   SMBDomain  .                no        The Windows domain to use for authentication
   SMBPass                     no        The password for the specified username
   SMBUser                     no        The username to authenticate as
   THREADS    1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb_version) > exploit

[*] 192.40.195.3:445      - Host could not be identified: Windows 6.1 (Samba 4.3.11-Ubuntu)
[*] 192.40.195.3:445      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```



### Nmap Script

* What is the NetBIOS computer name of samba server? Use appropriate nmap scripts.

> ```bash
> NetBIOS computer name: SAMBA-RECON
> ```

```
root@attackdefense:~# nmap --script smb-os-discovery -p445 192.40.195.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 10:40 UTC
Nmap scan report for target-1 (192.40.195.3)
Host is up (0.000044s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:28:C3:03 (Unknown)

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: victim-1
|   NetBIOS computer name: SAMBA-RECON\x00
|   Domain name: \x00
|   FQDN: victim-1
|_  System time: 2024-06-26T10:40:21+00:00

Nmap done: 1 IP address (1 host up) scanned in 0.39 seconds
```



### nmblookup

* Find the NetBIOS computer name of samba server using `nmblookup`

> ```bash
> SAMBA-RECON     <20>
> ```

```bash
root@attackdefense:~# nmblookup -h
Usage: [-?fMRSTrAV] [-?|--help] [--usage] [-B|--broadcast=BROADCAST-ADDRESS]
        [-f|--flags] [-U|--unicast=STRING] [-M|--master-browser]
        [-R|--recursion] [-S|--status] [-T|--translate] [-r|--root-port]
        [-A|--lookup-by-ip] [-d|--debuglevel=DEBUGLEVEL]
        [-s|--configfile=CONFIGFILE] [-l|--log-basename=LOGFILEBASE]
        [-V|--version] [--option=name=value]
        [-O|--socket-options=SOCKETOPTIONS] [-n|--netbiosname=NETBIOSNAME]
        [-W|--workgroup=WORKGROUP] [-i|--scope=SCOPE] <NODE> ...
root@attackdefense:~# nmblookup -A 192.40.195.3
Looking up status of 192.40.195.3
        SAMBA-RECON     <00> -         H <ACTIVE> 
        SAMBA-RECON     <03> -         H <ACTIVE> 
        SAMBA-RECON     <20> -         H <ACTIVE> 
        ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE> 
        RECONLABS       <00> - <GROUP> H <ACTIVE> 
        RECONLABS       <1d> -         H <ACTIVE> 
        RECONLABS       <1e> - <GROUP> H <ACTIVE> 

        MAC Address = 00-00-00-00-00-00

```



### smbclient

* Using `smbclient` determine whether anonymous connection (`null session`) is allowed on the samba server or not.

> ```bash
> IPC$            IPC       IPC Service (samba.recon.lab)
> ```
>
> * Anonymous connection is allowed since shares are displayed without requirement of password

```bash
root@attackdefense:~# smbclient -h
Usage: smbclient [-?EgqBVNkPeC] [-?|--help] [--usage]
        [-R|--name-resolve=NAME-RESOLVE-ORDER] [-M|--message=HOST]
        [-I|--ip-address=IP] [-E|--stderr] [-L|--list=HOST]
        [-m|--max-protocol=LEVEL] [-T|--tar=<c|x>IXFqgbNan]
        [-D|--directory=DIR] [-c|--command=STRING] [-b|--send-buffer=BYTES]
        [-t|--timeout=SECONDS] [-p|--port=PORT] [-g|--grepable] [-q|--quiet]
        [-B|--browse] [-d|--debuglevel=DEBUGLEVEL]
        [-s|--configfile=CONFIGFILE] [-l|--log-basename=LOGFILEBASE]
        [-V|--version] [--option=name=value]
        [-O|--socket-options=SOCKETOPTIONS] [-n|--netbiosname=NETBIOSNAME]
        [-W|--workgroup=WORKGROUP] [-i|--scope=SCOPE] [-U|--user=USERNAME]
        [-N|--no-pass] [-k|--kerberos] [-A|--authentication-file=FILE]
        [-S|--signing=on|off|required] [-P|--machine-pass] [-e|--encrypt]
        [-C|--use-ccache] [--pw-nt-hash] service <password>
        
root@attackdefense:~# smbclient -L 192.40.195.3 -N

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



### rpcclient

* Using `rpcclient` determine whether anonymous connection (`null session`) is allowed on the samba server or not.

> Answer: Allowed
>
> Anonymous connection is allowed since no errors are thrown while connecting to samba server without any credentials

```bash
root@attackdefense:~# rpcclient -h
Usage: rpcclient [OPTION...]
  -c, --command=COMMANDS                 Execute semicolon separated cmds
  -I, --dest-ip=IP                       Specify destination IP address
  -p, --port=PORT                        Specify port number

Help options:
  -?, --help                             Show this help message
      --usage                            Display brief usage message

Common samba options:
  -d, --debuglevel=DEBUGLEVEL            Set debug level
  -s, --configfile=CONFIGFILE            Use alternate configuration file
  -l, --log-basename=LOGFILEBASE         Base name for log files
  -V, --version                          Print version
      --option=name=value                Set smb.conf option from command line

Connection options:
  -O, --socket-options=SOCKETOPTIONS     socket options to use
  -n, --netbiosname=NETBIOSNAME          Primary netbios name
  -W, --workgroup=WORKGROUP              Set the workgroup name
  -i, --scope=SCOPE                      Use this Netbios scope

Authentication options:
  -U, --user=USERNAME                    Set the network username
  -N, --no-pass                          Don't ask for a password
  -k, --kerberos                         Use kerberos (active directory)
                                         authentication
  -A, --authentication-file=FILE         Get the credentials from a file
  -S, --signing=on|off|required          Set the client signing state
  -P, --machine-pass                     Use stored machine account password
  -e, --encrypt                          Encrypt SMB transport
  -C, --use-ccache                       Use the winbind ccache for
                                         authentication
      --pw-nt-hash                       The supplied password is the NT hash
      
root@attackdefense:~# rpcclient -U "" -N 192.40.195.3
rpcclient $> 
```















