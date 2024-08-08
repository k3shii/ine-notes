# 🔬Tshark

## Lab 1 <a href="#lab-1" id="lab-1"></a>

> 🔬 [Getting Started: Tshark](https://attackdefense.com/challengedetails?cid=1)
>
> * **Tshark** usage

### Set A

1. Which version of Tshark is installed in the lab?

```bash
tshark -v
```

```bash
TShark (Wireshark) 2.6.1 (Git v2.6.1 packaged as 2.6.1-0ubuntu2~16.04.0)
```

<figure><img src="../../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. Find all Tshark supported network interfaces for monitoring

```bash
tshark -D
```

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

3. What is the Tshark command to sniff on eth0? Why did this command fail?

```bash
tshark -i eth0
```

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Set B

1. Tshark supports PCAP files. The lab environment has a sample file: HTTP\_traffic.pcap. How can you read this file in Tshark and display the packet list on the console?

```bash
tshark -r HTTP_traffic.pcap
```

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. How can you find the total number of packets in HTTP\_traffic.pcap?

* Read the pcap file with Tshark and pipe the output to wc (word count) tools.

```bash
tshark -r HTTP_traffic.pcap | wc -l
```

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

3. Tshark command to read the first 100 packets only from HTTP\_traffic.pcap?

```bash
tshark -r HTTP_traffic.pcap -c 100
```

<figure><img src="../../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

4. Print the list of protocols in HTTP\_traffic.pcap

```bash
tshark -r HTTP_traffic.pcap -z io,phs -q
```

<figure><img src="../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



