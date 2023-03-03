---
{"dg-publish":true,"permalink":"/cybersecurity/penetration-tester/network-enumeration-with-nmap/"}
---


**Enumeration is the key.**

## Introduction to Nmap

Nmap is an open -source network analysis and security auditing tool written in c, c++, pythpn and lua.

### Nmap Architecture

Nmap offers many different types of scans that can be used to obtain various results about our targets. Basically, Nmap can be divided into the following scanning techniques:

-   Host discovery
-   Port scanning
-   Service enumeration and detection
-   OS detection
-   Scriptable interaction with the target service (Nmap Scripting Engine)

### Syntax

Generally: `nmap <scan types> <opetions> <target>`

### Scan Techniques.

There are many.  They can be seen with nmap --help.

Popular are:
- -sS: the TCP-SYN Scan. (Stealthy)
- -sU: UDP
- -sC: Run with common scripts.
- -sV: query versions.

## Host Discovery

An example might bi: 
```shell-session
sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5

10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Scanning Options</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>10.129.2.0/24</code></td>
<td>Target network range.</td>
</tr>
<tr>
<td><code>-sn</code></td>
<td>Disables port scanning.</td>
</tr>
<tr>
<td><code>-oA tnet</code></td>
<td>Stores the results in all formats starting with the name 'tnet'.</td>
</tr>
</tbody>
</table>

### Scan IP List

If we have a hosts.lst then we give it to nmap using -iL parameter.

If we want to scan multiple ips, we just put them in there. If the ips are adjacent we can do 10.129.2.18-20

If we disable port scan (`-sn`), Nmap automatically ping scan with `ICMP Echo Requests` (`-PE`). Once such a request is sent, we usually expect an `ICMP reply` if the pinging host is alive. The more interesting fact is that our previous scans did not do that because before Nmap could send an ICMP echo request, it would send an `ARP ping` resulting in an `ARP reply`. We can confirm this with the "`--packet-trace`" option. To ensure that ICMP echo requests are sent, we also define the option (`-PE`) for this.

We have already mentioned in the "`Learning Process`," and at the beginning of this module, it is essential to pay attention to details. An `ICMP echo request` can help us determine if our target is alive and identify its system. More strategies about host discovery can be found at https://nmap.org/book/host-discovery-strategies.html

## Host and Port Scanning

Once we find a host that is alive we want to find out more about it. We do that by identifying Open ports and services, service versions, information taht the services provide and the operating system.

<p>There are a total of 6 different states for a scanned port we can obtain:</p>
<div class="table-responsive"><table class="table table-striped text-left">
<thead>
<tr>
<th><strong>State</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>open</code></td>
<td>This indicates that the connection to the scanned port has been established. These connections can be <strong>TCP connections</strong>, <strong>UDP datagrams</strong> as well as <strong>SCTP associations</strong>.</td>
</tr>
<tr>
<td><code>closed</code></td>
<td>When the port is shown as closed, the TCP protocol indicates that the packet we received back contains an <code>RST</code> flag. This scanning method can also be used to determine if our target is alive or not.</td>
</tr>
<tr>
<td><code>filtered</code></td>
<td>Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target.</td>
</tr>
<tr>
<td><code>unfiltered</code></td>
<td>This state of a port only occurs during the <strong>TCP-ACK</strong> scan and means that the port is accessible, but it cannot be determined whether it is open or closed.</td>
</tr>
<tr>
<td><code>open|filtered</code></td>
<td>If we do not get a response for a specific port, <code>Nmap</code> will set it to that state. This indicates that a firewall or packet filter may protect the port.</td>
</tr>
<tr>
<td><code>closed|filtered</code></td>
<td>This state only occurs in the <strong>IP ID idle</strong> scans and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall.</td>
</tr>
</tbody>
</table></div>

### Discovering Open TCP Ports

Nmap scans the top 1000 ports with the SYN scan. This scan is set to default only if run as root.  Otherwise the TCP scan -sT is the default.

#### Scan the top 10 TCP ports
```shell-session
 sudo nmap 10.129.2.28 --top-ports=10 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:36 CEST
Nmap scan report for 10.129.2.28
Host is up (0.021s latency).

PORT     STATE    SERVICE
21/tcp   closed   ftp
22/tcp   open     ssh
23/tcp   closed   telnet
25/tcp   open     smtp
80/tcp   open     http
110/tcp  open     pop3
139/tcp  filtered netbios-ssn
443/tcp  closed   https
445/tcp  filtered microsoft-ds
3389/tcp closed   ms-wbt-server
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 1.44 seconds
```

#### Nmap -  Trace the packets.

```shell-session
sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-ping

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:39 CEST
SENT (0.0429s) TCP 10.10.14.2:63090 > 10.129.2.28:21 S ttl=56 id=57322 iplen=44  seq=1699105818 win=1024 <mss 1460>
RCVD (0.0573s) TCP 10.129.2.28:21 > 10.10.14.2:63090 RA ttl=64 id=0 iplen=40  seq=0 win=0
Nmap scan report for 10.11.1.28
Host is up (0.014s latency).

