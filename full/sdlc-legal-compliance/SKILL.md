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

## Step 8: International Data Transfers

### Transfer Mechanisms

```
GDPR Chapter V requires lawful transfer mechanism:

1. Adequacy Decision:
   Countries with adequate protection:
   - EU/EEA member states
   - UK, Switzerland, Japan, South Korea, Canada (commercial)
   - Israel, New Zealand, Uruguay, Argentina
   
2. Standard Contractual Clauses (SCCs):
   - EU Commission approved templates
   - Must complete Annex I (parties, transfer details)
   - Must complete Annex II (technical/organizational measures)
   - Transfer Impact Assessment (TIA) required
   
3. Binding Corporate Rules (BCRs):
   - For multinational groups
   - Requires DPA approval
   - Takes 12-18 months to obtain
   
4. Derogations (limited):
   - Explicit consent (with caveats)
   - Contract performance
   - Legal claims
   - Important public interest

Practical steps:
  1. Map all data flows (processor, sub-processor)
  2. Identify transfer mechanism for each flow
  3. Execute SCCs with all processors
  4. Complete TIAs for high-risk transfers
  5. Implement supplementary measures (encryption, pseudonymization)
```

### Data Localization Requirements

```
Country-specific requirements:

Russia (Federal Law 242-FZ):
  - Personal data of Russian citizens must be stored in Russia
  - Processing can happen abroad, but storage must be local
  - Fines: Up to 18M RUB (~$200K) per violation

China (PIPL):
  - Critical data must stay in China
  - Cross-border transfers require security assessment
  - Data localization for CIIOs (Critical Information Infrastructure Operators)

Brazil (LGPD):
  - No strict localization requirement
  - But international transfers need adequacy or consent
  
India (DPDP Act):
  - Government can notify categories that must stay in India
  - Cross-border transfers allowed to approved countries

Practical approach:
  - Use regional data centers (AWS, GCP, Azure)
  - Implement data residency controls in application
  - Provide data export for users in regulated regions
```

## Step 9: Contract Management

### Contract Lifecycle

```
Stage 1: Template Selection
  - NDA (mutual or one-way)
  - MSA (Master Service Agreement)
  - SOW (Statement of Work)
  - DPA (Data Processing Agreement)
  - Order Form (commercial terms)
  
Stage 2: Drafting
  - Use approved templates
  - Legal review for non-standard terms
  - Redline tracking (version control)
  
Stage 3: Negotiation
  - Track all changes
  - Escalation path for business terms
  - Legal approval for liability/indemnity changes
  
Stage 4: Execution
  - DocuSign / HelloSign for e-signatures
  - Counter-execution tracking
  - Filing in contract management system
  
Stage 5: Obligation Management
  - SLA monitoring
  - Renewal tracking (90/60/30 day alerts)
  - Compliance verification
  - Audit rights exercise
  
Stage 6: Renewal/Termination
  - Auto-renewal notification (30 days)
  - Price escalation review
  - Performance evaluation
  - Offboarding checklist
```

### Key Contract Clauses

```
Limitation of Liability:
  - Cap: Typically 12 months of fees paid
  - Exclusions: Data breach, IP infringement, gross negligence
  - Carve-outs: Confidentiality, indemnification

Indemnification:
  - Provider indemnifies: IP infringement, data breach
  - Customer indemnifies: Content, misuse, compliance
  - Process: Prompt notice, sole control of defense, cooperation

Service Level Agreement:
  - Uptime: 99.9% (43.8 min/month downtime)
  - Measurement: Monthly, excluding scheduled maintenance
  - Credits: 5% per 0.1% below SLA (capped at 30%)
  - Response times: P1 (1h), P2 (4h), P3 (8h), P4 (24h)

Termination:
  - For convenience: 30-90 days notice
  - For cause: Material breach + 30-day cure period
  - Effect: Data return (30 days), data deletion (90 days)


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


## Step 13: Employment Law

### Employment Agreement Key Terms

```
At-will employment:
  - Either party can terminate at any time
  - No guaranteed employment duration
  - Exceptions: Implied contract, public policy, good faith

