# Skill: Secure Development

> Building security into the development lifecycle, derived from Google's secure development practices.

## When to Use

- Starting a new project
- Reviewing existing code security
- Establishing development standards
- Training developers
- Building CI/CD pipelines

## Prerequisites

- Development environment
- Version control
- CI/CD system
- Code review process
- Testing infrastructure

## Steps

### 1. Design for Security
```
THREAT MODELING:
□ Identify assets (what are you protecting?)
□ Identify threats (who might attack?)
□ Identify attack vectors (how might they attack?)
□ Design mitigations (how do you prevent it?)

SECURITY PRINCIPLES:
□ Least privilege
□ Defense in depth
□ Fail secure
□ Separation of concerns
□ Don't trust user input
□ Minimize attack surface
```

### 2. Choose Secure Defaults
```
FRAMEWORK SELECTION:
□ Use frameworks with security built-in
□ Prefer memory-safe languages where possible
□ Use vetted cryptography libraries
□ Use ORM instead of raw SQL
□ Use templating with auto-escaping

DEFAULT CONFIGURATIONS:
□ TLS required
□ Authentication required
□ CSRF protection enabled
□ Security headers set
□ Logging enabled
```

### 3. Implement Secure Coding Practices
```
INPUT VALIDATION:
□ Validate all input (type, length, format, range)
□ Allowlist over denylist
□ Validate on server side
□ Sanitize for output context

OUTPUT ENCODING:
□ Encode for output context (HTML, JS, URL, SQL)
□ Use parameterized queries
□ Use template auto-escaping
□ Set proper Content-Type headers

AUTHENTICATION:
□ Use proven auth frameworks
□ Strong password requirements
□ Multi-factor authentication
□ Secure session management
□ Rate limiting

AUTHORIZATION:
□ Check permissions on every request
□ Use role-based access control
□ Deny by default
□ Audit access decisions

ERROR HANDLING:
□ Don't leak sensitive info in errors
□ Log security-relevant events
□ Fail closed
□ Generic error messages to users

CRYPTOGRAPHY:
□ Use Tink or equivalent library
□ Don't implement crypto primitives
□ Use authenticated encryption
□ Secure key management
```

### 4. Set Up Security Tooling
```
STATIC ANALYSIS:
□ Linter with security rules
□ SAST tool (Semgrep, CodeQL)
□ Dependency scanner
□ Secret scanner

DYNAMIC ANALYSIS:
□ Sanitizers (ASan, MSan, TSan)
□ Fuzzing
□ DAST scanner

CI/CD INTEGRATION:
□ Run security tools on every PR
□ Block merges on critical findings
□ Track security metrics
□ Automate dependency updates
```

### 5. Secure Dependencies
```
DEPENDENCY MANAGEMENT:
□ Pin dependency versions
□ Use lockfiles
□ Audit dependencies regularly
□ Monitor for vulnerabilities
□ Minimal dependencies

TOOLS:
□ Dependabot / Renovate
□ npm audit / pip audit
□ Snyk / OWASP Dependency-Check
```

### 6. Code Review for Security
```
SECURITY REVIEW CHECKLIST:
□ Input validation present?
□ Output encoding correct?
□ Authentication/authorization checked?
□ Sensitive data protected?
□ Cryptography used correctly?
□ Error handling secure?
□ Logging appropriate?
□ No hardcoded secrets?
□ Dependencies safe?
```

### 7. Test for Security
```
SECURITY TESTING:
□ Unit tests for security controls
□ Integration tests for auth/authz
□ Fuzzing for parsers
□ Penetration testing
□ Security regression tests

TEST CASES:
□ Boundary values
□ Invalid input
□ Missing authentication
□ Privilege escalation
□ Injection attempts
□ Race conditions
```

### 8. Secure Deployment
```
DEPLOYMENT SECURITY:
□ Secrets management (not in code)
□ Environment separation
□ Minimal permissions
□ Security monitoring
□ Incident response plan
□ Backup and recovery
```

## Security Checklist by Language

