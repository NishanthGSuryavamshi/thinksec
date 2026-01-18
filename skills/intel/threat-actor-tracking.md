# Skill: Threat Actor Tracking

> Long-term monitoring and analysis of threat actors, derived from TAG's methodology for tracking 270+ government-backed groups.

## When to Use

- Investigating a security incident with suspected APT involvement
- Building threat intelligence capability
- Monitoring threats relevant to your organization
- Understanding adversary evolution over time
- Attributing attacks to known actors

## Prerequisites

- Access to threat intelligence sources
- IOC management system
- Analysis infrastructure
- MITRE ATT&CK familiarity
- Historical threat data

## Steps

### 1. Establish Actor Profile
```
□ Assign tracking identifier (if new actor)
□ Document first observed activity
□ Initial capability assessment
□ Suspected origin/affiliation
□ Apparent motivation (espionage, financial, etc.)
```

### 2. Collect Indicators
```
□ Network indicators (IPs, domains, URLs)
□ Host indicators (hashes, file paths, registry keys)
□ Behavioral indicators (TTPs)
□ Infrastructure patterns
□ Code signatures/artifacts
```

### 3. Map TTPs to ATT&CK
```
□ Initial Access technique
□ Execution methods
□ Persistence mechanisms
□ Privilege escalation
□ Defense evasion
□ Credential access
□ Discovery
□ Lateral movement
□ Collection
□ Exfiltration
□ Command and control
```

### 4. Build Actor Dossier
```
ACTOR PROFILE TEMPLATE:

Identity:
  - Tracking ID: [APT-XX / UNC-XXXX]
  - Aliases: [Other names used by vendors]
  - First Seen: [Date]
  - Last Seen: [Date]
  - Status: [Active/Inactive]

Attribution:
  - Suspected Origin: [Country]
  - Confidence: [Low/Medium/High]
  - Supporting Evidence: [Summary]

Motivation:
  - Primary: [Espionage/Financial/Hacktivism/etc.]
  - Targeting: [Industries, regions, victim types]

Capabilities:
  - Sophistication: [Low/Medium/High/Advanced]
  - Resources: [Estimated]
  - Known Tools: [List]
  - Custom Malware: [Yes/No - details]

TTPs:
  - [ATT&CK mappings]

Infrastructure:
  - Patterns: [Hosting preferences, etc.]
  - Known Indicators: [Reference to IOC database]

Timeline:
  - [Chronological activity summary]
```

### 5. Track Over Time
```
□ Monitor for new activity
□ Update indicators continuously
□ Track TTP evolution
□ Note infrastructure changes
□ Document targeting shifts
□ Correlate with external reporting
```

### 6. Correlate Across Sources
```
□ Internal telemetry
□ Partner intelligence
□ Open source reporting
□ Vendor reports
□ Government advisories
□ Academic research
```

### 7. Produce Intelligence
```
□ Regular status updates
□ Activity alerts (new campaigns)
□ Trend analysis
□ Predictive assessments
□ Defensive recommendations
```

## Actor Classification

### By Motivation
| Type | Motivation | Examples |
|------|------------|----------|
| APT | Espionage | Nation-state actors |
| FIN | Financial gain | Ransomware groups |
| Hacktivist | Ideology | Anonymous variants |
| Insider | Various | Disgruntled employees |

### By Sophistication
```
TIER 1: Advanced
  - Custom tooling
  - 0-day usage
  - Sophisticated operational security
  - Long-term persistent access

TIER 2: Capable
  - Modified public tools
  - N-day exploitation
  - Moderate OPSEC
  - Campaign-based operations

TIER 3: Basic
  - Public tools (Cobalt Strike, etc.)
  - Known vulnerabilities
  - Limited OPSEC
  - Opportunistic targeting
```

## Tracking Infrastructure

### Data Model
```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   ACTOR     │────▶│  CAMPAIGN   │────▶│  INCIDENT   │
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │                   │
       │                   │                   │
       ▼                   ▼                   ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│    TTPs     │     │    IOCs     │     │   VICTIMS   │
└─────────────┘     └─────────────┘     └─────────────┘
```

### Confidence Levels
| Level | Definition | When to Use |
|-------|------------|-------------|
| High | Multiple independent sources, direct evidence | Confirmed attribution |
| Medium | Consistent with known TTPs, some corroboration | Likely attribution |
| Low | Single source, circumstantial | Possible attribution |

## Examples

### Good: Comprehensive Tracking
```
APT42 Profile Update (Q3 2024)

New Activity:
- 15 credential phishing campaigns observed
- New custom backdoor variant deployed
- Expanded targeting to energy sector

TTP Evolution:
- Shifted from spearphishing to watering holes
- New persistence via scheduled tasks
- Updated C2 protocol (HTTPS → DNS-over-HTTPS)

Infrastructure:
- 12 new domains registered
- Shifted to bulletproof hosting
- Using compromised legitimate sites

Recommendation:
- Update detection rules for new backdoor
- Block identified infrastructure
- Alert energy sector customers
```

### Good: Attribution Assessment
```
Attribution: APT42 (High Confidence)

Evidence:
1. Malware code overlap (85% similarity to known APT42 tool)
2. Infrastructure reuse (3 domains previously attributed)
3. TTP consistency (same initial access, same C2 pattern)
4. Targeting alignment (matches APT42 interests)
5. Timing correlation (active during Iran business hours)

Alternative Hypotheses:
- False flag: Low probability (code overlap too specific)
- Shared tooling: Medium probability (but infrastructure unique)

Conclusion: High confidence APT42 attribution
```

## Anti-patterns

### ❌ Single-Source Attribution
```
DON'T: Attribute based on one indicator
WHY: IOCs can be planted, tools shared
FIX: Require multiple independent data points
```

### ❌ Static Profiles
```
DON'T: Create profile and never update
WHY: Actors evolve constantly
FIX: Continuous monitoring and updates
```

### ❌ Indicator Hoarding
```
DON'T: Collect IOCs without context
WHY: IOCs without TTPs are less useful
FIX: Always capture behavioral context
```

### ❌ Overconfident Attribution
```
DON'T: "Definitely APT29" without evidence
WHY: Misattribution has consequences
FIX: Use confidence levels, show evidence
```

### ❌ Ignoring OPSEC Evolution
```
DON'T: Assume actors won't change
WHY: Sophisticated actors adapt
FIX: Track infrastructure and TTP changes
```

## Integration Points

- SIEM: Feed IOCs for detection
- SOAR: Automate response playbooks
- TIP: Central intelligence repository
- EDR: Behavioral detection rules
- Network: Block malicious infrastructure

## Metrics

- Actors tracked
- IOC freshness (time since last update)
- Detection rate (incidents attributed)
- Lead time (detection before public reporting)
- Coverage (ATT&CK techniques mapped)

## References

- TAG Blog: Actor reports
- MITRE ATT&CK Groups
- Mandiant APT reports
- CISA advisories
