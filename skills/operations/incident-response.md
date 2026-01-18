# Skill: Incident Response

> Systematic approach to investigating and remediating security breaches, derived from Mandiant's frontline IR methodology.

## When to Use

- Security breach detected or suspected
- Anomalous activity requiring investigation
- Ransomware or malware incident
- Data exfiltration detected
- Compromise indicators found

## Prerequisites

- Incident response plan
- Forensic tools and infrastructure
- Legal/HR coordination channels
- Executive escalation path
- Evidence preservation capability

## Steps

### 1. Preparation (Pre-Incident)
```
□ IR plan documented and tested
□ Forensic workstations ready
□ Evidence storage prepared
□ Contact lists current
□ Retainers in place (legal, IR vendor)
□ Playbooks for common scenarios
```

### 2. Detection & Triage
```
INITIAL TRIAGE:
□ What was detected? (alert, report, discovery)
□ When was it detected?
□ What systems are involved?
□ Is it ongoing or historical?
□ What is the potential impact?

SEVERITY ASSESSMENT:
  Critical: Active breach, data exfil, ransomware
  High: Confirmed compromise, lateral movement
  Medium: Suspicious activity, single system
  Low: Potential false positive, minor anomaly
```

### 3. Containment
```
IMMEDIATE CONTAINMENT:
□ Isolate affected systems (network segmentation)
□ Disable compromised accounts
□ Block known IOCs at perimeter
□ Preserve evidence before changes

CONTAINMENT DECISIONS:
  - Isolate vs. monitor (tactical choice)
  - Credential resets (scope)
  - System shutdown (when necessary)
  - Communication restrictions
  
AVOID:
  ✗ Tipping off attacker unnecessarily
  ✗ Destroying evidence
  ✗ Partial containment (whack-a-mole)
```

### 4. Evidence Collection
```
COLLECTION PRIORITY:

Volatile (collect first):
  □ Running processes
  □ Network connections
  □ Memory (RAM dump)
  □ Logged-in users
  □ Clipboard contents

Non-Volatile:
  □ Disk images
  □ Log files
  □ Registry hives
  □ Browser artifacts
  □ Email data

Network:
  □ Firewall logs
  □ Proxy logs
  □ DNS logs
  □ NetFlow data
  □ PCAP (if available)

CHAIN OF CUSTODY:
  □ Document who collected what
  □ Hash all evidence
  □ Secure storage
  □ Access logging
```

### 5. Investigation
```
INVESTIGATION QUESTIONS:

Initial Access:
  □ How did attacker get in?
  □ What was the entry point?
  □ When did access begin?

Execution:
  □ What malware/tools were used?
  □ What commands were run?
  □ What was executed?

Persistence:
  □ How is attacker maintaining access?
  □ What backdoors exist?
  □ What accounts were created?

Lateral Movement:
  □ What systems were accessed?
  □ What credentials were stolen?
  □ What was the attack path?

Actions on Objectives:
  □ What data was accessed?
  □ What was exfiltrated?
  □ What was damaged/encrypted?
```

### 6. Timeline Reconstruction
```
BUILD TIMELINE:
  □ Correlate events across sources
  □ Normalize timestamps (UTC)
  □ Identify key milestones:
    - Initial compromise
    - Lateral movement
    - Privilege escalation
    - Data access
    - Exfiltration
    - Detection
  
TIMELINE FORMAT:
| Timestamp (UTC) | Source | Event | Details |
|-----------------|--------|-------|---------|
| 2024-01-15 14:32| Email  | Phish delivered | user@company.com |
| 2024-01-15 14:45| Endpoint| Macro executed | Document1.docm |
| 2024-01-15 14:46| Endpoint| Beacon installed | C:\Windows\Temp\x.exe |
```

### 7. Eradication
```
ERADICATION STEPS:
□ Remove all malware
□ Close all backdoors
□ Reset compromised credentials
□ Patch exploited vulnerabilities
□ Remove attacker persistence
□ Verify eradication complete

VERIFICATION:
□ Re-scan all systems
□ Check for reinfection
□ Monitor for attacker return
□ Validate containment holds
```

### 8. Recovery
```
RECOVERY STEPS:
□ Restore systems from clean backups
□ Rebuild compromised systems
□ Restore data (verify integrity)
□ Re-enable services gradually
□ Enhanced monitoring during recovery
□ User communication

RECOVERY ORDER:
1. Critical business systems
2. Authentication infrastructure
3. Security monitoring
4. User systems
5. Non-critical systems
```

### 9. Post-Incident
```
LESSONS LEARNED:
□ What happened? (root cause)
□ What went well?
□ What could improve?
□ What changes are needed?

DOCUMENTATION:
□ Complete incident report
□ Timeline documentation
□ IOCs for future detection
□ Recommendations

IMPROVEMENTS:
□ Detection rule updates
□ Process improvements
□ Training needs
□ Architecture changes
```

## Investigation Techniques

### Memory Analysis
```
TOOLS: Volatility, Rekall

ARTIFACTS:
- Running processes
- Network connections
- Injected code
- Credentials in memory
- Command history
- Encryption keys
```

### Disk Forensics
```
TOOLS: FTK, EnCase, Autopsy

ARTIFACTS:
- File system timeline
- Deleted files
- Prefetch files
- Jump lists
- LNK files
- $MFT analysis
```

### Log Analysis
```
KEY LOGS:
- Windows Event Logs (Security, System, PowerShell)
- Authentication logs
- Firewall/proxy logs
- Application logs
- Cloud audit logs

ANALYSIS:
- Search for known IOCs
- Identify anomalies
- Build execution timeline
- Track lateral movement
```

## Incident Report Template

```markdown
# Incident Report: [Incident ID]

## Executive Summary
[2-3 paragraphs: what happened, impact, response, current status]

## Incident Details
- **Detection Date**: [Date]
- **Incident Type**: [Ransomware/Breach/etc.]
- **Severity**: [Critical/High/Medium/Low]
- **Status**: [Active/Contained/Resolved]

## Timeline
[Key events chronologically]

## Technical Findings
### Initial Access
[How attacker gained entry]

### Attack Progression
[What attacker did]

### Impact
[Systems affected, data accessed/stolen]

## Response Actions
[What was done to contain/eradicate]

## Recommendations
[Improvements to prevent recurrence]

## IOCs
[Indicators for detection]

## Appendices
[Detailed evidence, logs, etc.]
```

## Anti-patterns

### ❌ Premature Eradication
```
DON'T: Immediately wipe systems
WHY: Destroys evidence, may miss persistence
FIX: Scope fully before eradication
```

### ❌ Incomplete Scoping
```
DON'T: Assume single system affected
WHY: Attackers move laterally
FIX: Investigate all potentially affected systems
```

### ❌ Credential Reset Timing
```
DON'T: Reset passwords before cutting attacker access
WHY: Attacker may still have access, sees reset
FIX: Coordinate reset with containment
```

### ❌ Skipping Lessons Learned
```
DON'T: Close incident without retrospective
WHY: Same incident will recur
FIX: Document and implement improvements
```

## Metrics

- Mean time to detect (MTTD)
- Mean time to contain (MTTC)
- Mean time to recover (MTTR)
- Scope accuracy (initial vs. final)
- Recurrence rate

## References

- Mandiant IR methodology
- NIST SP 800-61 (IR Guide)
- M-Trends reports
