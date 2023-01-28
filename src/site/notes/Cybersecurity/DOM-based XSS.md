---
{"dg-publish":true,"permalink":"/cybersecurity/dom-based-xss/"}
---

A type of [[Cybersecurity/Cross-Site Scripting\|Cross-Site Scripting]] that is completely processed on the client-side through [[Programming/JavaScript\|JavaScript]].

DOM-based XSS occurs when the **sink** function--the function that writes user input to the DOM object on a page--does not properly sanitzie user input taken in via the **source** function.

Common JavaScript functions used to write to the DOM object:
* document.write()
* DOM.innerHTML
* DOM.outerHTML

Some JQuery library functions that write to DOM objects are:
* add()
* after()
* append()

DOM attacks move the code to the innerHTML function that does not allow the use of \<script> tags as a security feature.  But we can use other payloads.

```html
	<img src="" onerror=alert(window.origin)>
```
