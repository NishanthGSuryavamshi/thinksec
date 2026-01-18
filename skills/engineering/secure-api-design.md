# Skill: Secure API Design

> Designing APIs that are secure by default and hard to misuse, derived from Google ISE's Tink philosophy.

## When to Use

- Designing new APIs (internal or external)
- Reviewing API security
- Refactoring insecure interfaces
- Building security libraries
- Creating developer tools

## Prerequisites

- Understanding of threat model
- Knowledge of common API misuse patterns
- Familiarity with target language idioms
- User research (who will use this API?)

## Steps

### 1. Define Security Goals
```
□ What security properties must the API provide?
□ What are the consequences of misuse?
□ Who are the users? (security experts? developers?)
□ What are the trust boundaries?
```

### 2. Identify Misuse Patterns
```
COMMON API MISUSE:
  - Wrong parameter order
  - Missing validation
  - Incorrect defaults
  - Confusing overloads
  - Ignoring return values
  - Improper error handling
  - Race conditions
  - Resource leaks
```

### 3. Apply Secure-by-Default Principles
```
PRINCIPLE 1: Safe Defaults
  - Default behavior should be secure
  - Dangerous options require explicit opt-in
  - Never default to "off" for security features

PRINCIPLE 2: Hard to Misuse
  - Make incorrect usage fail loudly
  - Use type system to prevent errors
  - Minimize configuration surface

PRINCIPLE 3: Easy to Use Correctly
  - Simple API for common cases
  - Clear documentation
  - Good error messages

PRINCIPLE 4: Fail Securely
  - Errors should not leak information
  - Fail closed, not open
  - Clean up resources on failure
```

### 4. Design the Interface
```
FOR EACH OPERATION:
  □ What's the minimal required input?
  □ What should the default behavior be?
  □ What errors are possible?
  □ How can the user misuse this?
  □ How can we prevent misuse?
```

### 5. Use Type System for Safety
```
TECHNIQUES:

Strong Typing:
  - Use distinct types for distinct concepts
  - Avoid stringly-typed APIs
  - Use enums instead of magic numbers

Wrapper Types:
  - Wrap sensitive data (passwords, keys)
  - Prevent accidental exposure

Builder Pattern:
  - Enforce required parameters
  - Make configuration explicit

Immutability:
  - Immutable objects prevent mutation bugs
  - Thread-safe by default
```

### 6. Eliminate Footguns
```
FOOTGUN PATTERNS TO ELIMINATE:

❌ Dangerous defaults
✓ Secure defaults, explicit opt-out

❌ Easy to confuse parameter order
✓ Use builder pattern or named parameters

❌ Silent failures
✓ Throw exceptions or return errors

❌ Allows partial initialization
✓ All-or-nothing construction

❌ Exposes internals
✓ Encapsulate implementation details
```

### 7. Document Security Properties
```
FOR EACH API:
  □ Security guarantees (what it provides)
  □ Security non-guarantees (what it doesn't)
  □ Threat model assumptions
  □ Correct usage examples
  □ Common mistakes to avoid
```

### 8. Test for Misuse
```
MISUSE TESTS:
  □ Can user pass null/empty values?
  □ Can user pass invalid combinations?
  □ Can user forget required steps?
  □ Can user call methods in wrong order?
  □ Can user ignore errors?
  □ Fuzz the API
```

## Design Patterns

### Builder Pattern (Enforced Configuration)
```java
// GOOD: Builder ensures all required params
Encryptor encryptor = Encryptor.builder()
    .setKey(key)                    // Required
    .setAlgorithm(AES_GCM)          // Required
    .build();                        // Validates completeness

// BAD: Easy to forget parameters
Encryptor encryptor = new Encryptor();
encryptor.setKey(key);
// Forgot algorithm - silent failure?
```

