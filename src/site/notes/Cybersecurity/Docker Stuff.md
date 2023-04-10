---
{"dg-publish":true,"permalink":"/cybersecurity/docker-stuff/"}
---


Are we in a docker container:
- Running as root?
- `.dockerenv` in the fileystem root?
- Permissions shown as numbers vice names.
- User not in `/etc/passwd`
- Ifconfig shows local network.

Can do a simple ping sweep to determine if there are other hosts:
`for i in {1..254}; do (ping -c 1 172.19.0.${i} | grep "bytes from" | grep -v "Unreachable" &); done;`

Ping as a port scan :
`for port in {1..65535}; do echo > /dev/tcp/172.19.0.1/$port && echo "$port open"; done 2>/dev/null`


