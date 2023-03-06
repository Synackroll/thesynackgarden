---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/jwt-confusion-attack/"}
---


# Algorithm Confusion Attack

Algorithm confusion attacks (also known as key confusion attacks) occur when an attacker is able to force the server to verify the signature of a JSON web token ([JWT](https://portswigger.net/web-security/jwt)) using a different algorithm than is intended by the website's developers. If this case isn't handled properly, this may enable attackers to forge valid JWTs containing arbitrary values without needing to know the server's secret signing key.

[Portswigger's writeup is very good.](https://portswigger.net/web-security/jwt/algorithm-confusion)

JWT's can be forged for this attack using [JWT Tool](https://github.com/ticarpi/jwt_tool).