### Wrapper Types (Prevent Confusion)
```java
// GOOD: Types prevent parameter confusion
void encrypt(Plaintext plaintext, Key key, Nonce nonce);

// BAD: Easy to swap parameters
void encrypt(byte[] plaintext, byte[] key, byte[] nonce);
```

### Secure Defaults
```java
// GOOD: Secure by default
TlsClient client = TlsClient.create();
// Uses TLS 1.3, validates certificates, modern ciphers

// BAD: Requires configuration for security
TlsClient client = new TlsClient();
client.setValidateCertificates(true);  // Easy to forget
client.setMinTlsVersion(TLS_1_3);      // Easy to forget
```

### Factory Methods (Hide Complexity)
```java
// GOOD: Simple factory for common case
Aead aead = AeadFactory.createAes256Gcm(key);

// More options available but not required
Aead aead = AeadFactory.create(config);
```

## Examples

### Good: Tink AEAD API
```java
// Secure by default:
// - Key management built-in
// - No mode selection (always authenticated)
// - Nonce handled automatically
// - Secure algorithms only

Aead aead = keysetHandle.getPrimitive(Aead.class);
byte[] ciphertext = aead.encrypt(plaintext, associatedData);
byte[] decrypted = aead.decrypt(ciphertext, associatedData);

// What you CAN'T do (by design):
// - Use ECB mode
// - Reuse nonces
// - Use weak algorithms
// - Skip authentication
```

### Bad: Raw Crypto API
```java
// Insecure by default:
// - User must choose algorithm
// - User must manage nonce
// - User must handle padding
// - Easy to make mistakes

Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
cipher.init(Cipher.ENCRYPT_MODE, key, iv);
byte[] ciphertext = cipher.doFinal(plaintext);
// Missing: authentication, nonce management, key derivation
```

### Good: Input Validation
```java
public class Email {
    private final String address;
    
    private Email(String address) {
        this.address = address;
    }
    
    public static Email parse(String input) throws InvalidEmailException {
        // Validate format
        if (!isValidEmail(input)) {
            throw new InvalidEmailException("Invalid email: " + sanitize(input));
        }
        return new Email(input.toLowerCase());
    }
    
    // Can't create invalid Email - type system enforces validity
}
```

## Anti-patterns

### ❌ Security Through Configuration
```java
DON'T:
  HttpClient client = new HttpClient();
  client.setVerifySsl(true);  // Default is false!

WHY: Users forget, defaults should be secure

FIX:
  HttpClient client = HttpClient.secure();
  // Or explicitly insecure for testing:
  HttpClient client = HttpClient.insecure(); // Obvious
```

### ❌ Stringly-Typed APIs
```java
DON'T:
  encrypt(data, "AES", "CBC", "PKCS5");

WHY: Typos, invalid combinations, no compile-time checking

FIX:
  encrypt(data, Algorithm.AES_GCM);
```

### ❌ Silent Failures
```java
DON'T:
  if (!verify(signature)) {
    log.warn("Invalid signature");
    // Continues anyway!
  }

WHY: Security failures must be hard failures

FIX:
  if (!verify(signature)) {
    throw new SignatureVerificationException();
  }
```

### ❌ Exposing Internals
```java
DON'T:
  byte[] getKeyBytes();  // Exposes raw key material

WHY: Keys can be logged, leaked, misused

FIX:
  // No getter - key only usable through API
  Ciphertext encrypt(Plaintext data);
```

## Checklist

```
□ Are defaults secure?
□ Is dangerous functionality opt-in?
□ Does type system prevent misuse?
□ Are errors explicit and secure?
□ Is the common case simple?
□ Are security properties documented?
□ Have misuse cases been tested?
□ Is the API hard to misuse by accident?
```

## Metrics

- Misuse bug rate (bugs from API misuse)
- Time to correct usage
- Support ticket rate
- Security review findings
- Fuzzing bug count

## References

- Tink SECURITY-USABILITY.md
- "Usable Security" research
- API design guidelines (Google, Microsoft)
