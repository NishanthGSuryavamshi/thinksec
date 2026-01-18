# Skill: Threat Report Writing

> Creating actionable, well-structured threat intelligence reports, derived from TAG's public reporting standards.

## When to Use

- Publishing threat research findings
- Briefing stakeholders on threats
- Sharing intelligence with partners
- Documenting investigation results
- Warning at-risk communities

## Prerequisites

- Completed threat analysis
- Verified findings
- Cleared for publication (legal, sensitivity)
- Target audience identified
- IOCs prepared for sharing

## Steps

### 1. Define Report Purpose
```
□ Who is the audience?
  - Executives (strategic)
  - Security teams (tactical)
  - General public (awareness)
  - Partners (actionable intel)

□ What action should readers take?
  - Update defenses
  - Investigate for compromise
  - Inform leadership
  - Share with constituents
```

### 2. Choose Report Type
```
REPORT TYPES:

Actor Profile:
  - Who they are
  - What they do
  - How they operate

Campaign Report:
  - Specific operation analysis
  - Timeline and scope
  - Technical details

Threat Bulletin:
  - Current threat summary
  - Quick actionable intelligence
  - IOCs and detection

Strategic Assessment:
  - Trend analysis
  - Predictive outlook
  - Risk implications
```

### 3. Structure the Report
```
STANDARD STRUCTURE:

1. Title
2. TLP Classification
3. Executive Summary
4. Key Findings
5. Threat Overview
6. Technical Analysis
7. Recommendations
8. IOCs / Detection
9. References
10. Appendices
```

### 4. Write Executive Summary
```
FORMULA:
  WHO: [Threat actor or campaign name]
  WHAT: [Activity observed]
  WHEN: [Timeframe]
  WHERE: [Targeted regions/sectors]
  IMPACT: [What's at risk]
  ACTION: [What to do]

LENGTH: 1-2 paragraphs max

EXAMPLE:
"Google's Threat Analysis Group has identified an ongoing 
campaign by APT42, targeting journalists and human rights 
activists in the Middle East. Since January 2024, we have 
observed credential phishing operations using spoofed login 
pages. Organizations and individuals in at-risk communities 
should enable phishing-resistant MFA and review recent 
account activity."
```

### 5. Present Key Findings
```
FORMAT: Bulleted list of 3-5 key points

EXAMPLE:
• APT42 continues to target journalists and NGOs
• Campaign uses custom phishing kit with real-time 
  credential capture
• 150+ individuals targeted across 12 countries
• New infrastructure registered in Q1 2024
• Attackers pivoting to OAuth consent phishing
```

### 6. Write Technical Analysis
```
SECTIONS:

Attack Chain:
  - Initial access method
  - Execution technique
  - Persistence mechanism
  - Actions on objectives

Infrastructure:
  - Domains, IPs used
  - Hosting patterns
  - Historical connections

Tooling:
  - Malware analysis
  - Phishing kit details
  - Custom tools

TTP Summary:
  - MITRE ATT&CK mapping
  - Notable techniques
```

### 7. Provide Actionable Recommendations
```
PRIORITIZED RECOMMENDATIONS:

Immediate (24-48 hours):
  □ Block known IOCs
  □ Search for indicators in logs
  □ Alert high-risk users

Short-term (1-2 weeks):
  □ Implement detection rules
  □ Review authentication logs
  □ Enhance email filtering

Long-term (ongoing):
  □ Deploy phishing-resistant MFA
  □ Security awareness training
  □ Threat hunting based on TTPs
```

### 8. Format IOCs for Action
```
GOOD IOC FORMAT:

| Type | Indicator | Context |
|------|-----------|---------|
| Domain | login-secure[.]com | Phishing page |
| IP | 192.0.2.1 | C2 server |
| Hash | abc123... | Malware sample |

ALSO PROVIDE:
  - YARA rules (if applicable)
  - Sigma rules
  - Search queries
```

### 9. Review & Publish
```
REVIEW CHECKLIST:
  □ Technical accuracy verified
  □ No victim-identifying information
  □ Sources and methods protected
  □ TLP classification correct
  □ Legal review (if needed)
  □ Clear, jargon-free language
  □ Actionable for audience
```

## Report Templates

### Actor Report Template
```markdown
# Threat Actor Report: [Actor Name]

**TLP**: [CLEAR/GREEN/AMBER]
**Date**: [Publication date]
**Report ID**: [Tracking number]

## Executive Summary
[1-2 paragraphs]

## Key Findings
- [Finding 1]
- [Finding 2]
- [Finding 3]

## Actor Overview
### Identity
[Actor description, aliases, first seen]

### Motivation
[Espionage, financial, etc.]

### Targeting
[Industries, regions, victim types]

## Capabilities
### Tools and Malware
[Custom and commodity tools used]

### TTPs
[MITRE ATT&CK mapping table]

## Recent Activity
[Recent campaigns, evolution]

## Recommendations
### For Defenders
[Tactical recommendations]

### For At-Risk Organizations
[Specific guidance]

## Indicators of Compromise
[IOC table with context]

## References
[Links to related reports]
```

### Campaign Report Template
```markdown
# Campaign Report: [Campaign Name]

**TLP**: [CLEAR/GREEN/AMBER]
**Date**: [Publication date]

## Executive Summary
[WHO did WHAT to WHOM with WHAT IMPACT]

## Key Findings
- [Finding 1]
- [Finding 2]

## Campaign Overview
- **Duration**: [Timeframe]
- **Scope**: [Victims, geography]
- **Objective**: [Goals]
- **Attribution**: [Actor, confidence]

## Attack Analysis
### Initial Access
[How victims were compromised]

### Execution
[What was run]

### Impact
[What was achieved]

## Technical Details
### Infrastructure
[C2, phishing, staging]

### Malware
[Tools deployed]

## Timeline
| Date | Event |
|------|-------|
| [Date] | [Event] |

## Recommendations
[Prioritized actions]

## Indicators of Compromise
[Actionable IOCs]
```

## Writing Guidelines

### Be Clear
```
GOOD: "Attackers sent phishing emails with malicious attachments"
BAD: "Threat actors leveraged spearphishing with weaponized payloads"
```

### Be Specific
```
GOOD: "150 individuals targeted across 12 countries"
BAD: "Many people were targeted globally"
```

### Be Actionable
```
GOOD: "Block domain: malicious[.]com at your email gateway"
BAD: "Organizations should improve their security posture"
```

### Protect Victims
```
GOOD: "A telecommunications company in Country X"
BAD: "TeleCorp International was breached on March 15"
```

## Anti-patterns

### ❌ Jargon Overload
```
DON'T: "APT deployed C2 implant with P2P DGA"
WHY: Excludes non-expert readers
FIX: Explain terms or use plain language
```

### ❌ Burying the Lead
```
DON'T: Start with methodology before findings
WHY: Busy readers never reach the point
FIX: Executive summary first, details after
```

### ❌ No Actionable Guidance
```
DON'T: Just describe the threat
WHY: Readers don't know what to do
FIX: Always include specific recommendations
```

### ❌ Stale Intelligence
```
DON'T: Publish analysis of old activity as new
WHY: Misleads readers about current threat
FIX: Be clear about timeline, freshness
```

## Metrics

- Report readership
- IOC block rate (how many acted)
- Feedback from partners
- Time to publication
- Detection rate post-publication

## References

- TAG Blog (exemplary reports)
- GTIG/Mandiant reports
- MITRE ATT&CK
- FIRST TLP definitions