PORT   STATE  SERVICE
21/tcp closed ftp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
```

#### Connect Scan

The Nmap [TCP Connect Scan](https://nmap.org/book/scan-methods-connect-scan.html) (`-sT`) uses the TCP three-way handshake to determine if a specific port on a target host is open or closed. The scan sends an `SYN` packet to the target port and waits for a response. It is considered open if the target port responds with an `SYN-ACK` packet and closed if it responds with an `RST` packet.

This is the most accurate way to determine a port state.

### Filtered Ports

When a port is shown as filtered, it can have several reasons. In most cases, firewalls have certain rules set to handle specific connections. The packets can either be `dropped`, or `rejected`. When a packet gets dropped, `Nmap` receives no response from our target, and by default, the retry rate (`--max-retries`) is set to 1. This means `Nmap` will resend the request to the target port to determine if the previous packet was not accidentally mishandled.

Let us look at an example where the firewall `drops` the TCP packets we send for the port scan. Therefore we scan the TCP port **139**, which was already shown as filtered. To be able to track how our sent packets are handled, we deactivate the ICMP echo requests (`-Pn`), DNS resolution (`-n`), and ARP ping scan (`--disable-arp-ping`) again.

### Discovering Open UDP Ports

```shell-session
sudo nmap 10.129.2.28 -F -sU

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:01 CEST
Nmap scan report for 10.129.2.28
Host is up (0.059s latency).
Not shown: 95 closed ports
PORT     STATE         SERVICE
68/udp   open|filtered dhcpc
137/udp  open          netbios-ns
138/udp  open|filtered netbios-dgm
631/udp  open|filtered ipp
5353/udp open          zeroconf
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 98.07 seconds
```

If the UDP port is `open`, we only get a response if the application is configured to do so.

## Service Enumeration

First do a quick scan and try and get an application and version as accurately as possible.

### Service Version Detection.

After you get a view of the available ports we can run a port scan in the background that runs all open ports. We can use the version scan to scan the spcific ports for services and their versions. -sV

So we could do:
`sudo nmap TGT -p- -sV --stats-every=5s`

We can also increase verbosity so that nmap will give us detections. -v or -vv

## Nmap Scripting Engine

Scripts can be written in lua and there are 14 categories for scripts.

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Category</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>auth</code></td>
<td>Determination of authentication credentials.</td>
</tr>
<tr>
<td><code>broadcast</code></td>
<td>Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans.</td>
</tr>
<tr>
<td><code>brute</code></td>
<td>Executes scripts that try to log in to the respective service by brute-forcing with credentials.</td>
</tr>
<tr>
<td><code>default</code></td>
<td>Default scripts executed by using the <code>-sC</code> option.</td>
</tr>
<tr>
<td><code>discovery</code></td>
<td>Evaluation of accessible services.</td>
</tr>
<tr>
<td><code>dos</code></td>
<td>These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services.</td>
</tr>
<tr>
<td><code>exploit</code></td>
<td>This category of scripts tries to exploit known vulnerabilities for the scanned port.</td>
</tr>
<tr>
<td><code>external</code></td>
<td>Scripts that use external services for further processing.</td>
</tr>
<tr>
<td><code>fuzzer</code></td>
<td>This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time.</td>
</tr>
<tr>
<td><code>intrusive</code></td>
<td>Intrusive scripts that could negatively affect the target system.</td>
</tr>
<tr>
<td><code>malware</code></td>
<td>Checks if some malware infects the target system.</td>
</tr>
<tr>
<td><code>safe</code></td>
<td>Defensive scripts that do not perform intrusive and destructive access.</td>
</tr>
<tr>
<td><code>version</code></td>
<td>Extension for service detection.</td>
</tr>
<tr>
<td><code>vuln</code></td>
<td>Identification of specific vulnerabilities.</td>
</tr>
</tbody>
</table>

Specify default scripts with the -sC switch. Specify scripts with the --script switch.

The -A option scans the target with multiple options such as service detection -sV, OS detection -O, traceroute --traceroute and with the default NSE scripts -sC.

### Vulnerability Assessment

Can be done using the vuln category from NSE

`sudo nmap 10.129.2.28 -p 80 -sV --script vuln`
```shell-session
sudo nmap 10.129.2.28 -p 80 -sV --script vuln 

