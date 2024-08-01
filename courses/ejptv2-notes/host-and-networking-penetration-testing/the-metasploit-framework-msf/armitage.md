# Armitage

## [Armitage](https://www.offsec.com/metasploit-unleashed/armitage/) - MSF GUI <a href="#armitage-msf-gui" id="armitage-msf-gui"></a>

🗒️ **Armitage** is a Java-based GUI front-end for the MSF.

* Automate port scanning, exploitation, post exploitation
* Visualize targets
* Requires MSFdb and services to be running
* Pre-packed with Kali Linux

> 🔬 **Port Scanning & Enumeration With Armitage** - lab by INE
>
> * Victim Machine 1: `10.0.30.169`
> * Victim Machine 2: `10.0.21.13`

```bash
service postgresql start && msfconsole -q
db_status
	[*] Connected to msf. Connection type: postgresql.

# Open a new tab and start Armitage
armitage
# Answer "YES" for the RPC server
```

<figure><img src="../../../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

* **Hosts - Add Hosts**
  * Add victim 1 IP
  * Set the lab as `Victim 1`
* Right-click the target and **Scan** it

<figure><img src="../../../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

* Check **Services**
* Perform an **Nmap Scan** from the **Hosts** menu

<figure><img src="../../../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

* Check **Services**

<figure><img src="../../../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

### Exploitation <a href="#exploitation" id="exploitation"></a>

* Search for `rejetto` and launch the exploit module

<figure><img src="../../../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

* Try **Dump Hashes** via the `registry method`

<figure><img src="../../../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

* Saved hashes can be found under the **View - Loot** menu

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:5c4d59391f656d5958dab124ffeabc20:::
```

* **Browse Files**

<figure><img src="../../../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

* **Show Processes**

<figure><img src="../../../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

### Pivoting <a href="#pivoting" id="pivoting"></a>

* Setup **Pivoting**

<figure><img src="../../../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

* Add, Enumerate and Exploit `Victim 2`

<figure><img src="../../../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

* Port forward the port `80` and use `nmap`

```
# In the Meterpreter tab
portfwd add -l 1234 -p 80 -r 10.0.21.13
```

```bash
# In the msf Console tab
db_nmap -sV -p 1234 localhost
```

<figure><img src="../../../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

* Remove the created localhost `127.0.0.1`
* Search for `BadBlue` and use the `badblue_passthru` exploit on `Victim 2`

<figure><img src="../../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

* Migrate to an `x64` from the **Processes** tab
* Dump hashes with the `lsass method`

### Armitage Kali Linux Install <a href="#armitage-kali-linux-install" id="armitage-kali-linux-install"></a>

```bash
sudo apt install armitage -y
```

```bash
sudo msfdb init
```

```bash
sudo nano /etc/postgresql/15/main/pg_hba.conf
# On line 87 switch “scram-sha-256” to “trust”
```

```bash
sudo systemctl enable postgresql
sudo systemctl restart postgresql
```

```bash
sudo armitage
```
