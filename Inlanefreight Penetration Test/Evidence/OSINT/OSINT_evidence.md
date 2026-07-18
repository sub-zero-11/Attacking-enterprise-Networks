# OSINT - Extracted Evidence

Compiled from ACME-IPT engagement notes (Attacking Enterprise Networks vault).

---

### #### **Scenario & Kickoff**
_Source: External-Attack1, line ~18_

```
External Testing	                          Internal Testing
10.129.x.x ("external" facing target host)	   172.16.8.0/23
*.inlanefreight.local (all subdomains)	       172.16.9.0/23
INLANEFREIGHT.LOCAL (Active Directory domain)
```

---

### #### **External Information Gathering**
_Source: External-Attack1, line ~39_

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ sudo nmap --open -p- -A -oA inlanefreight.local -iL scope                    
Starting Nmap 7.98 ( https://nmap.org ) at 2026-06-08 07:03 -0400
Nmap scan report for inlanefreight.local (10.129.32.24)
Host is up (0.27s latency).
Not shown: 65212 closed tcp ports (reset), 312 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE  VERSION
21/tcp   open  ftp      vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.15.118
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              38 May 30  2022 flag.txt
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 71:08:b0:c4:f3:ca:97:57:64:97:70:f9:fe:c5:0c:7b (RSA)
|   256 45:c3:b5:14:63:99:3d:9e:b3:22:51:e5:97:76:e1:50 (ECDSA)
|_  256 2e:c2:41:66:46:ef:b6:81:95:d5:aa:35:23:94:55:38 (ED25519)
25/tcp   open  smtp     Postfix smtpd
|_smtp-commands: ubuntu, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
53/tcp   open  domain   (unknown banner: 1337_HTB_DNS)
| dns-nsid: 
|_  bind.version: 1337_HTB_DNS
| fingerprint-strings: 
|   DNSVersionBindReqTCP: 
|     version
|     bind
|_    1337_HTB_DNS
80/tcp   open  http     Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Inlanefreight
|_http-server-header: Apache/2.4.41 (Ubuntu)
110/tcp  open  pop3     Dovecot pop3d
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-05-30T17:15:40
|_Not valid after:  2032-05-27T17:15:40
|_pop3-capabilities: PIPELINING STLS SASL CAPA UIDL TOP RESP-CODES AUTH-RESP-CODE
111/tcp  open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|_  100000  3,4          111/udp6  rpcbind
143/tcp  open  imap     Dovecot imapd (Ubuntu)
|_imap-capabilities: listed have more capabilities ENABLE post-login ID SASL-IR IMAP4rev1 OK LOGINDISABLEDA0001 STARTTLS LITERAL+ IDLE Pre-login LOGIN-REFERRALS
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-05-30T17:15:40
|_Not valid after:  2032-05-27T17:15:40
993/tcp  open  ssl/imap Dovecot imapd (Ubuntu)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-05-30T17:15:40
|_Not valid after:  2032-05-27T17:15:40
|_imap-capabilities: listed more have ENABLE post-login ID capabilities IMAP4rev1 OK LITERAL+ SASL-IR AUTH=PLAINA0001 IDLE Pre-login LOGIN-REFERRALS
995/tcp  open  ssl/pop3 Dovecot pop3d
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-05-30T17:15:40
|_Not valid after:  2032-05-27T17:15:40
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: PIPELINING TOP SASL(PLAIN) CAPA UIDL USER RESP-CODES AUTH-RESP-CODE
8080/tcp open  http     Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Support Center
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port53-TCP:V=7.98%I=7%D=6/8%Time=6A26A1FE%P=x86_64-pc-linux-gnu%r(DNSVe
SF:rsionBindReqTCP,39,"\x007\0\x06\x85\0\0\x01\0\x01\0\0\0\0\x07version\x0
SF:4bind\0\0\x10\0\x03\xc0\x0c\0\x10\0\x03\0\0\0\0\0\r\x0c1337_HTB_DNS");
Device type: general purpose|router
Running: Linux 5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 2 hops
Service Info: Host:  ubuntu; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   269.93 ms 10.10.14.1
2   270.36 ms inlanefreight.local (10.129.32.24)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 177.81 seconds

```

---

### #### **External Information Gathering**
_Source: External-Attack1, line ~143_

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ dig axfr inlanefreight.local @10.129.32.24

; <<>> DiG 9.20.20-1-Debian <<>> axfr inlanefreight.local @10.129.32.24
;; global options: +cmd
inlanefreight.local.    86400   IN      SOA     ns1.inlanfreight.local. dnsadmin.inlanefreight.local. 21 604800 86400 2419200 86400
inlanefreight.local.    86400   IN      NS      inlanefreight.local.
inlanefreight.local.    86400   IN      A       127.0.0.1
blog.inlanefreight.local. 86400 IN      A       127.0.0.1
careers.inlanefreight.local. 86400 IN   A       127.0.0.1
dev.inlanefreight.local. 86400  IN      A       127.0.0.1
flag.inlanefreight.local. 86400 IN      TXT     "HTB{DNs_ZOn3_Tr@nsf3r}"
gitlab.inlanefreight.local. 86400 IN    A       127.0.0.1
ir.inlanefreight.local. 86400   IN      A       127.0.0.1
status.inlanefreight.local. 86400 IN    A       127.0.0.1
support.inlanefreight.local. 86400 IN   A       127.0.0.1
tracking.inlanefreight.local. 86400 IN  A       127.0.0.1
vpn.inlanefreight.local. 86400  IN      A       127.0.0.1
inlanefreight.local.    86400   IN      SOA     ns1.inlanfreight.local. dnsadmin.inlanefreight.local. 21 604800 86400 2419200 86400
;; Query time: 247 msec
;; SERVER: 10.129.32.24#53(10.129.32.24) (TCP)
;; WHEN: Mon Jun 08 07:10:18 EDT 2026
;; XFR size: 14 records (messages 1, bytes 448)



```

---

### #### **Web Application Enumeration**
_Source: External-Attack1, line ~301_

```
──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ eyewitness -f ilfreight_subdomains -d ILFREIGHT_subdomain_EyeWitness
################################################################################
#                                  EyeWitness                                  #
################################################################################
#           Red Siege Information Security - https://www.redsiege.com           #
################################################################################

Directory Exists! Do you want to overwrite? [y/n] y
Starting Web Requests (11 Hosts)
Attempting to screenshot http://inlanefreight.local
Attempting to screenshot http://blog.inlanefreight.local
Attempting to screenshot http://careers.inlanefreight.local
Attempting to screenshot http://dev.inlanefreight.local
Attempting to screenshot http://gitlab.inlanefreight.local
Attempting to screenshot http://ir.inlanefreight.local
Attempting to screenshot http://status.inlanefreight.local
Attempting to screenshot http://support.inlanefreight.local
Attempting to screenshot http://tracking.inlanefreight.local
Attempting to screenshot http://vpn.inlanefreight.local
Attempting to screenshot http://monitoring.inlanefreight.local
[*] Hit timeout limit when connecting to http://support.inlanefreight.local, retrying
[*] Hit timeout limit when connecting to http://tracking.inlanefreight.local, retrying
[*] Hit timeout limit when connecting to http://support.inlanefreight.local
Finished in 36.36999583244324 seconds

[*] Done! Report written in the /home/kali/HTB/CPTS/Attacking_Enterprise_Networks/ILFREIGHT_subdomain_EyeWitness folder!
Would you like to open the report now? [Y/n]
y
MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
M                                                                M
M       .”cCCc”.                                                 M
M      /cccccccc\                                                M
M      §cccccccc|            Check Back Soon For                 M
M      :ccccccccP                 Upcoming Training              M
M      \cccccccc()                                               M
M       \ccccccccD                                               M
M       |cccccccc\        _                                      M
M       |ccccccccc)     //                                       M
M       |cccccc|=      //                                        M
M      /°°°°°°”-.     (CCCC)                                     M
M      ;----._  _._   |cccc|                                     M
M   .*°       °°   °. \cccc/                                     M
M  /  /       (      )/ccc/                                      M
M  |_/        |    _.°cccc|                                      M
M  |/         °^^^°ccccccc/                                      M
M  /            \cccccccc/                                       M
M /              \cccccc/                                        M
M |                °*°                                           M
M /                  \      Psss. Follow us on >> Twitter        M
M °*-.__________..-*°°                         >> Facebook       M
M  \WWWWWWWWWWWWWWWW/                          >> LinkedIn       M
M   \WWWWWWWWWWWWWW/                                             M
MMMMM|WWWWWWWWWWWW|MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
                                                                     
```
