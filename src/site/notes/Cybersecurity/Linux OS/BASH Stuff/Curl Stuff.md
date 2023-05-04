---
{"dg-publish":true,"permalink":"/cybersecurity/linux-os/bash-stuff/curl-stuff/"}
---


curl has variables that can be displayed using the -w switch. You can get the size of a response by doing:

`curl -so /dev/null -w '%{size_download}' http://TGT:PORT/page`

This might be useful for fuzzing.