Non-compete:
  - Enforceability varies by state
  - California: Generally unenforceable
  - Other states: Must be reasonable in scope, geography, time
  - FTC proposed ban (pending as of 2024)
  - Alternative: Non-solicit (more enforceable)

Non-solicitation:
  - Cannot solicit company's customers or employees
  - Typically 12-24 months
  - More enforceable than non-competes

IP assignment:
  - Work-for-hire: Company owns all work product
  - Prior inventions: Excluded from assignment
  - Side projects: May or may not be covered
  - State laws vary (CA Labor Code §2870)

Confidentiality:
  - Survives termination
  - No time limit (trade secrets)
  - Exceptions: Public knowledge, independent development
```

### Multi-State Employment

```
Compliance considerations:
  - State-specific wage and hour laws
  - Paid sick leave requirements
  - Pay transparency laws
  - Background check restrictions
  - Non-compete enforceability
  - Tax withholding and unemployment insurance

Payroll:
  - Register in each state where employees reside
  - Withhold state income tax (varies by state)
  - Unemployment insurance (SUI) registration
  - Workers' compensation coverage
  - State-specific payroll tax filings

Tools:
  - Gusto: SMB payroll and HR
  - Rippling: HR, IT, finance platform
  - Deel: Global payroll and compliance
  - Remote: International employment
  - Papaya Global: Global payroll management
```

## Step 14: IP Portfolio Management

### Patent Strategy

```
What to patent:
  - Novel technical solutions
  - Unique algorithms or processes
  - Hardware innovations
  - Business methods (limited patentability)

What NOT to patent:
  - Trade secrets (better kept secret)
  - Software alone (Alice Corp v. CLS Bank)
  - Obvious improvements
  - Fast-moving technology (patent takes 2-3 years)

Process:
  1. Invention disclosure (engineer submits idea)
  2. Prior art search (patent attorney)
  3. Patentability assessment
  4. Provisional application ($1,500-3,000)
  5. Non-provisional application ($10,000-15,000)
  6. Prosecution (2-3 years)
  7. Issuance ($1,000-3,000 issue fee)
  8. Maintenance fees (3.5, 7.5, 11.5 years)

Portfolio management:
  - Annual patent audit
  - Prune low-value patents
  - Monitor competitor filings
  - Consider defensive publications
  - Evaluate licensing opportunities
```

### Trademark Registration

```
Process:
  1. Trademark search (TESS database)
  2. Application filing ($250-350 per class)
  3. Examination (3-4 months)
  4. Publication for opposition (30 days)
  5. Registration (if no opposition)
  6. Maintenance (Section 8 & 15 filings)

Classes for tech companies:
  - Class 9: Software, apps, downloadable content
  - Class 35: Business services, SaaS
  - Class 38: Telecommunications
  - Class 41: Education, training
  - Class 42: Software development, hosting

International:
  - Madrid Protocol: Single application for 120+ countries
  - National filings: For countries not in Madrid
  - Budget: $5,000-20,000 for key markets


## Step 15: Commercial Agreements

### SaaS Agreement Key Sections

```
1. Service description: Features, functionality, SLA, support terms
2. Subscription terms: Term length, pricing, payment, usage limits
3. Data processing: DPA reference, data ownership, security measures
4. Intellectual property: IP ownership, license terms, feedback
5. Limitation of liability: Cap, exclusions, carve-outs
6. Termination: For convenience, for cause, data return/deletion
7. Warranties: Service as described, compliance, no known infringement
8. Indemnification: Provider (IP, data breach), Customer (content, misuse)
```

### Contract Negotiation Playbook

```
Common negotiation points:

Liability cap:
  Customer request: Uncapped or 3-5x annual fees
  Our position: 12 months of fees
  Compromise: 2x annual fees for data breach, 1x for all else

Indemnification scope:
  Customer request: Broad indemnification
  Our position: Specific to IP infringement and data breach
  Compromise: Add gross negligence and willful misconduct

Data portability:
  Customer request: Real-time export, all formats
  Our position: Standard export on termination
  Compromise: Monthly export capability + termination export

SLA credits:
  Customer request: Service credits for any downtime
  Our position: Credits for downtime exceeding SLA
  Compromise: Credits at 5% per 0.1% below SLA (cap 30%)

Audit rights:
  Customer request: Annual audit rights
  Our position: SOC 2 report sufficient
  Compromise: SOC 2 + right to audit on material breach
```

