#### Engagement Overview

Acme Security, Ltd. was engaged by Inlanefreight to perform a full-scope External Penetration Test of its Internet-facing perimeter. This document, part 1 of the External Attack findings, covers information gathering and the initial compromise of two subdomains hosted within scope.

**Objective:** Identify as many exploitable vulnerabilities as possible from an unauthenticated Internet perspective; evasive testing was not required. Per the Rules of Engagement, if the DMZ could be breached, testing was authorized to continue toward internal foothold and Active Directory domain compromise. No credentials were provided for web applications, VPN, or Active Directory prior to testing.

**Scope:**

```
External Testing                             Internal Testing
10.129.x.x ("external" facing target host)    172.16.8.0/23
*.inlanefreight.local (all subdomains)        172.16.9.0/23
INLANEFREIGHT.LOCAL (Active Directory domain)
```

Testing began Monday morning following environment setup and a kickoff email to the client confirming the start of the assessment.

![[Pasted image 20260608065458.png]]

---

#### Information Gathering

An initial full-port Nmap scan (`-p- -A`) against the in-scope host identified the following exposed services: FTP (21), SSH (22), SMTP (25), DNS (53), HTTP (80), POP3/IMAP (110/143/993/995), rpcbind (111), and a secondary HTTP service on 8080 flagged as a potentially open proxy. Full scan output is retained at `Evidence/Scans/Service/Service_evidence.md`.

A DNS zone transfer against `INLANEFREIGHT.LOCAL` succeeded and disclosed 10 additional subdomains — **Finding: DNS Zone Transfer Allowed**. A follow-up virtual host fuzzing pass with `ffuf` confirmed one further host (`monitoring`) not returned by the transfer. All discovered hosts were added to `/etc/hosts` for further testing. Full output: `Evidence/OSINT/OSINT_evidence.md`.

**Service-level notes:**
- **FTP (21):** Anonymous login was enabled, exposing a test flag file — **Finding: Anonymous FTP Access Enabled**. No viable public exploits exist for vsFTPd 3.0.3 beyond an out-of-scope DoS PoC.
- **SSH (22):** OpenSSH 8.2 banner grabbed; no known unauthenticated vulnerabilities. A small set of common credentials were tried without success; broader brute-forcing was not pursued without a valid username list.

With 11 subdomains in scope, EyeWitness was used to screenshot each site in bulk rather than reviewing them manually, in line with standard efficiency practice for larger scopes (spot-checked to confirm tooling accuracy). Full output: `Evidence/Scans/Web/Web_evidence.md`.

---

#### Finding: blog.inlanefreight.local — Unused Drupal Instance

The blog subdomain hosts an apparently unused/unhardened Drupal 9 installation.

![[Pasted image 20260609070804.png]]

The current stable Drupal release at the time of testing was 9.4, and the Drupalgeddon 1–3 exploit chains do not affect the 9.x branch. Weak-credential login attempts and self-registration were unsuccessful. **Recommendation:** decommission this instance to reduce external attack surface.

---

#### Finding: careers.inlanefreight.local — Insecure Direct Object Reference (IDOR)

The careers portal supports account registration, CV upload, and a profile page (`/profile?id=<n>`). Parameter fuzzing for SQLi/command injection/file inclusion/XSS on the `id` parameter was unsuccessful, but incrementing the numeric ID exposed **other users' profiles and submitted job applications** without authorization — a classic IDOR.

![[Pasted image 20260609095142.png]]

The authenticated session cookie (a Flask-signed JWT) was extracted via browser DevTools and reused directly in scripted requests. A bash loop iterating profile IDs 1–50 with the captured cookie confirmed the vulnerability and recovered a validation flag at ID 4. Full request/response detail and the enumeration script: `Evidence/Scans/Web/Web_evidence.md`.

**Risk:** Sensitive applicant data (CVs, personal details) can be enumerated by any authenticated user.

---

#### Finding: dev.inlanefreight.local — HTTP Verb Tampering → Unrestricted File Upload → RCE

The "dev" subdomain (a Key Vault-style credential manager) yielded a chain of three linked issues:

1. **Directory Listing Enabled** on `/uploads`, discovered via content discovery (Gobuster).
2. **HTTP Verb Tampering:** `/upload.php` returned an inconsistent `403`-on-`200` response. Re-requesting with the `TRACK` method via Burp Repeater revealed a hidden `X-Custom-IP-Authorization` header; setting this header to `127.0.0.1` unlocked a file-upload form belonging to an internal "Pixel Shop" image editor.

   ![[Pasted image 20260611095238.png]]
   ![[Pasted image 20260611095250.png]]
   ![[Pasted image 20260611095305.png]]

3. **Unrestricted File Upload → Remote Code Execution:** Direct upload of a PHP web shell (`<?php system($_GET['cmd']); ?>`) was blocked by client-side file-type validation. Intercepting the upload and changing `Content-Type` to `image/png` bypassed this check, placing the shell at `/uploads/shell.php` and yielding code execution as `www-data`, escalated to an interactive reverse shell.

Full commands and shell output: `Evidence/Scans/Web/Web_evidence.md` and `Evidence/Misc files/Misc_Files_evidence.md`.

**Risk:** Combined, these three issues give an unauthenticated attacker remote code execution on the host.

---

#### Finding: ir.inlanefreight.local — Weak WordPress Credentials → Authenticated RCE

The Investor Relations Portal runs WordPress 6.0 with the `cbusiness-investment` theme and two plugins, `b2i-investor-tools` and `mail-masta`. Full WPScan output for all three passes below is retained at `Evidence/Scans/Web/Web_evidence.md` and `Evidence/Scans/Vuln/Vuln_evidence.md`.

- **Local File Inclusion (LFI):** The outdated `mail-masta` plugin allowed arbitrary file reads (confirmed via `/etc/passwd` disclosure) through a known, unauthenticated LFI vulnerability.
- **User enumeration:** Four valid usernames were recovered — `ilfreightwp`, `tom`, `james`, `john`.
- **Weak WordPress Admin Credentials:** A WPScan password attack against `ilfreightwp` recovered the credential pair `ilfreightwp`/`password1`, granting administrative access to `/wp-admin/`.
- **Authenticated code execution:** Using the built-in theme editor, the `404.php` template of the inactive Twenty Twenty theme was modified to embed a PHP web shell, yielding remote code execution as `www-data`.

![[Pasted image 20260613105513.png]]

**Summary of findings for this host:** Local File Inclusion, Weak WordPress Admin Credentials, and Authenticated Arbitrary Code Execution via Theme Editor.

---

*Continued in External Attack (Part 2) — remaining subdomains, host pivoting, and internal foothold.*
