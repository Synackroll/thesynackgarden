---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/command-injections/"}
---

# Intro to Command Injections

## What are injections?

Injection occurs when user-controlled input is misinterpreted as part of the web query or code being executed.

Some examples:
* OS Command Injection
* Code Injection
* [[Cybersecurity/Bug Bounty Hunter/SQL Injection/Structured Query Language (SQL) Injection Fundamentals\|SQL Injection]]
* [[Cybersecurity/Bug Bounty Hunter/Cross-Site Scripting/Cross-Site Scripting (XSS)\|Cross-Site Scripting (XSS)]]/HTML Injection
* LDAP Injection
* Etc.

## OS Command Injection
When user nput we control must directly or indirectly go into or affect a web query that executes system commands.

### PHP Example

PHP may use the exec, system, shell_exec, passthru, or popen functions to execute commands directly on the back-end server.  An example:
```php
<?php
if (isset($_GET['filename'])) {
    system("touch /tmp/" . $_GET['filename'] . ".pdf");
}
?>
```

### NodeJS Example

```javascript
app.get("/createfile", function(req, res){
    child_process.exec(`touch /tmp/${req.query.filename}.txt`);
})
```

## Detection

Generally the same as exploiting other injection vulnerabilities.  Look for areas where users can enter data and see if they are vulnerable.  Some operators for determining OS Command Injections:

|**Injection Operator**|**Injection Character**|**URL-Encoded Character**|**Executed Command**|
|-----|------|-------|-------|
|Semicolon|`;`|`%3b`|Both|
|New Line|`\n`|`%0a`|Both|
|Background|`&`|`%26`|Both (second output generally shown first)|
|Pipe| \| |`%7c`|Both (only second output is shown)|
|AND|`&&`|`%26%26`|Both (only if first succeeds)|
|OR|\|\||%7c%7c|Second (only if first fails)|
|Sub-Shell| \`\` |`%60%60`|Both (Linux-only)|
|Sub-Shell|`$()`|`%24%28%29`|Both (Linux-only)|

## Injecting Commands

Sometimes the front-end will be validating the potential payload, so you'll need to use a proxy such as BurpSuite to intercept and change the request.

It is common for developers to perform input validation on the front-end only.

## Identifying Filters

Generally if an error message is displayed a different page with information like our IP and our request, this may indicate that it was denied by a [[Cybersecurity/Bug Bounty Hunter/Web Application Firewall (WAF)\|Web Application Firewall (WAF)]].

## Blacklisted Characters

```php
$blacklist = ['&', '|', ';', ...SNIP...];
foreach ($blacklist as $character) {
    if (strpos($_POST['ip'], $character) !== false) {
        echo "Invalid input";
    }
}
```

The Web Application may have a list of blacklisted characters like the one above.  If any character matches the blacklist, the request is denied.

### Bypassing Space Filters

The new-line character is often not blacklisted because it is often needed in the payload. But a space is often blacklisted.

### Using Tabs

Tabs (%09) will often work because Linux and Windows accept commands withtabs between arguments.

### $IFS

$IFS is a Linux environmental variable that has the default value of a space and a tab.  This will often work to bypass space filters as well.

### Brace Expansion

**Bash Brace Expansion** will autmatically add spaces between arguments wrapped bewteen Braces. (eg., "{ls,-la}" will execute "ls -la"

### Bypassing Other Blacklisted Characters

#### Linux
Slash (/) and backslash (\) are commonly blacklisted. There is no set environmental variable for them, but we can slice the $PATH environmental variable to get a backslash.
```bash
User@computer $ echo ${PATH}

/usr/local/bin:/usr/bin:/bin:/usr/games

User@computer $ echo ${PATH:0:1}

/
```

Same for the semi-colon:
```shell-session
User@computer $ echo ${LS_COLORS:10:1}

;
```

The "printenv" command in Linux will show all the environmental variables that might be available for 

#### Windows

Slicing environmental variables to get special characters works in Windows also. Slice by specifying the starting position from the left and then a negative ending position starting from the right.  For example: %HOMEPATH%-> \\Users\\Student then:
```shell
C:\> echo %HOMEPATH:~6,-11%\
```

#### Character Shifting

In Linux:
```shell-session
echo $(tr '!-}' '"-~'<<<[)
```
Will shift the last charracter character one ascii number to the right.  So, put the character that is one to the left in the ascii table in the final position.
## Blacklisted Commands

### Commands Blacklist

Commands will sometimes be blacklisted as well.  A PHP command filter might look like:
```php
$blacklist = ['whoami', 'cat', ...SNIP...];
foreach ($blacklist as $word) {
    if (strpos('$_POST['ip']', $word) !== false) {
        echo "Invalid input";
    }
}
```

### Linux & Windows

To evade blacklisted commands add characters that are ignored in command shells like bash or powershell.

Some of these are the single or double quotes. ( '  or ")  You must remember that you can't mix quote types and the number of quotes must be even.

### Linux Only

You can use the \ and the postitional parameter character $@ in Linux.  The number of characters does not need to be even.

### Windows Only

You can use the ^ character.
## Advanced Command Obfuscation

### Case Manipulation

We can try things like WhOamI to bypass a filter.  This will often work because the blacklist won't check for variations since Linux systems are case-sensitive.  For windows powershell, we can just send the obfuscated command.

But for Linux we need to do more like use a command that will convert the obfuscated string to lowercase.

```shell-session
21y4d@htb[/htb]$ $(tr "[A-Z]" "[a-z]"<<<"WhOaMi")

21y4d
```
or:

```
$(a="WhOaMi";printf %s "${a,,}")
```

### Reversed Commands

imwho instead of whoami.  Then reverse it in the request.

```bash
User@Computer $ echo 'whoami' | rev
imaohw
```

Once we have the string we use:
$(rev<<<'imaohw') in the request to evade the filter.

### Encoded Commands

We can create unique commands for each case.  We do this by using encoding tools like base64 (for base 64 encoding) or xxd (for hex encoding).

First we encode our command:
```shell
user@computer$ echo -n 'cat /etc/passwd | grep 33' | base64

Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==
```
then to execute our command:
```
user@computer$ bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)
```
We need to convert that to a payload for injection at the appropriate place by taking out the spaces.

## Evasion Tools

**Bashfuscator** can help us create obfuscated command payloads for bash.

Dosfuscator can help us create obfuscate command payloads for windwos.




## Command Injection Prevention
### System Commands
Avoid using function that execute system commands.  Especially if there is user input being used with them.  But even if not, we should avoid system functions if possible.

### Input Validation

Input validation should be done on the front and backend.

### Input Sanitization

Input santization, the removal of an non-necessary special characters ofr user input, is critical  IT should alays be performaed after input validation.

### Server Configuration

* Use a built-in WAF in addition to an external WAF.
* Abide by the principle of least privilege by running the web server as a low privileged user.
* Prevetn certain functions from being executed by the web server(e.g. in OPHP disable_functions=system.)
* Limit the scope accessible by the web application to its folders.
* Reject double-encoded requests and non-ASCII chracterrs in URLs.
* Avoid the use of sensitive libraries and modules.