## Step 16: Privacy Engineering

### Privacy by Design

```
Principles:
  1. Proactive, not reactive
  2. Privacy as default setting
  3. Privacy embedded into design
  4. Full functionality (positive-sum)
  5. End-to-end security
  6. Visibility and transparency
  7. User-centric

Implementation:
  Data minimization:
    - Collect only what is necessary
    - Delete when no longer needed
    - Anonymize/pseudonymize where possible
  
  Purpose limitation:
    - Document purpose for each data element
    - Enforce purpose in code
    - Re-consent for new purposes
  
  Consent management:
    - Granular consent options
    - Easy withdrawal
    - Consent records maintained
    - Cookie consent management

Tools:
  - OneTrust: Privacy management
  - TrustArc: Privacy compliance
  - Cookiebot: Cookie consent
  - BigID: Data discovery and classification
```

## Step 17: Regulatory Monitoring

### Compliance Calendar

```
Monthly:
  - Review new regulations and guidance
  - Update compliance documentation
  - Employee training completion check
  - Incident review and reporting

Quarterly:
  - Risk assessment update
  - Policy review and updates
  - Vendor compliance review
  - Board compliance report

Annually:
  - SOC 2 audit
  - ISO 27001 surveillance audit
  - GDPR compliance review
  - Employee training refresh
  - Business continuity plan test
  - Insurance policy review

Regulatory sources:
  - FTC.gov (US privacy enforcement)
  - ICO.org.uk (UK GDPR)
  - CNIL.fr (French GDPR)
  - EDPB.europa.eu (EU guidance)
  - NIST.gov (cybersecurity frameworks)
  - IAPP.org (privacy professional resources)
```


## Step 18: Employment Law

### Employment Agreement Key Terms

```
At-will employment:
  - Either party can terminate at any time
  - No guaranteed employment duration
  - Exceptions: Implied contract, public policy, good faith

Non-compete:
  - Enforceability varies by state
  - California: Generally unenforceable
  - Other states: Must be reasonable in scope, geography, time
  - FTC proposed ban (pending as of 2024)
  - Alternative: Non-solicit (more enforceable)

Non-solicitation:
  - Cannot solicit company's customers or employees
  - Typically 12-24 months
  - More enforceable than non-competes

IP assignment:
  - Work-for-hire: Company owns all work product
  - Prior inventions: Excluded from assignment
  - Side projects: May or may not be covered
  - State laws vary (CA Labor Code 2870)

Confidentiality:
  - Survives termination
  - No time limit (trade secrets)
  - Exceptions: Public knowledge, independent development
```

### Multi-State Employment

```
Compliance considerations:
  - State-specific wage and hour laws
  - Paid sick leave requirements
  - Pay transparency laws
  - Background check restrictions
  - Non-compete enforceability
  - Tax withholding and unemployment insurance

Payroll:
  - Register in each state where employees reside
  - Withhold state income tax (varies by state)
  - Unemployment insurance (SUI) registration
  - Workers compensation coverage
  - State-specific payroll tax filings

Tools:
  - Gusto: SMB payroll and HR
  - Rippling: HR, IT, finance platform
  - Deel: Global payroll and compliance
  - Remote: International employment
  - Papaya Global: Global payroll management
```

## Step 19: IP Portfolio Management

### Patent Strategy