### Python
```python
# ✓ Use parameterized queries
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))

# ✗ Never string interpolation
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

# ✓ Use secrets module for tokens
import secrets
token = secrets.token_urlsafe(32)

# ✗ Never use random for security
import random
token = random.randint(0, 1000000)  # Predictable!

# ✓ Hash passwords properly
from argon2 import PasswordHasher
ph = PasswordHasher()
hash = ph.hash(password)

# ✗ Never use fast hashes
import hashlib
hash = hashlib.md5(password).hexdigest()  # Too fast!
```

### JavaScript/TypeScript
```javascript
// ✓ Use parameterized queries
db.query('SELECT * FROM users WHERE id = $1', [userId]);

// ✗ Never string concatenation
db.query(`SELECT * FROM users WHERE id = ${userId}`);

// ✓ Use textContent for user data
element.textContent = userInput;

// ✗ Never innerHTML with user data
element.innerHTML = userInput;  // XSS!

// ✓ Use crypto.randomUUID()
const id = crypto.randomUUID();

// ✗ Never Math.random() for security
const token = Math.random().toString(36);  // Predictable!
```

### Go
```go
// ✓ Use parameterized queries
db.Query("SELECT * FROM users WHERE id = ?", userID)

// ✗ Never fmt.Sprintf for SQL
db.Query(fmt.Sprintf("SELECT * FROM users WHERE id = %s", userID))

// ✓ Use html/template (auto-escapes)
tmpl.Execute(w, data)

// ✗ Never text/template for HTML
// (doesn't escape)

// ✓ Use crypto/rand
buf := make([]byte, 32)
crypto_rand.Read(buf)

// ✗ Never math/rand for security
n := math_rand.Int()  // Predictable!
```

## CI/CD Security Pipeline

```yaml
# .github/workflows/security.yml
name: Security Checks

on: [push, pull_request]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      # Secret scanning
      - name: Scan for secrets
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          
      # Dependency scanning
      - name: Check dependencies
        run: |
          pip audit
          # or: npm audit, go list -m -json all | nancy
          
      # SAST
      - name: Run Semgrep
        uses: returntocorp/semgrep-action@v1
        with:
          config: p/security-audit
          
      # Container scanning
      - name: Scan container
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: 'myapp:latest'
```

## Examples

### Good: Input Validation
```python
from pydantic import BaseModel, validator, constr

class UserInput(BaseModel):
    username: constr(min_length=3, max_length=50, regex=r'^[a-zA-Z0-9_]+$')
    email: str
    age: int
    
    @validator('email')
    def validate_email(cls, v):
        if '@' not in v:
            raise ValueError('Invalid email')
        return v.lower()
    
    @validator('age')
    def validate_age(cls, v):
        if not 0 <= v <= 150:
            raise ValueError('Invalid age')
        return v

# Use it
try:
    user = UserInput(**request.json())
except ValidationError as e:
    return error_response(400, "Invalid input")
```

### Good: Secure Session
```python
from flask import Flask, session
import secrets

app = Flask(__name__)
app.secret_key = secrets.token_bytes(32)
app.config.update(
    SESSION_COOKIE_SECURE=True,      # HTTPS only
    SESSION_COOKIE_HTTPONLY=True,    # No JS access
    SESSION_COOKIE_SAMESITE='Lax',   # CSRF protection
    PERMANENT_SESSION_LIFETIME=3600  # 1 hour
)
```

## Anti-patterns

### ❌ Security as Afterthought
```
DON'T: "We'll add security later"
WHY: Retrofitting is expensive and incomplete
FIX: Design security in from the start
```

### ❌ Rolling Your Own Crypto
```
DON'T: Implement encryption algorithms
WHY: Subtle bugs lead to total failure
FIX: Use Tink, libsodium, or equivalent
```

### ❌ Trusting Client-Side Validation
```
DON'T: Only validate in JavaScript
WHY: Client can bypass all checks
FIX: Always validate server-side
```

### ❌ Secrets in Code
```
DON'T: Hardcode API keys, passwords
WHY: Exposed in version control
FIX: Use environment variables, secret managers
```

## Metrics

- SAST findings over time
- Dependency vulnerabilities
- Time to fix security bugs
- Security test coverage
- Penetration test findings

## References

- OWASP Top 10
- OWASP Cheat Sheets
- Google secure coding guidelines
- CWE/SANS Top 25
