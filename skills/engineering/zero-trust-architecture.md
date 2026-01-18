# Skill: Zero Trust Architecture

> Designing and implementing zero trust security models, derived from Google's BeyondCorp architecture.

## When to Use

- Designing enterprise security architecture
- Modernizing legacy perimeter security
- Enabling secure remote access
- Implementing least-privilege access
- Moving applications to cloud

## Prerequisites

- Understanding of current architecture
- Device management capability
- Identity provider (IdP)
- Inventory of applications and data
- Stakeholder buy-in

## Steps

### 1. Understand Zero Trust Principles
```
CORE PRINCIPLES:

1. Never Trust, Always Verify
   - No implicit trust based on network location
   - Every request authenticated and authorized

2. Assume Breach
   - Design as if attackers are already inside
   - Minimize blast radius

3. Verify Explicitly
   - Authenticate all users
   - Verify all devices
   - Check context (time, location, behavior)

4. Least Privilege Access
   - Minimal permissions needed
   - Just-in-time access
   - Just-enough access

5. Micro-segmentation
   - Isolate workloads
   - Limit lateral movement
```

### 2. Assess Current State
```
INVENTORY:
  □ Users (employees, contractors, partners)
  □ Devices (managed, BYOD, IoT)
  □ Applications (internal, SaaS, legacy)
  □ Data (classification, location, flows)
  □ Networks (on-prem, cloud, hybrid)
  
CURRENT CONTROLS:
  □ Authentication mechanisms
  □ Authorization models
  □ Network segmentation
  □ Monitoring capabilities
```

### 3. Define Trust Signals
```
TRUST INPUTS:

User Trust:
  - Identity verification (MFA)
  - Role/group membership
  - Authentication method strength
  - Recent activity patterns
  - Risk indicators

Device Trust:
  - Managed vs unmanaged
  - OS version / patch level
  - Security software status
  - Encryption status
  - Certificate validity

Context Trust:
  - Time of access
  - Location (geo, network)
  - Resource sensitivity
  - Action requested
  - Historical behavior
```

### 4. Design Architecture
```
BEYONDCORP COMPONENTS:

┌─────────────────────────────────────────────────────────────────┐
│                         ZERO TRUST ARCHITECTURE                  │
│                                                                 │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐        │
│  │   Device    │    │    User     │    │   Context   │        │
│  │  Inventory  │    │  Directory  │    │   Signals   │        │
│  └──────┬──────┘    └──────┬──────┘    └──────┬──────┘        │
│         │                  │                  │                │
│         └─────────────┬────┴─────────────────┘                │
│                       │                                        │
│                       ▼                                        │
│              ┌─────────────────┐                              │
│              │  Trust Inferer  │                              │
│              └────────┬────────┘                              │
│                       │                                        │
│                       ▼                                        │
│              ┌─────────────────┐                              │
│              │  Access Policy  │                              │
│              │     Engine      │                              │
│              └────────┬────────┘                              │
│                       │                                        │
│                       ▼                                        │
│              ┌─────────────────┐                              │
│              │  Access Proxy   │ ◄──── User Request           │
│              └────────┬────────┘                              │
│                       │                                        │
│                       ▼                                        │
│              ┌─────────────────┐                              │
│              │   Application   │                              │
│              └─────────────────┘                              │
└─────────────────────────────────────────────────────────────────┘
```

### 5. Implement Device Trust
```
DEVICE INVENTORY:
  □ Enroll all managed devices
  □ Track device attributes:
    - Hardware identifiers
    - OS version
    - Patch status
    - Installed software
    - Security configuration
  □ Update inventory continuously
  □ Detect unmanaged devices

DEVICE REQUIREMENTS:
  □ Certificate-based identity
  □ Minimum OS version
  □ Encryption enabled
  □ Security software active
  □ Compliance with policy
```

