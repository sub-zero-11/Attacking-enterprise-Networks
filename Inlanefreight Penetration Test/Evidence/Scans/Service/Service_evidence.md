# Service - Extracted Evidence

Compiled from ACME-IPT engagement notes (Attacking Enterprise Networks vault).

---

### #### **monitoring.inlanefreight.local**
_Source: External-Attack2, line ~409_

```
        php
<?php
ini_set('display_errors', 1);
ini_set('display_startup_errors', 1);
error_reporting(E_ALL);
$output = '';

function filter($str)
{
  $operators = ['&', '|', ';', '\\', '/', ' '];
  foreach ($operators as $operator) {
    if (strpos($str, $operator)) {
      return true;
    }
  }
  $words = ['whoami', 'echo', 'rm', 'mv', 'cp', 'id', 'curl', 'wget', 'cd', 'sudo', 'mkdir', 'man', 'history', 'ln', 'grep', 'pwd', 'file', 'find', 'kill', 'ps', 'uname', 'hostname', 'date', 'uptime', 'lsof', 'ifconfig', 'ipconfig', 'ip', 'tail', 'netstat', 'tar', 'apt', 'ssh', 'scp', 'less', 'more', 'awk', 'head', 'sed', 'nc', 'netcat'];
  foreach ($words as $word) {
    if (strpos($str, $word) !== false) {
      return true;
    }
  }

  return false;
}

if (isset($_GET['ip'])) {
  $ip = $_GET['ip'];
  if (filter($ip)) {
    $output = "Invalid input";
  } else {
    $cmd = "bash -c 'ping -c 1 " . $ip . "'";
    $output = shell_exec($cmd);
  }
}
?>
<?php
echo $output;
?>

```

---

 ##### **Setting Up Pivoting - SSH**
_Source: Internal-Testing, line ~249_

```
netstat -antp | grep 8081

```

---

###     proxy types: http, socks4, socks5
_Source: Internal-Testing, line ~264_

```
$ proxychains nmap -sT -p 21,22,80,8080 172.16.8.120

ProxyChains-3.1 (http://proxychains.sf.net)
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-21 21:15 EDT
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.120:80-<><>-OK
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.120:80-<><>-OK
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.120:22-<><>-OK
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.120:21-<><>-OK
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.120:8080-<><>-OK
Nmap scan report for 172.16.8.120
Host is up (0.13s latency).

PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
80/tcp   open  http
8080/tcp open  http-proxy

Nmap done: 1 IP address (1 host up) scanned in 0.71 seconds
```

---

###  **Host Discovery - 172.16.8.0/23 Subnet - SSH Tunnel**
_Source: Internal-Testing, line ~289_

```
for i in $(seq 254); do ping 172.16.8.$i -c1 -W1 & done | grep from

```

---

 #### **Enumerating 172.16.8.20 - DotNetNuke (DNN)**
_Source: Internal-Testing, line ~532_

```
$ proxychains showmount -e 172.16.8.20

ProxyChains-3.1 (http://proxychains.sf.net)
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.20:111-<><>-OK
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.20:2049-<><>-OK
Export list for 172.16.8.20:
/DEV01 (everyone)
```

---

 #### **Lateral Movement**
_Source: Lateral-Movement, line ~39_

```
$ proxychains nmap -sT -p 3389 172.16.8.20

ProxyChains-3.1 (http://proxychains.sf.net)
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-22 13:35 EDT
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.20:80-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.20:3389-<><>-OK
Nmap scan report for 172.16.8.20
Host is up (0.11s latency).

PORT     STATE SERVICE
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 0.30 seconds
```
