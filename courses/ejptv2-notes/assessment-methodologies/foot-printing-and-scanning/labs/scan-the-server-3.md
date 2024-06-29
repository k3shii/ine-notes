# Scan the Server 3

#### Overview

> Learn how to scan and detect open ports and services on a network using Nmap in this lab. It helps you understand the process of network scanning and reconnaissance.

#### Task:

> ## Task: Scan the Server 3
>
> ### Video: Scan the Server 3
>
> **Estimated time:** 20 minutes
>
> ### Goal
>
> This lab covers the process of performing port scanning and service detection with Nmap.
>
> ### Pre-requisites
>
> This lab does not have any pre-requisites.
>
> ### Requirements
>
> This lab does not require any configuration or setup.



#### Step 1:

> Identify the target IP address

```
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
206724: eth0@if206725: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:11 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.17/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
206727: eth1@if206728: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:cb:2c:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.203.44.2/24 brd 192.203.44.255 scope global eth1
       valid_lft forever preferred_lft forever
       
root@attackdefense:~# nmap -sn 192.203.44.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 03:22 UTC
Nmap scan report for 192.203.44.1
Host is up (0.000051s latency).
MAC Address: 02:42:5C:DB:E9:3E (Unknown)
Nmap scan report for target-1 (192.203.44.3)
Host is up (0.000013s latency).
MAC Address: 02:42:C0:CB:2C:03 (Unknown)
Nmap scan report for attackdefense.com (192.203.44.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 14.03 seconds
```

> To begin with, we can perform an Nmap port scan on the entire TCP port range (65,535 ports) to identify all the open ports on the target system. This can be done by running the following command:

As shown in the following screenshot, target system does not have any open TCP ports

```bash
root@attackdefense:~# nmap -p- -T4 192.203.44.3 
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-26 03:24 UTC
Nmap scan report for target-1 (192.203.44.3)
Host is up (0.0000090s latency).
All 65535 scanned ports on target-1 (192.203.44.3) are closed
MAC Address: 02:42:C0:CB:2C:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.12 seconds
```

Given that the target system does not have any TCP ports open, we can perform a UDP port scan do discover any open UDP ports on the target system.

> As shown in the following screenshot, the Nmap service detection scan confirms that an SNMP server is running on port 161, the scan also enumerates information from the SNMP server.

```bash
root@attackdefense:~# nmap -sU -T4 -A 192.203.44.3

Nmap scan report for target-1 (192.203.44.3)
Host is up (0.000053s latency).
Not shown: 952 closed ports, 47 open|filtered ports
PORT    STATE SERVICE VERSION
161/udp open  snmp    SNMPv1 server; net-snmp SNMPv3 server (public)
| snmp-info: 
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: d6b6fd101a897b6600000000
|   snmpEngineBoots: 1
|_  snmpEngineTime: 43m47s
| snmp-interfaces: 
|   lo
|     IP address: 127.0.0.1  Netmask: 255.0.0.0
|     Type: softwareLoopback  Speed: 10 Mbps
|     Traffic stats: 11.20 Kb sent, 11.20 Kb received
|   eth0
|     MAC address: 02:42:c0:cb:2c:03 (Unknown)
|     Type: ethernetCsmacd  Speed: 4 Gbps
|_    Traffic stats: 3.73 Mb sent, 4.09 Mb received
| snmp-netstat: 
|   TCP  127.0.0.1:22         0.0.0.0:0
|   TCP  127.0.0.1:22         127.0.0.1:56236
|   TCP  127.0.0.1:1313       0.0.0.0:0
|   TCP  127.0.0.1:56236      127.0.0.1:22
|   TCP  127.0.0.11:35335     0.0.0.0:0
|   UDP  0.0.0.0:161          *:*
|   UDP  0.0.0.0:40701        *:*
|_  UDP  127.0.0.11:36029     *:*
```







