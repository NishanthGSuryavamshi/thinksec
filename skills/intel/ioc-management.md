# Skill: IOC Management

> Curating, contextualizing, and sharing Indicators of Compromise effectively, derived from TAG's approach to threat intelligence sharing.

## When to Use

- Processing indicators from investigations
- Building detection rules
- Sharing intelligence with partners
- Managing a threat intelligence platform
- Correlating across data sources

## Prerequisites

- IOC storage system (TIP, database)
- Enrichment data sources
- Distribution channels
- Quality assurance process
- Stakeholder requirements

## Steps

### 1. Collect Indicators
```
□ Extract from investigations
□ Ingest from partners
□ Parse from reports
□ Gather from honeypots/sensors
□ Derive from malware analysis
```

### 2. Normalize Format
```
STANDARD FORMATS:

Network:
  - IP: xxx.xxx.xxx.xxx (IPv4) or full IPv6
  - Domain: lowercase, no trailing dot
  - URL: full URL with path, defanged for sharing
  - Email: full address

Host:
  - Hash: SHA256 preferred, MD5/SHA1 for compatibility
  - Filename: exact name, path if relevant
  - Registry: full key path

Behavioral:
  - MITRE ATT&CK ID
  - YARA rule
  - Sigma rule
```

### 3. Enrich with Context
```
FOR EACH IOC:
  □ Type (IP, domain, hash, etc.)
  □ First seen date
  □ Last seen date
  □ Confidence level
  □ Threat type (malware family, actor)
  □ Campaign association
  □ Kill chain phase
  □ Related indicators
  □ Source
```

### 4. Assess Quality
```
QUALITY CRITERIA:

Accuracy:
  □ Is this actually malicious?
  □ Could it be legitimate infrastructure?
  □ Is it a shared service (CDN, hosting)?

Relevance:
  □ Is it still active?
  □ Is it applicable to our environment?
  □ Is it actionable?

Completeness:
  □ Is context provided?
  □ Can it be understood without original report?
```

### 5. Score Confidence
```
CONFIDENCE LEVELS:

HIGH (90-100%):
  - Direct observation in incident
  - Multiple independent sources
  - Confirmed malicious behavior

MEDIUM (50-89%):
  - Single reliable source
  - Corroborating circumstantial evidence
  - Matches known patterns

LOW (1-49%):
  - Single unverified source
  - Circumstantial only
  - May have legitimate uses
```

### 6. Manage Lifecycle
```
IOC LIFECYCLE:

New → Active → Aging → Expired → Archived

AGING CONSIDERATIONS:
  - IPs: Short lifespan (days to weeks)
  - Domains: Medium lifespan (weeks to months)
  - Hashes: Long lifespan (months to years)
  - TTPs: Longest lifespan (years)

EXPIRATION TRIGGERS:
  - Time-based (no activity in X days)
  - Infrastructure change confirmed
  - False positive identified
  - Actor abandoned
```

### 7. Distribute Appropriately
```
DISTRIBUTION LEVELS:

TLP:CLEAR - Public
  - General threat information
  - Aged/expired IOCs
  
TLP:GREEN - Community
  - Shareable with security community
  - Detection-focused IOCs

TLP:AMBER - Limited
  - Specific partner sharing
  - Active investigation IOCs

TLP:RED - Restricted
  - Named victim information
  - Sources and methods
```

### 8. Enable Detection
```
IOC → DETECTION RULE

For each IOC type, create:
  □ SIEM query
  □ EDR rule
  □ Network detection signature
  □ YARA rule (for hashes)
  □ Sigma rule (for behavior)
```

## IOC Data Model

```yaml
indicator:
  type: domain
  value: malicious-domain.com
  
  # Temporal
  first_seen: 2024-01-15
  last_seen: 2024-03-20
  
  # Classification
  threat_type: c2
  malware_family: BACKDOOR.X
  actor: APT-42
  campaign: Operation-Name
  kill_chain_phase: command-and-control
  
  # Quality
  confidence: high
  source: internal-investigation
  tlp: amber
  
  # Context
  description: "C2 domain used in Q1 2024 campaign"
  related_indicators:
    - ip: 192.168.1.1
    - hash: abc123...
  
  # Detection
  detection_rule: |
    SELECT * FROM dns WHERE query = 'malicious-domain.com'
```

## Sharing Format Examples

### STIX 2.1
```json
{
  "type": "indicator",
  "spec_version": "2.1",
  "id": "indicator--uuid",
  "created": "2024-01-15T00:00:00Z",
  "modified": "2024-01-15T00:00:00Z",
  "name": "Malicious Domain",
  "pattern": "[domain-name:value = 'malicious-domain.com']",
  "pattern_type": "stix",
  "valid_from": "2024-01-15T00:00:00Z",
  "labels": ["malicious-activity"],
  "confidence": 90
}
```

### CSV (Simple)
```csv
type,indicator,first_seen,last_seen,threat_type,confidence,description
domain,malicious-domain.com,2024-01-15,2024-03-20,c2,high,APT-42 C2 domain
ip,192.168.1.1,2024-01-15,2024-02-01,c2,medium,Associated C2 IP
hash,abc123...,2024-01-15,,malware,high,BACKDOOR.X sample
```

### GitHub IOC Repo (TAG Style)
```
threat-team/
├── 2024/
│   ├── apt42_q1_campaign/
│   │   ├── domains.txt
│   │   ├── ips.txt
│   │   ├── hashes.txt
│   │   ├── yara_rules.yar
│   │   └── README.md
```

## Examples

### Good: Contextualized IOC
```
Indicator: evil-update.com

Context:
- Type: Domain
- Threat: APT-42 C2
- Campaign: Operation FakeUpdate
- First Seen: 2024-01-15
- Last Seen: 2024-03-20 (active)
- Confidence: High
- Source: Incident response investigation
- Related: 
  - IP: 198.51.100.1 (hosting)
  - Hash: abc123... (dropper that beacons here)
- Kill Chain: Command and Control
- Detection: DNS query monitoring

Note: Domain mimics legitimate software update site.
Check for beacon pattern: POST /update/check every 3600s
```

### Bad: Raw IOC List
```
❌ Just a list with no context:
evil-update.com
198.51.100.1
abc123...

WHY BAD:
- No threat context
- No confidence level
- No temporal information
- Not actionable
```

## Anti-patterns

### ❌ IOC Dumping
```
DON'T: Share massive lists without context
WHY: Noise, false positives, alert fatigue
FIX: Curate, contextualize, prioritize
```

### ❌ Stale Indicators
```
DON'T: Keep old IOCs active indefinitely
WHY: IP/domain reuse, false positives
FIX: Lifecycle management, expiration
```

### ❌ No Confidence Levels
```
DON'T: Treat all IOCs as equal confidence
WHY: Generates false positives
FIX: Score and communicate confidence
```

### ❌ Shared Infrastructure Blindness
```
DON'T: Block entire CDNs, cloud providers
WHY: Collateral damage to legitimate services
FIX: Add specificity (URL path, timing, etc.)
```

### ❌ Over-classification
```
DON'T: Mark everything TLP:RED
WHY: Prevents useful sharing
FIX: Classify appropriately for content
```

## Quality Metrics

- False positive rate
- Detection effectiveness
- IOC freshness (avg age)
- Context completeness
- Partner feedback score

## References

- google/threat-team (GitHub IOC repo)
- STIX/TAXII standards
- Traffic Light Protocol (TLP)
- MITRE ATT&CK
