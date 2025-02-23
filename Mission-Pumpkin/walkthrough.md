# Mission-Pumpkin v1.0: PumpkinRaising Walkthrough

This guide provides a step-by-step walkthrough to identify and exploit vulnerabilities in the **Mission-Pumpkin v1.0: PumpkinRaising** virtual machine for educational purposes.

---

## Prerequisites

- **VirtualBox** or **VMware** installed on your machine.
- **Mission-Pumpkin v1.0 VM** downloaded and configured.
- A penetration testing OS such as **Kali Linux** installed.
- Familiarity with basic Linux commands and penetration testing tools.

---

## Step 1: Initial Setup

### Network Configuration

- Configure the VM and attacker machine to be on the same **NAT Network** or **Host-Only** adapter for communication.  

### Machine Details  

- **Name:** Mission-Pumpkin v1.0  
- **Date Released:** October 2021  
- **Author:** VulnHub Community  
- **Series:** Mission-Pumpkin  
- **Web Page:** [Mission-Pumpkin v1.0](https://www.vulnhub.com/entry/mission-pumpkin-v10-pumpkinraising,324/)  

### Example Target IP:
- **Target IP:** `192.168.48.165`

---

## Step 2: Reconnaissance

### Nmap Scan

#### Command:
```bash
nmap -p- -A 192.168.48.165
```
#### Example Output:
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu
80/tcp open  http    Apache httpd
```

### Enumeration of HTTP Service

#### Access the Website:
Visit `http://192.168.48.165` in your browser. The landing page displays a pumpkin-themed design with four pumpkin images.

#### Page Source Analysis:
- Found a **Base64-encoded message**:  
  ```
  VGhpcyBpcyBqdXN0IHRvIHJlbWFpbmQgeW91IHRoYXQgaXQncyBMZXZlbCAyIG9mIE1pc3Npb24tUHVtcGtpbiEgOyk=
  ```
  - Decoded Message: _"This is just to remind you that it's Level 2 of Mission-Pumpkin!"_

#### Robots.txt Analysis:
- Found `/hidden/note.txt` containing user credentials.
- Found `/seeds/seed.txt.gpg`, a GPG-encrypted file.

---

## Step 3: Exploitation

### **Base Encodings**
#### Decoding Base32 Strings:
A Base32-encoded string found in `pumpkin.html`:  
```
F5ZWG4TJOB2HGL3TOB4S44DDMFYA====
```
- **Decoded Output:** `/scripts/spy.pcap`

---

### **Analyzing the PCAP File**
Use **Wireshark** to analyze `/scripts/spy.pcap` and extract data from captured TCP streams.

#### Command:
```bash
wireshark spy.pcap
```
- Found a **seed ID**: `50609`.

---

### **Steganography Analysis**

#### File: `jackolantern.gif`
Analyze `jackolantern.gif` using **StegoSuite** with Mark’s password.  
- **Extracted File:** `decorative.txt`  
- **Content:** Seed ID `86568`.

---

### **Decrypting the GPG File**

#### Command:
```bash
gpg --decrypt seed.txt.gpg
```
- **Passphrase:** `SEEDWATERSUNLIGHT`  
- **Output:** Morse code decoded into seed ID `69507`.

---

## Step 4: SSH Access

Combine all the seed IDs (69507, 50609, 86568) to form the password:  
`69507506099645486568`

#### Command:
```bash
ssh jack@192.168.48.165
```
---

## Step 5: Privilege Escalation

After logging in as `jack`, identified a vulnerable `strace` binary with `sudo` privileges.

#### Command:
```bash
sudo strace /bin/sh
```
- Accessed the `/root` directory and retrieved `flag.txt`.

#### Example Commands:
```bash
cd /root
cat flag.txt
```

---

## Notes and Learnings

### Key Skills Developed:
1. **Base Encoding:** Learned to decode Base32 and Base64 strings.  
2. **Steganography:** Used tools like **StegoSuite** for extracting hidden data.  
3. **Privilege Escalation:** Leveraged vulnerable binaries for root access.

---

## References

- [VulnHub Mission Pumpkin Walkthrough](https://www.vulnhub.com/entry/mission-pumpkin-v10-pumpkinraising,324/)  
- [Official Documentation for Wireshark](https://www.wireshark.org/docs/)
