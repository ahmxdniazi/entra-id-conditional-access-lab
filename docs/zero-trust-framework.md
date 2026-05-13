# Zero Trust Framework Applied
## Microsoft Entra ID — Identity Security Principles

---

## What is Zero Trust?

Zero Trust is a security model based on the principle of **"never trust, always verify."** Unlike traditional perimeter-based security (trust everything inside the network), Zero Trust assumes breach and verifies every request as if it originates from an untrusted network.

Microsoft defines Zero Trust across three core principles:

| Principle | Meaning | Applied In This Lab |
|-----------|---------|---------------------|
| **Verify Explicitly** | Always authenticate and authorize based on all available data points | MFA required for every sign-in |
| **Use Least Privilege** | Limit user access with just-in-time and just-enough-access | Admin roles targeted separately |
| **Assume Breach** | Minimize blast radius, segment access, verify end-to-end encryption | Location-based blocking reduces exposure |

---

## Zero Trust Identity Pillars

### 1. Strong Authentication
Every identity must prove itself with more than a password.

- ✅ **Policy CA-001** enforces MFA for all users
- ✅ **Policy CA-003** enforces MFA specifically for admin roles
- Passwords alone are no longer sufficient to access any resource

### 2. Least Privilege Access
Users get only the access they need, when they need it.

- Admin accounts are separated from standard user accounts
- Breakglass account is carefully scoped with known exclusions
- Future: Privileged Identity Management (PIM) for just-in-time admin access

### 3. Device Health Verification
Access decisions include device compliance signals.

- **Policy CA-003** designed to include device compliance requirement
- Production deployment would integrate with **Microsoft Intune**
- Unmanaged devices would be blocked from admin operations

### 4. Network/Location Context
Location is used as a signal to restrict or challenge access.

- ✅ **Policy CA-002** blocks access from outside trusted geography
- Named Location "Allowed Countries" defines trusted territory (Pakistan)
- Anomalous locations trigger block — not just challenge

### 5. Continuous Monitoring
Security doesn't stop at sign-in — it continues throughout the session.

- ✅ **Sign-in logs** reviewed for anomalous patterns
- ✅ **Audit logs** track all identity lifecycle events
- Future: Continuous Access Evaluation (CAE) for real-time session revocation

---

## Attack Scenarios Mitigated

### Scenario A: Credential Stuffing Attack
```
Attacker obtains password list from dark web breach
→ Attempts login for all users in tenant
→ Policy CA-001 triggers: MFA required
→ Attacker has no access to user's phone/authenticator
→ Attack BLOCKED ✅
```

### Scenario B: Foreign Threat Actor
```
Attacker based in foreign country attempts login
→ Even with correct credentials
→ Policy CA-002 triggers: Sign-in from outside Pakistan
→ Access BLOCKED ✅
→ Sign-in event logged for investigation
```

### Scenario C: Admin Account Compromise
```
Admin password stolen via phishing
→ Attacker attempts to sign in as Global Admin
→ Policy CA-003 triggers: Admin MFA required
→ Attacker cannot complete MFA challenge
→ Attack BLOCKED ✅
→ Failed MFA attempt visible in sign-in logs
```

### Scenario D: Insider Threat
```
Disgruntled employee attempts to access resources from home country
→ Location is within Pakistan — not blocked by CA-002
→ Policy CA-001 triggers: MFA required
→ MFA event is logged with timestamp and IP
→ Audit trail preserved for investigation ✅
```

---

## Zero Trust Maturity Assessment

| Capability | Status | Notes |
|------------|--------|-------|
| MFA for all users | 🟡 Designed | Pending P1 license for enforcement |
| MFA for admins | 🟡 Designed | Highest priority for production |
| Location-based access | 🟡 Designed | Named Location created |
| Device compliance | 🔴 Not yet | Requires Intune integration |
| Risk-based sign-in | 🔴 Not yet | Requires Entra ID P2 |
| PIM for admin roles | 🔴 Not yet | Project 4 in roadmap |
| SIEM integration | 🔴 Not yet | Project 5 in roadmap |
| Identity Governance | 🔴 Not yet | Project 5 in roadmap |

**Current Maturity Level: Initial (Foundation established, enforcement pending)**

---

## Next Steps Toward Full Zero Trust

1. **Activate Entra ID P1** → Enforce the 3 designed policies
2. **Complete Project 3** → App Registration + OAuth 2.0 SSO
3. **Complete Project 4** → PIM Dashboard (just-in-time admin access)
4. **Complete Project 5** → Full Identity Governance + SIEM pipeline

---

*This document is part of the Microsoft Entra ID IAM Projects learning roadmap.*
