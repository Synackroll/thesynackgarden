---
{"dg-publish":true,"permalink":"/cybersecurity/cross-site-scripting/"}
---


When a vulnerable web application does not properly sanitize user input, a malicious user can inject extra JavaScript code in an input field (e.g., comment/reply), so once another user views the same page, they unknowingly execute the malicious JavaScript code.

* XSS Attacks can facilitate anything that can be executed through browser with JavaScript Code.
* Generally cannot execute a system-wide exploit--but can if there isa  binary vulnerability in the web browser because it may then be possible to break out of the browser's sandbox.

Three main types:
* [[Cybersecurity/Stored (Persistent) XSS\|Stored (Persistent) XSS]] - The most critical; occurs when user input is stored on the back-end database and then displayed upon retrieval.  (e.g., a post or comments)
* [[Cybersecurity/Reflected (Non-persistent) XSS\|Reflected (Non-persistent) XSS]] - Occurs when input is displayed after being processed by the backend, but without being stored.  (e.g., a search result or error message.)
* [[Cybersecurity/DOM-based XSS\|DOM-based XSS]] Occurs when user input is directyl shown in the browser and is completely processed client-side without reaching the back-end server.
