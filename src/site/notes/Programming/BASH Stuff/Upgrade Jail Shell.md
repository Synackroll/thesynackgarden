---
{"dg-publish":true,"permalink":"/programming/bash-stuff/upgrade-jail-shell/"}
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

