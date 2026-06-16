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
1. Acceptance of Terms
2. Description of Service
3. Account Registration
4. Acceptable Use Policy
5. Payment Terms (billing, refunds, cancellation)
6. Intellectual Property (your IP, user content, licenses)
7. User Content (ownership, license grant, moderation)
8. Privacy (link to Privacy Policy)
9. Disclaimers (no warranty, as-is)
10. Limitation of Liability (cap at fees paid)
11. Indemnification
12. Termination
13. Governing Law and Dispute Resolution
14. Changes to Terms (notice period)
15. Contact Information
```

### Privacy Policy Structure

```
1. What information we collect (personal, usage, device, cookies)
2. How we use information (service, analytics, marketing, legal)
3. How we share information (service providers, legal, business transfers)
4. Data retention (how long, deletion)
5. Your rights (access, delete, correct, portability, opt-out)
6. International transfers (SCCs, adequacy)
7. Security measures
8. Children's privacy (COPPA compliance if applicable)
9. Changes to policy
10. Contact information (DPO if applicable)
```

### Data Processing Agreement (DPA)

```
Required when: You process personal data on behalf of customers (B2B SaaS)
Key terms:
1. Subject matter, duration, nature of processing
2. Types of personal data and categories of data subjects
3. Obligations of the processor (you)
4. Sub-processor management (list, notification, objection rights)
5. Data security measures
6. Data subject rights assistance
7. Breach notification (72 hours)
8. Audit rights
9. Data deletion/return on termination
10. International transfer mechanisms
```

## Step 5: Intellectual Property

### IP Protection

```
1. Patents: Protect inventions (20-year term)
   - Software patents: controversial, expensive ($10K-$50K+)
   - Defensive patents: protect against trolls
   - Prior art search before filing

2. Trademarks: Protect brand names, logos, slogans
   - Register with USPTO (domestic) / WIPO (international)
   - ™ (unregistered) vs ® (registered)
   - Cost: $250-$350 per class (USPTO)

3. Copyright: Protect code, content, creative works
   - Automatic upon creation
   - Register for statutory damages ($200-$150K per work)
   - Work-for-hire: employer owns employee-created work

4. Trade secrets: Protect confidential business info
   - NDAs for employees, contractors, partners
   - Access controls and need-to-know basis
   - Non-compete agreements (varies by jurisdiction)
```

### Contractor vs Employee IP

```
Employee: Work-for-hire doctrine → Company owns IP created during employment
Contractor: IP ownership NOT automatic → Must have written assignment clause

Contractor agreement must include:
□ Work-for-hire clause OR IP assignment
□ Scope of work definition
□ Confidentiality obligations
□ Non-solicitation (optional)
□ Indemnification
□ Payment terms
□ Governing law

Warning: If contractor agreement is missing IP assignment, the contractor owns the code.
```

## Step 6: Enterprise Security Questionnaires

### Common Frameworks

```
SIG (Standardized Information Gathering): 800+ questions
CAIQ (Cloud Alliance): Cloud-specific questionnaire
VSA (Vendor Security Alliance): Abbreviated questionnaire
Custom: Enterprise-specific questions

Questions cover:
1. Organization security (policies, governance, training)
2. Asset management (inventory, classification)
3. Access control (RBAC, MFA, provisioning)
4. Encryption (at rest, in transit, key management)
5. Operations (logging, monitoring, incident response)
6. Business continuity (backup, DR, RTO/RPO)
7. Compliance (certifications, audits, regulatory)
8. Privacy (data handling, retention, DSR process)
9. Third-party management (sub-processors, vendor risk)
10. Vulnerability management (scanning, patching, pentesting)
```

### Questionnaire Response Tips

```
1. Create a "trust page" with certifications, policies, status
2. Maintain a living questionnaire response database
3. Map questionnaire questions to SOC 2 controls
4. Use automation tools (Vanta, Drata, Secureframe)
5. Update responses after each audit cycle
6. Be honest — "not yet" is better than "yes" (then failing audit)
```

## Step 7: Incident Response Legal

### Data Breach Response Plan

```
Phase 1: Detection & Containment (0-24 hours)
  □ Identify scope of breach
  □ Contain the breach (isolate systems, revoke access)
  □ Preserve evidence (logs, forensic images)
  □ Activate incident response team
  □ Engage legal counsel

Phase 2: Assessment (24-48 hours)
  □ Determine what data was affected
  □ Identify affected individuals (count, jurisdictions)
  □ Assess risk of harm (identity theft, financial loss)
  □ Determine notification obligations
  □ Document timeline and actions taken

Phase 3: Notification (48-72 hours)
  □ GDPR: Notify supervisory authority within 72 hours
  □ Notify affected individuals (without undue delay)
  □ Notify law enforcement (if criminal activity)
  □ Notify business partners / customers (if their data affected)
  □ Prepare public statement (if media attention likely)

Phase 4: Remediation (1-4 weeks)
  □ Fix root cause
  □ Implement additional controls
  □ Offer credit monitoring to affected individuals
  □ Update policies and procedures
  □ Conduct post-incident review

