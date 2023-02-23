---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/session-fixation/"}
---

This occurs when the attacker can fixate a valid session identifier then trick the victim into logging into the application using the aforementioned session identifier. The attacker then proceeds tot he a [[Cybersecurity/Bug Bounties/Session Hijacking\|session hijacking]] attack.

**Stage 1** Attacker manages to obtain a valid session identifier.
Many web sites will assign a valid session identifier without having to authenticate.

**Stage 2: Attacker manges to fixate a valid session identifier.**
Sesion fixation vulnerability occurs when the assigned session identifier pre-login remains the same post-login and session identifiers are being accepted from URL query strings or POST data and propagaed to the application.

**Stage 3: Attcker tricks the victim into establishing a session using the abovementioned session identifier.**
The attacker crafts a URL and lures the victim into visiting it. If the victim does, the web application will then assign the session identifier to the victim.

## Example:
We are presented with a page.  The image is broken, but notice that the token parameter is the same as the PHPSESSID cookie value. If any value or a valid session identifier specified in the token parameter of the URL is propagated tot he PHPSESSID cookie's value then we are probably dealing with a session fixation vulnerability.


