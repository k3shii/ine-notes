# Information Gathering

## Passive Information Gathering

* `host <website>`: dns lookup
* robots.txt / sitemap\_index.xml: show hidden file
* mozilla extension
  * builtwith/Wappalyzer: check what web technology used
* `whatweb <website>`
* HTTrack: download website to local directory
* whois: show information domain name of the website
* [Netcraft.com](http://netcraft.com)
* `DNSRecon -d <website>`: dns recons, get records for domain
* [DNSdumspter.com](http://dnsdumspter.com)
* wafw00f: web application firewall foot printing tool
  * `wafw00f <website>`
  * `wafw00f <website> -a: find all`
* sublist3r: subdomain enumeration
  * `sublist3r -d <website> -e google,yahoo`
* Google Dorking: google search filter
  * site:?.com
  * inurl:forum, auth\_user\_file.txt, passwd.txt
  * site:\*.ine.com - subdomain
  * intitle:index of
  * filetype:pdf,xlsx,docs,zip - subdomain with filetype
  * cache:ine.com - changes made by website
* Google Hacking Database: google dorking cheatsheet
* WayBackMachine
* theHarvester: email harvesting
* haveibeenpwned: [https://haveibeenpwned.com/](https://haveibeenpwned.com/)



* Extra knowledge
  * Custom local domain

```bash
sudo nano /etc/hosts

Configuration file:
localhost's IP <domain name>
```



## Active Information Gathering

![](https://prod-files-secure.s3.us-west-2.amazonaws.com/aff30b53-39ed-4d40-878b-ef9a93eec70e/c82c3dca-684f-48ac-ba83-6d0a0f9c1097/Untitled.png)

<figure><img src="../../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

### DNS Reconnaissance

1. **dnsenum**: automatically does zone transfer and brute-forcing after finding NS through passive recon
2. **dig**: we have to give the NS to do a zone transfer.(_Recommended by Alexis_)
3. **fierce**: zone transfer + brute-forcing

```bash
dnsenum <DomainName>
dig axrf @<ServerName> <Domainname>
```

### Host Discovery

* `ip a s`: Identify IP address
* netdiscover:
  * `sudo netdiscover -i eth0 -r <ip address>`

### Nmap Port Scanning

* **-`Pn`:** disable ping probe/block by windows machine
* **`-p-`:** scan entire port
  * **`-p 80,445,339`:** scan specific port
  * **`-p1-100`0:** scan specific range of port
* **`-F`:** fast scan
* **`-sU`:** perform UDP port scan
* **`-v`:** verbose of what happening in background
* **`-sV`:** service version detection
* **`-O`:** operating system detection
* **`-sC`:** run nmap script
* **-`A`:** aggressive scan / alternate: -sV -O -sC
* **`-T0-5`:** paranoid | sneaky | polit | normal | aggressive | insane
* **`-oN file.txt`:** save file with output the same way as user input in the terminal
* **`-oX file.xml`:** save file in xml format for user to import into metasploit

