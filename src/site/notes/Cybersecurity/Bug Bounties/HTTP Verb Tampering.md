---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/http-verb-tampering/"}
---


This type of attack exploits web servers that accept many HTTP verbs and methods. Malicious requests that use unexpected methods can lead to bypass of web applications auth mechanism or security controls against other web attacks.

Programmers mainly consider just the two GET and POST requests. But the web application and back-end web servers may accept other requests such as HEAD, PUT, etc.

There are 9 different verbs that can be accepted as HTTP methods by web servers. GET and POST, obviously.

| Verb | Description|
|---|---|
| HEAD | Identical to GET request but the response will contain only the headers without the response body |
| PUT | Writes the request payload to the specified location |
| DELETE | Deletes the resource at the specified location |
|OPTIONS | Shows different options accepted by a web server, like accepted HTTP verbs |
| PATCH | Apply partial modifications to the resource at the specified location |

Web servers must be securely configured to manage these methods, if not they can be used to gain control over the backend server. HTTP Verb Tampering attacks are often caused by misconfiguration of the back-end web server or the web application.


## Insecure Configurations
Insecure web server configurations may result from improperly configuring the web-server's methods and leaving other methods acceptable.

Code: xml
```xml
<Limit GET POST>
    Require valid-user
</Limit>
```
In this example, the configuration specifies limitats on GET and POST requests, but does not prevent other HTTP methods like HEAD.

## Insecure Coding
Similar to an insecure configuration but when the code to sanitize is applied only to the GET paramater.

Code: php
```php
$pattern = "/^[A-Za-z\s]+$/";

if(preg_match($pattern, $_GET["code"])) {
    $query = "Select * from ports where port_code like '%" . $_REQUEST["code"] . "%'";
    ...SNIP...
}
```
In this case, the query filter checks the GET parameters used but $\_REQUEST\["code"] parameter being used may accept POST requests.

## Bypassing Basic Authentication

Exploiting HTTP Verb Tampering vulnerabilities is relatively straightforward: try alternate HTTP methods to see how they are handled. Automated vulnerability scanners can find misconfigurations in the web server fairly easily but often miss ones caused by insecure coding.
]
### Identify

If we find a page that is restricted (i.e., returns 401 Unauthorized), try and see the scope of pages that are restricted (e.g., the whole directory or just a page?) then try and exploit.

### Exploit
* Identify the HTTP request method used by the web application.
* Intercept the request with a proxy and change "**Change Request Method**"
* Click forward and examine the page in the proxy browser.
* We can try an OPTIONS request and see what returns.

```shell-session
User@comp[/comp]$ curl -i -X OPTIONS http://SERVER_IP:PORT/

HTTP/1.1 200 OK
Date: 
Server: Apache/2.4.41 (Ubuntu)
Allow: POST,OPTIONS,HEAD,GET
Content-Length: 0
Content-Type: httpd/unix-directory
```

## Bypassing Security Filters

The more common type of HTTP Verb Tampering vulnerability is caused by Insecure Coding errors made during the development of the web application. Usually in poorly coded security filters.

Again, **Identify** and then **exploit.** If the original request is a post request and there are filters, try a get request with the data.


## Verb Tampering Prevention

### Insecure Cofinguration

Vulnerabilities in web-configuration usually occur when we limit a page's authorization to a particular set of HTTP verbs or methods. Apache server example:
```xml
<Directory "/var/www/html/admin">
    AuthType Basic
    AuthName "Admin Panel"
    AuthUserFile /etc/apache2/.htpasswd
    <Limit GET>
        Require valid-user
    </Limit>
</Directory>
```
By definition, the limit here applies only to GET requests. Same thing in a Tomcat server:
```xml
<security-constraint>
    <web-resource-collection>
        <url-pattern>/admin/*</url-pattern>
        <http-method>GET</http-method>
    </web-resource-collection>
    <auth-constraint>
        <role-name>admin</role-name>
    </auth-constraint>
</security-constraint>
```
These should be something like LimitExcept, http-method-omission and add/remove as these methods cover all verbs except the specified ones.

## Insecure Coding

Example of insecure code and why it's hard to find:
```php
if (isset($_REQUEST['filename'])) {
    if (!preg_match('/[^A-Za-z0-9. _-]/', $_POST['filename'])) {
        system("touch " . $_REQUEST['filename']);
    } else {
        echo "Malicious Request Denied!";
    }
}
```

The preg_match filter looks only in post parameters, but the actual system command uses the $\_REQUEST\['filename'] variable so when we send malicious input through a GET request it is not filtered.

These errors will be difficult to detect because the web page will run. Inconsistencies in the use of HTTP methods can lead to critical vulnerabilities. For the developer, they must be consistent in their use of HTTP methods. They should also expand the scope of testing in security filters to include testing against all parameters.



