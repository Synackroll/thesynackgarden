---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/open-redirect/"}
---

Occurs when an attacker can redirect a victm to an attacker controlled site by abusing a legimate application's redirection functionality.

Make sure you check for the following URL parameters when bug hunting, you'll often see them in login pages. Example: `/login.php?redirect=dashboard`

-   ?url=
-   ?link=
-   ?redirect=
-   ?redirecturl=
-   ?redirect_uri=
-   ?return=
-   ?return_to=
-   ?returnurl=
-   ?go=
-   ?goto=
-   ?exit=
-   ?exitpage=
-   ?fromurl=
-   ?fromuri=
-   ?redirect_to=
-   ?next=
-   ?newurl=
-   ?redir=