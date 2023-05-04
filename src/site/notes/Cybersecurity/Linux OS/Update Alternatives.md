---
{"dg-publish":true,"permalink":"/cybersecurity/linux-os/update-alternatives/"}
---


Generally used to choose which of several versions of a package you might use.

```
# java -version

openjdk version "1.8.0_51"
OpenJDK Runtime Environment (build 1.8.0_51-b16)
OpenJDK 64-Bit Server VM (build 25.51-b03, mixed mode)
```


```
# update-alternatives --config java

There are 3 programs which provide 'java'.

Selection Command
-----------------------------------------------
  1 /usr/lib/jvm/jre-1.6.0-openjdk.x86_64/bin/java
+ 2 /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.51-1.b16.el6_7.x86_64/jre/bin/java      <<<<<<< + indicate present version used by server. * indicate auto version used.
* 3 /usr/lib/jvm/jre-1.7.0-openjdk.x86_64/bin/java

Enter to keep the current selection[+], or type selection number: 3               <<<<<< Enter required selection number. For jre-1.7 provide 3
```


```
# java -version
java version "1.7.0_85"
OpenJDK Runtime Environment (rhel-2.6.1.3.0.1.el6_7-x86_64 u85-b01)
OpenJDK 64-Bit Server VM (build 24.85-b03, mixed mode)
```