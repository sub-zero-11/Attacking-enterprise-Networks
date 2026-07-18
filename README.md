# Inlanefreight Penetration Test — Engagement Vault

An Obsidian vault documenting a full-lifecycle penetration test against the fictional organization **Inlanefreight**, performed by **Acme Security, Ltd.** This is based on the *Attacking Enterprise Networks* capstone module (HTB Academy, Penetration Tester Job Role Path), restructured and rewritten here as a professional-style engagement record: External Attack → Internal Foothold → Active Directory Compromise → Post-Exploitation.

> **Scope note:** All hosts, domains, and IP ranges referenced (`inlanefreight.local`, `10.129.x.x`, `172.16.8.0/23`, `172.16.9.0/23`) belong to a training lab environment. Nothing in this repository targets real infrastructure.

## Vault Structure

```
Inlanefreight Penetration Test/
├── Admin/                  Rules of Engagement, scope, kickoff comms, contacts
├── Deliverables/           Engagement closeout notes
├── Evidence/
│   ├── Findings/           Narrative write-ups, organized by attack phase
│   │   ├── External Attack/    Perimeter recon → web app compromise → DMZ foothold
│   │   ├── Internal Attack/    Persistence, pivoting, internal AD enumeration
│   │   └── Lateral Movement/   AD attack paths → DCSync → post-exploitation
│   ├── Scans/               Raw tool output, grouped by category
│   │   ├── AD Enumeration/     SharpHound, PowerView, CrackMapExec, secretsdump...
│   │   ├── Service/            Nmap, host/service discovery
│   │   ├── Vuln/                sqlmap, WPScan vulnerability output
│   │   └── Web/                 Gobuster, EyeWitness, request/response evidence
│   ├── Logging output/      auditd / tcpdump capture output
│   ├── Misc files/          Pulled configs, scripts, and binaries
│   ├── Notes/                Supporting technique notes (pivoting, persistence, etc.)
│   ├── OSINT/                 DNS zone transfer / subdomain enumeration
│   └── Wireless/              (not applicable to this engagement)
└── Retest/                  (reserved for post-remediation retest results)
```

## Engagement Summary

| Phase | Outcome |
|---|---|
| External recon | 11 subdomains identified via DNS zone transfer + vhost fuzzing |
| Web app attacks | IDOR, XSS (session hijack), SQLi, XXE, HTTP verb tampering, unrestricted file upload, weak WordPress creds |
| DMZ foothold | Command injection → `webdev` shell → sudo misconfig → root on `dmz01` |
| Internal pivot | SSH dynamic port forwarding into `172.16.8.0/23` |
| Internal foothold | Exposed NFS share → hardcoded DNN admin creds → SYSTEM on `DEV01` |
| AD compromise | GenericWrite → targeted Kerberoasting → GenericAll on DCSync-capable group → full NTDS dump |
| Post-exploitation | Double pivot into isolated `172.16.9.0/23` management segment → DirtyPipe (CVE-2022-0847) → root on `MGMT01` |

Full technical detail, screenshots, and command evidence for each step live under `Evidence/Findings/`, with raw supporting tool output cross-referenced under `Evidence/Scans/` and the other `Evidence/` subfolders.

## Using This Vault

1. Clone the repository.
2. Open the **`HTB_Academy`** folder (the repository root) as a vault in [Obsidian](https://obsidian.md).
3. Start at `Inlanefreight Penetration Test/Admin/Rules of Engagement & Scope.md` for context, then follow the Findings folder in order: External Attack → Internal Attack → Lateral Movement.

## Disclaimer

This material documents activity performed against an authorized training environment (HTB Academy) under its own terms of use. It is shared for educational and portfolio purposes. Do not reuse any technique, payload, or tool documented here against systems you do not own or have explicit written authorization to test.
