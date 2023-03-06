---
{"dg-publish":true,"permalink":"/cybersecurity/penetration-tester/smtp/"}
---

The `Simple Mail Transfer Protocol` (`SMTP`) is a protocol for sending emails in an IP network. It can be used between an email client and an outgoing mail server or between two SMTP servers. SMTP is often combined with the IMAP or POP3 protocols, which can fetch emails and send emails. In principle, it is a client-server-based protocol, although SMTP can be used between a client and a server and between two SMTP servers. In this case, a server effectively acts as a client.

Default port 25, newer servers use TCP port 587.

SMTP works unencrypted without further measures and transmits all commands, data, or authentication information in plain text. To prevent unauthorized reading of data, the SMTP is used in conjunction with SSL/TLS encryption. Under certain circumstances, a server uses a port other than the standard TCP port `25` for the encrypted connection, for example, TCP port `465`.

<tr>
<th>Client (<code>MUA</code>)</th>
<th><code>➞</code></th>
<th>Submission Agent (<code>MSA</code>)</th>
<th><code>➞</code></th>
<th>Open Relay (<code>MTA</code>)</th>
<th><code>➞</code></th>
<th>Mail Delivery Agent (<code>MDA</code>)</th>
<th><code>➞</code></th>
<th>Mailbox (<code>POP3</code>/<code>IMAP</code>)</th>
</tr>

Two disadvantags:
1. Sending an email using SMTP does not return a usable delivery confirmation.
2. Users are not authenticated when a connection is established. SMTP relays are often misused, as the originators can use arbitrary fake send addresses.

An extension of SMTP is Extended SMTP (ESMTP). ESMTP uses TLS