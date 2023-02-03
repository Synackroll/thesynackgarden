---
{"dg-publish":true,"permalink":"/cybersecurity/structured-query-language-sql-injection-fundamentals/","tags":["SQL","SQLInjection","SQLi"]}
---


Web applications often have a database structure on the backend.  The front end interacts with the database in real time.  As HTTP(s) requests arrive from the user, the application back-end queries the database to build responses.

SQL Injection (SQLi) refers to attacks against relational [[Cybersecurity/Databases\|Databases]] such as MySQL.  SQL injection occurs when a mailcious user attempts to pass input that changes the final SQL queries directly against the database.

Some things that may be accessible:

* User names and passwords;
* Credit card info;
* etc.

SQLi is usually the result of poorly coded web apps or poorly secured back-end.

**Injection** occurs when user-input is inputted int a SQL query string without properly sanitizing or filtering the input.  To have a successful injection, we must ensure that the newly modified SQL query is still valid and does not have any syntax errors after our injection.

## Types of SQL Injections.

SQL Injections are categorized on how and where we retrieve their output.

**In-band**: when the output of the intended and new query are printed on the front end.  There are two types.  **Union Based and Error Based**.  **Union-based** we need to specify the exact column which we can read so the query will direct the output to be printed there.  In **Error based** we are able to get the PHP or SQL errors on the front end so we can intentionally cause an error that returns the output of our query.

**Blind**: When we do not get printed output of the injection.  Can be **boolean** and **time based**.  With **boolean** we structure SQL conditional statements to control whether the page returns any output at all.  For **time based** we use conditional statements taht delay the page response using the Sleep() function.

**Out-of-band** when we have no direct access to output whatsoever.

## [[Cybersecurity/Structured Query Language (SQL) Injection Fundamentals\|SQLi]] Discovery

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

Because of the [[Cybersecurity/MySQL\|MySQL]] operator precedences, we can convert this query to evaluate to true by doing an injection.  (If the query evaluates to true then the front end will log us in.)

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

We need to make sure taht the columns of both tables match.  This means fililng with junk data at times.  Typically, sequential numbers.
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