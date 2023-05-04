---
{"dg-publish":true,"permalink":"/cybersecurity/linux-os/introduction-to-linux-fundamentals/"}
---


# Linux Structure

## Philosophy

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Principle</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>Everything is a file</code></td>
<td>All configuration files for the various services running on the Linux operating system are stored in one or more text files.</td>
</tr>
<tr>
<td><code>Small, single-purpose programs</code></td>
<td>Linux offers many different tools that we will work with, which can be combined to work together.</td>
</tr>
<tr>
<td><code>Ability to chain programs together to perform complex tasks</code></td>
<td>The integration and combination of different tools enable us to carry out many large and complex tasks, such as processing or filtering specific data results.</td>
</tr>
<tr>
<td><code>Avoid captive user interfaces</code></td>
<td>Linux is designed to work mainly with the shell (or terminal), which gives the user greater control over the operating system.</td>
</tr>
<tr>
<td><code>Configuration data stored in a text file</code></td>
<td>An example of such a file is the <code>/etc/passwd</code> file, which stores all users registered on the system.</td>
</tr>
</tbody>
</table>

## Components

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Component</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>Bootloader</code></td>
<td>A piece of code that runs to guide the booting process to start the operating system. Parrot Linux uses the GRUB Bootloader.</td>
</tr>
<tr>
<td><code>OS Kernel</code></td>
<td>The kernel is the main component of an operating system. It manages the resources for system's I/O devices at the hardware level.</td>
</tr>
<tr>
<td><code>Daemons</code></td>
<td>Background services are called "daemons" in Linux. Their purpose is to ensure that key functions such as scheduling, printing, and multimedia are working correctly. These small programs load after we booted or log into the computer.</td>
</tr>
<tr>
<td><code>OS Shell</code></td>
<td>The operating system shell or the command language interpreter (also known as the command line) is the interface between the OS and the user. This interface allows the user to tell the OS what to do. The most commonly used shells are Bash, Tcsh/Csh, Ksh, Zsh, and Fish.</td>
</tr>
<tr>
<td><code>Graphics server</code></td>
<td>This provides a graphical sub-system (server) called "X" or "X-server" that allows graphical programs to run locally or remotely on the X-windowing system.</td>
</tr>
<tr>
<td><code>Window Manager</code></td>
<td>Also known as a graphical user interface (GUI). There are many options, including GNOME, KDE, MATE, Unity, and Cinnamon. A desktop environment usually has several applications, including file and web browsers. These allow the user to access and manage the essential and frequently accessed features and services of an operating system.</td>
</tr>
<tr>
<td><code>Utilities</code></td>
<td>Applications or utilities are programs that perform particular functions for the user or another program.</td>
</tr>
</tbody>
</table>

## Linux Architecture

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Layer</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>Hardware</code></td>
<td>Peripheral devices such as the system's RAM, hard drive, CPU, and others.</td>
</tr>
<tr>
<td><code>Kernel</code></td>
<td>The core of the Linux operating system whose function is to virtualize and control common computer hardware resources like CPU, allocated memory, accessed data, and others. The kernel gives each process its own virtual resources and prevents/mitigates conflicts between different processes.</td>
</tr>
<tr>
<td><code>Shell</code></td>
<td>A command-line interface (<strong>CLI</strong>), also known as a shell that a user can enter commands into to execute the kernel's functions.</td>
</tr>
<tr>
<td><code>System Utility</code></td>
<td>Makes available to the user all of the operating system's functionality.</td>
</tr>
</tbody>
</table>

## File System Hierarchy

