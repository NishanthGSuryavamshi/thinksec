# Skill: Cryptography Engineering

> Implementing cryptography correctly and safely, derived from Google ISE's Tink and Wycheproof projects.

## When to Use

- Implementing encryption/decryption
- Adding authentication to data
- Managing cryptographic keys
- Reviewing crypto implementations
- Selecting cryptographic algorithms

## Prerequisites

- Understanding of cryptographic primitives
- Knowledge of common crypto pitfalls
- Access to vetted crypto libraries
- Threat model for data protection

## Steps

### 1. Understand Requirements
```
□ What data needs protection?
□ Protection goals:
  - Confidentiality (encryption)
  - Integrity (authentication/MAC)
  - Authenticity (signatures)
  - Non-repudiation (signatures)
□ Key management requirements
□ Performance constraints
□ Compliance requirements
```

### 2. Choose the Right Primitive
```
USE CASE → PRIMITIVE:

Encrypting data at rest:
  → AEAD (AES-GCM, ChaCha20-Poly1305)

Encrypting data in transit:
  → TLS 1.3

Signing data:
  → ECDSA, Ed25519, RSA-PSS

Hashing passwords:
  → Argon2id, bcrypt, scrypt

Generating random values:
  → CSPRNG (OS-provided)

Key derivation:
  → HKDF

Message authentication:
  → HMAC
```

### 3. Use a Vetted Library
```
RECOMMENDED LIBRARIES:

Multi-language:
  - Tink (Google) - Primary recommendation

Language-specific:
  - Go: crypto/*, golang.org/x/crypto
  - Python: cryptography, PyNaCl
  - Java: Tink, Bouncy Castle (carefully)
  - Rust: ring, RustCrypto
  - C/C++: BoringSSL, libsodium

AVOID:
  - Rolling your own crypto
  - Outdated libraries
  - Libraries without security review
```

### 4. Implement Correctly
```
KEY RULES:

1. Never implement primitives yourself
   ✓ Use: library.encrypt(data, key)
   ✗ Don't: Implement AES yourself

2. Use authenticated encryption
   ✓ AES-GCM, ChaCha20-Poly1305
   ✗ AES-CBC without MAC

3. Never reuse nonces
   ✓ Random nonce or counter
   ✗ Fixed nonce, timestamp as nonce

4. Use proper key derivation
   ✓ HKDF, Argon2 for passwords
   ✗ Raw password as key, MD5(password)

5. Handle errors securely
   ✓ Constant-time comparisons
   ✗ Early exit on mismatch

6. Zeroize sensitive data
   ✓ Clear keys from memory when done
   ✗ Let GC eventually collect
```

### 5. Manage Keys Properly
```
KEY LIFECYCLE:

Generation:
  □ Use CSPRNG
  □ Sufficient key size (256-bit minimum)
  □ Generate in secure environment

Storage:
  □ Never hardcode keys
  □ Use key management system (KMS)
  □ Encrypt keys at rest
  □ Limit access to keys

Rotation:
  □ Regular rotation schedule
  □ Support multiple key versions
  □ Deprecate old keys gradually

Destruction:
  □ Secure deletion
  □ Audit trail
  □ Confirm destruction
```

### 6. Test with Wycheproof
```
WYCHEPROOF TEST CATEGORIES:
  □ AEAD (authenticated encryption)
  □ Digital signatures
  □ Key agreement
  □ Message authentication
  □ Hashing

HOW TO USE:
  1. Download test vectors
  2. Run vectors against implementation
  3. Verify correct behavior on edge cases
  4. Catch known attack patterns
```

### 7. Review & Verify
```
REVIEW CHECKLIST:
  □ Algorithm selection appropriate?
  □ Key sizes sufficient?
  □ Nonce handling correct?
  □ Error handling constant-time?
  □ Keys zeroized after use?
  □ No timing side-channels?
  □ Authenticated encryption used?
  □ Key derivation proper?
```

## Algorithm Selection Guide

