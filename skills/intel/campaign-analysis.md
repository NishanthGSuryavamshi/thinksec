# Skill: Campaign Analysis

> Investigating and documenting coordinated attack campaigns, derived from TAG's approach to tracking government-backed operations.

## When to Use

- Multiple related security incidents detected
- Coordinated attack pattern identified
- Investigating suspected APT activity
- Analyzing exploitation campaigns
- Documenting influence operations

## Prerequisites

- Incident data from multiple sources
- IOC correlation capability
- Timeline reconstruction tools
- Victim notification process
- Publishing/reporting framework

## Steps

### 1. Identify Campaign Boundaries
```
□ Define what constitutes "this campaign"
□ Establish time boundaries (start, end, ongoing)
□ Identify geographic scope
□ Determine victim scope
□ Distinguish from other actor activity
```

### 2. Collect Campaign Data
```
□ All related incidents
□ Infrastructure used
□ Malware/tools deployed
□ Exploitation methods
□ Victim information
□ Timeline of events
```

### 3. Reconstruct Attack Chain
```
FOR EACH VICTIM:
  □ Initial access (how did they get in?)
  □ Execution (what did they run?)
  □ Persistence (how did they stay?)
  □ Actions on objectives (what did they do?)
  □ Exfiltration (what did they take?)

ACROSS CAMPAIGN:
  □ Common patterns
  □ Variations
  □ Evolution over time
```

### 4. Analyze Infrastructure
```
□ C2 servers (IPs, domains)
□ Hosting patterns
□ Registration patterns
□ SSL certificate patterns
□ Passive DNS data
□ Historical ownership
□ Connections to other campaigns
```

### 5. Analyze Tooling
```
□ Malware families used
□ Custom vs. commodity tools
□ Code overlap with known actors
□ Unique identifiers
□ Build artifacts
□ Evolution from previous versions
```

### 6. Build Timeline
```
CAMPAIGN TIMELINE:

[Date] - Infrastructure setup begins
[Date] - First victim compromised
[Date] - Campaign expands (new victims)
[Date] - TTP shift observed
[Date] - Campaign detected
[Date] - Actor awareness (if known)
[Date] - Campaign end / ongoing
```

### 7. Assess Impact
```
□ Number of victims
□ Types of data accessed/stolen
□ Operational impact on victims
□ Strategic implications
□ Comparison to similar campaigns
```

### 8. Attribute (If Possible)
```
□ TTP alignment with known actors
□ Infrastructure connections
□ Code overlap
□ Targeting alignment
□ Timing patterns
□ Confidence assessment
```

### 9. Document & Report
```
□ Executive summary
□ Technical details
□ IOCs (with context)
□ Detection guidance
□ Mitigation recommendations
□ Attribution assessment
```

## Campaign Report Template

```markdown
# Campaign Report: [Campaign Name]

## Executive Summary
[2-3 paragraphs: what happened, who's affected, what to do]

## Campaign Overview
- **Duration**: [Start] to [End/Ongoing]
- **Victims**: [Count, types, geography]
- **Objective**: [Espionage/Financial/etc.]
- **Attribution**: [Actor, confidence level]

## Attack Chain

### Initial Access
[How attackers gained entry]

### Execution
[What attackers ran]

### Persistence
[How attackers maintained access]

### Actions on Objectives
[What attackers did]

## Technical Analysis

### Infrastructure
[C2, staging, exfiltration infrastructure]

### Malware
[Tools and malware used]

### TTPs
[MITRE ATT&CK mapping]

## Timeline
| Date | Event |
|------|-------|
| YYYY-MM-DD | [Event description] |

## Indicators of Compromise
[Structured IOC list with context]

## Detection Guidance
[How to detect this campaign]

## Mitigation Recommendations
[How to protect against this]

## Attribution Assessment
[Who, confidence, evidence]

## Appendices
- Full IOC list
- YARA rules
- Detection queries
```

## Analysis Techniques

### Clustering Related Incidents
```
PIVOT POINTS:
- Shared infrastructure (IP, domain)
- Shared malware (hash, code similarity)
- Shared TTPs (execution pattern)
- Shared targeting (victim profile)
- Temporal proximity

CLUSTERING PROCESS:
1. Start with seed incident
2. Pivot on indicators
3. Validate connections
4. Expand cluster
5. Define boundaries
```

### Timeline Reconstruction
```
DATA SOURCES:
- Firewall/proxy logs
- Endpoint telemetry
- DNS logs
- Email gateway
- Authentication logs
- File system timestamps

TECHNIQUE:
1. Collect all timestamps
2. Normalize to UTC
3. Order chronologically
4. Identify gaps
5. Cross-validate sources
```

## Examples

### Good: Watering Hole Campaign
```
Campaign: Operation WateringHole (Q1 2024)

Summary:
TAG discovered a watering hole attack targeting aerospace 
industry visitors to 3 compromised websites. The campaign 
used a browser 0-day to deploy a custom backdoor.

Attack Chain:
1. Initial Access: Compromised legitimate aerospace news sites
2. Delivery: Browser exploit (CVE-2024-XXXX)
3. Execution: Fileless PowerShell loader
4. Persistence: Scheduled task + registry key
5. C2: HTTPS to actor-controlled infrastructure
6. Actions: Credential theft, document exfiltration

Victims: 12 confirmed, aerospace and defense sectors

Attribution: APT-XX (Medium-High confidence)
- Infrastructure overlap with previous campaigns
- Malware code similarity (78%)
- Targeting consistent with known interests
```

### Good: Influence Operation
```
Campaign: Coordinated Influence Operation (Q4 2024)

Summary:
TAG terminated 89 YouTube channels linked to a coordinated 
influence operation sharing pro-[Country] content.

Indicators:
- Coordinated posting times (business hours in target TZ)
- Shared content/narratives
- Inauthentic engagement patterns
- Infrastructure connections

Content Themes:
- [Theme 1]
- [Theme 2]

Platform Actions:
- 89 YouTube channels terminated
- Associated domains reported
- IOCs shared with partners
```

## Anti-patterns

### ❌ Tunnel Vision
```
DON'T: Focus only on one aspect (e.g., just malware)
WHY: Miss campaign context and connections
FIX: Analyze holistically: infra + tools + TTPs + victims
```

### ❌ Rushing to Publish
```
DON'T: Report incomplete analysis
WHY: Miss connections, spread incorrect info
FIX: Thorough analysis before publication
```

### ❌ Victim Shaming
```
DON'T: Identify victims inappropriately
WHY: Further harms victims, deters reporting
FIX: Anonymize victims, focus on attack
```

### ❌ Overreaching Attribution
```
DON'T: Claim nation-state without evidence
WHY: Geopolitical implications
FIX: State confidence level, show evidence
```

## Metrics

- Time to detect campaign
- Victim notification rate
- IOC sharing speed
- Campaign disruption effectiveness
- False positive rate in clustering

## References

- TAG Blog: Campaign reports
- MITRE ATT&CK: Techniques reference
- Mandiant: Campaign analysis methodology
