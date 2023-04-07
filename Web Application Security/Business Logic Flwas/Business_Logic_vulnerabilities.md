
# Business logic flaws

Business logic flaws also known as Application flaws or logic flaws and vulnerabilities are the vulnerability that occurs due to poor logic in application designs. Developers often make bad assumptions about how users interact with the application and what type of data they can send, This poor assumption could lead to various problems that can be exploited to do what they didn't able to do.

Business logic flaws are difficult to find and they cannot be easily detected with vulnerability scanners because they have vulnerabilities in their logic or implementation. That's why these types of vulnerabilities are the best target for bug bounty hunters and pentester.

## How do they arise?

As I mentioned above they often arise because the design and development teams make flawed assumptions about how users will interact with the application. These bad assumptions can lead to an inadequate validation of user input. For example, if the developers assume that users will pass data exclusively via a web browser, the application may rely entirely on weak client-side controls to validate input. These are easily bypassed by an attacker using an intercepting proxy.

## Impact of Business logic flaws

The impact of business logic vulnerabilities can, at times, be fairly trivial. It is a broad category and the impact is highly variable. However, any unintended behavior can potentially lead to high-severity attacks if an attacker is able to manipulate the application in the right way. For this reason, quirky logic should ideally be fixed even if you can't work out how to exploit it yourself. There is always a risk that someone else will be able to.

Fundamentally, the impact of any logic flaw depends on what functionality it is related to. If the flaw is in the authentication mechanism, for example, this could have a serious impact on your overall security. Attackers could potentially exploit this for privilege escalation, or to bypass authentication entirely, gaining access to sensitive data and functionality. This also exposes an increased attack surface for other exploits.

Flawed logic in financial transactions can obviously lead to massive losses for the business through stolen funds, fraud, and so on.

You should also note that even though logic flaws may not allow an attacker to benefit directly, they could still allow a malicious party to damage the business in some way.

## Example of business logic flaws

Business logic flaws have different types and they occur for different reasons but some common names for business logic flaws are the following.

- **Authorization bypass**
	This occurs when the attacker is able to bypass the authorization process and access the functionality or data that should not be accessible to them. Let's say an e-commerce website that allows users to view their order history by entering their order number and email address. The website has implemented an authorization check to ensure that only the user who placed the order can view the order history. However, the website has a vulnerability that allows an attacker to bypass the authorization check and view the order history of any user without their email address, this is a type of authorization bypass.
- **Input validation bypass**
	This occurs when an attacker is able to manipulate the input data to bypass validation checks and perform actions that should not be allowed. Input validation bypass mostly occurs in places where they inform that dangerous characters are not sent to the server or reflected back to the application i.e They may implement to validate that no SQL injection characters are not added in any fields or they may implement no user-supplied input are stored or reflected back to the user to protect against XSS attacks. These are input validation and in which they may strip or change that input and if an attacker is able to bypass that and this could lead to input validation bypass.
- **Business process bypass**
	This occurs when an attacker is able to manipulate the flow of the application's business logic to skip or bypass the steps in a process, allowing them to perform unauthorized actions. If I name an example let's say the attacker is able to bypass the MFA process and get access to the application without going through the MFA process, and they do a transaction without giving an MFA code, etc, these are types of Business process bypass.
- **Inconsistent enforcement of business rules**
	This occurs when the application does not consistently enforce the business rules and policies, allowing attackers to bypass restrictions to perform actions that should not be allowed. Let's say a company implements a password policy that says the minimum length is eight characters and they also have upper case, lower case, special symbols, and numbers but an attacker is able to create an account that doesn't have the password that is compliant with company policy, this is a type of Inconsistent enforcement of business rules.
- **Time of check to time of use (TOCTTOU) race condition**
	This type of vulnerability occurs when there is a race condition between the time when a resource is checked (i.e., its status or permissions are evaluated) and the time when it is actually used. An attacker can exploit this vulnerability by modifying the resource between the time it was checked and the time it is used, resulting in unintended behavior.
	Let's understand it with an example consider a web application that allows users to transfer funds between accounts. The application implements an authorization check to ensure that the user has sufficient funds to make the transfer. However, the application has a vulnerability that allows an attacker to exploit a TOCTTOU race condition by sending multiple transfer request at the same time with the same account, The first request checks the available balance but the other request modify the balance before the first request completes.

There are some other examples of business logic flaws also i.e **Inadequate business process validation**, **Improper session management** and **Insecure direct object references (IDOR)** but I cannot explain everyone here so I encourage the reader to do its own research on the topic.