```
What to patent:
  - Novel technical solutions
  - Unique algorithms or processes
  - Hardware innovations
  - Business methods (limited patentability)

What NOT to patent:
  - Trade secrets (better kept secret)
  - Software alone (Alice Corp v. CLS Bank)
  - Obvious improvements
  - Fast-moving technology (patent takes 2-3 years)

Process:
  1. Invention disclosure (engineer submits idea)
  2. Prior art search (patent attorney)
  3. Patentability assessment
  4. Provisional application ($1,500-3,000)
  5. Non-provisional application ($10,000-15,000)
  6. Prosecution (2-3 years)
  7. Issuance ($1,000-3,000 issue fee)
  8. Maintenance fees (3.5, 7.5, 11.5 years)

Portfolio management:
  - Annual patent audit
  - Prune low-value patents
  - Monitor competitor filings
  - Consider defensive publications
  - Evaluate licensing opportunities
```

### Trademark Registration

```
Process:
  1. Trademark search (TESS database)
  2. Application filing ($250-350 per class)
  3. Examination (3-4 months)
  4. Publication for opposition (30 days)
  5. Registration (if no opposition)
  6. Maintenance (Section 8 and 15 filings)

Classes for tech companies:
  - Class 9: Software, apps, downloadable content
  - Class 35: Business services, SaaS
  - Class 38: Telecommunications
  - Class 41: Education, training
  - Class 42: Software development, hosting

International:
  - Madrid Protocol: Single application for 120+ countries
  - National filings: For countries not in Madrid
  - Budget: $5,000-20,000 for key markets
```

## Step 20: Security Compliance

### SOC 2 Type II Controls

```
Trust Service Criteria:

1. Security (Common Criteria)
   - CC1: Control environment
   - CC2: Communication and information
   - CC3: Risk assessment
   - CC4: Monitoring activities
   - CC5: Control activities
   - CC6: Logical and physical access
   - CC7: System operations
   - CC8: Change management
   - CC9: Risk mitigation

2. Availability
   - A1.1: Capacity management
   - A1.2: Environmental protections
   - A1.3: Recovery procedures

3. Processing Integrity
   - PI1.1: Data processing objectives

4. Confidentiality
   - C1.1: Confidential information identification

5. Privacy
   - P1-P8: Privacy principles (aligned with GDPR)

Audit timeline:
  - Type I: 3-6 months (design of controls)
  - Type II: 6-12 months (operating effectiveness)
  - Annual renewal required
  - Auditor: Big 4 or specialized firms (A-LIGN, Coalfire)
```

## Step 21: AI/ML Compliance

### AI Governance Framework

```
Principles:
  1. Fairness: No discrimination by protected characteristics
  2. Transparency: Explain how AI decisions are made
  3. Accountability: Clear ownership of AI outcomes
  4. Privacy: Data used for training must be lawful
  5. Safety: AI must not cause harm

Risk assessment:
  Low risk: Spam filter, recommendation engine
  Medium risk: Credit scoring, hiring screening
  High risk: Medical diagnosis, autonomous vehicles
  
EU AI Act requirements:
  - High-risk AI: Conformity assessment, CE marking
  - Limited risk: Transparency obligations
  - Minimal risk: No specific requirements

Practical steps:
  1. Inventory all AI/ML models in production
  2. Classify risk level per model
  3. Implement monitoring for bias/drift
  4. Document training data provenance
  5. Establish human oversight for high-risk decisions
  6. Regular bias audits
  7. Incident response plan for AI failures
```


## Step 22: International Expansion Legal

### Entity Structure

```
US company expanding internationally:

Option 1: Branch office
  - Simple setup
  - No separate legal entity
  - Parent company liable for all obligations
  - Tax: Income taxed in both countries (with treaty relief)

Option 2: Subsidiary (most common)
  - Separate legal entity in foreign country
  - Limited liability (parent protected)
  - Tax: Local taxation, dividends to parent
  - Transfer pricing requirements

Option 3: Partnership/JV
  - Local partner provides market access
  - Shared control and profits
  - Complex governance
  - Used for highly regulated markets

Recommended structure:
  US parent (C-Corp)
    -> Ireland holding company (IP, tax optimization)
      -> UK subsidiary (European operations)
      -> Singapore subsidiary (APAC operations)
      -> Brazil subsidiary (LATAM operations)
```

### Transfer Pricing

