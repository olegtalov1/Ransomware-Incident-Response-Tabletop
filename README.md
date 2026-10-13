# Ransomware Incident Response Tabletop (NIST 800-61 Lifecycle)

**Frameworks:** NIST SP 800-61, MITRE ATT&CK | **Type:** Academic tabletop exercise (hypothetical scenario)
**Skills:** Incident response, triage, containment, recovery planning, stakeholder communication

## Scenario
The IT Security team receives SIEM and EDR alerts for ransomware-like behavior. Users report files renamed with unusual extensions. This write-up walks the incident through each phase of the incident response lifecycle. It is a planning exercise, not a real incident.

## 1. Detection
- **Sources:** SIEM alerts, EDR telemetry, user reports
- **Initial cause suspected:** a malicious email attachment opened on a user workstation
- **Documented at detection:** time, alert source, affected hosts and IPs, associated user account, encrypted file names and extensions, and the ransom note

## 2. Identification and Scoping
| Source | What to review |
|---|---|
| Windows Security logs | Event 4624/4625 (logons), 4688 (process creation), 7045 (new service installed) |
| Domain controller logs | Authentication attempts, privilege escalation |
| Email server logs | Delivery of the malicious attachment |
| Network traffic (Wireshark, IDS/IPS, SIEM) | Known-bad IPs/domains, C2 traffic, unusual SMB traffic (lateral movement), internal traffic spikes |

- **IoC hunting:** PowerShell or EDR queries on a single host; SIEM and EDR threat hunting across all hosts.
- **Scope (assumed for the exercise):** the initial workstation plus several servers, with no evidence of domain controller compromise.
- **Severity:** High. Communicated to IT leadership.

## 3. Containment
- Disconnect infected systems from the network
- Disable compromised accounts
- Block malicious IPs and domains at the firewall
- Enforce network segmentation to limit lateral movement
- Restrict shared drives where needed

**User notice (excerpt):** Explains what happened, tells users not to open suspicious attachments, to disconnect unusual systems from the network, and to report anything suspicious to IT immediately.

## 4. Eradication
- Preserve evidence first: disk images, memory dumps, secured logs
- Identify the ransomware variant using file hashes and threat intelligence
- Run EDR/antivirus scans, remove malicious files and persistence (registry keys, scheduled tasks), and patch exploited vulnerabilities
- Clean or rebuild affected systems

## 5. Recovery
- Restore from verified clean backups, after checking the backups were not compromised
- **Restoration order:** domain controllers (if affected), file servers, email and web servers, then workstations
- **Estimated timeline:** containment in hours, servers in 1-3 days, full recovery up to a week
- Provide regular status updates to stakeholders

## 6. Post-Incident Review
**Lessons learned:** phishing awareness training, stronger email filtering, better lateral-movement controls.
**Improvements:** attachment sandboxing, stronger EDR/XDR coverage, enforced MFA, better network segmentation.
**Preventive measures:** regular backup validation, routine patching, continuous monitoring and threat hunting, security awareness programs.

## MITRE ATT&CK Mapping
| Phase of the attack | Technique |
|---|---|
| Initial access via email | T1566.001 Phishing: Spearphishing Attachment |
| Victim opens the file | T1204.002 User Execution: Malicious File |
| Persistence | T1053.005 Scheduled Task |
| Spread to servers | T1021.002 SMB/Windows Admin Shares |
| Impact | T1486 Data Encrypted for Impact |

## References
NIST SP 800-61 (Computer Security Incident Handling Guide) · MITRE ATT&CK · CISA Ransomware Readiness Guide

## What I Learned
- Preserve evidence before you clean, because eradication destroys it.
- Containment speed matters more than a perfect diagnosis.
- Good communication is part of the response, not an afterthought.
