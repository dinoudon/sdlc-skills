---
name: sdlc-devsecops
description: "DevSecOps: supply chain security, SBOMs, policy-as-code, zero-trust, security automation."
version: 6.3.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [devsecops, supply-chain, sbom, policy-as-code, zero-trust, security-automation]
---

---
name: sdlc-devsecops
description: "DevSecOps: supply chain security, SBOMs, policy-as-code, zero-trust, security automation."
version: 6.3.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [devsecops, supply-chain, sbom, policy-as-code, zero-trust, security-automation]
---

# DevSecOps Skill

Integrate security into the software development lifecycle. Covers supply chain security, SBOMs, policy-as-code, zero-trust, and security automation.


## When to Use This Skill
- Implementing supply chain security
- Generating and managing SBOMs
- Writing policy-as-code
- Implementing zero-trust architecture
- Automating security checks
- Secure CI/CD pipelines
- Vulnerability management
- Compliance automation
## Overview
DevSecOps integrates security practices into the DevOps workflow. It shifts security left (earlier in the lifecycle) and makes it continuous (automated at every stage).
```
Shift left:
  - Security earlier in lifecycle
  - Catch issues before production
  - Reduce cost of fixes
  - Developer responsibility

Supply chain security:
  - Protect dependencies
  - Verify integrity
  - Monitor for vulnerabilities
  - SBOMs for transparency

Policy-as-code:
  - Security rules as code
  - Automated enforcement
```
## Step 1: Supply Chain Security
```
Supply chain attacks:
  - Dependency confusion
  - Typosquatting
  - Malicious packages
  - Compromised build tools
  - Stolen credentials

Notable incidents:
  - SolarWinds (2020)
  - Log4Shell (2021)
  - ua-parser-js (2021)
  - colors.js (2022)
  - xz-utils (2024)

Attack vectors:
```
## Step 2: SBOM (Software Bill of Materials)
```
What is SBOM:
  - Inventory of software components
  - Dependencies and versions
  - Licenses and vulnerabilities
  - Machine-readable format

Why SBOM:
  - Transparency
  - Vulnerability tracking
  - License compliance
  - Incident response
  - Regulatory requirements

SBOM formats:
  SPDX:
```
## Step 3: Policy-as-Code
```
OPA (Open Policy Agent):
  - General-purpose policy engine
  - Rego language
  - Kubernetes, API, data policies
  - CNCF graduated project

  Example policy:
    package authz
    
    default allow = false
    
    allow {
        input.method == "GET"
        input.path == ["api", "users"]
        input.user.role == "admin"
```
## Step 4: Zero-Trust Architecture
```
Core principles:
  1. Never trust, always verify
  2. Least privilege access
  3. Assume breach
  4. Verify explicitly
  5. Minimize blast radius

Implementation pillars:
  Identity:
    - Strong authentication
    - Multi-factor authentication
    - Continuous verification
    - Identity-based access
  
  Device:
```
## Step 5: Security Automation
```
SAST (Static Application Security Testing):
  - Analyze source code
  - Find vulnerabilities early
  - IDE integration
  
  Tools:
    - SonarQube (multi-language)
    - Semgrep (pattern matching)
    - CodeQL (GitHub)
    - Bandit (Python)
    - ESLint Security (JavaScript)

DAST (Dynamic Application Security Testing):
  - Test running application
  - Find runtime vulnerabilities
```
## Step 6: Vulnerability Management
```
Discovery:
  - Automated scanning
  - Bug bounty programs
  - Penetration testing
  - Security advisories

Assessment:
  - CVSS scoring
  - Context analysis
  - Exploitability assessment
  - Business impact

Prioritization:
  - Severity (Critical, High, Medium, Low)
  - Exploitability
```
## Step 7: Secrets Management
```
Types of secrets:
  - API keys
  - Database credentials
  - TLS certificates
  - Encryption keys
  - OAuth tokens

Storage options:
  HashiCorp Vault:
    - Dynamic secrets
    - Secret rotation
    - Audit logging
    - High availability
  
  AWS Secrets Manager:
```
## Step 8: Compliance Automation
```
SOC 2:
  - Security
  - Availability
  - Processing integrity
  - Confidentiality
  - Privacy

GDPR:
  - Data protection
  - Privacy by design
  - Right to erasure
  - Data portability

HIPAA:
  - Healthcare data
```
## Step 9: Incident Response
```
Preparation:
  - Incident response team
  - Communication channels
  - Runbooks and playbooks
  - Tools and access

Detection:
  - Monitoring and alerting
  - Log analysis
  - Threat intelligence
  - User reports

Containment:
  - Isolate affected systems
  - Preserve evidence
```
## Step 10: Security Metrics
```
Vulnerability metrics:
  - Mean time to detect (MTTD)
  - Mean time to remediate (MTTR)
  - Vulnerability density
  - Open vulnerability count

Compliance metrics:
  - Compliance score
  - Audit findings
  - Policy violations
  - Control effectiveness

Security posture:
  - Security coverage
  - Security debt
```
## Related Skills
- [sdlc-adversarial-review](sdlc-adversarial-review): Security review and threat modeling
- [sdlc-cicd-pipeline](sdlc-cicd-pipeline): Secure CI/CD implementation
- [sdlc-deployment](sdlc-deployment): Secure deployment practices
- [sdlc-legal-compliance](sdlc-legal-compliance): Regulatory compliance