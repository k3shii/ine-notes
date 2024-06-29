# Windows Recon: SMB Discover and Mount

### Task

> Windows machine (Server 2012) is provided to you.
>
> Learn to use Nmap to scan the target machine and mount the SMB share of the target machine using the Windows File Explorer as well as using the command prompt.
>
> Objective: Discover SMB share and mount it
>
> The following username and password may be used to access the service:
>
> \| Username | Password | | administrator | smbserver\_771 |
>
> Instructions:
>
> * You can check the IP address of the machine by running "ipconfig" command on the command prompt i.e cmd.exe
> * Do not attack the gateway located at IP address 10.0.0.1



* Check for the IP address

<figure><img src="../../../../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

* Host discovery by scanning all the open port of the IP address within the /20 subnet

```
PS C:\Users\Administrator> nmap 10.0.26.0/20 --open
```

> Assume 10.0.18.177 as target because only it has an open port of 445 which is SMB port

<figure><img src="../../../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

* Open “Map Network Drive”

> Go to This PC → Network → Right Click on Network → Map Network Drive

<figure><img src="../../../../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

* Type target machine IP address `“\10.0.18.177”` in Folder: field and hit `Browse`

<figure><img src="../../../../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

* Select the target machine IP address and we would expect a network credential prompt.&#x20;

<figure><img src="../../../../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

* Enter target machine credentials which are provided
  * Username: administrator
  * Password: smbserver\_771

<figure><img src="../../../../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

* Click “Ok”  will received all the shared folders from the target machine.&#x20;
* Select any folder to create a network drive. In this case,  the “C:\” drive is selected of the target machine. Select the folder and click “Ok” → Finish

<figure><img src="../../../../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

* Successfully mounted the target machine shared folders.

<figure><img src="../../../../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

* Open Run and type cmd.exe to access the windows command prompt.

> Clear previous stored session

<figure><img src="../../../../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

* Mount the target folder.

```
net use Z: \\10.0.18.177\C$ smbserver_771 /user:administrator
```

<figure><img src="../../../../../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

* Again, visit This PC and, there is a new network shared drive i.e Z:\\

<figure><img src="../../../../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>



