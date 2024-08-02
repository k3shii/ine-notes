# 🔬Traffic Analysis

> 🔬 [WiFi Security: Traffic Analysis I](https://attackdefense.com/challengedetails?cid=1141)
>
> * **WiFi** basic traffic analysis with `Wireshark`

1. What is the name of the Open (No Security) SSID present in the packet dump?

> **Answer:** `SecurityTube_Open`

* Find the name of the Open SSID in the packet dump, using the filter
  * Beacon frame = `0x0008`
  * Wlan tag `48` = [**RSN-IE**](https://mrncciew.com/2014/08/21/cwsp-rsn-information-elements/) (Robust Security Network Information Element)

```bash
(wlan.fc.type_subtype == 0x0008) && (!(wlan.wfa.ie.wpa.version == 1)) && !(wlan.tag.number == 48)
```

<figure><img src="../../../../.gitbook/assets/image (25) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. The SSID 'Home\_Network' is operating on which channel?

> `Answer:` `6`

```bash
wlan contains Home_Network
```

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

3. Which security mechanism is configured for SSID 'LazyArtists'? Your options are: OPEN, WPA-PSK, WPA2-PSK.

> **Answer:** `WPA2-PSK`

```bash
wlan contains LazyArtists
```

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

4. Is WiFi Protected Setup (WPS) enabled on SSID 'Amazon Wood'? State Yes or No.

> **Answer:** `Yes`

```
(wlan.ssid contains "Amazon") && (wlan.fc.type_subtype == 0x0008)
```

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

5. What is the total count of packets which were either transmitted or received by the device with MAC e8:de:27:16:87:18?

> **Answer:** `5701`

* Count the packets of the device with MAC `e8:de:27:16:87:18`
  * wlan.ta = transmitted
  * wlan.ra = received

```bash
(wlan.ta == ec:de:27:16:87:18) || (wlan.ra == ec:de:27:16:87:18)
```

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

6. What is the MAC address of the station which exchanged data packets with SSID 'SecurityTube\_Open'?

> **Answer:** `5c:51:88:31:a0:3b`

```bash
(wlan.bssid== e8:de:27:16:87:18) && (wlan.fc.type_subtype == 0x0020)
```

<figure><img src="../../../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

7. From the last question, we know that a station was connected to SSID 'SecurityTube\_Open'. Provide TSF timestamp of the association response sent from the access point to this station.

> **Answer:** 115152625

```bash
((wlan.bssid == e8:de:27:16:87:18) && (wlan.addr==5c:51:88:31:a0:3b)) &&
(wlan.fc.type_subtype == 0x0001)
```

<figure><img src="../../../../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

