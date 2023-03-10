---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/sql-injection/my-sql/","tags":["MySQL"]}
---


`MySQL` is an open-source SQL relational database management system developed and supported by Oracle. A database is simply a structured collection of data organized for easy use and retrieval. The database system can quickly process large amounts of data with high performance. Within the database, data storage is done in a manner to take up as little space as possible. The database is controlled using the [SQL database language](https://www.w3schools.com/sql/sql_intro.asp). MySQL works according to the `client-server principle` and consists of a MySQL server and one or more MySQL clients. The MySQL server is the actual database management system. It takes care of data storage and distribution. The data is stored in tables with different columns, rows, and data types. These databases are often stored in a single file with the file extension `.sql`, for example, like `wordpress.sql`.

# Command Line

mysql utility is used to authenticate and interact with the database.

You must login to the database.  If we do not specifiy, mysql will default to the local host.  We can specify a remote server using the -h and a port with -P.

```
user@parrot$ mysql -u root -h docker.hackthebox.eu -P 3306 -p
```


## Creating a database:
```shell-session
mysql> CREATE DATABASE users;

Query OK, 1 row affected (0.02 sec)
```


## Show databases that exist:

```shell-session
mysql> SHOW DATABASES;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| users              |
+--------------------+

mysql> USE users;

Database changed
```

Once connected to a database you can use "SHOW tables" to get the list of the tables in the database.

## SQL Statements

**Insert** - used to add new records to a given table.
```sql
INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);
```

**Select** - Used to retrieve data.
```sql
SELECT * FROM table_name;

mysql> SELECT * from logins;

```

**Drop** - used to remove tables and databases from the server.  This will permanently and completely delete the table with no confirmation, so it should be used with caution.

**Alter** - used to change the name of any table and any of its fields, or to delete or add a new column to an existing table.  e.g.,
```shell-session
mysql> ALTER TABLE logins RENAME COLUMN newColumn TO oldColumn;

Query OK, 0 rows affected (0.01 sec)

mysql> ALTER TABLE loginis MODIFY oldcolumn DATE;

mysql> ALTER TABLE logins DROP oldColumn;
```

**Update** - used to update specific records in a table.

## Sorting Results

**ORDER BY** can be used to sort the results of a query by the column selected.  Default is ascending, but you can use **ASC** or **DESC**.

**LIMIT** to limit the count. 

**WHERE** followed by a \<condition\> to meet.

**LIKE** will allow you to find records meeting a certain pattern.
```sql
mysql> SELECT * FROM logins WHERE username like 'admin%';
```

## Conditions
```mysql
SELECT _column1_, _column2, ..._  
FROM _table_name_  
WHERE _condition1_ AND _condition2_ AND _condition3 ..._;
```

Operators:  AND, OR  and NOT.  These can also be represented as &&, ||, and !

## Common Operators and their precedence:
-   Division (`/`), Multiplication (`*`), and Modulus (`%`)
-   Addition (`+`) and subtraction (`-`)
-   Comparison (`=`, `>`, `<`, `<=`, `>=`, `!=`, `LIKE`)
-   NOT (`!`)
-   AND (`&&`)
-   OR (`||`)
Operators at the top are evaluated before the ones at the bottom of the list.
