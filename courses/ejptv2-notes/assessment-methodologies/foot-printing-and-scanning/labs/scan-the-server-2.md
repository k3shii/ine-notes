# Scan the Server 2

#### Overview:

> In this lab, you will use Nmap to identify ports used by Bind DNS, TFTP, and SNMP servers. No prior setup required. Clear instructions provided for scan and port identification.

#### Task:

> ## Task: Scan the Server 2
>
> ### Goal
>
> This lab covers the process of performing port scanning and service detection with Nmap.
>
> The objectives of this lab are to:
>
> 1. Identify the port running a Bind DNS server.
> 2. Identify the port running a TFTP server.
> 3. Identify the port running the SNMP server.
>
> ### Pre-requisites
>
> This lab does not have any pre-requisites.
>
> ### Requirements
>
> This lab does not require any configuration or setup.



#### Step 1:

> Identify target IP address

```bash
root@INE:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
231860: eth0@if231861: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:0a brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.10/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
231863: eth1@if231864: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:5b:7a:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.91.122.2/24 brd 192.91.122.255 scope global eth1
       valid_lft forever preferred_lft forever

```

> Check for target IP address

As the machine IP address given 192.91.122.2 we knew that 192.91.122.3 is the target machine IP address.

Use ping 192.91.122.3 to check is the IP address is alive or not.

<pre class="language-bash"><code class="lang-bash"><strong>// alternative way by doing host discovery within the subnet using nmap
</strong><strong>
</strong><strong>root@INE:~# nmap -sn 192.91.122.0/24
</strong>Starting Nmap 7.92 ( https://nmap.org ) at 2024-06-26 07:59 IST
Nmap scan report for linux (192.91.122.1)
Host is up (0.000054s latency).
MAC Address: 02:42:AC:38:02:0A (Unknown)
Nmap scan report for ya17ek6ep9piuw05e0t86.temp-network_a-91-122 (192.91.122.3)
Host is up (0.000018s latency).
MAC Address: 02:42:C0:5B:7A:03 (Unknown)
Nmap scan report for INE (192.91.122.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.05 seconds
</code></pre>



#### Step 2:

> Identify the port running a Bind DNS server.

```bash
root@INE:~# nmap -p177 -A 192.91.122.3
Starting Nmap 7.92 ( https://nmap.org ) at 2024-06-26 08:05 IST
Nmap scan report for ya17ek6ep9piuw05e0t86.temp-network_a-91-122 (192.91.122.3)
Host is up (0.000050s latency).

PORT    STATE SERVICE VERSION
177/tcp open  domain  ISC BIND 9.10.3-P4 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.10.3-P4-Ubuntu
MAC Address: 02:42:C0:5B:7A:03 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.05 ms ya17ek6ep9piuw05e0t86.temp-network_a-91-122 (192.91.122.3)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

```

> Alternative way by scanning all the ports

```bash
root@INE:~# nmap -sS -sV -p- -T4 192.91.122.3
Starting Nmap 7.92 ( https://nmap.org ) at 2024-06-26 08:02 IST
Nmap scan report for ya17ek6ep9piuw05e0t86.temp-network_a-91-122 (192.91.122.3)
Host is up (0.0000090s latency).
Not shown: 65534 closed tcp ports (reset)
PORT    STATE SERVICE VERSION
177/tcp open  domain  ISC BIND 9.10.3-P4 (Ubuntu Linux)
MAC Address: 02:42:C0:5B:7A:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 32.49 seconds

```



#### Step 3:

> Perform a UDP port scan on the port range 1-250, this can be done by running the following command.

As shown in the following screenshot, the Nmap scan reveals that the target system has 3 open UDP ports.

```bash
root@INE:~# nmap -sU -p1-250 192.91.122.3
Starting Nmap 7.92 ( https://nmap.org ) at 2024-06-26 08:10 IST
Nmap scan report for ya17ek6ep9piuw05e0t86.temp-network_a-91-122 (192.91.122.3)
Host is up (0.000086s latency).
Not shown: 247 closed udp ports (port-unreach)
PORT    STATE         SERVICE
134/udp open|filtered ingres-net
177/udp open|filtered xdmcp
234/udp open|filtered unknown
MAC Address: 02:42:C0:5B:7A:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 271.83 seconds
```

> Service detection with Nmap

```bash
root@INE:~# nmap -sUV -p1-250 192.91.122.3
Starting Nmap 7.92 ( https://nmap.org ) at 2024-06-26 08:21 IST
Nmap scan report for ya17ek6ep9piuw05e0t86.temp-network_a-91-122 (192.91.122.3)
Host is up (0.000077s latency).
Not shown: 247 closed udp ports (port-unreach)
PORT    STATE         SERVICE    VERSION
134/udp open|filtered ingres-net
177/udp open          domain     ISC BIND 9.10.3-P4 (Ubuntu Linux)
234/udp open          snmp       SNMPv1 server; net-snmp SNMPv3 server (public)
MAC Address: 02:42:C0:5B:7A:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 377.11 seconds
```

> The Nmap scan reveals the services running on ports 177 and 234, but not 134. We can perform an Nmap script scan to enumerate information from port 134 by running the following command:

As shown in the following screenshot, the Nmap script scan does not reveal any useful information regarding the service running on port 134.

```bash
root@INE:~# nmap -sUV --script=discovery -p134 192.91.122.3
Starting Nmap 7.92 ( https://nmap.org ) at 2024-06-26 08:30 IST
Pre-scan script results:
| targets-asn: 
|_  targets-asn.asn is a mandatory parameter
|_hostmap-robtex: *TEMPORARILY DISABLED* due to changes in Robtex's API. See https://www.robtex.com/api/
|_http-robtex-shared-ns: *TEMPORARILY DISABLED* due to changes in Robtex's API. See https://www.robtex.com/api/
| broadcast-igmp-discovery: 
|   192.91.122.1
|     Interface: eth1
|     Version: 2
|     Group: 224.0.0.106
|     Description: All-Snoopers (rfc4286)
|_  Use the newtargets script-arg to add the results as targets
Nmap scan report for ya17ek6ep9piuw05e0t86.temp-network_a-91-122 (192.91.122.3)
Host is up (0.000034s latency).

PORT    STATE         SERVICE    VERSION
134/udp open|filtered ingres-net
MAC Address: 02:42:C0:5B:7A:03 (Unknown)

Host script results:
|_asn-query: No Such Name
|_whois-domain: You should provide a domain name.
|_sniffer-detect: Likely in promiscuous mode (tests: "11111111")
|_fcrdns: PASS (ya17ek6ep9piuw05e0t86.temp-network_a-91-122)
| dns-brute: 
|_  DNS Brute-force hostnames: No results.
|_whois-ip: ERROR: Script execution failed (use -d to debug)
Bug in ip-geolocation-geoplugin: no string output.

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 137.31 seconds

```



#### Step 4:

> Assume that port 134 is tftp since we have discovered that UDP ports 177 and 234 are running a DNS and SNMP server respectively.

```
root@INE:~# tftp 192.91.122.3 134
tftp> status
Connected to 192.91.122.3.
Mode: netascii Verbose: off Tracing: off
Rexmt-interval: 5 seconds, Max-timeout: 25 seconds
tftp> 
```

We have successfully been able to identify the ports running the BIND DNS server, SNMP server and TFTP server.