### 6. Implement User Trust
```
IDENTITY:
  □ Strong authentication (MFA required)
  □ Phishing-resistant methods (FIDO2, WebAuthn)
  □ Continuous authentication
  □ Session management

AUTHORIZATION:
  □ Role-based access control (RBAC)
  □ Attribute-based access control (ABAC)
  □ Group membership
  □ Temporary elevations
```

### 7. Implement Access Control
```
POLICY MODEL:
  
  IF (user.authenticated AND
      user.mfa_verified AND
      device.managed AND
      device.compliant AND
      context.risk_acceptable)
  THEN
      allow_access(resource, requested_permission)
  ELSE
      deny_access() OR step_up_auth()

DYNAMIC POLICIES:
  - Adjust requirements by resource sensitivity
  - Increase requirements for high-risk actions
  - Reduce friction for low-risk actions
```

### 8. Implement Access Proxy
```
PROXY FUNCTIONS:
  □ Terminate user connections
  □ Query trust inferer
  □ Enforce access policies
  □ Provide application access
  □ Log all access attempts

DEPLOYMENT:
  □ All internal apps behind proxy
  □ No direct access to applications
  □ Single enforcement point
```

### 9. Enable Monitoring
```
LOGGING:
  □ All authentication attempts
  □ All authorization decisions
  □ All access requests
  □ Device compliance changes
  □ Policy violations

ALERTING:
  □ Anomalous access patterns
  □ Failed authentication spikes
  □ Device compliance drops
  □ Policy violations
```

### 10. Migrate Incrementally
```
MIGRATION PHASES:

Phase 1: Inventory & Visibility
  - Deploy device management
  - Implement logging
  - Map applications

Phase 2: Identity Foundation
  - Strong authentication
  - MFA everywhere
  - Directory integration

Phase 3: Pilot Applications
  - Select low-risk apps
  - Deploy proxy
  - Implement policies
  - Gather feedback

Phase 4: Expand
  - Add more applications
  - Refine policies
  - Handle edge cases

Phase 5: Default Deny
  - All apps behind proxy
  - Remove network-based access
  - Continuous monitoring
```

## Policy Examples

### Basic Access Policy
```yaml
policy:
  name: standard-app-access
  resource: internal-app
  
  requirements:
    user:
      authenticated: true
      mfa: required
      groups: [employees]
    
    device:
      managed: true
      os_current: true
      encrypted: true
    
    context:
      allowed_hours: business_hours
      allowed_locations: [office, vpn]
```

### High-Security Access Policy
```yaml
policy:
  name: admin-access
  resource: admin-console
  
  requirements:
    user:
      authenticated: true
      mfa: phishing_resistant  # FIDO2
      groups: [admins]
      training: security_training_current
    
    device:
      managed: true
      corporate_owned: true
      os_current: true
      encrypted: true
      edr_active: true
    
    context:
      allowed_hours: any
      session_duration: 1h
      
    additional:
      require_justification: true
      notify_security: true
```

## Anti-patterns

### ❌ VPN = Zero Trust
```
DON'T: "We have VPN, so we're zero trust"
WHY: VPN is network access, not zero trust
FIX: Verify user + device + context for each request
```

### ❌ One-Time Verification
```
DON'T: Verify once, trust for session
WHY: Context changes, devices compromised
FIX: Continuous verification, step-up auth
```

### ❌ All-or-Nothing Access
```
DON'T: Full access or no access
WHY: Doesn't match real risk levels
FIX: Graduated access based on trust level
```

### ❌ Ignoring Legacy Apps
```
DON'T: Zero trust only for new apps
WHY: Legacy apps are highest risk
FIX: Proxy/wrapper for legacy applications
```

## Metrics

- Percentage of apps behind proxy
- MFA adoption rate
- Device compliance rate
- Access policy violations
- Time to detect anomalies

## References

- BeyondCorp papers (USENIX)
- NIST SP 800-207 (Zero Trust Architecture)
- Google Cloud BeyondCorp Enterprise
