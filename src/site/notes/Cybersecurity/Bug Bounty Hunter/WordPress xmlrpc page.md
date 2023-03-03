---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/word-press-xmlrpc-page/","tags":["WordPress, xmlrpc"]}
---

[[XML-RPC\|XML-RPC]] is a specification that enables communication between wordpress and other systems. IT standardizes communications using HTTP as the transport mecahnism and [[Cybersecurity/Bug Bounty Hunter/XML\|XML]] as the encoding mechanism.

XML-RPC predates WordPress, the code for the system is in the `xmlrpc.php` file located in the root directory of a wordpress site and its enabled by default.

WordPres uses the REST API now, so xmlrpc.php should be disabled. Possible vulnearbilites:
- DDos Attacks via XML-RPC Pingbacks
- Brute Force Attacks via XML-RPC. Each time xmlrpc.php makes a request, it sends the username and password for authentication. REST API doesn't do this. REST API uses OAuth tokens.