Phase 5: Lessons Learned (4-8 weeks)
  □ Postmortem report
  □ Update incident response plan
  □ Additional training if needed
  □ Regulatory follow-up as required
```

### Breach Notification Templates

```
To Supervisory Authority (GDPR):
  - Nature of breach
  - Categories and approximate number of individuals affected
  - Categories and approximate number of records affected
  - Name and contact details of DPO
  - Likely consequences of the breach
  - Measures taken or proposed to address the breach

To Affected Individuals:
  - What happened (plain language)
  - What data was involved
  - What you're doing about it
  - What they should do (password change, monitoring)
  - Contact information for questions
```

## Step 8: Employment Law Essentials

### Employment Agreement Key Terms

```
1. At-will employment (US): Either party can terminate at any time
2. Non-compete: Restrictive, varies by state (banned in CA, limited in others)
3. Non-solicitation: Cannot recruit former employer's employees/customers
4. Confidentiality: Protect trade secrets and proprietary information
5. IP assignment: Work created during employment belongs to company
6. Arbitration clause: Disputes resolved via arbitration, not court
7. Severance: Terms for termination without cause

International considerations:
  EU: Notice periods, severance requirements, works councils
  UK: Similar to EU, post-Brexit variations
  APAC: Varies significantly by country
```

### Contractor vs Employee Classification

```
IRS 20-Factor Test (simplified):

Behavioral control:
  □ Does company control HOW work is done? → Employee
  □ Does worker control their own methods? → Contractor

Financial control:
  □ Does company provide tools/equipment? → Employee
  □ Does worker invest in own tools? → Contractor
  □ Is worker paid hourly/salary? → Employee
  □ Is worker paid per project? → Contractor

Relationship type:
  □ Is there a written contract? → Consider both
  □ Does worker receive benefits? → Employee
  □ Is relationship ongoing? → Employee
  □ Is relationship project-based? → Contractor

Misclassification risk:
  - Back taxes + penalties
  - Unpaid benefits + overtime
  - Class action lawsuits
  - IRS audit
```

## Step 9: Compliance Automation Tools

### Compliance Tool Landscape

```
SOC 2 Automation:
  Vanta: $10K-$30K/year, integrates with 100+ tools
  Drata: $10K-$25K/year, continuous monitoring
  Secureframe: $8K-$20K/year, fast setup
  Sprinto: $5K-$15K/year, startup-friendly

Privacy Management:
  OneTrust: Enterprise privacy platform
  TrustArc: Privacy compliance management
  Ethyca: Automated privacy requests
  Transcend: Data mapping + consent management

Security Questionnaires:
  Vanta Questionnaire Automation
  SecurityScorecard
  BitSight
  Whistic

Vendor Risk Management:
  Vanta
  Drata
  OneTrust Vendor Risk
  Prevalent
```

### Compliance Calendar

```
Monthly:
  □ Access review (who has access to what)
  □ Security training completion check
  □ Vendor risk review (new vendors)

Quarterly:
  □ Policy review and updates
  □ Incident response drill
  □ Penetration test (external)
  □ Board security report

Annually:
  □ SOC 2 audit (Type II)
  □ Risk assessment
  □ Business continuity test
  □ Employee security training refresh
  □ Vendor security reassessment
  □ Privacy policy update
  □ Data mapping refresh
```

## Pitfalls

1. **Ignoring GDPR until fined** — GDPR fines are real (Meta: €1.2B, Amazon: €746M). Compliance is cheaper than fines.
2. **Copy-paste legal docs** — Template terms of service may not cover your specific business model. Get legal review.
3. **No DPA for B2B** — Enterprise customers WILL ask for a DPA. Have one ready.
4. **Open source license ignorance** — Using GPL code in proprietary SaaS can force you to open-source everything. Audit dependencies.
5. **Missing IP assignment** — Contractors without IP assignment clauses own their code. Fix before it's a problem.
6. **SOC 2 theater** — Getting SOC 2 without implementing real controls is fraud. Customers will find out.
7. **No breach response plan** — GDPR requires 72-hour notification. If you discover a breach and don't have a plan, you'll miss the deadline.
8. **Privacy policy not updated** — If your data practices change but your privacy policy doesn't, you're in violation.
9. **Ignoring data residency** — Some customers (government, healthcare, finance) require data in specific regions. Plan for it.
10. **No legal counsel** — Use a startup-friendly law firm (Cooley, Wilson Sonsini, Gunderson). Pro bono legal clinics exist for early-stage.

## Sources

- GDPR Official Text: https://gdpr.eu/
- CCPA Official: https://oag.ca.gov/privacy/ccpa
- SOC 2 (AICPA): https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/trustservicescriteria
- ISO 27001: https://www.iso.org/isoiec-27001-information-security.html
- OWASP Legal: https://owasp.org/
- Choose a License: https://choosealicense.com/
- TLDRLegal: https://tldrlegal.com/
- Vanta (SOC 2 automation): https://www.vanta.com/
- Drata (compliance automation): https://www.drata.com/
- Cooley GO (startup legal): https://www.cooleygo.com/
- Gunderson Dettmer (startup law): https://www.gunder.com/
