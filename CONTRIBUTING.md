# Contributing to thinksec

Thank you for contributing to thinksec! This guide will help you submit high-quality skill files.

## Types of Contributions

### 1. New Skill Files
Add a skill file for a methodology not yet covered.

### 2. Improvements
Enhance existing skills with better examples, additional steps, or corrections.

### 3. Translations
Help make skills accessible in other languages.

### 4. Bug Fixes
Fix typos, broken links, or formatting issues.

## Skill File Guidelines

### Quality Standards

Every skill file must:

- [ ] Follow the template structure exactly
- [ ] Be actionable (someone can execute it)
- [ ] Include real-world examples
- [ ] Document anti-patterns (what NOT to do)
- [ ] Be technically accurate
- [ ] Cite sources where applicable

### Writing Style

| Do | Don't |
|----|-------|
| Use active voice | Use passive voice |
| Be specific and concrete | Be vague or abstract |
| Include code/command examples | Describe without showing |
| Use checklists (□) for steps | Write prose paragraphs |
| Define acronyms on first use | Assume reader knows all terms |

### Formatting

```markdown
# Skill: [Skill Name]

> One-line description of the skill.

## When to Use
[Bullet points of trigger conditions]

## Prerequisites
[What's needed before starting]

## Steps
### 1. First Major Step
```
□ Subtask with checkbox
□ Another subtask
```

### 2. Second Major Step
[Continue pattern...]

## Examples
### Good: [Example Title]
[Concrete example of good execution]

### Bad: [Example Title]
[What went wrong and why]

## Anti-patterns
### ❌ [Anti-pattern Name]
```
DON'T: [Bad practice]
WHY: [Reason it's problematic]
FIX: [Correct approach]
```

## Metrics
[How to measure success]

## References
[Sources and further reading]
```

## Submission Process

### 1. Check for Duplicates
Search existing skills to avoid duplication. If a similar skill exists, consider improving it instead.

### 2. Use the Template
Copy `templates/skill-template.md` as your starting point.

### 3. Choose the Right Category

| Category | Content |
|----------|---------|
| `offensive/` | Red team, pentesting, exploitation, vuln research |
| `defensive/` | Blue team, detection, hardening, monitoring |
| `analysis/` | Malware, forensics, reverse engineering |
| `intel/` | Threat intel, attribution, tracking, reporting |
| `engineering/` | Secure dev, crypto, architecture, tools |
| `operations/` | IR, SOC, hunting, security ops |

### 4. Name Your File
Use lowercase with hyphens: `skill-name.md`

Good: `memory-forensics.md`, `yara-rule-writing.md`
Bad: `Memory_Forensics.md`, `yaraRuleWriting.md`

### 5. Submit a Pull Request

Include in your PR description:
- What skill this covers
- Why it's valuable
- Your experience with this skill (optional)

## Review Criteria

Reviewers will check:

1. **Completeness** — All template sections filled
2. **Accuracy** — Technically correct
3. **Actionability** — Someone can follow the steps
4. **Originality** — Not duplicating existing content
5. **Quality** — Well-written, clear examples

## Code of Conduct

- Be respectful and constructive
- Focus on the content, not the contributor
- Assume good intent
- Help others improve their submissions

## Questions?

Open an issue with the `question` label.

---

## Quick Reference: Skill File Checklist

Before submitting, verify:

```
□ Used skill-template.md as base
□ Filled all required sections
□ "When to Use" has clear triggers
□ Steps are numbered and have checkboxes
□ At least one concrete example
□ At least one anti-pattern
□ Metrics defined
□ File named correctly (lowercase-with-hyphens.md)
□ Placed in correct category folder
□ Spell-checked
□ Links work
```
