---
{"dg-publish":true,"permalink":"/programming/bash-stuff/history/"}
---


Use ctl-r to interactively search history.
Use ctl-j while seraching interactively to send the command you found to the current input.
Use enter to execute the command you found.

The history command stores the last 500 commands.

`$history | grep [whatever you're looking for]`

Will find you the line number of the command you're interested in.

In the shell, you can then do:

```bash
$ ![## of the command you found]
```
