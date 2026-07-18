#### Lateral Movement — Domain Enumeration & Credential Harvesting

*Starting credentials: `INLANEFREIGHT\hporter` / `Gr8hambino!`, recovered from DEV01 LSA secrets.*

---

#### Active Directory Enumeration (BloodHound)

SharpHound (`-c All`) was executed from the DEV01 foothold and the resulting archive collected via the DNN file manager for ingestion into BloodHound.

Reviewing `hporter`'s First Degree Object Control showed **ForceChangePassword** rights over the `ssmalls` account.

![[Pasted image 20260711051240.png]]

Separately, BloodHound showed that **all Domain Users have RDP access to DEV01** — **Finding: Excessive Active Directory Group Privileges (Medium)**. This is a lower-severity finding than blanket local admin rights would be, but still expands the effective attack surface of the host to the entire domain.

![[Pasted image 20260711051305.png]]

**Password reset abuse.** RDP access to DEV01 was set up (via SSH local port forwarding through `dmz01`) to obtain a full PowerShell console, and PowerView was used to reset `ssmalls`'s password:

```powershell
Set-DomainUserPassword -Identity ssmalls -AccountPassword (ConvertTo-SecureString 'Str0ngpass86!' -AsPlainText -Force) -Verbose
```

*Note: password resets are intrusive and were only performed after confirming this action was authorized under the Rules of Engagement; the change was logged for inclusion in the report's activity/appendix log.* The new credential (`ssmalls` / `Str0ngpass86!`) was validated against the DC via CrackMapExec.

---

#### Finding: Sensitive Data Exposed via File Shares (Domain-wide)

With `ssmalls`'s credentials, a second share-hunting pass (Snaffler, then CrackMapExec's `spider_plus` module) against the `Department Shares` on the DC surfaced several items of interest:

- **`IT/Private/Development/SQL Express Backup.ps1`** — a backup script containing hardcoded SQL credentials for the `backupadm` account (another weak, "keyboard-walk"-style password), full contents in `Evidence/Scans/Vuln/Vuln_evidence.md`.
- **`SYSVOL/.../scripts/adum.vbs`** (readable by all Domain Users) — an Active Directory user-management script containing a further hardcoded credential (`account@inlanefreight.local`), likely stale but retained for completeness.

Full share listings and downloaded artifacts: `Evidence/Misc files/Misc_Files_evidence.md`.

**Risk:** Multiple hardcoded/plaintext credentials, some for privileged service accounts, are accessible to any authenticated domain user via file shares and SYSVOL — this is the primary source of lateral-movement credentials throughout the engagement.

---

#### Finding: Weak Kerberos Configuration (Kerberoasting) & Weak AD Passwords

**Kerberoasting.** PowerView was used to enumerate all accounts with a Service Principal Name (SPN) set (13 candidates, including `mssqladm`, `svc_sql`, `sqlprod`, and others), and to request/export their service tickets for offline cracking:

```powershell
Get-DomainUser * -SPN -Verbose | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_spns.csv -NoTypeInformation
```

One ticket (`backupjob`) cracked with Hashcat against `rockyou.txt`, though the resulting account had no further useful access. **Finding: Weak Kerberos Authentication Configuration (Kerberoasting) — Medium.**

**Password spraying.** `Invoke-DomainPasswordSpray` against the common password `Welcome1` recovered two further valid, low-privilege accounts (`kdenunez`, `mmertle`). **Finding: Weak Active Directory Passwords Allowed — Medium.**

**Other techniques attempted, no useful result:** GPP autologon password hunting (via CrackMapExec's `gpp_autologin` module) and a search of AD user `Description` fields (one hit, `frontdesk`, not useful) — both still worth noting as **Passwords Stored in AD User Description Field**, a low-effort finding to fix.

Full command output for all of the above: `Evidence/Scans/AD Enumeration/AD_Enumeration_evidence.md`.

---

#### Finding: MS01 (172.16.8.50) — Compromise via Backup Script Credentials

With no further path from the DC or DEV01, a WinRM sweep (`nmap -p 5985`) identified `172.16.8.50` (`MS01`) as reachable, and the `backupadm` credential recovered from the SQL backup script granted access via Evil-WinRM.

```
proxychains evil-winrm -i 172.16.8.50 -u backupadm 
...
*Evil-WinRM* PS C:\Users\backupadm\Documents> hostname
ACADEMY-AEN-MS01
```

`backupadm` is not a local administrator and holds no useful privileges directly. Further enumeration located a leftover Windows deployment artifact, `C:\panther\unattend.xml`, containing a **plaintext autologon credential**:

```xml
<AutoLogon>
    <Password><Value>Sys26Admin</Value><PlainText>true</PlainText></Password>
    <Username>ilfserveradm</Username>
</AutoLogon>
```

**Finding: Cleartext Credentials in Unattended Windows Setup Files — High.** `ilfserveradm` / `Sys26Admin` proved to be a local administrator on MS01 and a member of `INLANEFREIGHT\Domain Admins`.

```
net localgroup administrators
Administrator
ilfserveradm
INLANEFREIGHT\Domain Admins
```

**Credential harvesting as `ilfserveradm`.** Mimikatz was used to elevate to SYSTEM and dump LSA secrets, recovering the local `DefaultPassword` LSA secret (`DBAilfreight1!`) and the machine account hash. Cross-referencing the Winlogon registry key identified the associated username:

```powershell
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\' -Name "DefaultUserName"
DefaultUserName : mssqladm
```

This yielded a new, high-value credential pair: **`mssqladm` / `DBAilfreight1!`**. Browser credential extraction (LaZagne) and LLMNR/NBT-NS poisoning (Inveigh) were also attempted from MS01; Inveigh captured one incidental NTLMv2 hash (`mpalledorous`) from network broadcast traffic, of no further use in this chain.

Full Mimikatz, LaZagne, and Inveigh output: `Evidence/Scans/AD Enumeration/AD_Enumeration_evidence.md`.

---

*Result: valid credentials for `mssqladm` — a Domain Admin-equivalent, per BloodHound, via delegated rights. Continued in Active Directory Compromise.*
