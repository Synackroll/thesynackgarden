---
{"dg-publish":true,"permalink":"/cybersecurity/penetration-tester/server-message-block/"}
---


A client-server protocol that regulates access to files and entire directories and other network resources such as printers, routers, or interfaces released for the network. Information exchange between different system processes can also be handled based on the SMB protocol. [SMB](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-smb/f210069c-7086-4dc2-885e-861d837df688) first became available to a broader public, for example, as part of the OS/2 network operating system LAN Manager and LAN Server. Since then, the main application area of the protocol has been the Windows operating system series in particular, whose network services support SMB in a downward-compatible manner - which means that devices with newer editions can easily communicate with devices that have an older Microsoft operating system installed. With the free software project Samba, there is also a solution that enables the use of SMB in Linux and Unix distributions and thus cross-platform communication via SMB.

In IP networks, SMB uses TCP.

SMB servers can provide parts of its local file system as shares. Access rights are defined in the [[Cybersecurity/Penetration Tester/Acess Control Lists\|Acess Control Lists]]. 

# SAMBA

An alternative variant to the SMB server, called Samba, developed for Unix-based operating system. Samba implements the `Common Internet File System` (`CIFS`) network protocol. [CIFS](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-cifs/934c2faa-54af-4526-ac74-6a24d126724e) is a "dialect" of SMB. In other words, CIFS is a very specific implementation of the SMB protocol, which in turn was created by Microsoft. This allows Samba to communicate with newer Windows systems. Therefore, it usually is referred to as `SMB / CIFS`.

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>SMB Version</strong></th>
<th><strong>Supported</strong></th>
<th><strong>Features</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td>CIFS</td>
<td>Windows NT 4.0</td>
<td>Communication via NetBIOS interface</td>
</tr>
<tr>
<td>SMB 1.0</td>
<td>Windows 2000</td>
<td>Direct connection via TCP</td>
</tr>
<tr>
<td>SMB 2.0</td>
<td>Windows Vista, Windows Server 2008</td>
<td>Performance upgrades, improved message signing, caching feature</td>
</tr>
<tr>
<td>SMB 2.1</td>
<td>Windows 7, Windows Server 2008 R2</td>
<td>Locking mechanisms</td>
</tr>
<tr>
<td>SMB 3.0</td>
<td>Windows 8, Windows Server 2012</td>
<td>Multichannel connections, end-to-end encryption, remote storage access</td>
</tr>
<tr>
<td>SMB 3.0.2</td>
<td>Windows 8.1, Windows Server 2012 R2</td>
<td></td>
</tr>
<tr>
<td>SMB 3.1.1</td>
<td>Windows 10, Windows Server 2016</td>
<td>Integrity checking, AES-128 encryption</td>
</tr>
</tbody>
</table>

In a network, each host participates in a `workgroup`. The workgroup identifies a collection of computers and their resources on an SMB network. There can be multiple workgroups at any particular time.

