# 🔬Windows Recon: Zenmap

#### Task:

> Windows machine (Server 2012) is provided to you.
>
> Your task is to discover the live host machines using the provided Zenmap tool. The subnet mask you need to focus on is "255.255.240.0" and CIDR 20.&#x20;
>
> [Zenmap](https://nmap.org/zenmap/)
>
> * A GUI version of the Nmap tool
> * Easy for beginners use
> * Ability to store scan data into the database.
> * Plot the network diagram based on the scan result i.e services, hostname, etc.
>
> Objective: Discover all available live hosts.
>
> Instructions:
>
> * You can check the IP address of the machine by running "ipconfig" command on the command prompt i.e cmd.exe
> * Do not attack the gateway located at IP address 10.0.0.1



Step 1:

> Check for windows IP address by using ipconfig

<figure><img src="../../../../../.gitbook/assets/image (36) (1).png" alt=""><figcaption></figcaption></figure>

Step 2:

> Run Zenmap scan against the subnet to discover the target machine’s IP address.

<figure><img src="../../../../../.gitbook/assets/image (38) (1).png" alt=""><figcaption></figcaption></figure>

We have discovered 12 hosts using Zenmap. We can switch the tab to “ Services ” and we can observe that when we select the “Services” tab, it filters the output of all host machines by open ports.

<figure><img src="../../../../../.gitbook/assets/image (39) (1).png" alt=""><figcaption></figcaption></figure>

> Zenmap has discovered port 80 on only one host i.e 10.0.28.197.
>
> We have also discovered that port `80` is running `HFS 2.3.`



Step 4:

> Select “ msrpc ” service and we can observe that two machines are exposed to RPC ports which is port `135`

<figure><img src="../../../../../.gitbook/assets/image (43) (1).png" alt=""><figcaption></figcaption></figure>

Step 5:

> We can also plot the alive hosts diagram using Topology → Fisheye

<figure><img src="../../../../../.gitbook/assets/image (41) (1).png" alt=""><figcaption></figcaption></figure>

In the plotted diagram we can see that the “Yellow” colored one is the machine i.e 10.0.25.204 where we have launched the scan (that is, the attacker machine) and it has plotted the other hosts connection with hostname and IP addresses to localhost.&#x20;

Color “Green” means the machine is accessible. Color “Red” means the machine is alive but not responding or not directly accessible.





