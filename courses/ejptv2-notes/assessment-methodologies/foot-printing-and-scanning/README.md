# Foot Printing & Scanning

## Network Mapping

1. Discovery of Live Hosts
2. Identification of Open Ports and Services
3. Network Topology Mapping
4. Operating System Fingerprinting
5. Service Version Detection
6. Identifying Filtering and Security Measures

## Host Discovery&#x20;

Tools:

1. [#ping](./#ping "mention")
2. [#fping](./#fping "mention")
3. nmap
4. zenmap

### Ping Sweeps

#### ping

```bash
// Linux Ping Sweeps
ping -c 5 10.0.22.138

// Windows Ping Sweeps
ping -n 5 10.0.22.138
```

```bash
root@attackdefense:~# ifconfig

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.10.16.5  netmask 255.255.255.0  broadcast 10.10.16.255
        ether 02:42:0a:0a:10:05  txqueuelen 0  (Ethernet)
        RX packets 16  bytes 1104 (1.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 94  bytes 9668 (9.4 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

root@attackdefense:~# sudo wireshark -i eth1
```

<figure><img src="../../../../.gitbook/assets/image (78).png" alt=""><figcaption><p>Host is down</p></figcaption></figure>

#### fping

```bash
// manual
root@attackdefense:~# fping --help

Probing options:
   -g, --generate     generate target list (only if no -f specified)
   (give start and end IP in the target list, or a CIDR address)
   (ex. fping -g 192.168.1.0 192.168.1.255 or fping -g 192.168.1.0/24)

Output options:
   -a, --alive        show targets that are alive
```

```bash
root@attackdefense:~# fping -a -g 10.10.16.0/24

ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.251
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.250
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.250
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.249
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.249
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.254
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.254
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.253
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.253
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.252
ICMP Host Unreachable from 10.10.16.5 for ICMP Echo sent to 10.10.16.252

//clean list of ip address that alive or online within the subnet
root@attackdefense:~# fping -a -g 10.10.16.0/24 2>/dev/null
10.10.16.1
10.10.16.5
```



Nmap

```bash
// scan on specifc range
root@attackdefense:~# nmap -sn 10.1.0.0-10

//scan all active host within the subnet (ARP)
root@attackdefense:~# nmap -sn 10.1.0.0/24

//scan all active host within the subnet (ICMP)
root@attackdefense:~# nmap -sn 10.1.0.0/24 --send-ip

//specify other ip 
root@attackdefense:~# nmap -sn 10.0.20.168 10.0.20.169
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 09:56 IST
Nmap done: 2 IP addresses (0 hosts up) scanned in 3.14 seconds

//specify range
root@attackdefense:~# nmap -sn 10.0.20.168-200        
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 09:57 IST
Nmap done: 33 IP addresses (0 hosts up) scanned in 27.16 seconds
```

```
// target.txt
10.0.20.168
10.0.20.169
10.0.20.1-10
```

```bash
root@attackdefense:~# nmap -sn -iL target.txt
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 10:04 IST
Nmap done: 12 IP addresses (0 hosts up) scanned in 11.14 seconds
```

### ARP Scanning

```bash
ip -br -c a
# -br = brief
# -c  = color
```

```bash
sudo arp-scan -I eth1 192.168.31.0/24
```

### TCP SYN Ping

Started with sending SYN packet

* default send to port 80
* `Port closed` -> Response with `RST` packet
* `Port Opened` -> Response with `SYN + ACK` packet -> `RST` is sent to reset connection

```bash
// normal ping scan 0 host up
root@attackdefense:~# nmap -sn 10.0.20.168
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 10:11 IST
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 3.09 seconds

// tcp scan 1 host is up (send SYN packet)
root@attackdefense:~# nmap -sn -PS 10.0.20.168
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 10:11 IST
Nmap scan report for 10.0.20.168
Host is up (0.0029s latency).
Nmap done: 1 IP address (1 host up) scanned in 0.10 seconds

//port scan
root@attackdefense:~# nmap -PS 10.0.18.109
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 10:33 IST
Nmap scan report for 10.0.18.109
Host is up (0.0029s latency).
Not shown: 993 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 4.84 seconds
```

<figure><img src="../../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

### UDP Ping

```bash
root@attackdefense:~# nmap -sn -PU137,138 10.0.18.109
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 11:03 IST
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 3.08 seconds
```

<figure><img src="../../../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

### TCP ACK Ping

{% hint style="info" %}
Not recommended to use because normally will block by firewall
{% endhint %}

Started with sending `ACK` packet

> * default send to port 80
> * `Port Closed` -> No response the request
> * `Port Opened` -> Response with `RST` packet



```bash
// Some code
root@attackdefense:~# nmap -sn -PA 10.0.18.109
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 10:47 IST
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 2.11 seconds

```

<figure><img src="../../../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

```bash
// ICMP echo request
root@attackdefense:~# nmap -sn -PE 10.0.18.109
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 10:54 IST
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 2.11 seconds
```

<figure><img src="../../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

### SYN-ACK Ping



## Port Scanning

{% hint style="info" %}
Running with root or sudo privileges: SYN port scan response by RST

Running without root: TCP connect port scan (will complete the 3-way handshake)
{% endhint %}

> **Port State**
>
> `active` - the port is active
>
> `filtered` - port dealing with windows firewall
>
> `close` - port not dealing with windows firewall



1. Default Port Scan

```bash
// default port scanning
root@attackdefense:~# nmap -Pn 10.0.21.189
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 12:28 IST
Nmap scan report for 10.0.21.189
Host is up (0.0027s latency).
Not shown: 993 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 4.79 seconds

//fast scanning for thousance port
root@attackdefense:~# nmap -Pn -F 10.0.21.189
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 12:29 IST
Nmap scan report for 10.0.21.189
Host is up (0.0030s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.93 seconds
```



2. Stealth Scan

```bash
// stealth scanning with not privileges mode
root@attackdefense:~# nmap -Pn -sT 10.0.21.189
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 13:25 IST
Nmap scan report for 10.0.21.189
Host is up (0.0028s latency).
Not shown: 993 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 5.98 seconds

//stealth scanning with priivileges mode / sudo
root@attackdefense:~# nmap -Pn -sS 10.0.21.189
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 13:25 IST
Nmap scan report for 10.0.21.189
Host is up (0.0027s latency).
Not shown: 993 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 4.79 seconds
```



3. UDP Scan

```bash
root@attackdefense:~# nmap -Pn -sU -p53,137,138,139 10.0.21.189
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 13:29 IST
Nmap scan report for 10.0.21.189
Host is up.

PORT    STATE         SERVICE
53/udp  open|filtered domain
137/udp open|filtered netbios-ns
138/udp open|filtered netbios-dgm
139/udp open|filtered netbios-ssn

Nmap done: 1 IP address (1 host up) scanned in 3.13 seconds
```



### Service Version & OS Detection

#### Service Version Detection

```bash
//without -sV (service version unknown)
root@attackdefense:~# nmap -sS -T4 -p- 192.35.25.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 09:09 UTC
Nmap scan report for target-1 (192.35.25.3)
Host is up (0.0000090s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE
6421/tcp  open  nim-wan
41288/tcp open  unknown
55413/tcp open  unknown
MAC Address: 02:42:C0:23:19:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.17 seconds

//with -sV (service version shown)
root@attackdefense:~# nmap -sS -sV -T4 -p- 192.35.25.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 09:07 UTC
Nmap scan report for target-1 (192.35.25.3)
Host is up (0.0000090s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE   VERSION
6421/tcp  open  mongodb   MongoDB 2.6.10
41288/tcp open  memcached Memcached
55413/tcp open  ftp       vsftpd 3.0.3
MAC Address: 02:42:C0:23:19:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.43 seconds
```

> More accurate service detection
>
> * `--version-intensity <0-9>`

```bash
root@attackdefense:~# nmap -sS -sV --version-intensity 8 -T4 -p- 192.35.25.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 09:32 UTC
Nmap scan report for target-1 (192.35.25.3)
Host is up (0.0000090s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE   VERSION
6421/tcp  open  mongodb   MongoDB 2.6.10
41288/tcp open  memcached Memcached
55413/tcp open  ftp       vsftpd 3.0.3
MAC Address: 02:42:C0:23:19:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.44 seconds
```

#### OS Version Detection

> No exact OS matched

```bash
root@attackdefense:~# nmap -sS -sV -O -T4 -p- 192.35.25.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 09:11 UTC
Nmap scan report for target-1 (192.35.25.3)
Host is up (0.000048s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE   VERSION
6421/tcp  open  mongodb   MongoDB 2.6.10
41288/tcp open  memcached Memcached
55413/tcp open  ftp       vsftpd 3.0.3
MAC Address: 02:42:C0:23:19:03 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.70%E=4%D=6/24%OT=6421%CT=1%CU=39137%PV=N%DS=1%DC=D%G=Y%M=0242C0
OS:%TM=66793864%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10E%TI=Z%CI=Z%II
OS:=I%TS=A)OPS(O1=M5B4ST11NW7%O2=M5B4ST11NW7%O3=M5B4NNT11NW7%O4=M5B4ST11NW7
OS:%O5=M5B4ST11NW7%O6=M5B4ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%
OS:W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M5B4NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S
OS:=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%R
OS:D=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=
OS:0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U
OS:1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DF
OS:I=N%T=40%CD=S)

Network Distance: 1 hop
Service Info: OS: Unix

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.01 seconds
```

> Scan OS detection more aggressively

```bash
root@attackdefense:~# nmap -sS -sV -O --osscan-guess -T4 -p- 192.35.25.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 09:26 UTC
Nmap scan report for target-1 (192.35.25.3)
Host is up (0.000043s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE   VERSION
6421/tcp  open  mongodb   MongoDB 2.6.10
41288/tcp open  memcached Memcached
55413/tcp open  ftp       vsftpd 3.0.3
MAC Address: 02:42:C0:23:19:03 (Unknown)
Aggressive OS guesses: Linux 2.6.32 (96%), Linux 3.2 - 4.9 (96%), Linux 2.6.32 - 3.10 (96%), Linux 3.4 - 3.10 (95%), Synology DiskStation Manager 5.2-5644 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Linux 2.6.32 - 2.6.35 (94%), Linux 2.6.32 - 3.5 (94%)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.70%E=4%D=6/24%OT=6421%CT=1%CU=32402%PV=N%DS=1%DC=D%G=Y%M=0242C0
OS:%TM=66793BE6%P=x86_64-pc-linux-gnu)SEQ(SP=FD%GCD=1%ISR=106%TI=Z%CI=Z%II=
OS:I%TS=A)OPS(O1=M5B4ST11NW7%O2=M5B4ST11NW7%O3=M5B4NNT11NW7%O4=M5B4ST11NW7%
OS:O5=M5B4ST11NW7%O6=M5B4ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W
OS:6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M5B4NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=
OS:O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD
OS:=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0
OS:%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1
OS:(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI
OS:=N%T=40%CD=S)

Network Distance: 1 hop
Service Info: OS: Unix

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.02 seconds
```



### Nmap Scripting Engine (NSE)

> `ls -al /usr/share/nmap/scripts` - lsit all nmap script listing

<figure><img src="../../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

```bash
//filter http relevant nmap script

root@attackdefense:~# ls -al /usr/share/nmap/scripts/ | grep -e "http"
-rw-r--r-- 1 root root  2153 Jan  9  2019 http-adobe-coldfusion-apsa1301.nse
-rw-r--r-- 1 root root  5111 Jan  9  2019 http-affiliate-id.nse
-rw-r--r-- 1 root root  1950 Jan  9  2019 http-apache-negotiation.nse
-rw-r--r-- 1 root root  4499 Jan  9  2019 http-apache-server-status.nse
-rw-r--r-- 1 root root  1805 Jan  9  2019 http-aspnet-debug.nse
-rw-r--r-- 1 root root  3961 Jan  9  2019 http-auth-finder.nse
-rw-r--r-- 1 root root  3187 Jan  9  2019 http-auth.nse
```

> adding `-sC` for running default script scanning

<pre class="language-bash"><code class="lang-bash">root@attackdefense:~# nmap -sS -sV -sC -p- -T4 192.35.25.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 10:03 UTC
Nmap scan report for target-1 (192.35.25.3)
Host is up (0.0000090s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE   VERSION
6421/tcp  open  mongodb   MongoDB 2.6.10 2.6.10
| mongodb-databases: 
|   totalSize = 83886080.0
|   ok = 1.0
|   databases
|     0
|       empty = false
|       name = local
|       sizeOnDisk = 83886080.0
|     1
|       empty = true
|       name = admin
|_      sizeOnDisk = 1.0
| mongodb-info: 
|   MongoDB Build info
|     loaderFlags = -fPIC -pthread -Wl,-z,now -rdynamic
|     allocator = tcmalloc
|     version = 2.6.10
|     gitVersion = nogitversion
|     bits = 64
|     OpenSSLVersion = OpenSSL 1.0.2g  1 Mar 2016
|     ok = 1.0
|     maxBsonObjectSize = 16777216
|     debug = false
|     javascriptEngine = V8
|     compilerFlags = -Wnon-virtual-dtor -Woverloaded-virtual -fPIC -fno-strict-aliasing -ggdb -pthread -Wall -Wsign-compare -Wno-unused-function -Wno-unused-variable -Wno-maybe-uninitialized -Wno-unknown-pragmas -Winvalid-pch -pipe -Werror -O3 -Wno-unused-local-typedefs -Wno-unused-function -Wno-deprecated-declarations -fno-builtin-memcmp
|     sysInfo = Linux lgw01-12 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 BOOST_LIB_VERSION=1_58
|     versionArray
|       2 = 10
|       1 = 6
|       0 = 2
|       3 = 0
|   Server status
|     dur
|       compression = 0.0
|       commitsInWriteLock = 0
|       timeMs
|         prepLogBuffer = 0
|         writeToJournal = 0
|         dt = 3069
|         remapPrivateView = 0
|         writeToDataFiles = 0
|       commits = 30
|       earlyCommits = 0
|       journaledMB = 0.0
|       writeToDataFilesMB = 0.0
|     host = victim-1:6421
|     backgroundFlushing
|       average_ms = 0.33333333333333
|       last_ms = 0
|       last_finished = 1719223411302
|       flushes = 66
|       total_ms = 22
|     localTime = 1719223417405
|     locks
|       .
|         timeAcquiringMicros
|           R = 20054
|           W = 4231
|         timeLockedMicros
|           R = 40741
|           W = 45045
|       admin
|         timeAcquiringMicros
|           r = 117
|           w = 0
|         timeLockedMicros
|           r = 2382
|           w = 0
|       local
|         timeAcquiringMicros
|           r = 5211
|           w = 0
|         timeLockedMicros
|           r = 15691
|           w = 0
|     pid = 26
|     metrics
|       queryExecutor
|         scannedObjects = 0
|         scanned = 0
|       operation
|         scanAndOrder = 0
|         idhack = 0
|         fastmod = 0
|       cursor
|         timedOut = 0
|         open
|           pinned = 0
|           noTimeout = 0
|           total = 0
|       record
|         moves = 0
|       ttl
|         deletedDocuments = 0
|         passes = 66
|       storage
|         freelist
|           search
|             bucketExhausted = 0
|             scanned = 11
|             requests = 6
|       repl
|         network
|           getmores
|             totalMillis = 0
|             num = 0
|           ops = 0
|           readersCreated = 0
|           bytes = 0
|         buffer
|           count = 0
|           sizeBytes = 0
|           maxSizeBytes = 268435456
|         preload
|           docs
|             totalMillis = 0
|             num = 0
|           indexes
|             totalMillis = 0
|             num = 0
|         apply
|           batches
|             totalMillis = 0
|             num = 0
|           ops = 0
|       document
|         inserted = 1
|         returned = 0
|         deleted = 0
|         updated = 0
|       getLastError
|         wtime
|           totalMillis = 0
|           num = 0
|         wtimeouts = 0
|     uptime = 3966.0
|     uptimeEstimate = 3926.0
<strong>|     ok = 1.0
</strong>|     mem
|       resident = 44
|       mapped = 80
|       virtual = 382
|       mappedWithJournal = 160
|       supported = true
|       bits = 64
|     opcountersRepl
|       delete = 0
|       command = 0
|       update = 0
|       insert = 0
|       query = 0
|       getmore = 0
|     process = mongod
|     network
|       bytesOut = 14995
|       numRequests = 5
|       bytesIn = 325
|     version = 2.6.10
|     recordStats
|       local
|         pageFaultExceptionsThrown = 0
|         accessesNotInMemory = 0
|       pageFaultExceptionsThrown = 0
|       admin
|         pageFaultExceptionsThrown = 0
|         accessesNotInMemory = 0
|       accessesNotInMemory = 0
|     writeBacksQueued = false
|     opcounters
|       delete = 0
|       command = 8
|       update = 0
|       insert = 1
|       query = 133
|       getmore = 0
|     connections
|       totalCreated = 18
|       available = 838858
|       current = 2
|     indexCounters
|       accesses = 2
|       resets = 0
|       misses = 0
|       hits = 2
|       missRatio = 0.0
|     asserts
|       user = 0
|       warning = 0
|       rollovers = 0
|       regular = 0
|       msg = 0
|     uptimeMillis = 3966128
|     extra_info
|       note = fields vary by platform
|       page_faults = 3
|       heap_usage_bytes = 62618928
|     globalLock
|       lockTime = 45045
|       activeClients
|         readers = 0
|         writers = 0
|         total = 0
|       totalTime = 3966128000
|       currentQueue
|         readers = 0
|         writers = 0
|         total = 0
|     cursors
|       note = deprecated, use server status metrics
|       timedOut = 0
|       pinned = 0
|       totalNoTimeout = 0
|       totalOpen = 0
|_      clientCursors_size = 0
41288/tcp open  memcached Memcached
55413/tcp open  ftp       vsftpd 3.0.3
MAC Address: 02:42:C0:23:19:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.93 seconds
</code></pre>

> Search for script function

```bash
// search for relevant nmap script
root@attackdefense:~# ls -al /usr/share/nmap/scripts/ | grep -e "mongodb"
-rw-r--r-- 1 root root  2578 Jan  9  2019 mongodb-brute.nse
-rw-r--r-- 1 root root  2583 Jan  9  2019 mongodb-databases.nse
-rw-r--r-- 1 root root  3663 Jan  9  2019 mongodb-info.nse

//info of mangodb-databases.nse
root@attackdefense:~# nmap --script-help=mongodb-databases
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 13:36 UTC

mongodb-databases
Categories: default discovery safe
https://nmap.org/nsedoc/scripts/mongodb-databases.html
  Attempts to get a list of tables from a MongoDB database.
  
//info of mangodb-info.nse
root@attackdefense:~# nmap --script-help=mongodb-info     
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 13:36 UTC

mongodb-info
Categories: default discovery safe
https://nmap.org/nsedoc/scripts/mongodb-info.html
  Attempts to get build info and server status from a MongoDB database.
```

> Apply nmap script

{% hint style="info" %}
No need to type nse when appy the script
{% endhint %}

```bash
root@attackdefense:~# nmap -sS -sV --script=mongodb-info -p- -T4 192.231.22.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-24 14:27 UTC
Nmap scan report for target-1 (192.231.22.3)
Host is up (0.0000090s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE   VERSION
6421/tcp  open  mongodb   MongoDB 2.6.10 2.6.10
| mongodb-info: 
|   MongoDB Build info
|     sysInfo = Linux lgw01-12 3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 BOOST_LIB_VERSION=1_58
|     loaderFlags = -fPIC -pthread -Wl,-z,now -rdynamic
|     compilerFlags = -Wnon-virtual-dtor -Woverloaded-virtual -fPIC -fno-strict-aliasing -ggdb -pthread -Wall -Wsign-compare -Wno-unused-function -Wno-unused-variable -Wno-maybe-uninitialized -Wno-unknown-pragmas -Winvalid-pch -pipe -Werror -O3 -Wno-unused-local-typedefs -Wno-unused-function -Wno-deprecated-declarations -fno-builtin-memcmp
|     allocator = tcmalloc
|     versionArray
|       3 = 0
|       2 = 10
|       1 = 6
|       0 = 2
|     version = 2.6.10
|     maxBsonObjectSize = 16777216
|     OpenSSLVersion = OpenSSL 1.0.2g  1 Mar 2016
|     debug = false
|     ok = 1.0
|     bits = 64
|     gitVersion = nogitversion
|     javascriptEngine = V8
```

```bash
// use wild card to run all script
root@attackdefense:~# nmap -sS -sV --script=ftp-* -p- -T4 192.231.22.3

// use multiple script
root@attackdefense:~# nmap -sS -sV --script=mongodb-info, ftp-anon -p- -T4 192.231.22.3
```



## Firewall Detection and IDS Evasion

> Default scan showing port `STATE` is `open`

```bash
root@attackdefense:~# nmap -sn 10.0.22.226
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-24 20:49 IST
Nmap scan report for 10.0.22.226
Host is up (0.0029s latency).
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds


root@attackdefense:~# nmap -sS -F 10.0.22.226
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-24 20:51 IST
Nmap scan report for 10.0.22.226
Host is up (0.0027s latency).
Not shown: 92 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.33 seconds
```

> Check specific port, `STATE-unfiltered` means no firewall is applied

```bash
root@attackdefense:~# nmap -sA -p445,3389 10.0.22.226
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-24 20:53 IST
Nmap scan report for 10.0.22.226
Host is up (0.0028s latency).

PORT     STATE      SERVICE
445/tcp  unfiltered microsoft-ds
3389/tcp unfiltered ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 0.25 seconds
```

> Fragmentation: break down packet

```bash
// without fragmentation
root@attackdefense:~# nmap -sS -sV -F 10.0.22.226
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-24 21:30 IST
Nmap scan report for 10.0.22.226
Host is up (0.0027s latency).
Not shown: 92 closed ports
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 65.45 seconds
```

<figure><img src="../../../../.gitbook/assets/image (62).png" alt=""><figcaption><p>Packet sending without fragmentation</p></figcaption></figure>

```bash
// With -f fragmentation is set

root@attackdefense:~# nmap -sS -sV -p80,445,3389 -f 10.0.22.226
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-24 21:36 IST
Nmap scan report for 10.0.22.226
Host is up (0.0028s latency).

PORT     STATE  SERVICE            VERSION
80/tcp   closed http
445/tcp  open   microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp open   ssl/ms-wbt-server?
Service Info: OS: Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 65.48 seconds

```

<figure><img src="../../../../.gitbook/assets/image (63).png" alt=""><figcaption><p>Packet sending with fragmentation</p></figcaption></figure>

```bash
// With minimum tramission unit (MTU)

root@attackdefense:~# nmap -sS -sV -p80,445,3389 --mtu 32 10.0.22.226
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-24 21:47 IST
Nmap scan report for 10.0.22.226
Host is up (0.0027s latency).

PORT     STATE  SERVICE            VERSION
80/tcp   closed http
445/tcp  open   microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp open   ssl/ms-wbt-server?
Service Info: OS: Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 65.45 seconds
```



> Spoof IP address of gateway

```bash
// check the local ethernet ip address to determine the network gateway

root@attackdefense:~# ifconfig

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.10.21.2  netmask 255.255.255.0  broadcast 10.10.21.255
        ether 02:42:0a:0a:15:02  txqueuelen 0  (Ethernet)
        RX packets 1192  bytes 168872 (164.9 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1463  bytes 170829 (166.8 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

// send packet with decoy IP of gateway to act like a normal gateway sending packet 

root@attackdefense:~# nmap -sS -sV -p445,3389 -f --data-length 200 -D 10.10.21.1,10.10.21.2 10.0.22.226
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-24 22:02 IST
Nmap scan report for 10.0.22.226
Host is up (0.0032s latency).

PORT     STATE SERVICE            VERSION
445/tcp  open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp open  ssl/ms-wbt-server?
Service Info: OS: Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 65.47 seconds
```

<figure><img src="../../../../.gitbook/assets/image (64).png" alt=""><figcaption><p>Sending request with decoy IP and random source</p></figcaption></figure>

```bash
// add -g <port number> to set specific source port of the packet sending

root@attackdefense:~# nmap -sS -sV -p445,3389 -f --data-length 200 -g 53 -D 10.10.21.1,10.10.21.2 10.0.22.226
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-24 22:09 IST
Nmap scan report for 10.0.22.226
Host is up (0.0027s latency).

PORT     STATE SERVICE            VERSION
445/tcp  open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp open  ssl/ms-wbt-server?
Service Info: OS: Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 65.44 seconds

```

<figure><img src="../../../../.gitbook/assets/image (65).png" alt=""><figcaption><p>Specified source port is set to send packet</p></figcaption></figure>

### Optimizing Nmap Scan

Slow down scan when dealing with intrusion detection by reducing amount of the packet&#x20;

1. Reduce amount of packet sending and make networkactivity looks less suspicious&#x20;
2. Dealing with networking utilizing very old network hardware

#### Nmap Time Template

> `--host-timeout <time limit, s,m,h>`: continue scanning others once timeout
>
> `--scan-delay <time delay, s,m,h>`

{% hint style="info" %}
Not recommended
{% endhint %}

```bash
// taking longer time for deault scanning

root@attackdefense:~# nmap -sS -sV -F 10.0.29.0/24
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 13:46 IST
Nmap scan report for 10.0.29.77
Host is up (0.0025s latency).
Not shown: 92 closed ports
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (1 host up) scanned in 78.75 seconds

// taking shorter time for adding --host-timeout <time taken>
root@attackdefense:~# nmap -sS -sV -F --host-timeout 5s 10.0.29.0/24
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 13:48 IST
Nmap scan report for 10.0.29.77
Host is up (0.0023s latency).
Skipping host 10.0.29.77 due to host timeout
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (1 host up) scanned in 19.76 seconds
```





## Nmap Output Formats



### Output Formats

> OUTPUT:&#x20;
>
> `-oN`: Output scan in normal,&#x20;
>
> `-oX`: Output scan in XML
>
> `-oS`: s|\<rIpt kIddi3
>
> `-oG`: Grepable format
>
> `-oA`: Output in the three major formats at once

### Output In Normal Format

```bash
root@attackdefense:~# nmap -Pn -sS -F -T4 10.0.23.85 -oN nmap_normal.txt
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 18:09 IST
Nmap scan report for 10.0.23.85
Host is up (0.0023s latency).
Not shown: 92 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.26 seconds
```

```bash
root@attackdefense:~# ls
Desktop  nmap_normal.txt  thinclient_drives

root@attackdefense:~# cat nmap_normal.txt 
# Nmap 7.91 scan initiated Tue Jun 25 18:09:50 2024 as: nmap -Pn -sS -F -T4 -oN nmap_normal.txt 10.0.23.85
Nmap scan report for 10.0.23.85
Host is up (0.0023s latency).
Not shown: 92 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

# Nmap done at Tue Jun 25 18:09:51 2024 -- 1 IP address (1 host up) scanned in 1.26 seconds
```



### Output in XML Format

```bash
root@attackdefense:~# nmap -Pn -sS -F -T4 10.0.23.85 -oN nmap_xml.xml   
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 18:19 IST
Nmap scan report for 10.0.23.85
Host is up (0.0024s latency).
Not shown: 92 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.27 seconds
```

```bash
root@attackdefense:~# ls
Desktop  nmap_normal.txt  nmap_xml.xml	thinclient_drives

root@attackdefense:~# cat nmap_xml.xml 
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE nmaprun>
<?xml-stylesheet href="file:///usr/bin/../share/nmap/nmap.xsl" type="text/xsl"?>
<!-- Nmap 7.91 scan initiated Tue Jun 25 19:05:32 2024 as: nmap -Pn -sS -F -T4 -oX nmap_xml.xml 10.0.20.239 -->
<nmaprun scanner="nmap" args="nmap -Pn -sS -F -T4 -oX nmap_xml.xml 10.0.20.239" start="1719322532" startstr="Tue Jun 25 19:05:32 2024" version="7.91" xmloutputversion="1.05">
<scaninfo type="syn" protocol="tcp" numservices="100" services="7,9,13,21-23,25-26,37,53,79-81,88,106,110-111,113,119,135,139,143-144,179,199,389,427,443-445,465,513-515,543-544,548,554,587,631,646,873,990,993,995,1025-1029,1110,1433,1720,1723,1755,1900,2000-2001,2049,2121,2717,3000,3128,3306,3389,3986,4899,5000,5009,5051,5060,5101,5190,5357,5432,5631,5666,5800,5900,6000-6001,6646,7070,8000,8008-8009,8080-8081,8443,8888,9100,9999-10000,32768,49152-49157"/>
<verbose level="0"/>
<debugging level="0"/>
<host starttime="1719322532" endtime="1719322533"><status state="up" reason="user-set" reason_ttl="0"/>
<address addr="10.0.20.239" addrtype="ipv4"/>
<hostnames>
</hostnames>
<ports><extraports state="closed" count="92">
<extrareasons reason="resets" count="92"/>
</extraports>
<port protocol="tcp" portid="135"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="msrpc" method="table" conf="3"/></port>
<port protocol="tcp" portid="139"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="netbios-ssn" method="table" conf="3"/></port>
<port protocol="tcp" portid="445"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="microsoft-ds" method="table" conf="3"/></port>
<port protocol="tcp" portid="3389"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="ms-wbt-server" method="table" conf="3"/></port>
<port protocol="tcp" portid="49152"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="unknown" method="table" conf="3"/></port>
<port protocol="tcp" portid="49153"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="unknown" method="table" conf="3"/></port>
<port protocol="tcp" portid="49154"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="unknown" method="table" conf="3"/></port>
<port protocol="tcp" portid="49155"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="unknown" method="table" conf="3"/></port>
</ports>
<times srtt="2139" rttvar="62" to="100000"/>
</host>
<runstats><finished time="1719322533" timestr="Tue Jun 25 19:05:33 2024" summary="Nmap done at Tue Jun 25 19:05:33 2024; 1 IP address (1 host up) scanned in 1.25 seconds" elapsed="1.25" exit="success"/><hosts up="1" down="0" total="1"/>
</runstats>
</nmaprun>
```

```bash
// run postgresql service    

root@attackdefense:~# service postgresql start
Starting PostgreSQL 13 database server: main.
```

<pre class="language-bash"><code class="lang-bash">// run metasploit

root@attackdefense:~# msfconsole

// creat workspace named pentest_1

msf6 > workspace
* default
msf6 > workspace -h
Usage:
    workspace                  List workspaces
    workspace -v               List workspaces verbosely
    workspace [name]           Switch workspace
    workspace -a [name] ...    Add workspace(s)
    workspace -d [name] ...    Delete workspace(s)
    workspace -D               Delete all workspaces
    workspace -r &#x3C;old> &#x3C;new>   Rename workspace
    workspace -h               Show this help information

msf6 > workspace -a pentest_1
[*] Added workspace: pentest_1
[*] Workspace: pentest_1

msf6 > workspace
  default
* pentest_1

// check database status

msf6 > db_status
[*] Connected to msf. Connection type: postgresql.
<strong>
</strong><strong>// import nmap_xmp.xml
</strong><strong>
</strong>msf6 > db_import nmap_xml.xml
[*] Importing 'Nmap XML' data
[*] Import: Parsing with 'Nokogiri v1.10.10'
[*] Importing host 10.0.20.239
[*] Successfully imported /root/nmap_xml.xml
</code></pre>

> Check imported host and services

<pre class="language-bash"><code class="lang-bash"><strong>msf6 > hosts
</strong>
Hosts
=====

address      mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------  ---------  -----  -------  ----  --------
10.0.20.239             Unknown                    device   

msf6 > services
Services
========

host         port   proto  name           state  info
----         ----   -----  ----           -----  ----
10.0.20.239  135    tcp    msrpc          open   
10.0.20.239  139    tcp    netbios-ssn    open   
10.0.20.239  445    tcp    microsoft-ds   open   
10.0.20.239  3389   tcp    ms-wbt-server  open   
10.0.20.239  49152  tcp    unknown        open   
10.0.20.239  49153  tcp    unknown        open   
10.0.20.239  49154  tcp    unknown        open   
10.0.20.239  49155  tcp    unknown        open   
</code></pre>

> Use metasploit to do port scanning by using `db_nmap`

```bash
msf6 > db_nmap -Pn -sS -sV -O -p445 10.0.20.239 
[*] Nmap: 'Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.'
[*] Nmap: Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 19:19 IST
[*] Nmap: Nmap scan report for 10.0.20.239
[*] Nmap: Host is up (0.0021s latency).
[*] Nmap: PORT    STATE SERVICE      VERSION
[*] Nmap: 445/tcp open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
[*] Nmap: Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
[*] Nmap: Aggressive OS guesses: Microsoft Windows 8.1 (97%), Microsoft Windows Server 2012 (96%), Microsoft Windows Server 2012 R2 (96%), Microsoft Windows Server 2012 R2 Update 1 (96%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1 Update 1 (96%), Microsoft Windows Vista SP1 (96%), Microsoft Windows Server 2012 or Server 2012 R2 (95%), Microsoft Windows Server 2008 SP2 Datacenter Version (94%), Microsoft Windows 7 or Windows Server 2008 R2 (94%), Microsoft Windows Server 2008 R2 (93%)
[*] Nmap: No exact OS matches for host (test conditions non-ideal).
[*] Nmap: Network Distance: 3 hops
[*] Nmap: Service Info: OS: Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
[*] Nmap: OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 9.38 seconds
```

> OS and version detected, and updated based on previous scan

```bash
msf6 > hosts

Hosts
=====

address      mac  name  os_name      os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------      ---------  -----  -------  ----  --------
10.0.20.239             Windows 8.1                    client    

msf6 > services
Services
========

host         port   proto  name           state  info
----         ----   -----  ----           -----  ----
10.0.20.239  135    tcp    msrpc          open   
10.0.20.239  139    tcp    netbios-ssn    open   
10.0.20.239  445    tcp    microsoft-ds   open   Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
10.0.20.239  3389   tcp    ms-wbt-server  open   
10.0.20.239  49152  tcp    unknown        open   
10.0.20.239  49153  tcp    unknown        open   
10.0.20.239  49154  tcp    unknown        open   
10.0.20.239  49155  tcp    unknown        open   

```

> Switch to default workspace instead of current pentest\_1 workspace and check all the information showing nothing

```bash
msf6 > workspace
  default
* pentest_1
msf6 > workspace default
[*] Workspace: default
msf6 > hosts

Hosts
=====

address  mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------  ---  ----  -------  ---------  -----  -------  ----  --------
```



### Output in Greppable Format

```bash
root@attackdefense:~# nmap -Pn -sS -F -T4 10.0.20.239 -oG nmap_grep.txt
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2024-06-25 19:30 IST
Nmap scan report for 10.0.20.239
Host is up (0.0021s latency).
Not shown: 92 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.25 seconds
```

```bash
root@attackdefense:~# ls
Desktop  nmap_grep.txt	nmap_xml.xml  thinclient_drives

root@attackdefense:~# cat nmap_grep.txt 
# Nmap 7.91 scan initiated Tue Jun 25 19:30:33 2024 as: nmap -Pn -sS -F -T4 -oG nmap_grep.txt 10.0.20.239
Host: 10.0.20.239 ()	Status: Up
Host: 10.0.20.239 ()	Ports: 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///, 49152/open/tcp//unknown///, 49153/open/tcp//unknown///, 49154/open/tcp//unknown///, 49155/open/tcp//unknown///	Ignored State: closed (92)
# Nmap done at Tue Jun 25 19:30:35 2024 -- 1 IP address (1 host up) scanned in 1.25 seconds
```



