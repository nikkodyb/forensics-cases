# Rhino Hunt Case Study

**Prepared by:**  
Nikolai Holmen Dyb

**Date:**  
September 2024

---

## Evidence and Resources

The forensic image files and network logs for this case can be found at the following official source:

### [Rhino Hunt Files and Tasks](https://cfreds-archive.nist.gov/dfrws/Rhino_Hunt.html)

- **Download the image files and network logs** from the provided link.
- **Reference**: The tasks and evidence files located on this page are used in this case study.
  
---

## Case Overview

This case involves an investigation into the illegal possession and trafficking of rhinoceros images. The city of New Orleans passed a law in 2004 making the possession of nine or more unique rhinoceros images a serious crime. The suspect in this case was flagged by network monitoring software at the University of New Orleans for suspicious activity.

Evidence analyzed includes:
- A USB key image (`RHINOUSB.dd`)
- Three network traffic logs (`rhino.log`, `rhino2.log`, `rhino3.log`)

---

[Forensic Report: Rhino Hunt Case](https://github.com/your-username/your-repo-name/blob/main/path-to-your-report)

---

## Key Findings

### 1. Who gave the accused a telnet/FTP account?

In the document `f0334472.doc` recovered from the USB image, the suspect mentioned that a person named **Jeremy** provided them with the FTP account.

---

### 2. What’s the username/password for the account?

- **Username:** `gnome`  
- **Password:** `gnome123`

The credentials were used to log in via FTP and Telnet on the University’s server.

---

### 3. What relevant file transfers appear in the network traces?

The network logs revealed multiple file transfers:
- `rhino1.jpg` via FTP
- `rhino3.jpg` via FTP
- `contraband.zip` (containing `rhino2.jpg`)

---

### 4. What happened to the hard drive in the computer? Where is it now?

The suspect mentioned in `f0334472.doc` that they destroyed the hard drive and threw it into the Mississippi River to eliminate evidence.

---

### 5. What happened to the USB key?

The suspect reformatted the USB key in an attempt to remove the illegal images. However, many files were recoverable using forensic analysis.

---

### Recovered Files from USB

Using forensic tools, the following files were recovered from the USB image (`RHINOUSB.dd`):

- **Rhinoceros Images:**
  - `f0106393.jpg`
  - `f0106409.jpg` (also found in `contraband.zip`)
  - `f0106865.gif`
  - `f0106889.gif`

- **Text Files:**
  - `gumbo1.txt` (a recipe)
  - `gumbo2.txt` (another recipe)

- **Documents:**
  - `f0334472.doc` (a key file with the suspect’s notes)

---

## Timeline of Events

The following timeline was reconstructed using the network logs and recovered files:

- **26/04/2004 16:42**: FTP login and file uploads (`rhino1.jpg`, `rhino3.jpg`).
- **17:59**: Upload of `contraband.zip` containing `rhino2.jpg`.
- **28/04/2004 15:50**: Download of `rhino4.jpg` and `rhino5.gif` via HTTP.
- **15:51**: Download of `rhino.exe`.

---

## Conclusion

The forensic investigation provided strong evidence linking the suspect to illegal rhinoceros image trafficking. Despite attempts to destroy evidence, several rhinoceros images were recovered, and network logs confirmed the illegal activity.

---

## MD5 Hashes of Recovered Files

| **File**                 | **MD5 Hash**                              |
|--------------------------|-------------------------------------------|
| f0106393.jpg              | ca03f2eed3db06a82a8a31b3a3defa24          |
| f0106409.jpg              | ed870202082ea4fd8f5488533a561b35          |
| f0106865.gif              | 76610b7bdb85e5f65e96df3f7e417a74          |
| f0106889.gif              | d03dc23d4ec39e4d16da3c46d2932d62          |
| f0334472.doc              | 68059d3355f0138c9fdd7eaa75e7bc16          |
| gumbo1.txt                | d3fc014d86a03730b707ad4205dbc63e          |
| gumbo2.txt                | 24695a1699fa85ea5553b49d62fd46c2          |

---

### Screenshots (to be added later)

- **Wireshark FTP Login Analysis**
- **Autopsy USB Key Analysis**
- **Steghide Analysis**

---

**Prepared by**: Nikolai Holmen Dyb
