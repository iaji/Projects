# Cuckoo Details

* Compatible with Virtualbox, KVM, ESXi, VMWare, VSphere, Xenserver, and QEMU (need to look into qemu more) [https://qemu.weilnetz.de/doc/qemu-doc.html]

* Parallel analysis possible across multiple VM's

* Integrates with TCPDump
    * Allows Berkeley Packet filtering

* Can leverage a man in the middle proxy (MITM)

* Capabilities to analyze Android devices via android emulator

* Uses Volatility to dump memory & analyze [https://github.com/volatilityfoundation/volatility/wiki]
    * Scans for hidden/injected code and dlls via malfind module

    * Lists hooked api and kernel space via apihooks module

    * Lists official processes & does not detect hidden processes done via pslist

    * Lists hidden processes via psxview

    * Shows all callbacks via callbacks module

    * Print the system's IDT (Interrupt Descriptor Table) via IDT

    * Shows all installed kernel timers via timers

    * Shows message hooks via messagehooks

    * Shows sids via getsids

    * Shows all privileges via privs

    * Display DLLs loaded by processes with dlllist

    * Lists all open handles of processes with handles

    * Scans for Mutexes through the whole system via mutantscan

    * Lists all devices and drivers via devicetree

    * Scans for services with svcscan

    * Scans for kernel drivers both hidden and unloaded with modscan

    * Works with YARA via yarascan

    * Scans System Service dispatch Table with ssdt [https://www.adlice.com/kernelmode-rootkits-part-1-ssdt-hooks/]

    * Includes option to mask data that shouldn't be logged on VM being used

    * Need to learn more about gtd, sockscan, netscan options

* Compatible with FOG for re-imaging physical machine [https://fogproject.org/] *similar to clonezilla*

* Process memory dumps compatible with IDA Pro

* Screenshots can be automated throughout analysis

* Compatible with Snort

* Compatible with Suricata

* Integrated with VirtusTotal, submit and check database

* Works with IRMA [https://github.com/quarkslab/irma] To my understanding this is an in house open source platform to analyze malicious files.  Need to study this more.

* Cuckoo reports all results on a reporting server, files can be submitted over the web using this

    * Uses mongodb

    * Dumps analysis results into json file

    * Can also create pdf/html reports per file analyzed

    * Can report to MISP for open source intel sharing

    * Can integrate with elasticsearch

    * Can integrate with Moloch

    * Has a notification feature to inform external systems that analysis is finished

    * Integrates with mattermost

    * Can show VirtusTotal hits, matched sigantures, urls, filenames, and more

* Supports routing through Tor network, VPN's, and InetSim
