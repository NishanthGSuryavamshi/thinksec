# Skill: Root Cause Analysis

> Systematic approach to understanding why vulnerabilities exist, derived from Project Zero's in-the-wild analysis program.

## When to Use

- After finding a vulnerability
- Analyzing an exploit found in the wild
- Understanding a patch to check completeness
- Identifying bug classes for systematic fixing
- Learning from security incidents

## Prerequisites

- Access to vulnerability details/exploit
- Source code or binary for analysis
- Debugging environment
- Understanding of the software architecture

## Steps

### 1. Establish the Facts
```
□ What is the vulnerability? (precise technical description)
□ What is the impact? (what can attacker achieve)
□ How was it discovered? (fuzzing, audit, in-the-wild)
□ What component is affected?
□ What versions are affected?
```

### 2. Reproduce & Understand
```
□ Create minimal reproduction case
□ Trace execution path to vulnerability
□ Identify the exact failing assumption
□ Understand the attacker's perspective
□ Map the exploit primitive (read, write, execute, etc.)
```

### 3. Ask "Why" Repeatedly (5 Whys)
```
Why #1: Why did this specific bug occur?
        → [Immediate cause]

Why #2: Why did that happen?
        → [Contributing factor]

Why #3: Why was that possible?
        → [Deeper cause]

Why #4: Why wasn't it caught?
        → [Process/tooling gap]

Why #5: Why does this pattern exist?
        → [Root cause / systemic issue]
```

### 4. Classify the Bug
```
□ Identify the CWE (Common Weakness Enumeration)
□ Determine the bug class:
  - Memory safety (buffer overflow, UAF, etc.)
  - Type safety (type confusion, etc.)
  - Logic (auth bypass, TOCTOU, etc.)
  - Injection (SQL, command, etc.)
  - Crypto (weak algorithm, misuse, etc.)
□ Check if this is a variant of known bugs
□ Identify the pattern signature
```

### 5. Analyze the Fix
```
□ Is the fix addressing the root cause?
□ Is the fix complete? (all code paths?)
□ Does the fix introduce new issues?
□ Are there similar patterns elsewhere?
□ Could this have been prevented structurally?
```

### 6. Identify Systemic Factors
```
□ Why did the bug class exist? (language, API, design)
□ Why wasn't it caught earlier? (testing, review, tools)
□ What structural change would prevent the class?
□ What detection would catch future instances?
```

### 7. Document Findings
```
□ Technical root cause
□ Bug classification
□ Contributing factors
□ Fix assessment
□ Structural recommendations
□ Detection strategies
```

## Root Cause Categories

### Code-Level Causes
| Cause | Example | Structural Fix |
|-------|---------|----------------|
| Missing bounds check | Buffer overflow | Safe APIs, sanitizers |
| Integer overflow | Undersized allocation | Safe integer arithmetic |
| Use-after-free | Dangling pointer | Ownership model, GC |
| Type confusion | Wrong cast | Strong typing, checks |
| Missing validation | Injection | Input validation framework |

### Design-Level Causes
| Cause | Example | Structural Fix |
|-------|---------|----------------|
| Confused deputy | Privilege escalation | Capability model |
| TOCTOU | Race condition | Atomic operations |
| Implicit trust | Auth bypass | Zero trust |
| Complex state machine | Logic bug | State machine verification |

### Process-Level Causes
| Cause | Example | Structural Fix |
|-------|---------|----------------|
| No fuzzing | Parsing bugs | Mandatory fuzzing |
| No code review | Logic bugs | Review requirements |
| No threat model | Design flaws | Security design review |

## Analysis Template

```markdown
# Root Cause Analysis: [CVE/Bug ID]

## Summary
[One-line description]

## Technical Details
- **Vulnerability Type**: [CWE-XXX]
- **Component**: [Affected component]
- **Discovery Method**: [How found]

## Immediate Cause
[What directly caused the vulnerability]

## Root Cause Chain
1. [Immediate cause]
2. [Why #1]
3. [Why #2]
4. [Why #3]
5. [Systemic root cause]

## Fix Analysis
- **Fix Approach**: [How it was fixed]
- **Fix Completeness**: [Complete/Partial]
- **Variants Remaining**: [Yes/No - details]

## Structural Recommendations
1. [Recommendation to prevent bug class]
2. [Detection improvement]
3. [Process improvement]

## Related Vulnerabilities
- [Similar bugs in same codebase]
- [Same bug class in other software]
```

## Examples

### Good: Memory Corruption RCA
```
Bug: Heap buffer overflow in image parser

Why #1: Write beyond allocated buffer
Why #2: Size calculated with integer overflow
Why #3: Unchecked multiplication of width × height × bpp
Why #4: No safe integer arithmetic library used
Why #5: Language (C) allows unsafe arithmetic by default

Root Cause: Unsafe arithmetic in memory-unsafe language
Structural Fix: Use safe integer library + AddressSanitizer
Detection: Fuzzing with sanitizers
```

### Good: Logic Bug RCA
```
Bug: Authentication bypass via parameter pollution

Why #1: Auth check used different param than handler
Why #2: Framework allowed duplicate params
Why #3: No canonicalization before auth check
Why #4: Auth and handler developed separately
Why #5: No unified security middleware

Root Cause: Decoupled auth from request handling
Structural Fix: Single auth middleware, canonical params
Detection: Security-focused integration tests
```

## Anti-patterns

### ❌ Stopping at Symptoms
```
DON'T: "Root cause: missing bounds check"
WHY: This is immediate cause, not root cause
FIX: Ask WHY the bounds check was missing
```

### ❌ Blaming Individuals
```
DON'T: "Root cause: developer error"
WHY: Not actionable, doesn't prevent recurrence
FIX: Focus on systemic factors
```

### ❌ Skipping Fix Analysis
```
DON'T: Assume the fix is complete
WHY: Incomplete fixes are common, variants remain
FIX: Always analyze fix for completeness
```

### ❌ Ignoring Patterns
```
DON'T: Treat each bug as unique
WHY: Miss opportunity for class-wide fixes
FIX: Look for bug class patterns
```

## Metrics

- Bugs per root cause category
- Variant rate (related bugs found)
- Fix completeness rate
- Time to structural fix
- Recurrence rate

## References

- Project Zero: Root Cause Analyses
- 0-days In-the-Wild tracking
- "The More You Know, The More You Know You Don't Know"
