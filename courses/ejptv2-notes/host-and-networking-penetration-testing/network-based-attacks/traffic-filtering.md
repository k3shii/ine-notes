# 🔬Traffic Filtering

> 🔬 [Filtering Advanced: WiFi](https://attackdefense.com/challengedetails?cid=4)
>
> * **Tshark** usage and filtering

## Set A

1. Which command can be used to show only WiFi traffic?

```bash
tshark -r WiFi_traffic.pcap -Y "wlan"
```

<figure><img src="../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. Which command can be used only view the deauthentication packets?

```bash
tshark -r WiFi_traffic.pcap -Y "wlan.fc.type_subtype==0x000c"
```

<figure><img src="../../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

3. Which command can be used to only display WPA handshake packets?

```bash
tshark -r WiFi_traffic.pcap -Y "eapol"
```

<figure><img src="../../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

4. Which command can be used to only print the SSID and BSSID values for all beacon frames?

```bash
tshark -r WiFi_traffic.pcap -Y "wlan.fc.type_subtype==8" -Tfields -e wlan.ssid -e wlan.bssid
```

<figure><img src="../../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

## Set B

1. What is BSSID of SSID “LazyArtists”?

```bash
tshark -r WiFi_traffic.pcap -Y "wlan.ssid==LazyArtists" -Tfields -e wlan.bssid
```

<figure><img src="../../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. SSID “Home\_Network” is operating on which channel?

```bash
tshark -r WiFi_traffic.pcap -Y "wlan.ssid==Home_Network" -Tfields -e wlan_radio.channel
```

<figure><img src="../../../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

3. Which two devices received the deauth messages? State the MAC addresses of both.

```bash
tshark -r WiFi_traffic.pcap -Y "wlan.fc.type_subtype==0x000c" -Tfields -e wlan.ra
```

<figure><img src="../../../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

4. Which device does MAC 5c:51:88:31:a0:3b belongs to? Mention manufacturer and model number of the device.

```bash
tshark -r WiFi_traffic.pcap -Y "wlan.ta==5c:51:88:31:a0:3b && http" -Tfields -e http.user_agent
```

<figure><img src="../../../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
