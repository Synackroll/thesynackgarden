---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/file-inclusion/"}
---

Modern web pages are build dynamically using parameters which dictate what is shown on the page. If the functions to grab and display the local files are not securely coded, then the parameters may be used to access unintended local files.

## [[Cybersecurity/Bug Bounties/Local File Inclusion\|Local File Inclusion]]
These bugs are usually found within templating engines. Static parts of the website (e.g, `header`, `navigation bar`, and `footer`) are shown and the other parts of the page are dynimcally loaded by paramater. We might see `/index.php?page=about`. in these cases, the index.php sets the static content and the parameters pull the dynamic content from a page called `about.php`.

### Examples of Code
Popular web frameworks (`PHP, NodeJS, Java, .Net`) all have different approaches for including local files but they all use the same mechanism: loading files from a specified path.

If we can control the path of the laoded file, then we can exploit this to read other files and potentially RCE. In these examples we look at `?language` as a get parameter where the language selected might change the directory from, for example, `/en` to `/es`.

#### PHP
```php
if (isset($_GET['language'])) {
    include($_GET['language']);
}
```
The language parameter is passed directly to the `include()` function. Any path loaded to it will be loaded to the page. Other functions in PHP that do the same thing are `include_once(), require(), require_once(), file_get_contents()`.

#### NodeJS
```javascript
if(req.query.language) {
    fs.readFile(path.join(__dirname, req.query.language), function (err, data) {
        res.write(data);
    });
}
```
This reads the file and then writes the data to the file content in the HTTP response. The language parameter might also be used in a render request like so:
```js
app.get("/about/:language", function(req, res) {
    res.render(`/${req.params.language}/about.html`);
});
```

#### Java
```jsp
<c:if test="${not empty param.language}">
    <jsp:include file="<%= request.getParameter('language') %>" />
</c:if>
```
The include function in Java takes a file or a page URL as its argument and then renders the object into the front-end template

The `import` function may also be used to render a local file or a url:
```jsp
<c:import url= "<%= request.getParameter('language') %>"/>
```

#### .NET
Uses the `Response.WriteFile` function which works similar to the above examples.
```cs
@if (!string.IsNullOrEmpty(HttpContext.Request.Query['language'])) {
    <% Response.WriteFile("<% HttpContext.Request.Query['language'] %>"); %> 
}
```
`@Html.Partial()` may also be used to render the file as part of the front-end template.
```cs
@Html.Partial(HttpContext.Request.Query['language'])
```
And the `include` function may be used to render local files or remote URLs, and may also execute the specified file as well.
```cs
<!--#include file="<% HttpContext.Request.Query['language'] %>"-->
```

### Read vs Execute

The tricky part is some functions read content and some content also execute content. Some allow specifying remot URLS and some don't.

| Function | Read Content | Execute | Remote URL |
|----|----|----|-----|
|PHP| | | | 
|include()/include_once()| 	✅| 	✅| 	✅|
|require()/require_once()| 	✅| 	✅| 	❌|
|file_get_contents() 	|✅ 	|❌ 	|✅|
|fopen()/file() |	✅| 	❌| 	❌|
|NodeJS||
|fs.readFile() |	✅ |	❌| 	❌|
|fs.sendFile() 	|✅ 	|❌ 	|❌|
|res.render() 	|✅ 	|✅ 	|❌|
|Java 		|	|
|include 	|✅ |	❌ 	|❌|
|import| 	✅ 	|✅ |	✅|
|.NET 			||
|@Html.Partial() |	✅ 	|❌ 	|❌|
|@Html.RemotePartial() 	|✅ 	|❌ |	✅|
|Response.WriteFile() |	✅ 	|❌ 	|❌|
|include |	✅ |	✅| 	✅|
