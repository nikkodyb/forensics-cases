
# Memory Analysis - Ransomware
This challenge is found on [Blue Team Labs Online - Memory Analysis Ransomware](https://blueteamlabs.online/home/challenge/memory-analysis-ransomware-7da6c9244d).


**Task**  
The Account Executive called the SOC earlier, frustrated and unable to access any files on his computer. He kept receiving a pop-up message stating that his files had been encrypted. We disconnected the computer from the network and extracted a memory dump of the machine for analysis. Our task is to investigate the memory dump using Volatility, uncover how the ransomware operates, and identify steps to mitigate it.

## Introduction

This writeup demonstrates the use of Volatility to perform memory forensics and analyze how ransomware has affected a system. Volatility is a widely used tool for memory forensics, known for its extensibility through plugins, making it essential for incident response teams.

### Volatility Setup

To install Volatility, you can visit the official download page:

- [Volatility Releases](https://www.volatilityfoundation.org/releases)


## Investigation Process

### 1. Image Info

To begin, I ran `imageinfo` to identify the OS profile for the memory dump:

```bash
volatility -f memory.vmem imageinfo
```

The profile detected was `Win7SP1x64`, which we will use for further analysis.

### 2. Listing Processes

Next, I scanned for processes running at the time of the memory capture:

```bash
volatility -f memory.vmem --profile=Win7SP1x64 psscan
```

From the results, I noticed the presence of suspicious processes: `@WanaDecryptor` and `or4qtckT.exe`. These do not belong to typical system processes and appear to be related to the ransomware.

**Answer (Suspicious Process Name):** `@WanaDecryptor`

### 3. Parent Process ID

To find the parent process ID of the ransomware, I used the following command:

```bash
volatility -f memory.vmem --profile=Win7SP1x64 pstree
```

The process `or4qtckT.exe` (PID 2732) was identified as the parent process, which spawned `@WanaDecryptor`.

**Answer (Parent Process ID):** `2732`

### 4. Malicious Executable

The initial malicious executable was confirmed to be `or4qtckT.exe`, which is responsible for starting the ransomware processes.

**Answer (Initial Malicious Executable):** `or4qtckT.exe`

### 5. File Deletion Process

Using `psscan` and `grep`, I examined the PID of the malicious process and discovered that a process named `taskdl.exe` was involved in deleting files.

```bash
volatility -f memory.vmem --profile=Win7SP1x64 psscan | grep 2732
```

**Answer (Process Deleting Files):** `taskdl.exe`

### 6. Path of Malicious Executable

To find the path from which the ransomware was executed, I used the `dlllist` plugin and searched for the process:

```bash
volatility -f memory.vmem --profile=Win7SP1x64 dlllist | grep or4qtckT.exe
```

The ransomware executable was launched from the following location:

**Answer (Malicious File Path):** `C:\Users\hacker\Desktop\or4qtckT.exe`

### 7. Identifying the Ransomware

A quick online search revealed that this ransomware was `WannaCry`, based on the suspicious processes identified.

**Answer (Ransomware Name):** `WannaCry`

### 8. Ransomware Public Key File

To locate the file containing the public key used to encrypt the private key, I performed a file scan and searched for files with an `.eky` extension:

```bash
volatility -f memory.vmem --profile=Win7SP1x64 filescan | grep .eky
```

The file found was `00000000.eky`, which contained the encryption key.

**Answer (Public Key Filename):** `00000000.eky`

## Conclusion

This investigation highlights how memory forensics using Volatility can reveal critical information about ransomware activity on a compromised system. By identifying the ransomware process, parent process, and the files involved, we can begin developing strategies to mitigate its impact and stop further attacks.

For more of my projects, check out my [GitHub repository](https://github.com/nikkodyb/forensics-cases).

