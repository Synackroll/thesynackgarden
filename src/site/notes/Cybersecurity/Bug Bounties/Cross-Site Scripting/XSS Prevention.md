---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/cross-site-scripting/xss-prevention/","tags":["XSS"]}
---

Methods to prevent [[Cybersecurity/Bug Bounties/Cross-Site Scripting/Cross-Site Scripting (XSS)\|Cross-Site Scripting (XSS)]] attacks.

The most important part for XSS prevention is input sanitization on the front and backend.

On the front end, We can do this via *input validation* and *input sanitization*.

Validation is, for example, using regex to confirm that the email input field is in the proper format.  Sanitization might occur by using DOMPurify to escape any special characters with a backslash.

On the backend, there are various sanitization tools but, generally, direct user input should never be directly displayed on the page.  

Output encoding is also useful.  Output encoding will change the specialcharacterss to their html codes so that the input can be displayed without risk of css.

A good [[Cybersecurity/Bug Bounties/Web Application Firewall (WAF)\|web application firewall]] will automatically detect any type of injection going through HTTP requests andautomaticallyl reject such requests.  Some frameworks havebuilt-inn XSS protection (ASP.NET)
