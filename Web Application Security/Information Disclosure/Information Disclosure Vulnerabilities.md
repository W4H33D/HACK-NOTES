# What is Information Disclosure

Information Disclosure is a type of vulnerability in which an application intentionally or unintentionally show some sensitive data in the request responses. The disclosed data can be any type, such as:

- User's data i.e. username, password, financial information, etc.
- Transmitting sensitive information over unsecured channels i.e. HTTP instead of HTTPS.
- Sensitive Business Data
- Technical details about backend infrastructure i.e. Server used and its version information, Content Management System(CMS) used and its version information, and other sensitive data like sensitive endpoint, etc.

Besides all that, this type of vulnerability may occur in other forms i.e. hard-coded secrets in comments disclosed debug logs, etc in which they disclosed some sensitive data.

The impact of information disclosure may vary on the type of information they are disclosing. If the application leaks some user's personally identifiable information(PII) then the impact is high but if they just disclose something banner or directory listing they are not that impact-full unless they show some sensitive information. These types of disclosure need to be fixed but they do not have much priority over the others.

## Information Disclosure Examples

- **Error messages**: Error messages that reveal too much information about the application or underlying system can provide attackers with valuable information. For example, an error message that displays a full SQL query can reveal information about the database schema and potentially allow for SQL injection attacks.
- **Source code disclosure**: Source code disclosure occurs when the application's source code is exposed to unauthorized parties. This can allow attackers to identify vulnerabilities in the application's code and potentially exploit them.
- **Misconfigured security settings**: Misconfigured security settings, such as incorrect file permissions or improperly configured access controls, can allow attackers to access sensitive files or functionality within the application.
- **Debug information**: Debug information, such as stack traces, can reveal sensitive information about the application's code and underlying system. This information can be useful to attackers looking to exploit vulnerabilities in the application.
- **Revealing Sensitive Directories**: Sensitive directories are those that contain files or information that could be used by an attacker to compromise the security of a web application or the server hosting it. They can be Admin panels or any other endpoints or files that contain sensitive information that is revealed.

## How do they Occur?

They can occur for different reasons but mostly they occur due to the following reasons.
- **Failure to remove internal content from public content**: For example, developer comments in markup are sometimes visible to users in the production environment.
- **Insecure configuration of the website and related technologies**: For example, failing to disable debugging and diagnostic features can sometimes provide attackers with useful tools to help them obtain sensitive information. Default configurations can also leave websites vulnerable, for example, by displaying overly verbose error messages.
- **Flawed design and application behavior**: For example, if a website returns distinct responses when different error states occur, this can also allow attackers to enumerate sensitive data, such as valid user credentials.
- **Default Configurations**: By default application doesn't set enough security settings. If the developer or user uses just the default setting in the production environment they may cause the information disclosure vulnerabilities.
