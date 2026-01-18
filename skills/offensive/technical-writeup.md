# Skill: Technical Writeup

> Writing deep, educational vulnerability writeups that advance collective security knowledge, derived from Project Zero's publication standards.

## When to Use

- Publishing vulnerability research
- Documenting an exploit for educational purposes
- Sharing findings with the security community
- Creating internal security documentation
- Building a security research portfolio

## Prerequisites

- Vulnerability fully understood
- Root cause analysis complete
- Disclosure process followed
- Patch available (for public writeups)

## Steps

### 1. Define the Audience & Goal
```
□ Who will read this? (researchers, defenders, vendors)
□ What should they learn?
□ What level of detail is appropriate?
□ What's the educational value?
```

### 2. Structure the Narrative
```
STANDARD STRUCTURE:
1. Introduction / Summary
2. Background / Context
3. Vulnerability Details
4. Exploitation
5. Fix / Mitigation
6. Conclusion / Lessons
```

### 3. Write the Introduction
```
□ Hook: Why should the reader care?
□ Summary: What was found?
□ Impact: What could an attacker do?
□ Scope: What's covered in this writeup?
```

### 4. Provide Background
```
□ Target description (what is the software?)
□ Relevant architecture (components, data flow)
□ Security model (trust boundaries, threats)
□ Prior art (related research, if any)
□ Just enough context—no more
```

### 5. Explain the Vulnerability
```
□ Step-by-step discovery narrative
□ Code snippets (annotated)
□ Diagrams where helpful
□ Root cause explanation
□ Why it matters (impact)
```

### 6. Detail the Exploitation (If Appropriate)
```
□ Exploit strategy
□ Challenges overcome
□ Key techniques used
□ Proof of concept (responsible)
□ Limitations / requirements
```

### 7. Discuss the Fix
```
□ How was it fixed?
□ Is the fix complete?
□ Alternative fixes considered?
□ How to detect exploitation?
□ Mitigation for unpatched systems
```

### 8. Conclude with Lessons
```
□ What can we learn?
□ Broader implications
□ Recommendations for developers
□ Future research directions
□ Acknowledgments
```

### 9. Polish & Review
```
□ Technical accuracy check
□ Clarity for target audience
□ Remove unnecessary jargon
□ Add diagrams where text is unclear
□ Proofread
```

## Writing Guidelines

### Be Precise
```
GOOD: "The function allocates width * height * 4 bytes, 
       but the multiplication can overflow for large images."

BAD:  "The function has a buffer overflow."
```

### Show, Don't Just Tell
```
GOOD: Show the vulnerable code with annotations
BAD:  "The code is vulnerable"
```

### Use Diagrams
```
Memory layout before/after exploitation:

BEFORE:                    AFTER:
┌────────────┐            ┌────────────┐
│  header    │            │  header    │
├────────────┤            ├────────────┤
│  data[n]   │            │  overflow  │──┐
├────────────┤            ├────────────┤  │
│  metadata  │            │  corrupted │←─┘
└────────────┘            └────────────┘
```

### Code Annotations
```c
// Vulnerable: no check for integer overflow
size_t alloc_size = width * height * 4;  // [1] overflow here
char *buf = malloc(alloc_size);          // [2] undersized allocation
memcpy(buf, data, actual_size);          // [3] heap overflow
```

## Writeup Template

```markdown
# [Vulnerability Title]

## Summary
[2-3 sentence overview: what, where, impact]

## Background
### Target Overview
[What is the software? What does it do?]

### Relevant Architecture  
[Components, data flow, trust boundaries]

### Threat Model
[What attacks are in scope?]

## Vulnerability Details
### Discovery
[How was it found?]

### Technical Analysis
[Deep dive with code, diagrams]

### Root Cause
[Why does this bug exist?]

## Exploitation
### Strategy
[High-level approach]

### Implementation
[Key techniques, challenges]

### Proof of Concept
[Responsible PoC]

### Limitations
[What's required, what doesn't work]

## Fix Analysis
### Patch
[How it was fixed]

### Completeness
[Is the fix sufficient?]

### Detection
[How to detect exploitation]

## Conclusion
### Lessons Learned
[What can we learn?]

### Recommendations
[For developers, defenders]

## Timeline
- [Date]: Vulnerability discovered
- [Date]: Reported to vendor
- [Date]: Patch released
- [Date]: Public disclosure

## References
- [Related research]
- [Vendor advisory]

## Acknowledgments
[Credits]
```

## Examples

### Good: Clear Narrative Flow
```
We discovered a vulnerability in the image parsing library 
that allows remote code execution. The bug is an integer 
overflow when calculating buffer sizes for large images.

When parsing an image header, the library multiplies 
width × height × bytes-per-pixel to determine allocation 
size. For specially crafted images, this calculation 
overflows, resulting in a small allocation...
```

### Good: Annotated Code
```c
// From src/image.c:142
int parse_image(uint8_t *data, size_t len) {
    uint32_t width = read_u32(data);      // attacker controlled
    uint32_t height = read_u32(data + 4); // attacker controlled
    
    // BUG: integer overflow when width * height > UINT32_MAX
    size_t size = width * height * 4;     // [1]
    
    uint8_t *buf = malloc(size);          // undersized buffer
    
    // heap overflow: actual_size >> size
    memcpy(buf, data + 8, width * height * 4); // [2]
}
```

## Anti-patterns

### ❌ Wall of Code
```
DON'T: Dump entire source files
WHY: Reader loses focus, misses key parts
FIX: Show only relevant snippets, annotate them
```

### ❌ Jargon Without Explanation
```
DON'T: "We used a type confusion to get AAR/AAW"
WHY: Excludes less experienced readers
FIX: Explain terms or link to resources
```

### ❌ Missing Context
```
DON'T: Jump straight to the bug
WHY: Reader can't understand significance
FIX: Provide background first
```

### ❌ Irresponsible Disclosure
```
DON'T: Publish weaponized exploits
WHY: Harms users who can't update quickly
FIX: Provide PoC that demonstrates bug, not full weaponization
```

### ❌ No Lessons
```
DON'T: End with just "here's the bug"
WHY: Misses educational opportunity
FIX: Extract generalizable lessons
```

## Quality Checklist

- [ ] Would a competent engineer understand the bug after reading?
- [ ] Is the root cause clearly explained?
- [ ] Are code snippets minimal and annotated?
- [ ] Are diagrams used where helpful?
- [ ] Is the fix discussed?
- [ ] Are there actionable takeaways?
- [ ] Is the writing clear and precise?

## References

- Project Zero blog (exemplary writeups)
- Google Security Blog
- Phrack articles (classic examples)
