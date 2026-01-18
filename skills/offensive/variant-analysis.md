# Skill: Variant Analysis

> Finding related vulnerabilities by analyzing patches and bug patterns, derived from Project Zero's approach to fixing bug classes.

## When to Use

- After a vulnerability is patched
- When studying a CVE or security advisory
- Looking for incomplete fixes
- Searching for bug class instances
- Maximizing impact of research effort

## Prerequisites

- Original vulnerability details
- Access to patch/fix
- Source code (ideally before and after fix)
- Understanding of the bug class

## Steps

### 1. Understand the Original Bug
```
□ What is the vulnerability type?
□ What is the root cause?
□ What is the trigger condition?
□ What is the exploitable primitive?
□ Document the "bug pattern signature"
```

### 2. Analyze the Fix
```
□ What did the fix change?
□ Does the fix address root cause or symptom?
□ What assumptions does the fix make?
□ Are there edge cases not covered?
□ Is the fix applied consistently?
```

### 3. Identify Variant Types
```
VARIANT CATEGORIES:

1. Incomplete Fix
   - Same location, different trigger
   - Edge case not covered
   - Off-by-one in fix

2. Same Pattern, Different Location
   - Same bug pattern elsewhere in codebase
   - Similar code, same mistake

3. Same Root Cause, Different Manifestation
   - Underlying issue not addressed
   - Bug class still present

4. Regression
   - Fix reverted or broken by later change
   - New code reintroduces pattern
```

### 4. Search for Pattern
```
□ Define search signature (code pattern, API usage)
□ Search codebase for similar patterns
□ Check related/copied code
□ Review same developer's other code
□ Check similar functionality
```

### 5. Validate Candidates
```
□ Is the pattern actually vulnerable?
□ Is it reachable from attacker input?
□ Can it be triggered?
□ What is the impact?
```

### 6. Document Findings
```
□ Original vulnerability reference
□ Variant description
□ Relationship to original
□ Independent reproduction
□ Recommended comprehensive fix
```

## Search Techniques

### Code Pattern Search
```bash
# Example: Find similar integer overflow patterns
grep -rn "malloc.*\*.*\*" src/
grep -rn "width.*height" src/

# Example: Find similar API misuse
grep -rn "strncpy" src/ | grep -v "sizeof"
```

### Semantic Search (CodeQL)
```ql
// Find unchecked malloc return values
from FunctionCall fc
where fc.getTarget().getName() = "malloc"
  and not exists(IfStmt i | i.getCondition().getAChild*() = fc)
select fc, "Unchecked malloc"
```

### Diff Analysis
```bash
# Compare fix to find what changed
git diff CVE_COMMIT^..CVE_COMMIT

# Search for similar unfixed code
git grep "pattern_from_vulnerable_version"
```

## Variant Analysis Framework

```
┌─────────────────────────────────────────────────────────────────┐
│                    ORIGINAL VULNERABILITY                        │
│                           │                                      │
│    ┌──────────────────────┼──────────────────────┐              │
│    │                      │                      │              │
│    ▼                      ▼                      ▼              │
│ INCOMPLETE FIX      SAME PATTERN           SAME ROOT CAUSE      │
│    │                      │                      │              │
│    │                      │                      │              │
│    ▼                      ▼                      ▼              │
│ - Edge cases         - Other files         - Bug class         │
│ - Race conditions    - Copy-paste code     - Design flaw       │
│ - Type variations    - Similar functions   - API misuse        │
└─────────────────────────────────────────────────────────────────┘
```

## Examples

### Good: Incomplete Fix Variant
```
Original CVE: Integer overflow in image width calculation
Fix: Added check for width > MAX_WIDTH

Variant Found: 
- Fix didn't check height
- width * height can still overflow
- Reported as separate vulnerability
```

### Good: Pattern-Based Variant
```
Original: Buffer overflow in parse_png()
Pattern: memcpy(dst, src, user_controlled_length)

Search: grep for similar memcpy patterns
Found: Same pattern in parse_gif(), parse_bmp()
Result: 3 additional vulnerabilities
```

### Good: Root Cause Variant
```
Original: Type confusion in JavaScript engine optimization
Root Cause: Missing type check after optimization pass

Search: Other optimization passes with same issue
Found: 5 additional type confusions in different passes
Recommendation: Add systematic type verification
```

## Checklist: Fix Completeness

```
□ Does fix cover all code paths to vulnerability?
□ Does fix handle all input types/sizes?
□ Does fix account for integer edge cases?
□ Does fix work in multithreaded context?
□ Does fix apply to all affected versions/branches?
□ Is fix consistent with similar code elsewhere?
□ Does fix address root cause or just symptom?
```

## Anti-patterns

### ❌ Assuming Fix is Complete
```
DON'T: Trust that the patch fixes everything
WHY: 1/3 of in-the-wild 0-days are variants of patched bugs
FIX: Always analyze fix completeness
```

### ❌ Only Searching Same File
```
DON'T: Limit search to the fixed file
WHY: Same pattern likely exists elsewhere
FIX: Search entire codebase
```

### ❌ Ignoring Similar Functions
```
DON'T: Only check the exact vulnerable function
WHY: Copy-paste code spreads bugs
FIX: Check related/similar functions
```

### ❌ Stopping at First Variant
```
DON'T: Report first variant and move on
WHY: More variants likely exist
FIX: Systematic search for all instances
```

## Statistics (from Project Zero)

> "Over one-third of the zero-day vulnerabilities exploited in the wild we've analyzed in 2022 are variants of earlier patched vulnerabilities."

This validates the importance of variant analysis.

## Metrics

- Variants found per original bug
- Time from original fix to variant discovery
- Fix completeness rate
- Codebase coverage of pattern search

## References

- "The More You Know, The More You Know You Don't Know" (P0)
- Project Zero: In-the-Wild Root Cause Analyses
- "New initiatives to reduce vulnerability risks" (Google)
