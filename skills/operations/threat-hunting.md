# Skill: Threat Hunting

> Proactively searching for threats that evade detection, derived from Mandiant's threat hunting methodology.

## When to Use

- Proactive security posture improvement
- New threat intelligence received
- Post-incident validation
- Detection gap assessment
- High-value asset protection

## Prerequisites

- Access to security telemetry (logs, EDR, network)
- Threat intelligence sources
- Hunting hypotheses or IOCs
- Query/analysis capabilities
- Documentation system

## Steps

### 1. Develop Hypothesis
```
HYPOTHESIS SOURCES:

Intelligence-Driven:
  - New threat actor TTPs
  - New malware family
  - Industry-specific threats
  - Partner intelligence

Analytics-Driven:
  - Anomaly detection output
  - Baseline deviation
  - Statistical outliers

Situational:
  - Post-incident validation
  - M&A activity
  - Geopolitical events
  - Insider threat concerns

HYPOTHESIS FORMAT:
"If [threat actor/technique] targeted our environment,
we would expect to see [observable evidence]
in [data source]."
```

### 2. Plan the Hunt
```
HUNT PLAN:
□ Hypothesis statement
□ Data sources required
□ Time range to search
□ Query/analysis approach
□ Expected outcomes
□ Escalation criteria
□ Documentation method

DATA SOURCES:
  - EDR telemetry
  - Network logs (DNS, proxy, firewall)
  - Authentication logs
  - Cloud audit logs
  - Email logs
  - Endpoint logs
```

### 3. Collect & Query Data
```
QUERY APPROACH:

Known IOCs:
  □ Search for specific indicators
  □ Hash matches
  □ Domain/IP matches
  □ File path patterns

TTP-Based:
  □ Search for technique patterns
  □ Process relationships
  □ Command patterns
  □ Network behavior

Anomaly-Based:
  □ Statistical outliers
  □ Rare events
  □ First occurrences
  □ Behavioral deviations
```

### 4. Analyze Results
```
TRIAGE FINDINGS:

For each result:
  □ Is this expected behavior?
  □ Is there a benign explanation?
  □ Does context support malicious?
  □ What additional data needed?

CLASSIFICATION:
  - True Positive: Confirmed threat
  - Suspicious: Needs investigation
  - False Positive: Benign, refine query
  - Expected: Known activity
```

### 5. Investigate Positives
```
IF SUSPICIOUS/POSITIVE:
  □ Gather additional context
  □ Expand scope (related systems)
  □ Check timeline (before/after)
  □ Correlate with other sources
  □ Determine if incident

ESCALATION CRITERIA:
  - Confirmed malicious activity → IR process
  - Suspicious, can't rule out → Deeper investigation
  - Policy violation → Appropriate team
```

### 6. Document & Improve
```
HUNT DOCUMENTATION:
  □ Hypothesis tested
  □ Data sources used
  □ Queries executed
  □ Findings summary
  □ Actions taken
  □ Recommendations

DETECTION IMPROVEMENT:
  □ Convert hunt to detection rule
  □ Improve logging gaps found
  □ Update threat model
  □ Share intelligence
```

## Hunting Techniques

### TTP-Based Hunting

```
MITRE ATT&CK MAPPING:

Initial Access (TA0001):
  - Search for: Phishing artifacts, exploit patterns

Execution (TA0002):
  - Search for: PowerShell, WMI, scripting engines

Persistence (TA0003):
  - Search for: Registry run keys, scheduled tasks, services

Privilege Escalation (TA0004):
  - Search for: Token manipulation, UAC bypass

Defense Evasion (TA0005):
  - Search for: Obfuscation, disabling security tools

Credential Access (TA0006):
  - Search for: LSASS access, Mimikatz patterns

Discovery (TA0007):
  - Search for: Network scanning, enumeration

Lateral Movement (TA0008):
  - Search for: PsExec, WMI, RDP anomalies

Collection (TA0009):
  - Search for: Archive creation, staging

Exfiltration (TA0010):
  - Search for: Large transfers, unusual destinations
```

### Anomaly Hunting

```
STATISTICAL APPROACHES:

Frequency Analysis:
  - Rare parent-child process relationships
  - Unusual command line arguments
  - Infrequent network destinations

First Occurrence:
  - New processes never seen before
  - New network connections
  - New file paths

Stacking:
  - Least common values in field
  - Outliers in aggregation
  - Unusual combinations
```

### IOC Hunting

```
SEARCH PATTERNS:

Network:
  SELECT * FROM dns_logs 
  WHERE query IN (known_bad_domains)

  SELECT * FROM proxy_logs
  WHERE dest_ip IN (known_bad_ips)

Host:
  SELECT * FROM process_events
  WHERE hash IN (known_bad_hashes)

  SELECT * FROM file_events
  WHERE path LIKE '%suspicious_pattern%'
```

## Hunt Queries (Examples)

### PowerShell Hunting
```sql
-- Encoded PowerShell
SELECT * FROM process_events
WHERE 
  process_name = 'powershell.exe'
  AND (
    command_line LIKE '%encodedcommand%'
    OR command_line LIKE '%-enc %'
    OR command_line LIKE '%-e %'
  )
```

### Lateral Movement Hunting
```sql
-- PsExec-like behavior
SELECT * FROM process_events
WHERE
  parent_process_name = 'services.exe'
  AND process_name NOT IN (known_services)
  AND process_name LIKE '%.exe'
```

### Persistence Hunting
```sql
-- Suspicious scheduled tasks
SELECT * FROM scheduled_tasks
WHERE
  created_time > (NOW - 7 days)
  AND action_path NOT LIKE 'C:\Windows\%'
  AND action_path NOT LIKE 'C:\Program Files%'
```

## Hunt Report Template

```markdown
# Threat Hunt Report

## Hunt Summary
- **Hypothesis**: [What we're looking for]
- **Date Range**: [Time period searched]
- **Status**: [Complete/Ongoing]
- **Findings**: [Summary]

## Methodology
### Data Sources
[What data was searched]

### Queries
[Queries/techniques used]

## Findings

### True Positives
[Confirmed threats found]

### Suspicious Activity
[Items requiring follow-up]

### False Positives
[Benign matches to refine]

## Recommendations
### Immediate Actions
[What to do now]

### Detection Improvements
[New rules to create]

### Visibility Gaps
[Logging improvements needed]

## Metrics
- Records searched: X
- Unique hosts covered: X
- Time invested: X hours
- Findings: X suspicious, X confirmed
```

## Anti-patterns

### ❌ Hunting Without Hypothesis
```
DON'T: Random searching without direction
WHY: Inefficient, unlikely to find threats
FIX: Formulate hypothesis before hunting
```

### ❌ Alert-Only Focus
```
DON'T: Only look at what alerts fired
WHY: Miss threats that evaded detection
FIX: Hunt for what alerts don't catch
```

### ❌ No Documentation
```
DON'T: Hunt without documenting queries
WHY: Can't repeat, can't improve
FIX: Document everything
```

### ❌ Ignoring False Positives
```
DON'T: Just dismiss false positives
WHY: Opportunity to improve detection
FIX: Use to refine queries and rules
```

## Metrics

- Hunts conducted
- True positives found
- Mean time in environment (for found threats)
- Detection rules created from hunts
- Coverage (TTPs hunted for)

## References

- MITRE ATT&CK
- Mandiant threat hunting
- SANS threat hunting
- Sqrrl hunting methodology