```
Arm's length principle:
  Related-party transactions must be priced as if between 
  independent parties in comparable circumstances

Common intercompany transactions:
  - IP license fees (parent to subsidiary)
  - Management fees (shared services)
  - Goods sold (manufacturing to distribution)
  - Loans (intercompany financing)

Documentation requirements:
  - Master file (group overview)
  - Local file (country-specific transactions)
  - Country-by-country report (revenue, tax, employees)

Penalties:
  - US: 20-40% penalty on underpayment
  - EU: Varies by country, can be significant
  - APAC: Increasing enforcement

Tools:
  - Thomson Reuters ONESOURCE
  - KPMG Transfer Pricing Solutions
  - EY Transfer Pricing Navigator
```

## Step 23: Insurance

### Startup Insurance Package

```
Required:
  General liability:
    - Covers bodily injury, property damage
    - $1-2M per occurrence
    - $2-4M aggregate
    - Cost: $500-2,000/year
    
  Workers compensation:
    - Required in most states
    - Covers work-related injuries
    - Cost: Varies by state and payroll
    
  Directors and officers (D&O):
    - Protects board and executives
    - Covers lawsuits, regulatory actions
    - $2-5M coverage
    - Cost: $5,000-20,000/year

Recommended:
  Cyber liability:
    - Covers data breaches, ransomware
    - $1-5M coverage
    - Includes forensics, notification, legal
    - Cost: $5,000-25,000/year
    
  Errors and omissions (E&O):
    - Covers professional service failures
    - $1-5M coverage
    - Important for SaaS, consulting
    - Cost: $3,000-15,000/year
    
  Employment practices liability (EPLI):
    - Covers wrongful termination, discrimination
    - $1-5M coverage
    - Cost: $3,000-10,000/year

Optional:
  Key person insurance:
    - Covers loss of key executive
    - Company is beneficiary
    - Amount: 5-10x executive salary
    
  Business interruption:
    - Covers lost revenue during disruption
    - Tied to property insurance
    - Cost: Varies by revenue and risk
```

## Step 24: Regulatory Filings

### Annual Compliance Calendar

```
Federal:
  - Annual tax return (C-Corp: Form 1120, due April 15)
  - Quarterly estimated taxes (Form 941)
  - Annual information returns (1099s, W-2s)
  - Form D (SEC, if fundraising)

State:
  - Annual report (varies by state)
  - Franchise tax (Delaware: March 1)
  - State income tax (varies)
  - Sales tax filings (monthly/quarterly)
  - Registered agent renewal

Local:
  - Business license renewal
  - City/county tax filings
  - Zoning compliance (if office)

International:
  - VAT returns (monthly/quarterly, EU)
  - Annual accounts (UK, EU)
  - Transfer pricing documentation
  - Country-by-country reporting (if >750M EUR revenue)

Tools:
  - Clerky: Formation and compliance
  - Stripe Atlas: Formation and banking
  - Capbase: Cap table and compliance
  - ZenBusiness: Registered agent and compliance
```

## Step 25: Legal Tech Stack

### Legal Operations Tools

```
Contract management:
  - Ironclad: Contract lifecycle management
  - DocuSign CLM: Contract management
  - Juro: Contract automation
  - SpotDraft: Contract management for startups

Entity management:
  - Carta: Cap table and equity
  - Pulley: Cap table management
  - Clerky: Formation and compliance
  - Stripe Atlas: Formation and banking

IP management:
  - Anaqua: IP portfolio management
  - PatSnap: Patent analytics
  - TrademarkNow: Trademark search
  - IP.com: Prior art search

Compliance:
  - Vanta: SOC 2 automation
  - Drata: Compliance monitoring
  - OneTrust: Privacy management
  - TrustArc: Privacy compliance

Legal research:
  - Westlaw: Legal research
  - LexisNexis: Legal research
  - Casetext: AI legal research
  - Law Insider: Contract templates


## Step 26: E-Commerce Legal

### Terms of Service Template

```
Key sections:
  1. Acceptance of terms
     - By using service, user agrees to terms
     - Clickwrap or browsewrap agreement
     
  2. Description of service
     - What the service provides
     - Service level commitments
     - Modification rights
     
  3. User accounts
     - Registration requirements
     - Account security responsibilities
     - Account termination
     
  4. Acceptable use
     - Permitted uses
     - Prohibited uses
     - Content policies
     - Enforcement
     
  5. Intellectual property
     - Service IP ownership
     - User content license
     - DMCA process
     
  6. Payment terms
     - Pricing and billing
     - Refund policy
     - Cancellation
     
  7. Limitation of liability
     - Cap on damages
     - Exclusion of consequential damages
     - Disclaimer of warranties
     
  8. Dispute resolution
     - Governing law
     - Arbitration clause
     - Class action waiver
