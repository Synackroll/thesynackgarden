---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/edge-side-include-injection/"}
---

[Gosecure Explanation](https://www.gosecure.net/blog/2018/04/03/beyond-xss-edge-side-include-injection/)

HTTP Surrogates cannot distinguish between legitimate ESI tags and malicious ones. IF an attacker can successfully reflect ESI tags in the HTTP response, then the surrogatewill blindly parse and evaluate them.

