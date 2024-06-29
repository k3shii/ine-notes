# Windows Recon: SMBMap

### Task

> A Kali GUI machine and a target machine running an SMB service are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target).&#x20;
>
> Your task is to fingerprint the service using the tools available on the Kali machine and run the smbmap tool to enumerate the target machine service.
>
> [**SMBMap**](https://github.com/ShawnDEvans/smbmap)**:**
>
> * Allows users to enumerate samba share
> * Allows file upload/download/delete
> * Permission enumeration (writable share, meet Metasploit)
> * etc.
>
> **Objective:** Enumerate the target machine SMB service using the smbmap tool and discover the flag.
>
> The following username and password may be used to access the service:
>
> \| Username | Password | | administrator | smbserver\_771 |
>
> **Instructions:**\* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y. \* The IP address of the target machine is mentioned in the file “/root/Desktop/target” \* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1



Target:  `10.0.31.120`

* Checking for target machine IP address is alive or not, and run a default port scanning

> Found out that SMB port 445 is exposed.

```bash
root@attackdefense:~# ping 10.0.31.120
PING 10.0.31.120 (10.0.31.120) 56(84) bytes of data.
64 bytes from 10.0.31.120: icmp_seq=1 ttl=125 time=3.66 ms
64 bytes from 10.0.31.120: icmp_seq=2 ttl=125 time=2.47 ms
64 bytes from 10.0.31.120: icmp_seq=3 ttl=125 time=2.52 ms
64 bytes from 10.0.31.120: icmp_seq=4 ttl=125 time=2.47 ms
64 bytes from 10.0.31.120: icmp_seq=5 ttl=125 time=2.74 ms

root@attackdefense:~# nmap 10.0.31.120
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-26 14:06 IST
Nmap scan report for 10.0.31.120
Host is up (0.0025s latency).
Not shown: 992 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.56 seconds
```

* Run Nmap script of `smb-protocols` to list the supported protocols and dialects of an SMB server.

```bash
root@attackdefense:~# nmap --script=smb-protocols -p445 10.0.31.120
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-26 14:07 IST
Nmap scan report for 10.0.31.120
Host is up (0.0024s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2.02
|     2.10
|     3.00
|_    3.02

Nmap done: 1 IP address (1 host up) scanned in 6.41 seconds
```

* Use the `smbmap` python script to enumerate the target machine with default `guest` and `null password` followed
  * `-d`: directory
  * `-H`: host

```bash
root@attackdefense:~# smbmap -u guest -p "" -d . -H 10.0.31.120
[+] Guest session   	IP: 10.0.31.120:445	Name: 10.0.31.120                                       
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	C                                                 	NO ACCESS	
	C$                                                	NO ACCESS	Default share
	D$                                                	NO ACCESS	Default share
	Documents                                         	NO ACCESS	
	Downloads                                         	NO ACCESS	
	IPC$                                              	READ ONLY	Remote IPC
	print$                                            	READ ONLY	Printer Drivers
```

* Notice that the `guest` account is enabled and it doesn’t have `permission` to `write` on any of the shared folders.&#x20;

> Run `smbmap` with administrator user credentials given
>
> * Username: `administrator`
> * Password: `smbserver_771`

```bash
root@attackdefense:~# smbmap -u administrator -p smbserver_771 -d . -H 10.0.31.120
[+] IP: 10.0.31.120:445	Name: 10.0.31.120                                       
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	READ, WRITE	Remote Admin
	C                                                 	READ ONLY	
	C$                                                	READ, WRITE	Default share
	D$                                                	READ, WRITE	Default share
	Documents                                         	READ ONLY	
	Downloads                                         	READ ONLY	
	IPC$                                              	READ ONLY	Remote IPC
	print$                                            	READ, WRITE	Printer Drivers
```

* Notice that found all the shares along with their permissions along with and the comment.

> Execute the command on the target machine through SMB by using `-x '<command>'`

* Commands can be executed on the target machine without any issue. With this, we can abuse and gain a normal or meterpreter shell. In this lab, will be focusing on enumeration using `smbmap`, without gaining the shell

```bash
root@attackdefense:~# smbmap -u administrator -p smbserver_771 -H 10.0.31.120 -x 'ipconfig'
                                
Windows IP Configuration


Ethernet adapter Ethernet 3:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Link-local IPv6 Address . . . . . : fe80::8a:2b40:30fe:e201%22
   IPv4 Address. . . . . . . . . . . : 10.0.31.120
   Subnet Mask . . . . . . . . . . . : 255.255.240.0
   Default Gateway . . . . . . . . . : 10.0.16.1

Tunnel adapter isatap.ap-southeast-1.compute.internal:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
```

* Listing all drives on the specified host with `-L` option.

```bash
root@attackdefense:~# smbmap -H 10.0.31.120 -u administrator -p smbserver_771 -L
[+] Host 10.0.31.120 Local Drives: C:\ D:\
[+] Host 10.0.31.120 Net Drive(s):
	No mapped network drives
```

* List contents of the directory of `C:\ drive` using `-r` option.

```bash
root@attackdefense:~# smbmap -H 10.0.31.120 -u administrator -p smbserver_771 -r 'C$'
[+] IP: 10.0.31.120:445	Name: 10.0.31.120                                       
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	C$                                                	READ, WRITE	
	.\C$\*
	dr--r--r--                0 Sat Sep  5 13:26:00 2020	$Recycle.Bin
	fw--w--w--           398356 Wed Aug 12 10:47:41 2020	bootmgr
	fr--r--r--                1 Wed Aug 12 10:47:40 2020	BOOTNXT
	dr--r--r--                0 Wed Aug 12 10:47:41 2020	Documents and Settings
	fr--r--r--               32 Mon Dec 21 21:27:10 2020	flag.txt
	fr--r--r--       8589934592 Wed Jun 26 14:03:35 2024	pagefile.sys
	dr--r--r--                0 Wed Aug 12 10:49:32 2020	PerfLogs
	dw--w--w--                0 Wed Aug 12 10:49:32 2020	Program Files
	dr--r--r--                0 Sat Sep  5 14:35:45 2020	Program Files (x86)
	dr--r--r--                0 Sat Sep  5 14:35:45 2020	ProgramData
	dr--r--r--                0 Sat Sep  5 09:16:57 2020	System Volume Information
	dw--w--w--                0 Sat Dec 19 11:14:55 2020	Users
	dr--r--r--                0 Wed Jun 26 14:12:35 2024	Windows
```

* Create a backdoor file

```bash
root@attackdefense:~# touch backdoor

root@attackdefense:~# ls
Desktop  backdoor  thinclient_drives
```

* Upload `backdoor` file to the `C:\` drive

```bash
root@attackdefense:~# smbmap -H 10.0.31.120 -u administrator -p smbserver_771 --upload '/root/backdoor' 'C$\backdoor'
[+] Starting upload: /root/backdoor (0 bytes)
[+] Upload complete

root@attackdefense:~# smbmap -H 10.0.31.120 -u administrator -p smbserver_771 -r 'C$'                                
[+] IP: 10.0.31.120:445	Name: 10.0.31.120                                       
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	C$                                                	READ, WRITE	
	.\C$\*
	dr--r--r--                0 Sat Sep  5 13:26:00 2020	$Recycle.Bin
	fr--r--r--                0 Wed Jun 26 14:21:18 2024	backdoor
	fw--w--w--           398356 Wed Aug 12 10:47:41 2020	bootmgr
	fr--r--r--                1 Wed Aug 12 10:47:40 2020	BOOTNXT
	dr--r--r--                0 Wed Aug 12 10:47:41 2020	Documents and Settings
	fr--r--r--               32 Mon Dec 21 21:27:10 2020	flag.txt
	fr--r--r--       8589934592 Wed Jun 26 14:03:35 2024	pagefile.sys
	dr--r--r--                0 Wed Aug 12 10:49:32 2020	PerfLogs
	dw--w--w--                0 Wed Aug 12 10:49:32 2020	Program Files
	dr--r--r--                0 Sat Sep  5 14:35:45 2020	Program Files (x86)
	dr--r--r--                0 Sat Sep  5 14:35:45 2020	ProgramData
	dr--r--r--                0 Sat Sep  5 09:16:57 2020	System Volume Information
	dw--w--w--                0 Sat Dec 19 11:14:55 2020	Users
	dr--r--r--                0 Wed Jun 26 14:12:35 2024	Windows

```

* Notice there's is a `flag.txt file`, download `flag.txt` file into local host machine.

```bash
root@attackdefense:~# smbmap -H 10.0.31.120 -u administrator -p smbserver_771 --download 'C$\flag.txt'
[+] Starting download: C$\flag.txt (32 bytes)
[+] File output to: /root/10.0.31.120-C_flag.txt

root@attackdefense:~# ls        
10.0.31.120-C_flag.txt	Desktop  backdoor  thinclient_drives

root@attackdefense:~# cat 10.0.31.120-C_flag.txt 
25f492dbef8453cdca69a173a75790f0
```

<details>

<summary>Flag</summary>

<pre data-overflow="wrap" data-full-width="false"><code><strong>25f492dbef8453cdca69a173a75790f0
</strong></code></pre>

</details>

