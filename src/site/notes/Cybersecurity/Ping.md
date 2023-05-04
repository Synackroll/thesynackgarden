---
{"dg-publish":true,"permalink":"/cybersecurity/ping/"}
---


Ping can be found on Linux and Windows hosts. "It uses the ICMP protocol's mandatory ECHO_REQUEST datagram to elicit an ICMP ECHO_RESPONSE from a host or gateway. ECHO_REQUEST datagrams (''pings'') have an IP and ICMP header, followed by a struct timeval and then an arbitrary number of ''pad'' bytes used to fill out the packet."

The ttl on the ping return can be used to identify machines, assuming the default values are set.

The default initial TTL value for **Linux/Unix** is **64**, and TTL value for **Windows** is **128**.