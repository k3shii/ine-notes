# Networking

## OSI Model

The OSI model serves as a guideline for developing and understanding network protocols and communication processes.

<table><thead><tr><th width="77">#</th><th width="147">OSI Layer</th><th width="340">Function</th><th>Example</th></tr></thead><tbody><tr><td>7</td><td>Application</td><td>Provides network services directly to end-users or applications.</td><td>HTTP, FTP, IRC, SSH, DNS</td></tr><tr><td>6</td><td>Presentation</td><td>Translates data between the application layer and lower layers. Responsible for data format translation, encryption, and compression to ensure that data is presented in a readable format.</td><td>SSL/TLS, JPEG, GIF, SSH, IMAP</td></tr><tr><td>5</td><td>Session</td><td>Manages sessions or connections between applications. Handles synchronization, dialog control, and token management. (Interhost communication)</td><td>APIs, NetBIOS, RPC</td></tr><tr><td>4</td><td>Transport</td><td>Ensures end-to-end communication and provides flow control.</td><td>TCP, UDP</td></tr><tr><td>3</td><td>Network</td><td>Responsible for logical addressing and routing.(Logical Addressing)</td><td>TCP, UDP</td></tr><tr><td>2</td><td>Data Link</td><td>Manages access to the physical medium and provides error detection. Responsible for framing, addressing, and error checking of data frames. (Physical addressing)</td><td>Ethernet, PPP, Switches etc</td></tr><tr><td>1</td><td>Physical</td><td>Deals with the physical connection between devices.</td><td>USB, Ethernet Cables, Coax, Fiber, Hubs etc</td></tr></tbody></table>



## Network Layer

### IP Header Format

The IP protocol defines many different fields in the packet header. These fields contain binary values that the IPv4 services reference as they forward packets across the network.

* IP Source Address - Packet Source
* IP Destination Address - Packet Destination
* Time-to-Live (TTL) - An 8-bit value that indicates the remaining life of the packet.
* Type-of-Service (ToS) - The Type-of-Service field contains an 8-bit binary value that is used to determine the priority of each packet.
* Protocol - This 8-bit value indicates the data payload type that the packet is carrying



### IPv4 Header Fields

<table><thead><tr><th width="288">Field </th><th>Purpose</th></tr></thead><tbody><tr><td>Version (4 bits)</td><td>Indicates the version of the IP protocol being used. For IPv4, the value is 4.</td></tr><tr><td>Header Length (4 bits)</td><td>Specifies the length of the IPv4 header in 32-bit words. The minimum value is 5, indicating a 20-byte header, and the maximum is 15, indicating a 60-byte header.</td></tr><tr><td>Type of Service (8 bits)</td><td>Specifies the length of the IPv4 header in 32-bit words. The minimum value is 5, indicating a 20-byte header, and the maximum is 15, indicating a 60-byte header.</td></tr><tr><td>Total Length (16 bits)</td><td>Represents the total size of the IP packet, including both the header and the payload (data). The maximum size is 65,535 bytes.</td></tr><tr><td>Identification (16 bits)</td><td>Used for reassembling fragmented packets. Each fragment of a packet is assigned the same identification value.</td></tr><tr><td>Flags (3 bits)</td><td>Includes three flags related to packet fragmentation: Reserved (bit 0): Always set to 0. Don't Fragment (DF, bit 1): If set to 1, indicates that the packet should not be fragmented. More Fragments (MF, bit 2): If set to 1, indicates that more fragments follow in a fragmented packet.</td></tr><tr><td>Time-to-Live (TTL, 8 bits)</td><td>Represents the maximum number of hops (routers) a packet can traverse before being discarded. It is decremented by one at each hop.</td></tr><tr><td>Protocol (8 bits)</td><td>Identifies the higher-layer protocol that will receive the packet after IP processing. Common values include 6 for TCP, 17 for UDP, and 1 for ICMP.</td></tr><tr><td>Source IP Address (32 bits)</td><td>Specifies the IPv4 address of the sender (source) of the packet.</td></tr><tr><td>Destination IP Address (32 bits)</td><td>Specifies the IPv4 address of the intended recipient (destination) of the packet.</td></tr></tbody></table>



## Transport Layer

<figure><img src="../../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

### TCP 3-Way Handshake

<table><thead><tr><th width="204">TCP Control Flags</th><th>Connection</th><th>Connection (Response)</th></tr></thead><tbody><tr><td>SYN (Set)</td><td>Initiates a connection request</td><td>Acknowledges the connection request</td></tr><tr><td>ACK (Cear)</td><td>No acknowledgment yet</td><td>Acknowledges the received data</td></tr><tr><td>FIN (Clear)</td><td>No termination request</td><td>No termination request</td></tr></tbody></table>



3-Way Handshake with Wireshark

<figure><img src="../../../.gitbook/assets/image (75).png" alt=""><figcaption><p>SYN</p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (77).png" alt=""><figcaption><p>SYN, ACK</p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (76).png" alt=""><figcaption><p>ACK</p></figcaption></figure>

Terminating a Connection:

* SYN (Clear): No connection request.
* ACK (Set): Acknowledges the received data.
* FIN (Set): Initiates connection termination.



### TCP Port Range

maximum port number: 65,535



#### Well Know Port (0-1023)

<table><thead><tr><th width="140">Ports</th><th>Services</th></tr></thead><tbody><tr><td>80</td><td>HTTP (Hypertext Transfer Protocol)</td></tr><tr><td>443</td><td>HTTPS (HTTP Secure)</td></tr><tr><td>21</td><td>FTP (File Transfer Protocol)</td></tr><tr><td>22</td><td>SSH (Secure Shell)</td></tr><tr><td>25</td><td>SMTP (Simple Mail Transfer Protocol)</td></tr><tr><td>110</td><td>POP3 (Post Office Protocol version 3)</td></tr></tbody></table>



#### Registered Port (1024-49151)

<table><thead><tr><th width="140">Ports</th><th>Services</th></tr></thead><tbody><tr><td>3389</td><td>Remote Desktop Protocol (RDP)</td></tr><tr><td>3306</td><td>MySQL Database</td></tr><tr><td>8080</td><td>HTTP alternative port</td></tr><tr><td>27017</td><td>MongoDB Database</td></tr></tbody></table>

