---
{"dg-publish":true,"permalink":"/cybersecurity/linux-os/bash-stuff/upgrade-jail-shell/"}
---
clea---
dg-publish: true
---
# On the shell:
```
python -c import pty;pty.spawn("/bin/bash")
export TERM=xterm
```

# CTL-Z to back out then do:

```
parrot@parrot $ stty raw -echo; fg
```

