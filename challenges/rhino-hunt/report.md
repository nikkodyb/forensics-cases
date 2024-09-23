# Forensic Report: Rhino Hunt Case

**Requested by:**  
New Orleans Police Department

**Prepared by:**  
Nikolai Holmen Dyb

**Date:**  
September 2024

---

## Introduction

In 2004, the city of New Orleans passed a law making the possession of nine or more unique rhinoceros images a serious crime. The network administrator at the University of New Orleans flagged illegal rhino image traffic through their monitoring system, RHINOVORE, leading to the seizure of a USB key from one of the University’s labs. The USB key was imaged, and the network administrator also provided three network traces linked to the machine that had a missing hard drive. The main suspect is a Ph.D. student who had been using the University computer since 1972.

This report presents the findings from the forensic analysis of the USB key image and network traces, reconstructing the suspect's activities and potentially retrieving evidence that connects to illegal rhino image trafficking.

---

## Evidence Analyzed

The following evidence items were received for analysis:

| **Evidence Item**       | **MD5 Hash**                              |
|-------------------------|-------------------------------------------|
| RHINOUSB.dd (USB image)  | 80348c58eec4c328ef1f7709adc56a54          |
| rhino.log (Network trace)| c0d0093eb1664cd7b73f3a5225ae3f30          |
| rhino2.log (Network trace)| cd21eaf4ac350f71ffff857d7968341          |
| rhino3.log (Network trace)| 7e29f9d67346df25faaf18efcd95fc30         |

The integrity of each evidence item was verified using the provided MD5 hash values.

---

## Tools Used

The following tools were used during the forensic analysis:

- **Wireshark**: For packet capture analysis and extraction of relevant files from network traces.
- **Autopsy**: Forensic analysis of the USB key image to recover files.
- **fcrackzip**: Password cracking tool to recover files from password-protected ZIP archives.

---

## Key Findings

### 1. **Who gave the accused a telnet/ftp account?**

In the file `f0334472.doc`, recovered from the USB image, the suspect wrote a diary entry that stated:

> "I need to change the password on the gnome account that **Jeremy** gave me."

This entry directly links Jeremy as the person who provided the suspect with the telnet/FTP account used for illegal activities.

### 2. **What is the username/password for the account?**

Analysis of the network logs (`rhino.log`) revealed that the suspect logged into the University’s server using the following credentials:

- **Username:** `gnome`  
- **Password:** `gnome123`

![FTP Login Credentials](./images/wireshark_ftp_login.png)

These credentials were used in both FTP and Telnet sessions by the suspect to transfer and access files.

### 3. **What relevant file transfers appear in the network traces?**

Using **Wireshark**, the following file transfers were identified in the network logs:

- **rhino1.jpg** – Transferred via FTP
- **rhino3.jpg** – Transferred via FTP
- **contraband.zip** – A password-protected ZIP file containing `rhino2.jpg`.

![FTP File Transfer](./images/wireshark_ftp_file_transfer.png)

The logs showed that the suspect uploaded these files from the University’s network. The `contraband.zip` file was crucial, as it contained one of the illegal rhino images, `rhino2.jpg`.

### 4. **What happened to the hard drive in the computer? Where is it now?**

The document `f0334472.doc` recovered from the USB key provided insight into the fate of the missing hard drive. The suspect wrote:

> "I zapped the hard drive and then threw it into the Mississippi River."

This indicates that the hard drive was deliberately destroyed and discarded in an attempt to eliminate evidence.

### 5. **What happened to the USB key?**

The suspect reformatted the USB key after deleting files in an attempt to overwrite sensitive data. However, forensic analysis using **Autopsy** revealed that the reformatting was incomplete, and many files were recoverable.

![Recovered USB Key Files](./images/autopsy_usb_recover.png)

---

## Recovery from the USB Image

### Recovered Files from `RHINOUSB.dd`

Using **Autopsy**, the following files were recovered from the USB key image:

- **Rhinoceros Images:**
  - `f0106393.jpg`
  - `f0106409.jpg` (Also found in the `contraband.zip`)
  - `f0106865.gif`
  - `f0106889.gif`

- **Crocodile Images:**
  - `f0104057.jpg`
  - `f0104249.jpg`
  - `f0105065.jpg`
  - `f0105873.jpg`
  - `f0335081.jpg`

- **Text Files:**
  - `gumbo1.txt`
  - `gumbo2.txt`

- **Document:**
  - `f0334472.doc` – A key file containing the suspect’s diary entry, which provided insight into the destruction of the hard drive and the attempt to reformat the USB key.


### ZIP File Analysis

The file `contraband.zip` was password-protected. Using **fcrackzip** and the **rockyou.txt** wordlist, the password `monkey` was successfully cracked, revealing the image `rhino2.jpg`, which was found in both the network logs and on the USB key.

![fcrackzip Password Recovery](./images/fcrackzip_password_recovery.png)

---

## Timeline Reconstruction

By analyzing the network logs, the following timeline was reconstructed:

- **26/04/2004 16:42:** Packet capture from `rhino.log` begins.
- **16:59:** The suspect uploads `rhino1.jpg` and `rhino3.jpg` via FTP.
- **17:59:** The suspect uploads `contraband.zip` containing `rhino2.jpg`.
- **28/04/2004 15:50:** Packet capture from `rhino2.log` begins. The suspect downloads `rhino4.jpg` and `rhino5.gif` from the University’s server.
- **15:51:** The suspect downloads `rhino.exe` from the University’s server, potentially to manage or hide the illegal images.

---

## Conclusion

This forensic investigation uncovered a wealth of evidence linking the suspect to illegal rhinoceros image trafficking. Despite attempts to destroy evidence by reformatting the USB key and discarding the hard drive, several rhinoceros images and incriminating documents were recovered. Additionally, network traffic logs confirmed the suspect's involvement in the transfer and possession of illegal files using the University’s computer network.

All nine rhinoceros images were successfully recovered from the evidence, providing substantial proof of the suspect's illegal activities.

---

## Appendix: MD5 Hashes of Recovered Files

| **File**                 | **MD5 Hash**                              |
|--------------------------|-------------------------------------------|
| f0106393.jpg              | ca03f2eed3db06a82a8a31b3a3defa24          |
| f0106409.jpg              | ed870202082ea4fd8f5488533a561b35          |
| f0106865.gif              | 76610b7bdb85e5f65e96df3f7e417a74          |
| f0106889.gif              | d03dc23d4ec39e4d16da3c46d2932d62          |
| f0104057.jpg              | ee67d8bef72f9b63fa93dc9ea1bb833a          |
| f0104249.jpg              | 4d37a1033450b8cc96ffd1564829d321          |
| f0105065.jpg              | 6bd0e9bd404a738f9ca4c351a853281           |
| f0105873.jpg              | f1bbcd31cd33badc65ca3d1d