```

### E-Commerce Compliance

```
Consumer protection:
  - Clear pricing (no hidden fees)
  - Return/refund policy (prominently displayed)
  - Order confirmation (email within 24h)
  - Shipping information (tracking, delivery estimates)
  - Cancellation rights (cooling-off period)

Payment compliance:
  - PCI DSS (if handling cards)
  - Strong Customer Authentication (SCA, EU)
  - PSD2 compliance (EU payment services)
  - Fraud prevention measures

Tax compliance:
  - Sales tax collection (by jurisdiction)
  - VAT collection (EU, UK)
  - GST collection (Australia, India)
  - Tax exemption handling
  - Tax reporting (1099-K, VAT returns)

Tools:
  - Stripe Tax: Automated tax calculation
  - Avalara: Tax compliance
  - TaxJar: Sales tax automation
  - Vertex: Enterprise tax management
```

## Step 27: Content and Media Law

### Copyright and Fair Use

```
Copyright basics:
  - Automatic protection upon creation
  - Duration: Life + 70 years (individuals)
  - Registration: Optional but strengthens enforcement
  - Fair use: Limited use for commentary, education, parody

Fair use factors:
  1. Purpose: Commercial vs educational
  2. Nature: Creative vs factual
  3. Amount: Substantial vs minimal
  4. Effect: Market impact vs no impact

DMCA safe harbor:
  - Platform not liable for user content if:
    - Designated agent registered
    - Takedown process implemented
    - No actual knowledge of infringement
    - No financial benefit from infringement
  - Counter-notice process for wrongful takedowns

Content licensing:
  - Creative Commons: Open licensing
  - Royalty-free: One-time payment, unlimited use
  - Rights-managed: Specific use, specific price
  - Editorial vs commercial use
```

### Trademark Enforcement

```
Enforcement process:
  1. Monitor for infringement
     - Google Alerts (brand name)
     - Trademark watching service
     - Social media monitoring
     - Marketplace monitoring (Amazon, eBay)
  
  2. Cease and desist letter
     - Identify the infringement
     - State your trademark rights
     - Demand cessation
     - Set deadline for response
  
  3. UDRP (domain disputes)
     - File with WIPO or NAF
     - Prove: confusing similarity, no legitimate interest, bad faith
     - Cost: $1,500-$5,000
     - Timeline: 60-90 days
  
  4. Litigation (last resort)
     - Federal court ( Lanham Act)
     - Injunctive relief
     - Damages (actual or statutory)
     - Attorney fees (in exceptional cases)
```

## Step 28: Data Privacy Operations

### Data Subject Request Handling

```
DSR types:
  - Access: Provide copy of personal data
  - Deletion: Delete personal data
  - Correction: Fix inaccurate data
  - Portability: Export in machine-readable format
  - Objection: Opt out of processing
  - Restriction: Limit processing

Process:
  1. Receive request (email, form, in-app)
  2. Verify identity (2-factor, knowledge-based)
  3. Locate data (search all systems)
  4. Process request (delete, export, correct)
  5. Respond (within 30 days, GDPR)
  6. Document (audit trail)

SLA:
  - GDPR: 30 days
  - CCPA: 45 days
  - Extension: Additional 60 days if complex

Tools:
  - OneTrust: DSR management
  - TrustArc: Privacy operations
  - BigID: Data discovery
  - Securiti: Privacy automation
```

### Privacy Impact Assessment

```
When required:
  - New data processing activity
  - Change to existing processing
  - New technology implementation
  - New data sharing arrangement

