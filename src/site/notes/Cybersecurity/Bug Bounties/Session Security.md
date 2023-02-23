---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/session-security/"}
---

## Introduction to Sessions

HTTP is a stateless communication protocol. Each request response transaction is unrelated to other transactions such that every request carries all needed information for the server to act on it appropriately. The session state resides client side.

A **session** is the sequence of requests originating from the same client and the associated responses fora specific time period.

Because HTTP is statless, web apps utilize cookies, URL parameters, URL arguments (on GET requests), body arguments (on POST requests), and other proprietary solutions for session tracking and management.

### Session Identifier Security

A **session identifier** (Session ID) or token is how user sessions are generated and distinguished.

**[[Cybersecurity/Bug Bounties/Session Hijacking\|Session Hijacking]]** occurs when an attacker obtains a session identifier and imperonates the victim in the web application.

Session identifier security depends on:
* **Validity Scope** how many sessions it is good for.
* **Randomness** in the generation of the SSID.
* **Validity Time** how long is it good for.
* **Location** where it is stored.
	* **URL**: Bad. HTTP Referer header can leak session identifier to other web sites. Browser history may contain the identifier.
	* **HTML**: Session identifier can be identified in the browser's cache memory and intermediate proxies.
	* **sessionStorage**: A browser storage feature introduced in HTML5. sessionStorage can be retrieved as long as the tab or browser is open. Gets celared when the *page session* exists.
	* **localStorage**: LocalStorage was introduced in HTML5 also. These can be retreived as long as localStorage is not deleted by the user.

### Session Attacks
* **[[Cybersecurity/Bug Bounties/Session Hijacking\|Session Hijacking]]**: attacker obtains identifiers and uses them to [[Cybersecurity/Bug Bounties/Authentication\|authenticate]]  to the server and impersonate the victim.
* **[[Cybersecurity/Bug Bounties/Session Fixation\|Session Fixation]]**: When an attacker can fixate a valid sessio identifier. The attacker tricks the victim into logging into the application using the fixed identifier.
* **[[Cybersecurity/Bug Bounties/Cross-Site Scripting/Cross-Site Scripting (XSS)\|XSS]]**: with a focus on user sessions.
* [[Cybersecurity/Bug Bounties/CSRF\|Cross Site Request Forgery]]: An attack that forces a user to execute inadvertent actions on a web application in which they are currently authenticated.
* [[Open Redirects\|Open Redirects]]: Occurs when an attacker redirects a victim to an attacker-controlled site by abusing a legimate applications redirection functionality.

Adding multiple hosts to /etc/hosts with a script:
```shell-session
$ IP=ENTER SPAWNED TARGET IP HERE
$ printf "%s\t%s\n\n" "$IP" "xss.htb.net csrf.htb.net oredirect.htb.net"
```

Don't forget to cleanup etc hosts