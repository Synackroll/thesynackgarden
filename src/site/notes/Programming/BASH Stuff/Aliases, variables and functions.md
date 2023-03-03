---
{"dg-publish":true,"permalink":"/programming/bash-stuff/aliases-variables-and-functions/"}
---


Aliases can be set by doing:

alias cmd='definition'

They should be replacements for long commands. If you want to have a command where you insert something (like for using an LFI) you should use a function with the variable portion of the command stored in a variable.

something like:
```
my_function(){
	echo This is an example of using a $1 variable as an argument.
}
```

Or this works:

`my() { curl -X GET "http://blog.inlanefreight.local/wp-content/themes/twentyseventeen/404.php?cmd="$1; }`


|cmd| Result |
|---|---|
|`declare -F` | List all your functions in the shell|
|`declare -f [function name]` | Show the actual function |



