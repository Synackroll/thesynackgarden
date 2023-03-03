---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/csrf/","tags":["CSRF"]}
---


## Cross Site Request Forgery

Websites employ tokens that are available only if the user has actually visited and used the page.
Cross-Site Request Forgery (CSRF or XSRF) is an attack that forces an end-user to execute inadvertent actions on a web application in which they are currently authenticated. This attack is usually mounted with the help of attacker-crafted web pages that the victim must visit or interact with, leveraging the lack of anti-CSRF security mechanisms. These web pages contain malicious requests that essentially inherit the identity and privileges of the victim to perform an undesired function on the victim's behalf. CSRF attacks generally target functions that cause a state change on the server but can also be used to access sensitive data.