Assessment steps:
  1. Describe processing (what, why, how)
  2. Assess necessity and proportionality
  3. Identify risks to individuals
  4. Mitigate risks
  5. Document decisions
  6. Consult DPO (if required)

Risk matrix:
  | Risk | Likelihood | Impact | Mitigation |
  |------|------------|--------|------------|
  | Data breach | Medium | High | Encryption, access controls |
  | Unauthorized access | Low | High | MFA, audit logging |
  | Data loss | Low | Medium | Backups, redundancy |
  | Re-identification | Low | High | Anonymization, pseudonymization |
```

## Step 29: Legal Operations

### Legal Tech Stack

```
Contract management:
  - Ironclad: CLM platform
  - DocuSign CLM: Contract lifecycle
  - Juro: Contract automation
  - SpotDraft: Contract management

Legal research:
  - Westlaw: Legal research
  - LexisNexis: Legal research
  - Casetext: AI legal research
  - Law Insider: Contract templates

Entity management:
  - Carta: Cap table and equity
  - Clerky: Formation and compliance
  - Stripe Atlas: Formation and banking

Compliance:
  - Vanta: SOC 2 automation
  - Drata: Compliance monitoring
  - OneTrust: Privacy management

Billing:
  - Clio: Legal billing
  - TimeSolv: Time tracking
  - Bill4Time: Legal billing
```

## Step 30: Crisis Management

### Crisis Communication Plan

```
Crisis types:
  - Data breach: Unauthorized access to personal data
  - Service outage: Extended downtime affecting customers
  - Legal action: Lawsuit, regulatory investigation
  - PR crisis: Negative media coverage, social media backlash
  - Executive departure: Key leader leaving unexpectedly

Communication team:
  - Spokesperson (CEO or designated)
  - Legal counsel
  - PR/Communications lead
  - Customer support lead
  - Engineering lead (for technical issues)

Communication channels:
  - Status page (service issues)
  - Email (affected customers)
  - Blog (public statements)
  - Social media (real-time updates)
  - Press (media inquiries)
  - Direct outreach (key accounts)

Templates:
  Initial statement: "We are aware of [issue]. We are 
  investigating and will provide updates every [X hours]."
  
  Update: "We have identified [cause]. We are implementing 
  [fix]. Estimated resolution: [time]."
  
  Resolution: "The issue has been resolved. [Root cause]. 
  [Preventive measures]. We apologize for the inconvenience."


## Step 31: SaaS Legal Framework

### SaaS Agreement Key Terms

```
Service description:
  - Features and functionality
  - Service level agreement (SLA)
  - Support terms and response times
  - Maintenance windows

Subscription terms:
  - Term length (monthly, annual, multi-year)
  - Auto-renewal provisions
  - Price escalation clauses
  - Usage limits and overages

Data terms:
  - Data ownership (customer owns their data)
  - Data processing (DPA reference)
  - Data portability (export on termination)
  - Data retention (how long after termination)

IP terms:
  - License grant (to use the service)
  - Feedback license (customer suggestions)
  - Pre-existing IP (each party retains own)
  - Work product (if professional services)

Limitation of liability:
  - Cap: Typically 12 months of fees
  - Exclusions: Data breach, IP infringement, gross negligence
  - Consequential damages: Typically excluded

Termination:
  - For convenience: 30-90 days notice
  - For cause: Material breach + cure period
  - Effect: Data return (30 days), data deletion (90 days)
```

### SLA Framework

```
Uptime SLA:
  - Target: 99.9% (43.8 min/month downtime)
  - Measurement: Monthly, excluding scheduled maintenance
  - Credits: 5% per 0.1% below SLA (capped at 30%)
  - Reporting: Monthly uptime report

Response time SLA:
  - P1 (Critical): 1 hour response, 4 hour resolution
  - P2 (High): 4 hour response, 8 hour resolution
  - P3 (Medium): 8 hour response, 24 hour resolution
  - P4 (Low): 24 hour response, 72 hour resolution

Support SLA:
  - Channels: Email, chat, phone (based on tier)
  - Hours: 24/7 for enterprise, business hours for standard
  - Escalation: Defined escalation path
  - Dedicated CSM: For enterprise accounts
```

