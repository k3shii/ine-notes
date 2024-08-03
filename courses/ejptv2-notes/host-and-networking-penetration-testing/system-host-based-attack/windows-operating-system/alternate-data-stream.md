# 🔬Alternate Data Stream

## Lab 1

> 🔬 Extra **Lab**
>
> I will use a _Windows 7 Virtual Machine_ for this demonstration

* Open a Command Prompt (`cmd`)

```bash
notepad text.txt
```

<figure><img src="../../../../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Data store within the data stream

<figure><img src="../../../../../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Check the Resource Stream (metadata) of the file

**What to do with this?**

* We can hide a malicious payload within the resource stream, and whenever execute/open the file, we can specify that we want to execute the malicious payload

<figure><img src="../../../../../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Delete the file by using `del`&#x20;

<figure><img src="../../../../../.gitbook/assets/image (18) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Hide the file within the `test.txt` with `secret.txt`

```bash
notepad test.txt:secret.txt
# secret.txt contains the Resource Stream
```

<figure><img src="../../../../../.gitbook/assets/image (19) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (20) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
The content is disappear when try to open the `test.txt` file and the file size is `0 byte`
{% endhint %}

* Add data to `test.txt` file

```bash
notepad test.txt
# normal Data Stream
```

<figure><img src="../../../../../.gitbook/assets/image (21) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* To open the hidden file

<figure><img src="../../../../../.gitbook/assets/image (22) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

`e.g.` - Download latest [winPEASx64\_ofs.exe](https://github.com/carlospolop/PEASS-ng/releases/tag/20230312) (_Antivirus may block the file_), rename it to `payload.exe` and move it to `C:\Temp` directory

* Using the `type` command, redirect the output into a new legitimate text file, passing in the actual executable into the text file resource stream.

```bash
type payload.exe > windows_log.txt:winpeas.exe
del payload.exe    
```

* `windows_log.txt` file can be filled with legitimate logs data to make it as realistic as possible
* Start the hidden executable

```bash
start windows_log.txt:winpeas.exe
```

<figure><img src="../../../../../.gitbook/assets/image (23) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Open an elevated `cmd` and create a symbolic link

```bash
cd C:\Windows\System32
mklink wupdate.exe C:\Temp\windows_log.txt:winpeas.exe
```

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-a31103c86d95e7673090782e49bbd51a1f22bea2%252Fimage-20230317184205318.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=92a592ce&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* Run `wupdate.exe` to launch the `winpeas.exe` payload from the Resource stream of the `windows_log.tx`t file

{% hint style="info" %}
Make sure that it is running in `administrator` to perform this action.
{% endhint %}

```bash
wupdate
```

> Same as above procedure can be done with a malicious `msfvenom` payload.



