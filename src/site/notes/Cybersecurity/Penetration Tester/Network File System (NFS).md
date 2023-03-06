---
{"dg-publish":true,"permalink":"/cybersecurity/penetration-tester/network-file-system-nfs/"}
---

NFS is a file system developed by Sun Microsystems. NFS is used between Linux and Unix systems. NFS cannot communicate with SMB servers.

NFS is based on the [Open Network Computing Remote Procedure Call](https://en.wikipedia.org/wiki/Sun_RPC) (`ONC-RPC`/`SUN-RPC`) protocol exposed on `TCP` and `UDP` ports `111`, which uses [External Data Representation](https://en.wikipedia.org/wiki/External_Data_Representation) (`XDR`) for the system-independent exchange of data. The NFS protocol has `no` mechanism for `authentication` or `authorization`. Instead, authentication is completely shifted to the RPC protocol's options.