## Step 32: Regulatory Compliance

### Industry-Specific Compliance

```
Healthcare (HIPAA):
  - Business associate agreement (BAA)
  - PHI encryption requirements
  - Access controls and audit logging
  - Breach notification (60 days)
  - Annual risk assessment

Financial (SOC 1, PCI DSS):
  - SOC 1: Controls over financial reporting
  - PCI DSS: Cardholder data protection
  - Annual audit
  - Quarterly vulnerability scans
  - Penetration testing (annual)

Government (FedRAMP, ITAR):
  - FedRAMP: Federal cloud security
  - ITAR: Export control for defense
  - Security assessment (3PAO)
  - Continuous monitoring
  - Annual authorization

Education (FERPA):
  - Student data protection
  - Parent consent requirements
  - Data sharing restrictions
  - Directory information policies
```

### Compliance Monitoring

```
Continuous monitoring:
  - Policy compliance checks (automated)
  - Access review (quarterly)
  - Vulnerability scanning (weekly)
  - Penetration testing (annual)
  - Audit log review (monthly)

Reporting:
  - Compliance dashboard (real-time)
  - Risk register (quarterly update)
  - Board report (annual)
  - Regulatory filing (as required)

Tools:
  - Vanta: SOC 2, ISO 27001 automation
  - Drata: Continuous compliance
  - Lacework: Cloud security
  - Prisma Cloud: Multi-cloud security
  - Prowler: AWS security
```

## Step 33: Legal Risk Management

### Risk Assessment Framework

```
Risk categories:
  - Contractual: Breach, disputes, liability
  - Regulatory: Non-compliance, fines, sanctions
  - IP: Infringement, theft, disputes
  - Employment: Discrimination, wrongful termination
  - Data: Breach, privacy violations, misuse
  - Litigation: Lawsuits, class actions

Risk assessment process:
  1. Identify risks (legal team, business stakeholders)
  2. Assess likelihood and impact (1-5 scale)
  3. Determine risk tolerance (accept, mitigate, transfer, avoid)
  4. Implement controls
  5. Monitor and review (quarterly)

Risk matrix:
  | Risk | Likelihood | Impact | Score | Mitigation |
  |------|------------|--------|-------|------------|
  | Data breach | 3 | 5 | 15 | Encryption, access controls, insurance |
  | IP infringement | 2 | 4 | 8 | Patent search, legal review |
  | Employment claim | 2 | 3 | 6 | Training, documentation, insurance |
```

## Step 34: Contract Lifecycle Management

### CLM Process

```
Stage 1: Request
  - Business submits contract request
  - Legal triages (template vs custom)
  - Assign to appropriate attorney

Stage 2: Drafting
  - Use approved templates
  - Customize for specific deal
  - Legal review of non-standard terms

Stage 3: Negotiation
  - Track all changes (redlines)
  - Escalation path for business terms
  - Legal approval for liability/indemnity

Stage 4: Execution
  - E-signature (DocuSign, HelloSign)
  - Counter-execution tracking
  - Filing in contract repository

Stage 5: Management
  - Obligation tracking
  - Renewal management (90/60/30 day alerts)
  - Performance monitoring
  - Compliance verification

Stage 6: Renewal/Termination
  - Performance evaluation
  - Renegotiation if needed
  - Offboarding checklist
```

### Contract Repository

```
Organization:
  - By type (customer, vendor, partner, employment)
  - By status (active, expired, pending)
  - By department (sales, procurement, HR)
  - By risk level (high, medium, low)

Metadata:
  - Contract ID
  - Parties
  - Effective date
  - Expiration date
  - Auto-renewal terms
  - Key terms (value, SLA, liability cap)
  - Owner (internal)
  - Status

Tools:
  - Ironclad: CLM platform
  - DocuSign CLM: Contract management
  - Juro: Contract automation
  - ContractWorks: Contract repository
  - Agiloft: Enterprise CLM
