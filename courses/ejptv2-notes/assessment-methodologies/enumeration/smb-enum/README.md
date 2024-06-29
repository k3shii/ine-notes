# 🔬SMB Enum

**`SMB`** (**S**erver **M**essage **B**lock) - a network file and resource sharing protocol, based on a _client-server_ model.

There are many variants of the SMB protocol like SMBv1, CIFS, SMBv2, SMBv2.1, SMBv3, and so on.

* `e.g.` Windows mapping and sharing drives as letter, uses SMB

Usually SMB can be found on ports **`139`** or **`445`** and `nmap` service and scripts enumeration (`-sV`, `-sC`) can find more info about the O.S. version.

After finding SMB through port scanning, gather more information with `nmap`.



## Lab

* [`Lab 1 - Windows Recon: SMB Discover and Mount`](windows-recon-smb-discover-and-mount.md)
* [`Lab 2 - Windows Recon: SMB Nmap Scripts`](windows-recon-smb-nmap-scripts.md)
* [`Lab 3 - Windows Recon: SMBMap`](windows-recon-smbmap.md)
* [`Lab 4: Samba Recon: Basics`](samba-recon-basics.md)
* [`Lab 5: Samba Recon: Basics II`](samba-recon-basics-ii.md)
* [`Lab 6: Samba Recon: Basics III`](samba-recon-basics-iii.md)
* [`Lab 7: Samba Recon: Dictionary Attack`](samba-recon-dictionary-attack.md)