The Linux operating system is structured in a tree-like hierarchy and is documented in the [Filesystem Hierarchy](http://www.pathname.com/fhs/) Standard (FHS). Linux is structured with the following standard top-level directories:

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Path</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>/</code></td>
<td>The top-level directory is the root filesystem and contains all of the files required to boot the operating system before other filesystems are mounted as well as the files required to boot the other filesystems. After boot, all of the other filesystems are mounted at standard mount points as subdirectories of the root.</td>
</tr>
<tr>
<td><code>/bin</code></td>
<td>Contains essential command binaries.</td>
</tr>
<tr>
<td><code>/boot</code></td>
<td>Consists of the static bootloader, kernel executable, and files required to boot the Linux OS.</td>
</tr>
<tr>
<td><code>/dev</code></td>
<td>Contains device files to facilitate access to every hardware device attached to the system.</td>
</tr>
<tr>
<td><code>/etc</code></td>
<td>Local system configuration files. Configuration files for installed applications may be saved here as well.</td>
</tr>
<tr>
<td><code>/home</code></td>
<td>Each user on the system has a subdirectory here for storage.</td>
</tr>
<tr>
<td><code>/lib</code></td>
<td>Shared library files that are required for system boot.</td>
</tr>
<tr>
<td><code>/media</code></td>
<td>External removable media devices such as USB drives are mounted here.</td>
</tr>
<tr>
<td><code>/mnt</code></td>
<td>Temporary mount point for regular filesystems.</td>
</tr>
<tr>
<td><code>/opt</code></td>
<td>Optional files such as third-party tools can be saved here.</td>
</tr>
<tr>
<td><code>/root</code></td>
<td>The home directory for the root user.</td>
</tr>
<tr>
<td><code>/sbin</code></td>
<td>This directory contains executables used for system administration (binary system files).</td>
</tr>
<tr>
<td><code>/tmp</code></td>
<td>The operating system and many programs use this directory to store temporary files. This directory is generally cleared upon system boot and may be deleted at other times without any warning.</td>
</tr>
<tr>
<td><code>/usr</code></td>
<td>Contains executables, libraries, man files, etc.</td>
</tr>
<tr>
<td><code>/var</code></td>
<td>This directory contains variable data files such as log files, email in-boxes, web application related files, cron files, and more.</td>
</tr>
</tbody>
</table>


## Getting help

`man` command
`apropos` command

Also use https://explainshell.com

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Command</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>whoami</code></td>
<td>Displays current username.</td>
</tr>
<tr>
<td><code>id</code></td>
<td>Returns users identity</td>
</tr>
<tr>
<td><code>hostname</code></td>
<td>Sets or prints the name of current host system.</td>
</tr>
<tr>
<td><code>uname</code></td>
<td>Prints basic information about the operating system name and system hardware.</td>
</tr>
<tr>
<td><code>pwd</code></td>
<td>Returns working directory name.</td>
</tr>
<tr>
<td><code>ifconfig</code></td>
<td>The ifconfig utility is used to assign or to view an address to a network interface and/or configure network interface parameters.</td>
</tr>
<tr>
<td><code>ip</code></td>
<td>Ip is a utility to show or manipulate routing, network devices, interfaces and tunnels.</td>
</tr>
<tr>
<td><code>netstat</code></td>
<td>Shows network status.</td>
</tr>
<tr>
<td><code>ss</code></td>
<td>Another utility to investigate sockets.</td>
</tr>
<tr>
<td><code>ps</code></td>
<td>Shows process status.</td>
</tr>
<tr>
<td><code>who</code></td>
<td>Displays who is logged in.</td>
</tr>
<tr>
<td><code>env</code></td>
<td>Prints environment or sets and executes command.</td>
</tr>
<tr>
<td><code>lsblk</code></td>
<td>Lists block devices.</td>
</tr>
<tr>
<td><code>lsusb</code></td>
<td>Lists USB devices</td>
</tr>
<tr>
<td><code>lsof</code></td>
<td>Lists opened files.</td>
</tr>
<tr>
<td><code>lspci</code></td>
<td>Lists PCI devices.</td>
</tr>
</tbody>
</table>

# Task Scheduling

## Systemd

Systemd is a service used to start processes and scripts at specific times. There are three steps to setting up a process with systemd.

1. Create a timer.
2. Create a service
3. Activate the timer.

### Create a timer

```shell-session
$ sudo mkdir /etc/systemd/system/mytimer.timer.d
$ sudo vim /etc/systemd/system/mytimer.timer
```

#### Mytimer.timer

```txt
[Unit]
Description=My Timer

[Timer]
OnBootSec=3min
OnUnitActiveSec=1hour

[Install]
WantedBy=timers.target
```

### Create the Service
```shell-session
$ sudo vim /etc/systemd/system/mytimer.service
```

```txt
[Unit]
Description=My Service

[Service]
ExecStart=/full/path/to/my/script.sh

[Install]
WantedBy=multi-user.target
```

### Activate

```shell-session
$ sudo systemctl daemon-reload
$ sudo systemctl start mytimer.service
$ sudo systemctl enable mytimer.service

```

## Cron

Cron is another tool that can automate. The Cron Daemonneeds a `crontab` that tells the daemon when to run tasks.

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Time Frame</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td>Minutes (0-59)</td>
<td>This specifies in which minute the task should be executed.</td>
</tr>
<tr>
<td>Hours (0-23)</td>
<td>This specifies in which hour the task should be executed.</td>
</tr>
<tr>
<td>Days of month (1-31)</td>
<td>This specifies on which day of the month the task should be executed.</td>
</tr>
<tr>
<td>Months (1-12)</td>
<td>This specifies in which month the task should be executed.</td>
</tr>
<tr>
<td>Days of the week (0-7)</td>
<td>This specifies on which day of the week the task should be executed.</td>
</tr>
</tbody>
</table>

```txt
# System Update
* */6 * * /path/to/update_software.sh

# Execute scripts
0 0 1 * * /path/to/scripts/run_scripts.sh

# Cleanup DB
0 0 * * 0 /path/to/scripts/clean_database.sh

# Backups
0 0 * * 7 /path/to/scripts/backup.sh
```

# Network Services

## SSH

Secure shell

- `systemctl status ssh`
- `ssh username@<IP.ADDRES>`
- Openssh can be configured by editing the /etc/ssh/sshd_config file.

## NFS

Network File System (NFS) is a network protocol that allows for storage and management of files on a remote system as though they were local.

#### Create NFS Share

```shell-session
~$ mkdir nfs_sharing
~$ echo '/home/cry0l1t3/nfs_sharing hostname(rw,sync,no_root_squash)' >> /etc/exports
~$ cat /etc/exports | grep -v "#"

/home/user/nfs_sharing hostname(rw,sync,no_root_squash)
```

## Mount NFS Share

Mount NFS Share

```shell-session
~$ mkdir ~/target_nfs
~$ mount 10.129.12.17:/home/john/dev_scripts ~/target_nfs
~$ tree ~/target_nfs

target_nfs/
├── css.css
├── html.html
├── javascript.js
├── php.php
└── xml.xml

0 directories, 5 files
```

## Web Server

We can use web server for a variety of tasks.  

```shell-session
$ sudo apt install apache2 -y
```

For Apache2, to specify which folders can be accessed, we can edit the file `/etc/apache2/apache2.conf` with a text editor. This file contains the global settings. We can change the settings to specify which directories can be accessed and what actions can be performed on those directories.

### Apache Configuration

```txt
<Directory /var/www/html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
</directory>
```

We could also do the python server if python 3 is installed.

# Working with Web Services

We can install apache as above.  The default page will then be available.

# Backup and Restore

When backing up on Ubuntu you can utilize tools like:

- Rsync
- Deja Dupe
- Duplicity

## Using Rsync

```shell-session
$ rsync -av /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

This command will copy the entire directory (`/path/to/mydirectory`) to a remote host (`backup_server`), to the directory `/path/to/backup/directory`. The option `archive` (`-a`) is used to preserve the original file attributes, such as permissions, timestamps, etc., and using the `verbose` (`-v`) option provides a detailed output of the progress of the `rsync` operation.

We can also add additional options to customize the backup process, such as using compression and incremental backups. We can do this like the following:

```shell-session
$ rsync -avz --backup --backup-dir=/path/to/backup/folder --delete /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

## Encrypted Rsync

```shell-session
$ rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

The data transfer between our local host and the backup server occurs over the encrypted SSH connection, which provides confidentiality and integrity protection for the data being transferred. This encryption process ensures that the data is protected from any potential malicious actors who would otherwise be able to access and modify the data without authorization. The encryption key itself is also safeguarded by a comprehensive set of security protocols, making it even more difficult for any unauthorized person to gain access to the data. In addition, the encrypted connection is designed to be highly resistant to any attempts to breach security, allowing us to have confidence in the protection of the data being transferred.

## Auto-Synchronization

To enable auto-synchronization using `rsync`, you can use a combination of `cron` and `rsync` to automate the synchronization process. Scheduling the cron job to run at regular intervals ensures that the contents of the two systems are kept in sync. This can be especially beneficial for organizations that need to keep their data synchronized across multiple machines. Furthermore, setting up auto-synchronization with `rsync` can be a great way to save time and effort, as it eliminates the need for manual synchronization. It also helps to ensure that the files and data stored in the systems are kept up-to-date and consistent, which helps to reduce errors and improve efficiency.

Therefore we create a new script called `RSYNC_Backup.sh`, which will trigger the `rsync` command to sync our local directory with the remote one.

# File System Management

Linux  file system uses the unix file system which is hierarchical. At the top is the inode table, teh basis for the file system.

## Disks and Drives

The main tool for disk management is `fdisk` which allows us to create, delete, and manage drive partitions. Each partition can then be formatted with a specific file system, such as ext4, NTFS, or FAT32, and can be mounted as a separate file system. The most common partitioning tool on Linux is also `fdisk`, `gpart`, and `GParted`.

```shell-session
$ sudo fdisk -l

Disk /dev/vda: 160 GiB, 171798691840 bytes, 335544320 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x5223435f

Device     Boot     Start       End   Sectors  Size Id Type
/dev/vda1  *         2048 158974027 158971980 75.8G 83 Linux
/dev/vda2       158974028 167766794   8792767  4.2G 82 Linux swap / Solaris

Disk /dev/vdb: 452 KiB, 462848 bytes, 904 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

Each partition or drive must be assigned to a specific directory on Linux. This is called mounting.

The `mount` tool is used to mount a file system and the /etc/fstab file is used to define the default file systems that are mounted at boot time.

```shell-session
$ mount

sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=4035812k,nr_inodes=1008953,mode=755,inode64)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,nodev,noexec,relatime,size=814580k,mode=755,inode64)
/dev/vda1 on / type btrfs (rw,noatime,nodiratime,nodatasum,nodatacow,space_cache,autodefrag,subvolid=257,subvol=/@)
```

```
$ sudo mount /dev/sdb1 /mnt/usb

$ sudo umount /mnt/usb
```

# Containerization

Containerization is a process of packaging and running applications in isolated environments, such as a container, virtual machine, or serverless environment. Technologies like Docker, Docker Compose, and Linux Containers make this process possible in Linux systems. These technologies allow users to create, deploy, and manage applications quickly, securely, and efficiently.

## Dockers

Docker is an open-source platform for automating the deployment of applications as self-contained units called containers. It uses a layered filesystem and resource isolation features to provide flexibility and portability. Additionally, it provides a robust set of tools for creating, deploying, and managing applications, which helps streamline the containerization process.

First, install the Docker engine.

Then you need to create or pull a docker image.  The docker image is created using a [Dockerfile](https://academy.hackthebox.com/module/18/section/%5Bhttps://docs.docker.com/engine/reference/builder/%5D(https://docs.docker.com/engine/reference/builder/)), which contains all the instructions the Docker engine needs to create the container.

https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

Create a docker file:

## Dockerfile

```bash
# Use the latest Ubuntu 22.04 LTS as the base image
FROM ubuntu:22.04

# Update the package repository and install the required packages
RUN apt-get update && \
    apt-get install -y \
        apache2 \
        openssh-server \
        && \
    rm -rf /var/lib/apt/lists/*

# Create a new user called "student"
RUN useradd -m docker-user && \
    echo "docker-user:password" | chpasswd

# Give the htb-student user full access to the Apache and SSH services
RUN chown -R docker-user:docker-user /var/www/html && \
    chown -R docker-user:docker-user /var/run/apache2 && \
    chown -R docker-user:docker-user /var/log/apache2 && \
    chown -R docker-user:docker-user /var/lock/apache2 && \
    usermod -aG sudo docker-user && \
    echo "docker-user ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Expose the required ports
EXPOSE 22 80

# Start the SSH and Apache services
CMD service ssh start && /usr/sbin/apache2ctl -D FOREGROUND
```

Build an image from the dockerfile by executing the docker build command in the directory containing the dockerfile.:

```shell-session
$ docker build -t FS_docker
```

Once the Docker image has been created, it can be executed through the Docker engine, making it a very efficient and easy way to run a container. It is similar to the virtual machine concept, based on images. Still, these images are read-only templates and provide the file system necessary for runtime and all parameters. A container can be considered a running process of an image. When a container is to be started on a system, a package with the respective image is first loaded if unavailable locally. We can start the container by the following command [docker run](https://academy.hackthebox.com/module/18/section/%5Bhttps://docs.docker.com/engine/reference/commandline/run/%5D(https://docs.docker.com/engine/reference/commandline/run/)):
```shell-session
$ docker run -p <host port>:<docker port> -d <docker container name>
```

```shell-session
$ docker run -p 8022:22 -p 8080:80 -d FS_docker
```

## Docker Management

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Command</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>docker ps</code></td>
<td>List all running containers</td>
</tr>
<tr>
<td><code>docker stop</code></td>
<td>Stop a running container.</td>
</tr>
<tr>
<td><code>docker start</code></td>
<td>Start a stopped container.</td>
</tr>
<tr>
<td><code>docker restart</code></td>
<td>Restart a running container.</td>
</tr>
<tr>
<td><code>docker rm</code></td>
<td>Remove a container.</td>
</tr>
<tr>
<td><code>docker rmi</code></td>
<td>Remove a Docker image.</td>
</tr>
<tr>
<td><code>docker logs</code></td>
<td>View the logs of a container.</td>
</tr>
</tbody>
</table>

## Linux Containers

Linux Containers (`LXC`) is a virtualization technology that allows multiple isolated Linux systems to run on a single host. It uses resource isolation features, such as `cgroups` and `namespaces`, to provide a lightweight virtualization solution.

LXC is a lightweight virtualization technology that uses resource isolation features of the Linux kernel to provide an isolated environment for applications. In LXC, images are manually built by creating a root filesystem and installing the necessary packages and configurations. Those containers are tied to the host system, may not be easily portable, and may require more technical expertise to configure and manage. LXC also provides some security features but may not be as robust as Docker.

```shell-session
$ sudo apt-get install lxc lxc-utils -y
```

#### Creating an LXC Container

To create a new LXC container, we can use the `lxc-create` command followed by the container's name and the template to use. For example, to create a new Ubuntu container named `linuxcontainer`, we can use the following command:

```shell-session
$ sudo lxc-create -n linuxcontainer -t ubuntu
```

<table class="table table-striped text-left">
<thead>
<tr>
<th>Command</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>lxc-ls</code></td>
<td>List all existing containers</td>
</tr>
<tr>
<td><code>lxc-stop -n &lt;container&gt;</code></td>
<td>Stop a running container.</td>
</tr>
<tr>
<td><code>lxc-start -n &lt;container&gt;</code></td>
<td>Start a stopped container.</td>
</tr>
<tr>
<td><code>lxc-restart -n &lt;container&gt;</code></td>
<td>Restart a running container.</td>
</tr>
<tr>
<td><code>lxc-config -n &lt;container name&gt; -s storage</code></td>
<td>Manage container storage</td>
</tr>
<tr>
<td><code>lxc-config -n &lt;container name&gt; -s network</code></td>
<td>Manage container network settings</td>
</tr>
<tr>
<td><code>lxc-config -n &lt;container name&gt; -s security</code></td>
<td>Manage container security settings</td>
</tr>
<tr>
<td><code>lxc-attach -n &lt;container&gt;</code></td>
<td>Connect to a container.</td>
</tr>
<tr>
<td><code>lxc-attach -n &lt;container&gt; -f /path/to/share</code></td>
<td>Connect to a container and share a specific directory or file.</td>
</tr>
</tbody>
</table>

It is important to configure LXC container security to prevent unauthorized access or malicious activities inside the container. This can be achieved by implementing several security measures, such as:

-   Restricting access to the container
-   Limiting resources
-   Isolating the container from the host
-   Enforcing mandatory access control
-   Keeping the container up to date

### Securing LXC

n order to configure `cgroups` for LXC and limit the CPU and memory, a container can create a new configuration file in the `/usr/share/lxc/config/<container name>.conf` directory with the name of our container. For example, to create a configuration file for a container named `linuxcontainer`, we can use the following command:

```shell-session
$ sudo vim /usr/share/lxc/config/linuxcontainer.conf
```

In this configuration file, we can add the following lines to limit the CPU and memory the container can use.

```txt
lxc.cgroup.cpu.shares = 512
lxc.cgroup.memory.limit_in_bytes = 512M
```

```shell-session
$ sudo systemctl restart lxc.service
```

LXC use `namespaces` to provide an isolated environment for processes, networks, and file systems from the host system. Namespaces are a feature of the Linux kernel that allows for creating isolated environments by providing an abstraction of system resources.

Each container is allocated a unique process ID (`pid`) number space, isolated from the host system's process IDs. This ensures that the container's processes cannot interfere with the host system's processes, enhancing system stability and reliability. Additionally, each container has its own network interfaces (`net`), routing tables, and firewall rules, which are completely separate from the host system's network interfaces. Any network-related activity within the container is cordoned off from the host system's network, providing an extra layer of network security.

# Network Configuration

Check configurations with:

`ifconfig` or `ip addr`

## Activate an interface:

```shell-session
$ sudo ifconfig eth0 up     # OR
$ sudo ip link set eth0 up
```

## Assign an IP Address to an Interface:

```shell-session
$ sudo ifconfig eth0 192.168.1.2
```

## Assign an Netmask to an Interface:

```shell-session
$ sudo ifconfig eth0 netmask 255.255.255.0
```

## Assign the Route to an Interface
```shell-session
$ sudo route add default gw 192.168.1.1 eth0
```

## Edit DNS Settings

DNS Settings are in the `/etc/resolv.conf` file.

## Editing Interfaces

The network configuration is in the `/etc/networkinterfaces`

```txt
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```

## Restart Networking Services

```shell-session
$ sudo systemctl restart networking
```

## Network Access Control

Network access control or (NAC) has different NACT technologies:

- Discretionary Access Control (DAC)

	This is as a NAC system that allows users to manage their resources by granting resource owners the responsibility of managing access to their resource.

- Mandatory ACcess Control (MAC)

	MAC systems define rules that determine resource access based on the resource's security level and the user's security level or process requesting the access. Resources have security labels and users have security levels. Access is granted only if the user's security level is equal to or greater than the security level of the resource.

- Role-based Access control (RBAC)

	RBAC assigns permissions to users based on their roles within an organization. Users are assigned roles based on their job responsibilities or other criteria, and each role is granted a set of permissions that determine the actions they can perform.

## Monitoring

Network monitoring involves capturing, analyzing, and interpreting network traffic to identify security threats, performance issues, and suspicious behavior.


## Troubleshooting Network Behavior

Various tools can help us identify and resolve issues regarding network troubleshooting on Linux systems. Some of the most commonly used tools include:

1.  Ping
2.  Traceroute
3.  Netstat
4.  Tcpdump
5.  Wireshark
6.  Nmap

### Ping

You know, ping.

### Traceroute

```shell-session
$ traceroute www.inlanefreight.com

traceroute to www.inlanefreight.com (134.209.24.248), 30 hops max, 60 byte packets
 1  * * *
 2  10.80.71.5 (10.80.71.5)  2.716 ms  2.700 ms  2.730 ms
 3  * * *
 4  10.80.68.175 (10.80.68.175)  7.147 ms  7.132 ms 10.80.68.161 (10.80.68.161)  7.393 ms
```

This will display the IP addresses of the devices that the packets pass through to reach the Google DNS server. The output of a traceroute command shows how it is used to trace the path of packets to the website [www.inlanefreight.com](http://www.inlanefreight.com/), which has an IP address of 134.209.24.248. Each line of the output contains valuable information.

### Netstat

`Netstat` is used to display active network connections and their associated ports. It can be used to identify network traffic and troubleshoot connectivity issues. To use `netstat`, we can enter the following command:

Netstat

```shell-session
Morrigar@htb[/htb]$ netstat -a

Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 localhost:5901          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:sunrpc          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:http            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN
...SNIP...
```

We can expect to receive detailed information about each connection when using this tool. This includes the protocol used, the number of bytes received and sent, IP addresses, port numbers of both local and remote devices, and the current connection state.

## Hardening

Several mechanisms are highly effective in securing Linux systems in keeping our and other companies' data safe. Three such mechanisms are SELinux, AppArmor, and TCP wrappers.

SELinux is a MAC system that is built into the Linux kernel. It is designed to provide fine-grained access control over system resources and applications. SELinux works by enforcing a policy that defines the access controls for each process and file on the system. It provides a higher level of security by limiting the damage that a compromised process can do.

AppArmor is also a MAC system that provides a similar level of control over system resources and applications, but it works slightly differently. AppArmor is implemented as a Linux Security Module (LSM) and uses application profiles to define the resources that an application can access. AppArmor is typically easier to use and configure than SELinux but may not provide the same level of fine-grained control.

TCP wrappers are a host-based network access control mechanism that can be used to restrict access to network services based on the IP address of the client system. It works by intercepting incoming network requests and comparing the IP address of the client system to the access control rules. These are useful for limiting access to network services from unauthorized systems.

# Remote Desktop Protool

Remote desktop protocols are used in Windows, Linux, and macOS to provide graphical remote access to a system.

## XServer

The XServer is the user-side part of the `X Window System network protocol` (`X11` / `X`). The `X11` is a fixed system that consists of a collection of protocols and applications that allow us to call application windows on displays in a graphical user interface.

Communication of the GUI with the operating system happens via n X server. The computer's internal network is used and the protocol uses TCP/IP (usually) and ports in the TCP/6001-6009 range.

X11 can be used to access other computers, and other computers can use the X Server. The data is unencrypted and must be tunneled over SSH to be encrypted. X11 forwarding in the `/etc/ssh/sshd_config` file must be set to yes.

## X11 Forwarding

```shell-session
$ cat /etc/ssh/sshd_config | grep X11Forwarding

X11Forwarding yes
```

```shell-session
$ ssh -X htb-student@10.129.23.11 /usr/bin/firefox

htb-student@10.129.14.130's password: ********
<SKIP>
```

## XDMCP

The `X Display Manager Control Protocol` (`XDMCP`) protocol is used by the `X Display Manager` for communication through UDP port 177 between X terminals and computers operating under Unix/Linux.

XDMCP is an insecure protocol and should not be used in any environment that requires high levels of security. With this, it is possible to redirect an entire graphical user interface (`GUI`) (such as KDE or Gnome) to a corresponding client. For a Linux system to act as an XDMCP server, an X system with a GUI must be installed and configured on the server. After starting the computer, a graphical interface should be available locally to the user.

## VNC

`Virtual Network Computing` (`VNC`) is a remote desktop sharing system based on the RFB protocol that allows users to control a computer remotely. It allows a user to view and interact with a desktop environment remotely over a network connection. The user can control the remote computer as if sitting in front of it. This is also one of the most common protocols for remote graphical connections for Linux hosts.

VNC is generally considered to be secure. It uses encryption to ensure the data is safe while in transit and requires authentication before a user can gain access.

Traditionally, the VNC server listens on TCP port 5900. So it offers its `display 0` there. Other displays can be offered via additional ports, mostly `590[x]`, where `x` is the display number. Adding multiple connections would be assigned to a higher TCP port like 5901, 5902, 5903, etc.

For these VNC connections, many different tools are used. Among them are for example:

-   [TigerVNC](https://tigervnc.org/)
-   [TightVNC](https://www.tightvnc.com/)
-   [RealVNC](https://www.realvnc.com/en/)
-   [UltraVNC](https://uvnc.com/)

# Linux Security

Keep the system up to date. Use principal of least privilege and common protection measures like `fail2ban`.

In addition, some security settings should be made, such as:

-   Removing or disabling all unnecessary services and software
-   Removing all services that rely on unencrypted authentication mechanisms
-   Ensure NTP is enabled and Syslog is running
-   Ensure that each user has its own account
-   Enforce the use of strong passwords
-   Set up password aging and restrict the use of previous passwords
-   Locking user accounts after login failures
-   Disable all unwanted SUID/SGID binaries

## TCP Wrappers

TCP wrapper is a security mechanism used in Linux systems that allows the system administrator to control which services are allowed access to the system. It works by restricting access to certain services based on the hostname or IP address of the user requesting access.

TCP wrappers use the following configuration files:

-   `/etc/hosts.allow`
-   `/etc/hosts.deny`

In short, the `/etc/hosts.allow` file specifies which services and hosts are allowed access to the system, whereas the `/etc/hosts.deny` file specifies which services and hosts are not allowed access. These files can be configured by adding specific rules to the files.

```shell-session
$ cat /etc/hosts.allow

# Allow access to SSH from the local network
sshd : 10.129.14.0/24

# Allow access to FTP from a specific host
ftpd : 10.129.14.10

# Allow access to Telnet from any host in the inlanefreight.local domain
telnetd : .inlanefreight.local
```

```shell-session
$ cat /etc/hosts.deny

# Deny access to all services from any host in the inlanefreight.com domain
ALL : .inlanefreight.com

# Deny access to SSH from a specific host
sshd : 10.129.22.22

# Deny access to FTP from hosts with IP addresses in the range of 10.129.22.0 to 10.129.22.255
ftpd : 10.129.22.0/24
```

# Firewall Setup

The primary goal of firewalls is to provide a security mechanism for controlling and monitoring network traffic between different network segments, such as internal and external networks or different network zones.

## Iptables

The iptables utility provides a flexible set of rules for filtering network traffic based on various criteria such as source and destination IP addresses, port numbers, protocols, and more. There also exist other solutions like nftables, ufw, and firewalld.

The main components of iptables are:

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Component</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>Tables</code></td>
<td>Tables are used to organize and categorize firewall rules.</td>
</tr>
<tr>
<td><code>Chains</code></td>
<td>Chains are used to group a set of firewall rules applied to a specific type of network traffic.</td>
</tr>
<tr>
<td><code>Rules</code></td>
<td>Rules define the criteria for filtering network traffic and the actions to take for packets that match the criteria.</td>
</tr>
<tr>
<td><code>Matches</code></td>
<td>Matches are used to match specific criteria for filtering network traffic, such as source or destination IP addresses, ports, protocols, and more.</td>
</tr>
<tr>
<td><code>Targets</code></td>
<td>Targets specify the action for packets that match a specific rule. For example, targets can be used to accept, drop, or reject packets or modify the packets in another way.</td>
</tr>
</tbody>
</table>


### Tables

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Table Name</strong></th>
<th><strong>Description</strong></th>
<th><strong>Built-in Chains</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>filter</code></td>
<td>Used to filter network traffic based on IP addresses, ports, and protocols.</td>
<td>INPUT, OUTPUT, FORWARD</td>
</tr>
<tr>
<td><code>nat</code></td>
<td>Used to modify the source or destination IP addresses of network packets.</td>
<td>PREROUTING, POSTROUTING</td>
</tr>
<tr>
<td><code>mangle</code></td>
<td>Used to modify the header fields of network packets.</td>
<td>PREROUTING, OUTPUT, INPUT, FORWARD, POSTROUTING</td>
</tr>
</tbody>
</table>

