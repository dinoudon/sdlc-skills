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

---
name: sdlc-legal-compliance
description: "Software company legal and compliance: GDPR, SOC 2, CCPA, privacy policy, terms of service, data processing agreements, IP protection, open source licensing, regulatory compliance, security certifications, data residency, incident response legal, employment law."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, legal, compliance, gdpr, soc2, ccpa, privacy, ip, licensing, sec
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
```
## Step 2: Security Certifications
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
```
## Step 3: Open Source Licensing
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
```
## Step 4: Terms of Service & Legal Documents
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
## Step 5: Intellectual Property
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

```
## Step 6: Enterprise Security Questionnaires
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
```
## Step 7: Incident Response Legal
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
```
## Step 8: Employment Law Essentials
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
## Step 9: Compliance Automation Tools
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
```
## Step 9: Contract Management
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
```
## Step 13: Employment Law
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
```
## Step 14: IP Portfolio Management
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
```
## Step 15: Commercial Agreements
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
## Step 16: Privacy Engineering
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
  
```
## Step 17: Regulatory Monitoring
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
```
## Step 18: Employment Law
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
```
## Step 19: IP Portfolio Management
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
```
## Step 20: Security Compliance
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
```
## Step 21: AI/ML Compliance
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
```
## Step 22: International Expansion Legal
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
```
## Step 23: Insurance
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
```
## Step 24: Regulatory Filings
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
```
## Step 25: Legal Tech Stack
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
```
## Step 26: E-Commerce Legal
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
     
```
## Step 27: Content and Media Law
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
```
## Step 28: Data Privacy Operations
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
```
## Step 29: Legal Operations
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
```
## Step 30: Crisis Management
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
```
## Step 31: SaaS Legal Framework
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
```
## Step 32: Regulatory Compliance
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
```
## Step 33: Legal Risk Management
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

```
## Step 34: Contract Lifecycle Management
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

```
## Step 35: Employment Law Compliance
```
Pre-employment:
  - Job descriptions (accurate, non-discriminatory)
  - Application forms (no prohibited questions)
  - Background checks (FCRA compliance)
  - Drug testing (state-specific requirements)
  - Reference checks (with consent)

Interview compliance:
  - Prohibited questions:
    - Age, race, religion, national origin
    - marital status, family plans
    - Disability, medical conditions
    - Sexual orientation, gender identity
  - Allowed questions:
    - Ability to perform job functions
```
## Step 36: Data Breach Response
```
Phase 1: Detection and Containment (0-24 hours)
  □ Identify scope of breach
  □ Contain the breach (isolate systems, revoke access)
  □ Preserve evidence (logs, forensic images)
  □ Activate incident response team
  □ Engage legal counsel

Phase 2: Assessment (24-48 hours)
  □ Determine what data was affected
  □ Identify affected individuals
  □ Assess risk of harm
  □ Determine notification obligations
  □ Document findings

Phase 3: Notification (48-72 hours)
```
## Step 37: Legal Technology
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
```
## Related Skills
  - [sdlc-finance-ops](sdlc-finance-ops): Software company finance and operations: unit economics, SaaS metrics, fundraising (seed to IPO), fi
  - [sdlc-hiring-talent](sdlc-hiring-talent): Technical hiring and team building: recruiting, interview design, coding assessments, system design 
  - [sdlc-adversarial-review](sdlc-adversarial-review): Multi-agent PR review: 3 specialized reviewers (architecture, security, quality) run in parallel, or