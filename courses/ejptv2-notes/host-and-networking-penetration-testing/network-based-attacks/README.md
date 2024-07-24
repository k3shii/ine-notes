# Network-Based Attacks

🗒️ **Network based attacks** are attacks targeted towards specific network traffic and services.

* ARP
* DHCP
* SMB
* FTP
* Telnet
* SSH

🗒️ **MITM** (**M**an **I**n **T**he **M**iddle) is a type of cybersecurity attack that _allows the attacker to eavesdrop/listen on the legitimate communication between two targets_.

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-7ba3d3b075846b91ada30fc919c18356749c825d%252Fveracode-appsec_man-middle-attack.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=813490d5&#x26;sv=1" alt=""><figcaption><p>https://www.veracode.com/security/man-middle-attack</p></figcaption></figure>

`e.g.`

* [**ARP Poisoning**](https://medium.com/geekculture/understanding-arp-poisoning-mitm-attack-7b12a3b061bd) - intercept communication through broadcasting ARP packets and waiting for answers from other machines.
* **Promiscuous mode** - listen to all the traffic on a network

## Tools <a href="#tools" id="tools"></a>

### [Wireshark](https://www.kali.org/tools/wireshark/) <a href="#wireshark" id="wireshark"></a>

> 🔬 Check some `Wireshark` traffic sniffing in [this lab](https://blog.syselement.com/ine/courses/ejpt/exam-preparation-labs/p.t.-prerequisites-labs/http-s-traffic-sniffing)

* `e.g.` Capture a `nmap` scan traffic with **`Wireshark`**
  * Check the interface before beginning the capture
  * Protocol Hierarchy Statistics
  * Identify all layers looking at captured packets
  * Default time display setting is _relative time in seconds_
  * Follow > TCP Stream to check an entire stream

### [Tshark](https://tshark.dev/) <a href="#tshark" id="tshark"></a>

> [**tshark**](https://tshark.dev/) - a network protocol analyzer that captures packet data from a live network or from a saved capture file. It is the CLI component of `Wireshark`.
>
> * Native capture file format is `pcapng` (Wireshark compatible)



### arpspoof <a href="#arpspoof" id="arpspoof"></a>

> [**`arpspoof`**](https://www.kali.org/tools/dsniff/#arpspoof) - a (old) tool to send out unrequested (and possibly forged) `ARP` replies and intercept packets on a switched LAN

