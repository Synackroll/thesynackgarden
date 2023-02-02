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
