#### Rules of Engagement & Scope

**Client:** Inlanefreight
**Assessor:** Acme Security, Ltd.
**Engagement type:** Full-scope External Penetration Test, with authorization to continue internally (up to and including Active Directory domain compromise) if a foothold is obtained via the DMZ.

**Objective:** Identify as many exploitable vulnerabilities as possible from an unauthenticated, Internet-facing perspective. Evasive testing techniques are not required — the client has prioritized coverage over stealth.

**Authorization boundary:** If the assessment team can breach the DMZ and establish an internal foothold, testing may continue against the internal network, up to and including full Active Directory domain compromise. Per a follow-up scoping discussion, the client additionally asked the team to validate whether access to the isolated management network (172.16.9.0/23) is achievable from the principal domain — this network is intended to be logically separated from general Domain Admin access.

**Credentials provided:** None. No web application, VPN, or Active Directory credentials were provided prior to testing; all access documented in this engagement was obtained during testing.

**Scope:**

```
External Testing                             Internal Testing
10.129.x.x ("external" facing target host)    172.16.8.0/23
*.inlanefreight.local (all subdomains)        172.16.9.0/23
INLANEFREIGHT.LOCAL (Active Directory domain)
```

**Out of scope / notes:**
- Denial-of-service testing (e.g., the vsFTPd 3.0.3 remote DoS PoC identified during testing) is explicitly out of scope.
- Any intrusive change made during testing (e.g., temporary SPN modifications, password resets used as part of an attack path) requires client authorization per the RoE and must be logged for reversal/disclosure in the final report appendix — see `Deliverables/Engagement Closeout.md`.

**Testing window:** Began Monday morning following environment setup; see kickoff correspondence in this folder.
