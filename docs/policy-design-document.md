# Policy Design Document
## Microsoft Entra ID — Conditional Access Framework

**Author:** Muhammad Ahmad  
**Date:** May 2026  
**Tenant:** niazi199743gmail.onmicrosoft.com  
**Role:** Global Administrator  

---

## 1. Executive Summary

This document defines the Conditional Access policy framework designed for the `niazi199743gmail.onmicrosoft.com` Entra ID tenant. The framework implements a **Zero Trust security model** by enforcing identity-based controls across all cloud application access.

Three core policies were designed covering the most critical attack vectors in modern identity infrastructure:
- Credential-based attacks (MFA enforcement)
- Geographic-based attacks (location restrictions)
- Privileged account compromise (admin MFA)

---

## 2. Design Principles

### 2.1 Zero Trust Model
All policies follow the Zero Trust principle: **"Never trust, always verify."**

- No user or device is implicitly trusted
- Access is granted based on verified identity + device state + location context
- Least-privilege access is enforced at every layer

### 2.2 Report-Only First
All policies are designed to be deployed in **Report-only mode** before enforcement. This:
- Prevents accidental lockouts
- Allows impact analysis before enforcement
- Creates an audit baseline for comparison

### 2.3 Breakglass Exclusion
A **Global Administrator breakglass account** (Muhammad Ahmad) is excluded from all user-targeting policies. This is a critical operational safety measure — without it, a misconfigured policy could lock all admins out of the tenant.

---

## 3. Policy Specifications

---

### Policy 1: Require MFA — All Users

| Field | Value |
|-------|-------|
| **Policy ID** | CA-001 |
| **Policy Name** | Require MFA - All Users |
| **Priority** | High |
| **Status** | Designed — Report-only pending P1 license |

#### Scope
- **Include:** All users
- **Exclude:** Muhammad Ahmad (Global Admin — breakglass)
- **Target Resources:** All cloud apps
- **Conditions:** None (applies to all sign-ins)

#### Controls
- **Grant Access** with requirement: **Require Multi-Factor Authentication**

#### Risk Addressed
| Attack Vector | Risk Level | Mitigation |
|---------------|-----------|------------|
| Password spray | Critical | MFA blocks even valid credentials |
| Credential stuffing | Critical | Second factor required on every login |
| Phishing | High | Stolen password alone is insufficient |

#### Deployment Plan
```
Phase 1: Report-only (14 days) — monitor impact
Phase 2: Enable for pilot group (5 users)
Phase 3: Enforce for all users
Phase 4: Monitor sign-in logs for MFA failures
```

---

### Policy 2: Block Access — Outside Allowed Countries

| Field | Value |
|-------|-------|
| **Policy ID** | CA-002 |
| **Policy Name** | Block - Outside Allowed Countries |
| **Priority** | High |
| **Status** | Designed — Report-only pending P1 license |

#### Named Location Configuration
```
Location Name : Allowed Countries
Location Type : Countries
Countries     : Pakistan (PK)
Trusted       : Yes
```

#### Scope
- **Include:** All users
- **Exclude:** Muhammad Ahmad (Global Admin — breakglass)
- **Target Resources:** All cloud apps
- **Conditions:**
  - Locations → Include: Any location
  - Locations → Exclude: Allowed Countries (Pakistan)

#### Controls
- **Block access**

#### Risk Addressed
| Threat | Risk Level | Mitigation |
|--------|-----------|------------|
| Foreign credential attacks | Critical | Sign-ins outside Pakistan are blocked entirely |
| Compromised credentials used abroad | High | Location mismatch triggers block |
| Nation-state threat actors | Medium | Geographic boundary reduces exposure |

#### Deployment Plan
```
Phase 1: Report-only (7 days) — identify legitimate travel use cases
Phase 2: Communicate policy to users
Phase 3: Enforce + add travel exception process
Phase 4: Monitor blocked sign-ins for threat analysis
```

---

### Policy 3: Require MFA — Admin Roles

| Field | Value |
|-------|-------|
| **Policy ID** | CA-003 |
| **Policy Name** | Require MFA - Admin Roles |
| **Priority** | Critical |
| **Status** | Designed — Report-only pending P1 license |

#### Scope
- **Include:** Directory Roles → Global Administrator
- **Exclude:** None (admins must always MFA — no exceptions)
- **Target Resources:** All cloud apps
- **Conditions:** None (applies to all admin sign-ins)

#### Controls
- **Grant Access** with requirement: **Require Multi-Factor Authentication**

#### Risk Addressed
| Threat | Risk Level | Mitigation |
|--------|-----------|------------|
| Admin account takeover | Critical | MFA required on every admin session |
| Privilege escalation via stolen creds | Critical | Credential alone is insufficient |
| Insider threat | High | MFA creates audit trail of admin access |

#### Deployment Plan
```
Phase 1: Report-only (3 days only — admin risk is too high to wait)
Phase 2: Enforce immediately for all admin roles
Phase 3: Verify all admins have MFA registered before enforcement
Phase 4: Alert on any admin MFA failure (possible compromise)
```

---

## 4. Policy Interaction Matrix

| User Type | Policy 1 (MFA All) | Policy 2 (Location) | Policy 3 (Admin MFA) | Net Result |
|-----------|-------------------|--------------------|--------------------|------------|
| Standard user — Pakistan | ✅ MFA required | ✅ Allowed | ➖ N/A | Access with MFA |
| Standard user — Foreign | ✅ MFA required | ❌ Blocked | ➖ N/A | Blocked |
| Admin — Pakistan | ✅ MFA required | ✅ Allowed | ✅ MFA required | Access with MFA |
| Admin — Foreign | ✅ MFA required | ❌ Blocked | ✅ MFA required | Blocked |
| Breakglass admin | ➖ Excluded | ➖ Excluded | ✅ MFA required | Access with MFA |

---

## 5. Monitoring Plan

### Sign-in Logs (Available — Free Tier)
- Review daily for failed MFA attempts
- Flag sign-ins from unexpected IP ranges
- Track "Conditional Access: Not Applied" entries (pre-enforcement baseline)

### Audit Logs (Available — Free Tier)
- Monitor user provisioning and deprovisioning
- Track role assignments and changes
- Alert on policy modifications

### Future — With P2 License
- Enable **Identity Protection** risk-based sign-in policies
- Configure **alerts** for high-risk sign-ins
- Integrate with **Microsoft Sentinel** for SIEM correlation

---

## 6. Lessons Learned

1. **Licensing Reality:** Conditional Access requires Entra ID P1 minimum. Free tier allows policy design and interface exploration but not enforcement — a common production constraint worth understanding early.

2. **Breakglass Design:** Every CA deployment must start with identifying and excluding at least one emergency admin account. This is non-negotiable in production.

3. **Report-only is not optional:** Enforcing CA policies without a report-only observation period is a common cause of user lockouts in enterprise environments.

4. **Named Locations provide context:** Geographic controls are a fast win for reducing noise from foreign credential attacks — especially for organizations operating in a single country.

5. **Admin policies have different urgency:** Admin MFA policies should be enforced faster than general user policies — the risk profile is simply too high to wait.

---

*Document prepared as part of Microsoft Entra ID IAM Projects learning roadmap.*