Nmap scan report for 10.129.2.28
Host is up (0.036s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-enum:
|   /wp-login.php: Possible admin folder
|   /readme.html: Wordpress version: 2
|   /: WordPress version: 5.3.4
|   /wp-includes/images/rss.png: Wordpress version 2.2 found.
|   /wp-includes/js/jquery/suggest.js: Wordpress version 2.5 found.
|   /wp-includes/images/blank.gif: Wordpress version 2.6 found.
|   /wp-includes/js/comment-reply.js: Wordpress version 2.7 found.
|   /wp-login.php: Wordpress login page.
|   /wp-admin/upgrade.php: Wordpress login page.
|_  /readme.html: Interesting, a readme.
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
| http-wordpress-users:
| Username found: admin
|_Search stopped at ID #25. Increase the upper limit if necessary with 'http-wordpress-users.limit'
| vulners:
|   cpe:/a:apache:http_server:2.4.29:
|     	CVE-2019-0211	7.2	https://vulners.com/cve/CVE-2019-0211
|     	CVE-2018-1312	6.8	https://vulners.com/cve/CVE-2018-1312
|     	CVE-2017-15715	6.8	https://vulners.com/cve/CVE-2017-15715
<SNIP>

```

## Performance

### Timeouts

Generally Nmap starts with a timeout of 100ms. We can optimize that by setting an --initial-rtt-timeout of 50ms and a --max-rtt-timeout of 100ms

### Retries

Nmap will do 10 retries as a default. WE can set that to 0 and not get too much loss.

### Rates

We can tell nmap to try and maintain a certain rate of packets sent with --min-rate ### like 300.

We can also use `-T <0-5>` to set timing templates.  0 is slowest 5 is fastest.

## Firewalls

Firewalls try to prevent unauthorized connection attempts from external networks.

### IDS/IPS

Intrusion detection systems and and Intrutions Prevention systems are software-based components.

### Determine Firewalls and Their Rules

When a port is shown as as filtered, there can be several reasons. Packets can be dropped or rejected. Dropped packets are ignored. Rejected packets are returned with an RST flag. They contain different types of ICMP error codes or nothing.

Error codes may be:

-   Net Unreachable
-   Net Prohibited
-   Host Unreachable
-   Host Prohibited
-   Port Unreachable
-   Proto Unreachable

Nmap's TCP ACK scan (-sA) is harder to filter because they only send a TCP packet with only the ACK flag. The ACK flag is often passed on by the firewall because the firewall can't determine if the connection was established first in the internal or external network.

### Detect IDS/IPS

`IDS systems` examine all connections between hosts. If the IDS finds packets containing the defined contents or specifications, the administrator is notified and takes appropriate action in the worst case.

`IPS systems` take measures configured by the administrator independently to prevent potential attacks automatically. It is essential to know that IDS and IPS are different applications and that IPS serves as a complement to IDS.

### Decoys

There are cases in which administrators block specific subnets from different regions in principle. This prevents any access to the target network. Another example is when IPS should block us. For this reason, the Decoy scanning method (`-D`) is the right choice. With this method, Nmap generates various random IP addresses inserted into the IP header to disguise the origin of the packet sent. With this method, we can generate random (`RND`) a specific number (for example: `5`) of IP addresses separated by a colon (`:`). Our real IP address is then randomly placed between the generated IP addresses. In the next example, our real IP address is therefore placed in the second position. Another critical point is that the decoys must be alive. Otherwise, the service on the target may be unreachable due to SYN-flooding security mechanisms.

```shell-session
$ sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5
```

### DNS Proxying

Nmap allows us to specify DNS servers which may be useful if we are in a demilitarized zone and the company's DNS servers are more trusted.

SYN-Scan of a Filtered Port
```shell-session
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 22:50 CEST
SENT (0.0417s) TCP 10.10.14.2:33436 > 10.129.2.28:50000 S ttl=41 id=21939 iplen=44  seq=736533153 win=1024 <mss 1460>
SENT (1.0481s) TCP 10.10.14.2:33437 > 10.129.2.28:50000 S ttl=46 id=6446 iplen=44  seq=736598688 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up.

PORT      STATE    SERVICE
50000/tcp filtered ibm-db2

Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds
```

SYN-Scan from DNS Port
```shell-session
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53

SENT (0.0482s) TCP 10.10.14.2:53 > 10.129.2.28:50000 S ttl=58 id=27470 iplen=44  seq=4003923435 win=1024 <mss 1460>
RCVD (0.0608s) TCP 10.129.2.28:50000 > 10.10.14.2:53 SA ttl=64 id=0 iplen=44  seq=540635485 win=64240 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.013s latency).

PORT      STATE SERVICE
50000/tcp open  ibm-db2
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds
```