### Symmetric Encryption
| Algorithm | Use Case | Notes |
|-----------|----------|-------|
| AES-256-GCM | General purpose | AEAD, hardware acceleration |
| ChaCha20-Poly1305 | Mobile, no AES-NI | AEAD, constant-time |
| XChaCha20-Poly1305 | Large nonces | Extended nonce version |

### Asymmetric Encryption
| Algorithm | Use Case | Notes |
|-----------|----------|-------|
| RSA-OAEP | Legacy compatibility | Min 2048-bit, prefer 3072+ |
| ECIES | Modern systems | Use with P-256 or X25519 |

### Signatures
| Algorithm | Use Case | Notes |
|-----------|----------|-------|
| Ed25519 | General purpose | Fast, secure, small sigs |
| ECDSA P-256 | Compatibility | Needs good RNG |
| RSA-PSS | Legacy | Min 2048-bit |

### Hashing
| Algorithm | Use Case | Notes |
|-----------|----------|-------|
| SHA-256 | General purpose | |
| SHA-384/512 | Higher security | |
| BLAKE2 | Performance | |
| SHA-3 | Diversity | |

### Password Hashing
| Algorithm | Use Case | Notes |
|-----------|----------|-------|
| Argon2id | Primary choice | Memory-hard |
| bcrypt | Compatibility | Max 72 bytes |
| scrypt | Alternative | Memory-hard |

## Examples

### Good: Tink AEAD
```java
// Generate key
KeysetHandle keysetHandle = KeysetHandle.generateNew(
    PredefinedAeadParameters.AES256_GCM);

// Encrypt
Aead aead = keysetHandle.getPrimitive(Aead.class);
byte[] ciphertext = aead.encrypt(plaintext, associatedData);

// Decrypt
byte[] decrypted = aead.decrypt(ciphertext, associatedData);

// Key is:
// - Generated securely
// - Managed by Tink
// - Nonce handled automatically
// - Authenticated
```

### Bad: Manual Crypto
```java
// DON'T DO THIS

// Bad: hardcoded key
byte[] key = "mysecretkey12345".getBytes();

// Bad: ECB mode, no authentication
Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
cipher.init(Cipher.ENCRYPT_MODE, new SecretKeySpec(key, "AES"));

// Bad: no authentication, can be tampered
byte[] ciphertext = cipher.doFinal(plaintext);
```

### Good: Password Hashing
```java
// Using Argon2id
String hash = Argon2Factory.create(Argon2Types.ARGON2id)
    .hash(10, 65536, 1, password.toCharArray());

// Verify
boolean valid = Argon2Factory.create(Argon2Types.ARGON2id)
    .verify(hash, password.toCharArray());
```

### Bad: Password Hashing
```java
// DON'T DO THIS

// Bad: using fast hash
String hash = MessageDigest.getInstance("MD5")
    .digest(password.getBytes());

// Bad: unsalted
// Bad: not memory-hard (fast to brute force)
```

## Anti-patterns

### ❌ ECB Mode
```
DON'T: AES/ECB
WHY: Patterns in plaintext visible in ciphertext
FIX: Use authenticated mode (GCM)
```

### ❌ Unauthenticated Encryption
```
DON'T: AES-CBC without HMAC
WHY: Ciphertext can be modified undetected
FIX: Use AEAD (GCM, ChaCha20-Poly1305)
```

### ❌ Nonce Reuse
```
DON'T: Reuse nonce with same key
WHY: Catastrophic security failure
FIX: Random nonces or strict counter
```

### ❌ Weak Key Derivation
```
DON'T: key = SHA256(password)
WHY: Fast to brute force
FIX: Argon2id, bcrypt, PBKDF2 (high iterations)
```

### ❌ Timing Side Channels
```
DON'T: 
  for(i=0; i<len; i++) {
    if(a[i] != b[i]) return false;
  }

WHY: Timing reveals how many bytes matched

FIX: Constant-time comparison
```

## Metrics

- Wycheproof test pass rate
- Key rotation compliance
- Crypto library freshness
- Security review findings

## References

- Tink documentation
- Wycheproof test vectors
- NIST crypto guidelines
- "Cryptography Engineering" (Ferguson, Schneier)
