---
name: sdlc-legal-compliance
description: "Software company legal and compliance: GDPR, SOC 2, CCPA, privacy policy, terms of service, data processing agreements, IP protection, open source licensing, regulatory compliance, security certifications, data residency, incident response legal, employment law."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, legal, compliance, gdpr, soc2, ccpa, privacy, ip, licensing, security-certifications, data-residency, employment-law]
    related_skills: [sdlc-adversarial-review, sdlc-finance-ops, sdlc-gtm-strategy]
---

# Legal & Compliance

Privacy regulations, security certifications, IP protection, and legal foundations for software companies.

## When to Use

Trigger when user:
- Creates privacy policy, terms of service, or DPA
- Plans GDPR, SOC 2, or CCPA compliance
- Evaluates open source licenses
- Protects intellectual property
- Handles data breach or incident response
- Plans data residency or sovereignty requirements
- Creates employment agreements or contractor terms
- Prepares for enterprise security questionnaires

## Step 1: Privacy Regulations

### GDPR (EU General Data Protection Regulation)

```
Applies to: Any company processing EU residents' data
Key requirements:
1. Lawful basis for processing (consent, contract, legitimate interest)
2. Data minimization (collect only what's needed)
3. Right to access (SAR — Subject Access Request)
4. Right to erasure ("right to be forgotten")
5. Right to data portability (export user data)
6. Data Protection Impact Assessment (DPIA) for high-risk processing
7. Data Protection Officer (DPO) for large-scale processing
8. 72-hour breach notification to supervisory authority
9. Cross-border data transfer mechanisms (SCCs, adequacy decisions)

Penalties: Up to €20M or 4% of global annual revenue (whichever is higher)

Implementation checklist:
□ Map all personal data flows
□ Create Record of Processing Activities (ROPA)
□ Implement consent management (cookie banner, preference center)
□ Build data subject request (DSR) workflow
□ Sign DPAs with all sub-processors
□ Implement data retention policies
□ Train employees on data handling
□ Appoint EU representative (if no EU establishment)
```

### CCPA/CPRA (California)

```
Applies to: Companies with >$25M revenue, >100K consumers, or >50% revenue from selling data
Key rights:
1. Right to know (what data collected, why, shared with whom)
2. Right to delete
3. Right to opt-out of sale/sharing
4. Right to non-discrimination
5. Right to correct inaccurate data
6. Right to limit use of sensitive data

Implementation:
□ "Do Not Sell or Share My Personal Information" link
□ Privacy policy updates (categories, purposes, retention)
□ Consumer request verification process (45-day response)
□ Service provider / contractor agreements
□ Employee training on CCPA procedures
```

### Other Privacy Laws

| Law | Jurisdiction | Key Requirement |
|-----|-------------|-----------------|
| PIPEDA | Canada | Consent, access, correction |
| LGPD | Brazil | Similar to GDPR, consent-based |
| POPIA | South Africa | Data subject rights, lawful processing |
| PDPA | Singapore | Consent, purpose limitation |
| APP | Australia | 13 Privacy Principles |
| PIPL | China | Data localization, consent, DPO |

## Step 2: Security Certifications

### SOC 2 Type II

```
What: Trust Services Criteria audit by independent CPA
Criteria:
1. Security (required): Protection against unauthorized access
2. Availability (optional): System uptime and performance
3. Processing Integrity (optional): System processing accuracy
4. Confidentiality (optional): Protection of confidential info
5. Privacy (optional): Personal data handling

Timeline:
Month 1-2: Readiness assessment (gap analysis)
Month 3-6: Implementation (policies, controls, tooling)
Month 7-12: Observation period (audit collects evidence)
Month 13: Audit report delivered

Cost: $30K-$100K+ (depends on auditor and company size)

Key controls:
□ Access management (RBAC, MFA, access reviews)
□ Change management (code review, deployment approval)
□ Incident response (detection, triage, notification)
□ Risk management (risk register, annual assessment)
□ Vendor management (due diligence, DPAs)
□ Business continuity (backup, DR, testing)
□ Employee security (training, background checks)
□ Encryption (at rest, in transit)
□ Logging and monitoring (audit trails, SIEM)
```

