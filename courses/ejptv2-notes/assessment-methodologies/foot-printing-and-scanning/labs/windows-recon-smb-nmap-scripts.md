# Windows Recon: SMB Nmap Scripts

Task:

> A Kali GUI machine and a target machine running an SMB service are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target).&#x20;
>
> Your task is to fingerprint the service using the tools available on the Kali machine and run Nmap scripts to enumerate the Windows target machine SMB service.
>
> **Objective:**
>
> 1. Identify SMB Protocol Dialects
> 2. Find SMB security level information
> 3. Enumerate active sessions, shares, Windows users, domains, services, etc.
>
> The following username and password may be used to access the service:
>
> \| Username | Password | | administrator | smbserver\_771 |
>
> **Instructions:**
>
> * Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.
> * The IP address of the target machine is mentioned in the file “/root/Desktop/target”
> * Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

{% hint style="info" %}
Check the nmap script [https://nmap.org/nsedoc/scripts](https://nmap.org/nsedoc/scripts)
{% endhint %}

#### Step 1:

> Run port scanning and port 445 is opened which showing that SMB services is active.

```bash
root@attackdefense:~# nmap 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:05 IST
Nmap scan report for 10.0.26.39
Host is up (0.0024s latency).
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

Nmap done: 1 IP address (1 host up) scanned in 1.53 seconds
```



#### Step 2:

> Check available script, not necessary

```bash
root@attackdefense:~# nmap -sC -p445 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:10 IST
Nmap scan report for 10.0.26.39
Host is up (0.0023s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
|_clock-skew: mean: 0s, deviation: 3s, median: -1s
| smb-os-discovery: 
|   OS: Windows Server 2012 R2 Standard 9600 (Windows Server 2012 R2 Standard 6.3)
|   OS CPE: cpe:/o:microsoft:windows_server_2012::-
|   Computer name: WIN-OMCNBKR66MN
|   NetBIOS computer name: WIN-OMCNBKR66MN\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-06-25T14:40:11+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-06-25T14:40:10
|_  start_date: 2024-06-25T14:21:09
```



#### Step 3:

> Task 1: Identify SMB Protocol Dialects

```bash
root@attackdefense:~# nmap --script=smb-protocols -p445 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:08 IST
Nmap scan report for 10.0.26.39
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

Nmap done: 1 IP address (1 host up) scanned in 6.48 seconds
```



#### Step 4:

> Task 2: Find SMB security level information

```bash
root@attackdefense:~# nmap --script=smb-security-mode -p445 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:09 IST
Nmap scan report for 10.0.26.39
Host is up (0.0023s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Nmap done: 1 IP address (1 host up) scanned in 1.40 seconds
```



#### Step 5:

> Task 3: Enumerate active sessions

We will use it with Nmap script to scan the target to discover sensitive information.

Enumerating the users logged into a system through an SMB share with Nmap script without login into any credentials first.

```bash
root@attackdefense:~# nmap --script=smb-enum-sessions -p445 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:14 IST
Nmap scan report for 10.0.26.39
Host is up (0.0023s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-sessions: 
|   Users logged in
|_    WIN-OMCNBKR66MN\bob since <unknown>

Nmap done: 1 IP address (1 host up) scanned in 3.74 seconds
```

We can observe that on the target machine we have discovered that user bob is logged into without any credentials.&#x20;

This is possible because the target machine is running with the guest login enable configuration and it is a misconfiguration.&#x20;



#### Step 6:

> In case guest login is not enabled we can always use valid credentials of the target machine to discover the same information.

```bash
root@attackdefense:~# nmap --script=smb-enum-sessions --script-args smbusername=administrator,smbpassword=smbserver_771 -p445 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:20 IST
Nmap scan report for 10.0.26.39
Host is up (0.0024s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-sessions: 
|   Users logged in
|     WIN-OMCNBKR66MN\bob since 2024-06-25T14:22:42
|   Active SMB sessions
|_    ADMINISTRATOR is connected from \\10.10.21.3 for [just logged in, it's probably you], idle for [not idle]

Nmap done: 1 IP address (1 host up) scanned in 3.59 seconds

```



Step 7:

> Task 3: Enumerate shares

```bash
root@attackdefense:~# nmap --script=smb-enum-shares -p445 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:22 IST
Nmap scan report for 10.0.26.39
Host is up (0.0024s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.0.26.39\ADMIN$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Remote Admin
|     Anonymous access: <none>
|     Current user access: <none>
|   \\10.0.26.39\C: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.26.39\C$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Anonymous access: <none>
|     Current user access: <none>
|   \\10.0.26.39\D$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Anonymous access: <none>
|     Current user access: <none>
|   \\10.0.26.39\Documents: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.26.39\Downloads: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.26.39\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: Remote IPC
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.26.39\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Anonymous access: <none>
|_    Current user access: READ

Nmap done: 1 IP address (1 host up) scanned in 44.09 seconds

```

We can observe, in the output that we have accessed all the shares using guest users and we have received the permission of each folder or drive.

> `IPC$` share has read and write permissions.

{% hint style="info" %}
“The IPC$ share is also known as a null session connection. By using this session, Windows lets anonymous users perform certain activities, such as enumerating the names of domain accounts and network shares.” Scanning all shares using valid credentials to check the permission
{% endhint %}



#### Step 8:

> Scanning all shares using valid credentials to check the permissions

We can observe that the administrator user has read and write privilege to the entire C$. i.e C:\\

```bash
root@attackdefense:~# nmap --script=smb-enum-shares --script-args smbusername=administrator,smbpassword=smbserver_771 -p445 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:42 IST
Nmap scan report for 10.0.26.39
Host is up (0.0025s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: administrator
|   \\10.0.26.39\ADMIN$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Remote Admin
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.26.39\C: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.26.39\C$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.26.39\D$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: D:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.26.39\Documents: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Documents
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.26.39\Downloads: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Downloads
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.26.39\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: Remote IPC
|     Users: 1
|     Max Users: <unlimited>
|     Path: 
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.26.39\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows\system32\spool\drivers
|     Anonymous access: <none>
|_    Current user access: READ/WRITE

Nmap done: 1 IP address (1 host up) scanned in 48.23 seconds
```



#### Step 9:

> Task 3: Enumerate Windows users

**3 users present on the target machine:**

1. Administrator
2. bob
3. Guest

```bash
root@attackdefense:~# nmap --script=smb-enum-users --script-args smbusername=administrator,smbpassword=smbserver_771 -p445 10.0.28.35
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 21:31 IST
Nmap scan report for 10.0.28.35
Host is up (0.0024s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-users: 
|   WIN-OMCNBKR66MN\Administrator (RID: 500)
|     Description: Built-in account for administering the computer/domain
|     Flags:       Password does not expire, Normal user account
|   WIN-OMCNBKR66MN\bob (RID: 1010)
|     Flags:       Password does not expire, Normal user account
|   WIN-OMCNBKR66MN\Guest (RID: 501)
|     Description: Built-in account for guest access to the computer/domain
|_    Flags:       Password does not expire, Normal user account, Password not required

Nmap done: 1 IP address (1 host up) scanned in 4.50 seconds

```



#### Step 10:

> Get information about the server statistics. It uses port 445 and port 139 to fetch the details.

We can notice that we have received failed logins, permission & system errors, and opened files and print jobs.

```bash
root@attackdefense:~# nmap --script=smb-server-stats --script-args smbusername=administrator,smbpassword=smbserver_771 -p445 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:47 IST
Nmap scan report for 10.0.26.39
Host is up (0.0024s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-server-stats: 
|   Server statistics collected since 2024-06-25T14:21:08 (56m02s):
|     116816 bytes (34.75 b/s) sent, 100834 bytes (29.99 b/s) received
|_    34 failed logins, 7 permission errors, 0 system errors, 0 print jobs, 41 files opened

Nmap done: 1 IP address (1 host up) scanned in 1.38 seconds

```



#### Step 11:

> Task 3: Enumerate domains

We have received the information about the built-in domain on the target machine

```bash
root@attackdefense:~# nmap --script=smb-enum-domains --script-args smbusername=administrator,smbpassword=smbserver_771 -p445 10.0.26.39
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:48 IST
Nmap scan report for 10.0.26.39
Host is up (0.0024s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-domains: 
|   WIN-OMCNBKR66MN
|     Groups: WinRMRemoteWMIUsers__
|     Users: Administrator, bob, Guest
|     Creation time: 2013-08-22T14:47:57
|     Passwords: min length: n/a; min age: n/a days; max age: 42 days; history: n/a passwords
|     Properties: Complexity requirements exist
|     Account lockout disabled
|   Builtin
|     Groups: Access Control Assistance Operators, Administrators, Backup Operators, Certificate Service DCOM Access, Cryptographic Operators, Distributed COM Users, Event Log Readers, Guests, Hyper-V Administrators, IIS_IUSRS, Network Configuration Operators, Performance Log Users, Performance Monitor Users, Power Users, Print Operators, RDS Endpoint Servers, RDS Management Servers, RDS Remote Access Servers, Remote Desktop Users, Remote Management Users, Replicator, Users
|     Users: n/a
|     Creation time: 2013-08-22T14:47:57
|     Passwords: min length: n/a; min age: n/a days; max age: 42 days; history: n/a passwords
|_    Account lockout disabled

Nmap done: 1 IP address (1 host up) scanned in 3.54 seconds

```



#### Step 12:

> Task 3: Enumerating available user groups on a target machine.

```bash
root@attackdefense:~# nmap --script=smb-enum-groups --script-args smbusername=administrator,smbpassword=smbserver_771 -p445 10.0.28.35
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:55 IST
Nmap scan report for 10.0.28.35
Host is up (0.0024s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-groups: 
|   Builtin\Administrators (RID: 544): Administrator, bob
|   Builtin\Users (RID: 545): bob
|   Builtin\Guests (RID: 546): Guest
|   Builtin\Power Users (RID: 547): <empty>
|   Builtin\Print Operators (RID: 550): <empty>
|   Builtin\Backup Operators (RID: 551): <empty>
|   Builtin\Replicator (RID: 552): <empty>
|   Builtin\Remote Desktop Users (RID: 555): bob
|   Builtin\Network Configuration Operators (RID: 556): <empty>
|   Builtin\Performance Monitor Users (RID: 558): <empty>
|   Builtin\Performance Log Users (RID: 559): <empty>
|   Builtin\Distributed COM Users (RID: 562): <empty>
|   Builtin\IIS_IUSRS (RID: 568): <empty>
|   Builtin\Cryptographic Operators (RID: 569): <empty>
|   Builtin\Event Log Readers (RID: 573): <empty>
|   Builtin\Certificate Service DCOM Access (RID: 574): <empty>
|   Builtin\RDS Remote Access Servers (RID: 575): <empty>
|   Builtin\RDS Endpoint Servers (RID: 576): <empty>
|   Builtin\RDS Management Servers (RID: 577): <empty>
|   Builtin\Hyper-V Administrators (RID: 578): <empty>
|   Builtin\Access Control Assistance Operators (RID: 579): <empty>
|   Builtin\Remote Management Users (RID: 580): <empty>
|_  WIN-OMCNBKR66MN\WinRMRemoteWMIUsers__ (RID: 1000): <empty>

Nmap done: 1 IP address (1 host up) scanned in 2.89 seconds

```



#### Step 13:

> Task 3: Enumerate services

```bash
root@attackdefense:~# nmap --script=smb-enum-services --script-args smbusername=administrator,smbpassword=smbserver_771 -p445 10.0.28.35
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 20:58 IST
Nmap scan report for 10.0.28.35
Host is up (0.0024s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
| smb-enum-services: 
|   AmazonSSMAgent: 
|     display_name: Amazon SSM Agent
|     state: 
|       SERVICE_RUNNING
|       SERVICE_PAUSE_PENDING
|       SERVICE_PAUSED
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDENABLE
|   DiagTrack: 
|     display_name: Diagnostics Tracking Service
|     state: 
|       SERVICE_RUNNING
|       SERVICE_PAUSE_PENDING
|       SERVICE_PAUSED
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDENABLE
|   Ec2Config: 
|     display_name: Ec2Config
|     state: 
|       SERVICE_RUNNING
|       SERVICE_PAUSE_PENDING
|       SERVICE_PAUSED
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDENABLE
|   MSDTC: 
|     display_name: Distributed Transaction Coordinator
|     state: 
|       SERVICE_RUNNING
|       SERVICE_PAUSE_PENDING
|       SERVICE_PAUSED
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDENABLE
|   Spooler: 
|     display_name: Print Spooler
|     state: 
|       SERVICE_RUNNING
|       SERVICE_PAUSE_PENDING
|       SERVICE_PAUSED
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_NETBINDENABLE
|   vds: 
|     display_name: Virtual Disk
|     state: 
|       SERVICE_RUNNING
|       SERVICE_PAUSE_PENDING
|       SERVICE_PAUSED
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|_      SERVICE_CONTROL_NETBINDENABLE

Nmap done: 1 IP address (1 host up) scanned in 1.46 seconds
```



#### Step 14:

> Enumerating all the shared folders and drives then running the ls command (The `ls` command is used to list files or directories, similarly dir in windows) on all the shared folders.

```bash
root@attackdefense:~# nmap --script=smb-enum-shares,smb-ls --script-args smbusername=administrator,smbpassword=smbserver_771 -p445 10.0.28.35
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 21:00 IST
Nmap scan report for 10.0.28.35
Host is up (0.0025s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: administrator
|   \\10.0.28.35\ADMIN$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Remote Admin
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.28.35\C: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.28.35\C$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.28.35\D$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: D:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.28.35\Documents: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Documents
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.28.35\Downloads: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Downloads
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.28.35\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: Remote IPC
|     Users: 1
|     Max Users: <unlimited>
|     Path: 
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.28.35\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows\system32\spool\drivers
|     Anonymous access: <none>
|_    Current user access: READ/WRITE
| smb-ls: Volume \\10.0.28.35\ADMIN$
|   maxfiles limit reached (10)
| SIZE   TIME                 FILENAME
| <DIR>  2013-08-22T13:36:16  .
| <DIR>  2013-08-22T13:36:16  ..
| <DIR>  2013-08-22T15:39:31  ADFS
| <DIR>  2013-08-22T15:39:31  ADFS\ar
| <DIR>  2013-08-22T15:39:31  ADFS\bg
| <DIR>  2013-08-22T15:39:31  ADFS\cs
| <DIR>  2013-08-22T15:39:31  ADFS\da
| <DIR>  2013-08-22T15:39:31  ADFS\de
| <DIR>  2013-08-22T15:39:31  ADFS\el
| <DIR>  2013-08-22T15:39:31  ADFS\en
| 
| 
| Volume \\10.0.28.35\C
|   maxfiles limit reached (10)
| SIZE   TIME                 FILENAME
| <DIR>  2013-08-22T15:39:30  PerfLogs
| <DIR>  2013-08-22T13:36:16  Program Files
| <DIR>  2014-05-17T10:36:57  Program Files\Amazon
| <DIR>  2013-08-22T13:36:16  Program Files\Common Files
| <DIR>  2014-10-15T05:58:49  Program Files\DIFX
| <DIR>  2013-08-22T15:39:31  Program Files\Internet Explorer
| <DIR>  2014-07-10T18:40:15  Program Files\Update Services
| <DIR>  2020-08-12T04:13:47  Program Files\Windows Mail
| <DIR>  2013-08-22T15:39:31  Program Files\Windows NT
| <DIR>  2013-08-22T15:39:31  Program Files\WindowsPowerShell
| 
| 
| Volume \\10.0.28.35\C$
|   maxfiles limit reached (10)
| SIZE   TIME                 FILENAME
| <DIR>  2013-08-22T15:39:30  PerfLogs
| <DIR>  2013-08-22T13:36:16  Program Files
| <DIR>  2014-05-17T10:36:57  Program Files\Amazon
| <DIR>  2013-08-22T13:36:16  Program Files\Common Files
| <DIR>  2014-10-15T05:58:49  Program Files\DIFX
| <DIR>  2013-08-22T15:39:31  Program Files\Internet Explorer
| <DIR>  2014-07-10T18:40:15  Program Files\Update Services
| <DIR>  2020-08-12T04:13:47  Program Files\Windows Mail
| <DIR>  2013-08-22T15:39:31  Program Files\Windows NT
| <DIR>  2013-08-22T15:39:31  Program Files\WindowsPowerShell
| 
| 
| Volume \\10.0.28.35\Documents
| SIZE   TIME                 FILENAME
| <DIR>  2020-09-10T09:50:27  .
| <DIR>  2020-09-10T09:50:27  ..
| 
| 
| Volume \\10.0.28.35\Downloads
| SIZE   TIME                 FILENAME
| <DIR>  2020-09-10T09:50:27  .
| <DIR>  2020-09-10T09:50:27  ..
| 
| 
| Volume \\10.0.28.35\print$
|   maxfiles limit reached (10)
| SIZE    TIME                 FILENAME
| <DIR>   2013-08-22T15:39:31  .
| <DIR>   2013-08-22T15:39:31  ..
| <DIR>   2013-08-22T15:39:31  color
| 1058    2013-08-22T06:54:44  color\D50.camp
| 1079    2013-08-22T06:54:44  color\D65.camp
| 797     2013-08-22T06:54:44  color\Graphics.gmmp
| 838     2013-08-22T06:54:44  color\MediaSim.gmmp
| 786     2013-08-22T06:54:44  color\Photo.gmmp
| 822     2013-08-22T06:54:44  color\Proofing.gmmp
| 218103  2013-08-22T06:54:44  color\RSWOP.icm
|_

Nmap done: 1 IP address (1 host up) scanned in 55.44 seconds

```

