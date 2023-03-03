---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/introduction-to-server-side-attacks/","tags":["SSRF"]}
---


Server side attacks target the application or service provided by a server. The objective is to leak sensitive data or inject unwarranted input into the application and possibly achieve remote code execution.

## AJP Proxy

AJP is a wire protocol. It an optimized version of the HTTP protocol to allow a standalone web server such as [Apache](https://httpd.apache.org/) to talk to Tomcat. Historically, Apache has been much faster than Tomcat at serving static content. The idea is to let Apache serve the static content when possible, but proxy the request to Tomcat for Tomcat related content.

AJP proxy port is 8009 TCP.

## [[Cybersecurity/Bug Bounty Hunter/Server Side Request Forgery\|Server Side Request Forgery]]