### ISO 27001

```
What: International standard for Information Security Management System (ISMS)
More prescriptive than SOC 2, requires formal ISMS documentation
Annex A: 93 security controls across 4 categories
Certification: Requires accredited auditor, 3-year cycle

Timeline: 6-18 months for initial certification
Cost: $50K-$200K+

When to get: Enterprise customers require it (especially EU/international)
```

### HIPAA (Healthcare)

```
Applies to: Companies handling Protected Health Information (PHI)
Requirements:
□ Business Associate Agreements (BAAs) with all parties
□ Administrative safeguards (policies, training, risk analysis)
□ Physical safeguards (facility access, workstation security)
□ Technical safeguards (access controls, encryption, audit logs)
□ Breach notification (60 days, HHS, affected individuals)

Penalties: $100-$50,000 per violation, up to $1.5M/year per category
```

### PCI DSS (Payments)

```
Applies to: Companies that process, store, or transmit cardholder data
Levels (based on transaction volume):
  Level 1: >6M transactions/year → Annual on-site audit
  Level 2: 1-6M → Annual SAQ
  Level 3: 20K-1M → Annual SAQ
  Level 4: <20K → Annual SAQ

12 Requirements:
1. Install and maintain firewall
2. Do not use vendor-supplied defaults
3. Protect stored cardholder data
4. Encrypt transmission of cardholder data
5. Use and regularly update antivirus
6. Develop secure systems and software
7. Restrict access on need-to-know basis
8. Assign unique ID to each person
9. Restrict physical access
10. Track and monitor all access
11. Regularly test security systems
12. Maintain information security policy

Tip: Use Stripe/Braintree/Adyen → they handle PCI compliance for you
```

## Step 3: Open Source Licensing

### License Categories

```
Permissive (do what you want):
  MIT: Use, modify, distribute. Include license notice.
  Apache 2.0: MIT + patent grant. Include NOTICE file.
  BSD 2/3-clause: Similar to MIT, slightly different wording.

Weak Copyleft (share modifications, not whole project):
  LGPL: Modifications to the library must be shared. Your code using it doesn't.
  MPL 2.0: Modifications to MPL-licensed files must be shared.

Strong Copyleft (share everything):
  GPL 2.0/3.0: If you distribute binaries, you must share source.
  AGPL 3.0: GPL + network use counts as distribution (SaaS must share source).

Source-available (not true open source):
  BSL (Business Source License): Source available, commercial use restricted until change date.
  SSPL: Must open-source entire service stack if offering as service.
  Elastic License 2.0: Cannot offer as managed service.
```

### License Compatibility Matrix

```
Can combine with MIT code:
  ✅ MIT, Apache 2.0, BSD, LGPL, MPL, GPL, AGPL

Can combine with GPL code:
  ✅ GPL, AGPL
  ❌ MIT, Apache 2.0 (one-way: GPL absorbs them)

Can combine with Apache 2.0 code:
  ✅ Apache 2.0, MIT, BSD, LGPL, MPL, GPL 3.0, AGPL
  ❌ GPL 2.0 (patent clause conflict)

Key rule: When in doubt, don't mix GPL with permissive licenses.
```

### License Compliance Checklist

```
□ Audit all dependencies (use tools: license-checker, FOSSA, Snyk)
□ Document licenses for all direct and transitive dependencies
□ Ensure no license conflicts with your business model
□ Include license notices in distributions
□ Create THIRD_PARTY_LICENSES file
□ Monitor for license changes in dependencies
□ Have legal review any AGPL/SSPL/BSL dependencies
```

## Step 4: Terms of Service & Legal Documents

### Terms of Service Structure

```
1. A