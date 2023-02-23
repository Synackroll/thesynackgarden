---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/authentication/"}
---

The act of proving an assertion; the process of determining if an entity is who or what it claims to be.

The most widespread type of authentication is **login forms**.

**Authorization** is the process of approving or disapproving a request from a given (authenticated) entity.

Our goal is to verify if login forms are implemented securely ind if we can bypass them to gain unauhthorized access.

# Overview of Authentication Methods

During authentication phase the entity that wants to authenticate sends an identification string (e.g., an ID or username) along with some additional data, usually a password.

## Multi-Factor Authentication

Oten called #MFA or #2FA. This is when there are two factors involved in authentication.
* Something the user **knows** like a username or password
* Something the user **has** like a hardware token
* Something the user **is**, usually a biometric footprint.

When an entity is required to send data that belongs to more than one of these domains. Single factor authentication ususally requires somethig the user **knows** like **Username and Password**.

It's also possible for the requirement to be only something the user has.  (e.g., a corporate badge or a train ticket.)

## Form-based Authentication (#FBA)

The most common authentication on the web is Form-Based Authentication. The application presents an HTML form where the user inputs their username and password, access is granted after the received data is compared at the backend.

After a successful login the application server creates a session tied to a unique key that is usually stored in a cookie.

Some web apps require multiple steps for authentication. Like a username, password, and a third one-time password (OTP). A OTP usually lasts for a limited amount of time then changes.

Sometimes there are vulnerabilities in the implementation, like when there is an assumption for the third factor that the other two factors had already been performed properly.

## HTTP Based Authentication

HTTP-based login functionality allows the application server to specifiy different authentication procedure schemes suchas Basic, Digest and NTLM. All HTTP authentication schemes revolve around 401 status codes and the WWW-Authenticate response headers and are used by application servers to challenge a client request and provide authetncation details.

The Authorization header holds the authentication data and needs to be in every request for the user to be authenticated. This can be less secure than FBA because the authentication data must be in every request.

### HTTP Authentication Header

The authorization header specifies the HTTP authetnication method. In Basic Auth, the header contains a base 64 encoded string of the authentication credentials.

### Other Forms of Authentication

It is possible that authentication can be performed by IP address check. APIs sometimes require a specifica authentication form.