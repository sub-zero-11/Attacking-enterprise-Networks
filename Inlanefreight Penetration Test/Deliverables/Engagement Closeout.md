#### Engagement Closeout

With testing complete, the client was notified that the active testing period had ended, along with an expected report delivery timeframe. Clear communication at this stage matters: the client should never encounter a high-risk finding for the first time inside the final report — ongoing status updates during testing should already have set expectations.

![[Pasted image 20260713065443.png]]
*Closeout email to Inlanefreight confirming completion of the external penetration test and summarizing high-level findings (e.g., command injection, weak passwords).*

---

#### Attack Path Recap

A clean, start-to-finish recap of the attack chain — external foothold → DMZ pivot → internal Active Directory compromise → management-network objective — is maintained separately from the full testing narrative. This recap intentionally omits dead ends and exploratory steps, showing only the path of least resistance, and is used both to sanity-check that nothing critical was missed and to support the report's executive attack-chain summary.

---

#### Findings Structure

Findings were logged throughout testing (rather than reconstructed at the end), each with supporting command output, screenshots, and a preliminary risk rating, to keep report drafting efficient and evidence-complete. The prioritized findings list (highest to lowest risk) is maintained in `Evidence/Findings` and cross-referenced against the supporting evidence in `Evidence/Scans`, `Evidence/OSINT`, `Evidence/Logging output`, and `Evidence/Misc files`.

---

#### Post-Engagement Cleanup

The following were tracked throughout testing for cleanup and reporting purposes:

- Every scan and attack attempt
- Every file placed on a target system (tools, shells, payloads)
- Every configuration change made (accounts created, SPNs modified, group memberships altered, etc.)

Uploaded files and tooling were removed and any test-only changes (e.g., the temporary SPN set on `ttimmons`, the `ssmalls` password reset) were reverted where authorized, or explicitly flagged for the client where reversal was not possible or not agreed. All changes, uploads, and compromised accounts/hosts are documented in the report appendices along with the methods used. Activity logs are retained for a period following the assessment in case the client needs to correlate testing activity against internal security alerts.

---

#### Client Communication & Retesting

The client was informed of the testing end date so any anomalous activity observed afterward can be correctly attributed as non-assessment-related. A report delivery date and summary of findings were communicated, with a report review meeting to follow delivery. Where retesting is in scope, timelines will be coordinated with the client once remediation work is underway.

---

#### Internal Project Closeout

Standard internal closeout steps for this engagement: archive the report and project data, hold an internal lessons-learned debrief, complete any sales/account follow-up documentation, and close out administrative/invoicing tasks. Post-remediation retesting will ideally be performed by the original assessment team where scheduling allows.
