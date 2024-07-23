# 🔬HTTP

## Lab 2 <a href="#lab-2" id="lab-2"></a>

> 🔬 [Filtering Basics: HTTP](https://attackdefense.com/challengedetails?cid=2)
>
> * **Tshark** usage and filtering

### Set A&#x20;

1. Command to show only the HTTP traffic from a PCAP file?

```bash
tshark -r HTTP_traffic.pcap -Y 'http'
tshark -r HTTP_traffic.pcap -Y 'http' | more
```

<figure><img src="../../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

2. Command to show only the IP packets sent from IP address 192.168.252.128 to IP address 52.32.74.91?

<figure><img src="../../../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

3. Command to print only packets containing GET requests?

```bash
tshark -r HTTP_traffic.pcap -Y "http.request.method==GET"
```

<figure><img src="../../../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

4. Command to print only packets only source IP and URL for all GET request packets?

```bash
tshark -r HTTP_traffic.pcap -Y "http.request.method==GET" -Tfields -e frame.time -e ip.src -e http.request.full_uri
```

<figure><img src="../../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### Set B

1. How many HTTP packets contain the "password" string?

> Answer: `4`

```bash
tshark -r HTTP_traffic.pcap -Y 'http contains password'
```

<figure><img src="../../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

2. What is the destination IP address for GET requests sent for New York Times (www.nytimes.com)?

> Answer: `170.149.159.130`

```bash
tshark -r HTTP_traffic.pcap -Y "http.request.method==GET && http.host==www.nytimes.com" -Tfields -e ip.dst
```

<figure><img src="../../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

3. What is the session ID being used by 192.168.252.128 for Amazon India store (amazon.in)?

> Answer: 278-7381968-4337153

```bash
tshark -r HTTP_traffic.pcap -Y "ip contains amazon.in && ip.src=192.168.252.128" -Tfields -e ip.src -e http.cookie
```

<figure><img src="../../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

4. What type of OS the machine on IP address 192.168.252.128 is using (i.e. Windows/Linux/MacOS/Solaris/Unix/BSD)? Bonus: Can you also guess the distribution/flavor?

> The OS is `Linux x86_64; rv:31.0` - [user-agents.net](https://user-agents.net/string/mozilla-5-0-x11-linux-x86-64-rv-31-0-gecko-20100101-firefox-31-0-iceweasel-31-8-0)

```bash
tshark -r HTTP_traffic.pcap -Y "ip.src==192.168.252.128 && http" -Tfields -e http.user_agent
```

<figure><img src="../../../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>
