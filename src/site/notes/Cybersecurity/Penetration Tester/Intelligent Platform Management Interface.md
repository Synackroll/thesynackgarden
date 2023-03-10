---
{"dg-publish":true,"permalink":"/cybersecurity/penetration-tester/intelligent-platform-management-interface/"}
---

[Intelligent Platform Management Interface](https://www.thomas-krenn.com/en/wiki/IPMI_Basics) (IPMI) is a set of standardized specifications for hardware-based host management systems used for system management and monitoring. It acts as an autonomous subsystem and works independently of the host's BIOS, CPU, firmware, and underlying operating system. IPMI provides sysadmins with the ability to manage and monitor systems even if they are powered off or in an unresponsive state. It operates using a direct network connection to the system's hardware and does not require access to the operating system via a login shell. IPMI can also be used for remote upgrades to systems without requiring physical access to the target host. IPMI is typically used in three ways:

-   Before the OS has booted to modify BIOS settings
-   When the host is fully powered down
-   Access to a host after a system failure

When not being used for these tasks, IPMI can monitor a range of different things such as system temperature, voltage, fan status, and power supplies. It can also be used for querying inventory information, reviewing hardware logs, and alerting using SNMP. The host system can be powered off, but the IPMI module requires a power source and a LAN connection to work correctly.

The IPMI protocol was first published by Intel in 1998 and is now supported by over 200 system vendors, including Cisco, Dell, HP, Supermicro, Intel, and more. Systems using IPMI version 2.0 can be administered via serial over LAN, giving sysadmins the ability to view serial console output in band. To function, IPMI requires the following components:

-   Baseboard Management Controller (BMC) - A micro-controller and essential component of an IPMI
-   Intelligent Chassis Management Bus (ICMB) - An interface that permits communication from one chassis to another
-   Intelligent Platform Management Bus (IPMB) - extends the BMC
-   IPMI Memory - stores things such as the system event log, repository store data, and more
-   Communications Interfaces - local system interfaces, serial and LAN interfaces, ICMB and PCI Management Bus
