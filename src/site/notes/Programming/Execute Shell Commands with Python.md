---
{"dg-publish":true,"permalink":"/programming/execute-shell-commands-with-python/"}
---


os.system to run a command.
os.system() runs a command, prints any output, and returns the exit code of the command.

The **subprocess** module is the recommended way to execute a shell command.
```python
import subprocess

list_files = subprocess.run(["ls", "-l"])
print("The exit code was: %d" % list_files.returncode)
```
Basically the command you type is put in as a list. Each item is what would be separated by a space.
```python
import subprocess

useless_cat_call = subprocess.run(["cat"], 
                                  stdout=subprocess.PIPE, 
                                  text=True, 
                                  input="Hello from the other side")

print(useless_cat_call.stdout)  # Hello from the other side
```
