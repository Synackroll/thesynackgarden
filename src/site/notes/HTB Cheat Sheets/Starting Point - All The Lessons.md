---
{"dg-publish":true,"permalink":"/htb-cheat-sheets/starting-point-all-the-lessons/"}
---


This is a cheatsheet that goes over all the basic points on hacking that are covered by the starting point machines on [Hack the Box](https://hackthebox.com).

> Note: Sometimes I indicate a linux prompt with `$` 
> 

# Go slow, RTFM, and make sure you understand the command you're typing.

Go slow and take your time. Presumably you're interested in hackthebox because you want to **actually** learn how to hack computers. You won't do that by rushing through the basics. It won't feel like it, but you **will** ultimately save time overall and learn more if you take the time on the frontend to learn what the commands you enter mean. That means, unfortunately, reading the manual and the help commands. It means lots and lots of reading. 

A recurring--and perplexing--question asked by new hackers on reddit is: "Is it supposed to be this hard?" The answer is yes. It's supposed to be hard. In fact, it's supposed to be impossible. Remember: in an ideal world unauthorized users would not be able to access computers at all. So you are, in fact, learning to do something that is meant to be impossible.

The good news is that it is not actually impossible to hack a computer. Far from it. And even better, the information you need is readily available almost anywhere you look. 

# Test if your VPN is up:

Most of the hackthebox machines require that you be on the hackthebox network and connected via the [[Cybersecurity/Virtual Private Network\|Virtual Private Network]].  You can test this by using the command:

```
ifconfig tun0
```

The pwnboxes will helpfully show your vpn address in the prompt:
```
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.36]─[htb-username@htb-kuvfgtkkrf]─[~]
└──╼ [★]$ 
```

# Test your connection to the Target

It's good to generally test whether the host is up next. We can do that to by using the ping command, as most hosts will respond to [[Cybersecurity/Ping\|ping]] requests.

`ping <target IP Address>`

# Scan the Target

Use [Nmap](http://nmap.org) to scan for open ports and enumerate services that are running.

`nmap -sC -sV <IP Address of Target>`

|Option | Meaning|
|--|---|
| `-sC`| Run default safe scripts |
| `-sV` | Probe open ports to determine Service and Version |

You can have nmap output to files using the output to files using `-oa <basename>` or `-oN <filename>`

Carefully read the scan results. It will provide you services, the OS, information about the services, etc.

# Test for Easy Access and Default Creds

Credentials, or creds, will often be listed as `username:password` so a user with the name 'admin' and a password 'admin' would be recorded as: `admin:admin` in the hacker's notes.

Whenever you're asked for a username and password try (1) entering common username and password combinations (e.g, "admin:admin" and (2) looking for default username and password combinations for the service.

[Top usernames](https://github.com/danielmiessler/SecLists/blob/master/Usernames/top-usernames-shortlist.txt)
[Top Passwords](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/best15.txt)

If a [telnet](https://en.wikipedia.org/wiki/Telnet) port is available, for example, you might telnet to the machine and try default or common creds.

```
$ telnet <IP Address>
```

# Search All Available FileShares

Open and accessible file services should be thoroughly searched. Start by checking for anonymous or null logins (i.e., logins without passwords)

## File Transfer Protocol (FTP)

Nmap will generally show if anonymous ftp login is allowed:
```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
| ftp-syst: 
|   STAT: 
```

In that case you can connect with the command `ftp <IP Address>` and then enter "anonymous" as the username and anything for the password. Once you're logged in you can do `help` to help with your commands.

## SMB Shares

The Server Message Block (SMB) is a network file sharing protocol allows applications on a computer to read and write to files and to request services from server programs in a computer network. For windows the default port is port 445.

If we do not have credentials, we can try logging in anonymously by doing the command:
```
smbclient -L <target_ip>
```

For example `smbclient -L 10.10.14.4` Just hit enter for the password. We can do a null authentication by doing:
```
smbclient -L <target_ip> -U ''
```

Login to the share:
```
smbclient \\\\<tgt_ip>\\ShareName
```

Using crackmap exec:
```
crackmapexec smb <tgt_ip> --shares
crackmapexec smb <tgt_ip> --shares -u ''
crackmapexec smb <tgt_up> --shares -u 'SomeUser' -p ''
```


# Understand How to Get Information from Databases

One of the main functions for computers is storing and reetrieving information, this information is often organized and stored in databases. There are a number of different kinds of databases [relational databases](https://en.wikipedia.org/wiki/Relational_database) that organize data into tables. Understanding how to query these databases is important.

1. Tell the computer which database you want to use.
2. Figure out which tables have the information you're interested in. Often this is something like "Users"
3. Figure out which columns of the table you're interested in.  For "Users" that might be "username" and "password"
4. Select the columns you're interested. In a structured query language (SQL) that query might look something like "SELECT username, password FROM Users"

# Understand the concept of remote access






