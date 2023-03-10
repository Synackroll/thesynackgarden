---
{"dg-publish":true,"permalink":"/programming/bash-stuff/suid/"}
---

## user + s (pecial)

Commonly noted as **SUID**, the special permission for the user access level has a single function: A file with **SUID** always executes as the user who owns the file, regardless of the user passing the command. If the file owner doesn't have execute permissions, then use an uppercase **S** here.

Now, to see this in a practical light, let's look at the `/usr/bin/passwd` command. This command, by default, has the SUID permission set:

```shell
[tcarrigan@server ~]$ ls -l /usr/bin/passwd 
-rwsr-xr-x. 1 root root 33544 Dec 13  2019 /usr/bin/passwd
```

Note the **s** where **x** would usually indicate execute permissions for the user.
