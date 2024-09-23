DumpMe Challenge Writeup

Platform: CyberDefenders Blue Team
Challenge Name: DumpMe
Tools Used: Volatility 2, REMnux VM

Summary:
In this challenge, we investigated a memory image captured from a Windows 7 machine infected with a Meterpreter payload. The goal was to analyze the memory image, extract indicators of compromise (IOCs), and answer specific questions related to the incident.
Volatility Plugins Overview

Volatility is a powerful memory forensics tool that allows investigators to extract artifacts and analyze memory dumps. The following plugins were used to answer the challenge questions:
Challenge Questions and Solutions
1. What is the SHA1 hash of the memory dump?

We computed the SHA1 hash to ensure the integrity of the memory image. In forensic investigations, validating the hash helps verify that the file has not been tampered with.

Hash: c95e8cc8c946f95a109ea8e47a6800de10a27abd

Command:


shasum Triage-Memory.mem

2. What Volatility profile is most appropriate for this machine?

The imageinfo and kdbgscan plugins help identify the operating system profile of the memory dump. Volatility needs this information to correctly interpret the memory structures in the dump.

    imageinfo: Provides suggested profiles by looking at various system-level artifacts.
    kdbgscan: Scans the memory image for kernel debugger blocks, allowing the determination of the correct profile.

Command:


vol.py -f Triage-Memory.mem imageinfo

Profile: Win7SP1x64
3. What was the process ID (PID) of notepad.exe?

The pslist plugin lists the processes running on the system at the time of the memory dump.

    pslist: Lists active processes along with their details, such as process ID, parent process ID, start time, and command line. Useful for identifying suspicious processes.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 pslist | grep "notepad"

PID: 3032
4. Name the child process of wscript.exe.

The pstree plugin displays the parent-child relationship of processes, making it easier to trace how processes are spawned and their lineage.

    pstree: Provides a hierarchical view of processes, which can reveal malware tactics such as process injection or spawning child processes.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 pstree | grep "wscript.exe"

Child Process: UWkpjFjDzM.exe
5. What was the IP address of the machine at the time the RAM dump was created?

The netscan plugin scans the memory dump for active and closed network connections. This is useful for detecting lateral movement, exfiltration, or C2 communications.

    netscan: Lists network connections (both open and closed), including local and foreign IP addresses, protocols, and ports. It can reveal attacker IPs or compromised machines.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 netscan

Local IP: 10.0.0.101
6. What is the IP address of the attacker?

By correlating the process responsible for the malware (UWkpjFjDzM.exe) with the network connections, I identified the attacker's IP address.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 netscan | grep "UWkpjFjDzM.exe"

Attacker IP: 10.0.0.106
7. How many processes are associated with VCRUNTIME140.dll?

The dlllist plugin lists all loaded dynamic-link libraries (DLLs) in the system. It’s useful for identifying malicious or suspicious libraries loaded by processes.

    dlllist: Enumerates all DLLs loaded by each process, making it useful for malware analysis when certain libraries are associated with malicious activities.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 dlllist | grep "VCRUNTIME140.dll" | wc -l

Processes with VCRUNTIME140.dll: 7
8. What is the MD5 hash of the dumped infected process?

The procdump plugin allows you to dump the memory of a specific process, which can then be further analyzed with tools or hash-checked against known malware.

    procdump: Dumps a process’s memory to disk, allowing investigators to further analyze the process, e.g., extracting malware samples.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 procdump --pid 3496 -D .
md5sum executable.3496.exe

MD5: 690ea20bc3bdfb328e23005d9a80c290
9. What is the LM hash of Bob’s account?

The hashdump plugin retrieves password hashes from the memory image, including LM (LanMan) and NTLM hashes. These hashes can be cracked to obtain plaintext passwords.

    hashdump: Extracts password hashes stored in memory, often used to verify password security or perform password cracking.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 hashdump | grep "Bob"

LM Hash: aad3b435b51404eeaad3b435b51404ee
10. What memory protection constants does the VAD node at 0xfffffa800577ba10 have?

The vadinfo plugin provides details about the Virtual Address Descriptor (VAD) nodes that track memory allocation by processes.

    vadinfo: Displays information about memory regions allocated to processes, including protection attributes (e.g., read-only, no access, execute).

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 vadinfo | grep "0xfffffa800577ba10"

Memory Protection: PAGE_READONLY
11. What memory protection did the VAD at 0x00000000033c0000 have?

Similar to the previous question, I used vadinfo to inspect the memory protection for this specific address range.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 vadinfo | grep "0x00000000033c0000"

Memory Protection: PAGE_NOACCESS
12. What is the name of the VBS script that ran on the machine?

The cmdline plugin reveals the command-line arguments passed to processes at the time they were launched, which can be useful to identify scripts or malware.

    cmdline: Retrieves the command line arguments for all processes, helping to understand how they were executed.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 cmdline | grep ".vbs"

Script: vhjReUDEuumrX.vbs
13. What application was run at 2019-03-07 23:06:58 UTC?

The shimcache plugin analyzes the Application Compatibility Cache (Shimcache), which records the execution of applications on the system, even if they no longer exist on disk.

    shimcache: Extracts a list of executed applications from the Shimcache registry, showing execution timestamps and paths.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 shimcache | grep "2019-03-07 23:06:58"

Application: Skype.exe
14. What was written in notepad.exe at the time of the memory capture?

The memdump plugin extracts the memory of a process, which can then be searched for relevant information using tools like strings.

    memdump: Dumps the memory of a process, allowing investigators to search for hidden data, malware artifacts, or flags like in this challenge.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 memdump -p 3032 -D .
strings -e l 3032.dmp | grep "flag<"

Flag: <REDBULL_IS_LIFE>
15. What is the short name of the file at record 59045?

The mftparser plugin parses the Master File Table (MFT) from NTFS, which contains details about files, such as their short and long names.

    mftparser: Analyzes the NTFS Master File Table (MFT), allowing investigators to extract detailed metadata about files, including timestamps and file paths.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 mftparser | grep "59045"

Short Name: EMPLOY~1.XLS
16. What is the infected PID of the Meterpreter malware?

By using pstree and analyzing the process list, I identified a suspicious process named UWkpjFjDzM.exe (PID 3496). Further analysis confirmed this was the infected process.

Command:


vol.py -f Triage-Memory.mem --profile=Win7SP1x64 pstree | grep "UWkpjFjDzM.exe"

Infected PID: 3496

This concludes the memory analysis for the DumpMe Challenge. By utilizing Volatility and its plugins, we successfully identified malicious processes, extracted critical artifacts, and completed the investigation.

This writeup now includes explanations of each Volatility plugin used, providing insight into their purpose and how they contributed to the investigation. Feel free to expand on any section or include additional screenshots.