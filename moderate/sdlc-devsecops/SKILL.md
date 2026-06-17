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

### Key Concepts

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
  - Version controlled
  - Consistent application

Zero-trust:
  - Never trust, always verify
  - Least privilege access
  - Micro-segmentation
  - Continuous authentication

Security automation:
  - Automated scanning
  - Automated remediation
  - Continuous monitoring
  - Incident response
```

---

## Step 1: Supply Chain Security

### Threat Landscape

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
  1. Compromised package maintainer
  2. Malicious dependency
  3. Build system compromise
  4. Stolen credentials
  5. Dependency confusion
```

### Defense Strategies

```
Dependency management:
  - Pin versions (exact, not ranges)
  - Use lockfiles
  - Verify checksums
  - Audit dependencies regularly
  
  Example (npm):
    {
      "dependencies": {
        "express": "4.18.2"  // Exact version
      }
    }
    
    # Verify integrity
    npm audit
    npm audit fix

Package verification:
  - Check package signatures
  - Verify publisher identity
  - Review package contents
  - Use trusted registries
  
  Tools:
    - npm audit
    - yarn audit
    - pip-audit
    - cargo audit

Dependency confusion:
  - Use scoped packages
  - Configure private registry
  - Block public registry for private packages
  
  Example (npm):
    // .npmrc
    @mycompany:registry=https://npm.mycompany.com/
    registry=https://registry.npmjs.org/
```

---

## Step 2: SBOM (Software Bill of Materials)

### SBOM Concepts

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
    - Linux Foundation standard
    - Comprehensive metadata
    - License information
    - Widely adopted
  
  CycloneDX:
    - OWASP standard
    - Security-focused
    - Vulnerability tracking
    - Modern format
  
  SWID:
    - ISO standard
    - Software identification
    - Used by government
    - Limited adoption
```

### SBOM Generation

```
Tools:
  Syft:
    - Generate SBOMs from containers
    - Multiple output formats
    - Fast and accurate
    
    # Generate SBOM
    syft packages dir:./myapp -o spdx-json > sbom.spdx.json
    syft packages image:myapp:latest -o cyclonedx-json > sbom.cdx.json
  
  Trivy:
    - Security scanner
    - SBOM generation
    - Vulnerability detection
    
    # Generate SBOM
    trivy image --format cyclonedx --output sbom.cdx.json myapp:latest
    
    # Scan for vulnerabilities
    trivy image myapp:latest
  
  cdxgen:
    - CycloneDX generator
    - Multi-language support
    - CI/CD integration
    
    # Generate SBOM
    cdxgen -o sbom.cdx.json .

CI/CD integration:
  # GitHub Actions
  - name: Generate SBOM
    uses: anchore/sbom-action@v0
    with:
      image: myapp:${{ github.sha }}
      format: spdx-json
      output-file: sbom.spdx.json
  
  - name: Upload SBOM
    uses: actions/upload-artifact@v3
    with:
      name: sbom
      path: sbom.spdx.json
```

---

## Step 3: Policy-as-Code

### Policy Frameworks

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
    }

Kyverno:
  - Kubernetes-native policies
  - YAML-based
  - Validation, mutation, generation
  - Easy to use

  Example policy:
    apiVersion: kyverno.io/v1
    kind: ClusterPolicy
    metadata:
      name: require-labels
    spec:
      validationFailureAction: enforce
      rules:
        - name: check-labels
          match:
            any:
              - resources:
                  kinds:
                    - Pod
          validate:
            message: "Label 'app' is required"
            pattern:
              metadata:
                labels:
                  app: "?*"

Sentinel (HashiCorp):
  - HashiCorp products
  - Policy as code
  - Fine-grained control
  - Enterprise features

  Example policy:
    import "strings"
    
    main = rule {
        strings.has_suffix(request.path, ".tf")
    }
```

### Implementation

```
Kubernetes policies:
  # Require resource limits
  apiVersion: kyverno.io/v1
  kind: ClusterPolicy
  metadata:
    name: require-resource-limits
  spec:
    validationFailureAction: enforce
    rules:
      - name: check-limits
        match:
          any:
            - resources:
                kinds:
                  - Pod
        validate:
          message: "Resource limits are required"
          pattern:
            spec:
              containers:
                - resources:
                    limits:
                      memory: "?*"
                      cpu: "?*"

Terraform policies:
  # Ensure encryption at rest
  package terraform.aws
  
  deny[msg] {
      resource := input.resource_changes[_]
      resource.type == "aws_s3_bucket"
      not resource.change.after.server_side_encryption_configuration
      
      msg := sprintf("S3 bucket '%s' must have encryption enabled", [
          resource.name
      ])
  }

API policies:
  # Rate limiting
  package api.rate_limit
  
  default allow = false
  
  allow {
      input.method == "GET"
      rate_limit_ok
  }
  
  rate_limit_ok {
      count_requests < 100
  }
  
  count_requests := count(data.requests[input.user_id])
```

---

## Step 4: Zero-Trust Architecture

### Zero-Trust Principles

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
    - Device health checks
    - Endpoint protection
    - Device compliance
    - Remote wipe capability
  
  Network:
    - Micro-segmentation
    - Encrypted communications
    - No implicit trust
    - Network monitoring
  
  Application:
    - Application-level security
    - API security
    - Input validation
    - Output encoding
  
  Data:
    - Data classification
    - Encryption at rest
    - Encryption in transit
    - Access controls
```

### Implementation

```
Service mesh:
  - mTLS between services
  - Traffic encryption
  - Access policies
  - Observability
  
  Istio example:
    apiVersion: security.istio.io/v1beta1
    kind: PeerAuthentication
    metadata:
      name: default
      namespace: istio-system
    spec:
      mtls:
        mode: STRICT

API gateway:
  - Authentication
  - Authorization
  - Rate limiting
  - Request validation
  
  Kong example:
    plugins:
      - name: jwt
        config:
          secret_is_base64: false
      - name: rate-limiting
        config:
          minute: 100
      - name: acl
        config:
          allow:
            - admin

Identity provider:
  - OAuth 2.0 / OpenID Connect
  - SAML for enterprise
  - MFA enforcement
  - Session management
  
  Keycloak example:
    {
      "realm": "my-realm",
      "clients": [{
        "clientId": "my-app",
        "protocol": "openid-connect",
        "publicClient": false,
        "redirectUris": ["https://myapp.com/*"]
      }]
    }
```

---

## Step 5: Security Automation

### CI/CD Security

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
  - Black-box testing
  
  Tools:
    - OWASP ZAP
    - Burp Suite
    - Nikto
    - Nuclei

SCA (Software Composition Analysis):
  - Analyze dependencies
  - Find known vulnerabilities
  - License compliance
  
  Tools:
    - Snyk
    - Dependabot
    - Renovate
    - Trivy

Container security:
  - Scan images
  - Check configurations
  - Runtime protection
  
  Tools:
    - Trivy
    - Grype
    - Clair
    - Falco
```

### Pipeline Integration

```
GitHub Actions:
  name: Security Scan
  
  on: [push, pull_request]
  
  jobs:
    security:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        
        - name: SAST
          uses: returntocorp/semgrep-action@v1
          with:
            config: p/owasp-top-ten
        
        - name: SCA
          uses: snyk/actions@master
          with:
            args: --all-projects
        
        - name: Container scan
          uses: aquasecurity/trivy-action@master
          with:
            image-ref: myapp:${{ github.sha }}
            format: 'sarif'
            output: 'trivy-results.sarif'
        
        - name: Upload results
          uses: github/codeql-action/upload-sarif@v2
          with:
            sarif_file: 'trivy-results.sarif'

GitLab CI:
  security_scan:
    stage: test
    image: aquasec/trivy
    script:
      - trivy image --severity HIGH,CRITICAL myapp:$CI_COMMIT_SHA
    artifacts:
      reports:
        container_scanning: gl-container-scanning-report.json
```

---

## Step 6: Vulnerability Management

### Vulnerability Lifecycle

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
  - Business impact
  - Exposure

Remediation:
  - Patch management
  - Configuration changes
  - Code fixes
  - Workarounds

Verification:
  - Re-scan after fix
  - Penetration testing
  - Regression testing
  - Monitoring
```

### Implementation

```
Vulnerability scanning:
  # Trivy
  trivy image myapp:latest
  trivy fs --security-checks vuln ./myapp
  
  # Grype
  grype myapp:latest
  grype dir:./myapp
  
  # Snyk
  snyk test
  snyk container test myapp:latest

Prioritization matrix:
  CVSS Score | Exposure | Action
  9.0-10.0   | Internet | Immediate (24h)
  7.0-8.9    | Internet | Urgent (72h)
  9.0-10.0   | Internal | High (1 week)
  7.0-8.9    | Internal | Medium (2 weeks)
  4.0-6.9    | Any      | Normal (30 days)
  0.0-3.9    | Any      | Low (90 days)

SLA targets:
  Critical: 24 hours
  High: 7 days
  Medium: 30 days
  Low: 90 days
```

---

## Step 7: Secrets Management

### Secrets Strategy

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
    - Managed service
    - Automatic rotation
    - Integration with AWS
    - Cost per secret
  
  Azure Key Vault:
    - Managed service
    - HSM backing
    - Integration with Azure
    - RBAC
  
  Google Secret Manager:
    - Managed service
    - Versioning
    - Integration with GCP
    - IAM policies
```

### Implementation

```
Kubernetes secrets:
  # Create secret
  kubectl create secret generic db-creds \
    --from-literal=username=admin \
    --from-literal=password=secret123
  
  # Use in pod
  apiVersion: v1
  kind: Pod
  metadata:
    name: myapp
  spec:
    containers:
      - name: myapp
        image: myapp:latest
        env:
          - name: DB_USERNAME
            valueFrom:
              secretKeyRef:
                name: db-creds
                key: username
          - name: DB_PASSWORD
            valueFrom:
              secretKeyRef:
                name: db-creds
                key: password

External secrets:
  apiVersion: external-secrets.io/v1beta1
  kind: ExternalSecret
  metadata:
    name: db-creds
  spec:
    refreshInterval: 1h
    secretStoreRef:
      name: vault-backend
      kind: SecretStore
    target:
      name: db-creds
    data:
      - secretKey: username
        remoteRef:
          key: secret/data/db
          property: username
      - secretKey: password
        remoteRef:
          key: secret/data/db
          property: password

Secret rotation:
  # AWS Secrets Manager
  aws secretsmanager rotate-secret \
    --secret-id db-creds \
    --rotation-lambda-arn arn:aws:lambda:us-east-1:123456789:function:RotateSecret
```

---

## Step 8: Compliance Automation

### Compliance Frameworks

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
  - Privacy rule
  - Security rule
  - Breach notification

PCI DSS:
  - Payment card data
  - 12 requirements
  - Quarterly scans
  - Annual assessment
```

### Automation

```
Policy enforcement:
  # OPA for SOC 2
  package soc2.security
  
  deny[msg] {
      resource := input.resource_changes[_]
      resource.type == "aws_s3_bucket"
      not resource.change.after.logging
      
      msg := sprintf("S3 bucket '%s' must have logging enabled", [
          resource.name
      ])
  }

Continuous compliance:
  # Regula for Terraform
  regula run ./terraform
  
  # Checkov for IaC
  checkov -d ./terraform
  
  # Prowler for AWS
  prowler aws --compliance soc2

Audit automation:
  # Generate compliance report
  def generate_compliance_report():
      checks = run_compliance_checks()
      findings = analyze_findings(checks)
      report = format_report(findings)
      return report
  
  # Schedule compliance checks
  schedule:
    - cron: "0 0 * * 1"  # Weekly
      job: compliance_check
    - cron: "0 0 1 * *"  # Monthly
      job: compliance_report
```

---

## Step 9: Incident Response

### Incident Response Plan

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
  - Prevent spread
  - Short-term fixes

Eradication:
  - Remove threat
  - Patch vulnerabilities
  - Update configurations
  - Verify clean state

Recovery:
  - Restore systems
  - Verify functionality
  - Monitor for recurrence
  - Document lessons

Post-incident:
  - Root cause analysis
  - Lessons learned
  - Process improvements
  - Update runbooks
```

### Automation

```
Automated response:
  # Falco for runtime detection
  - rule: Unauthorized process
    desc: Detect unauthorized process execution
    condition: spawned_process and not proc.name in (allowed_procs)
    output: "Unauthorized process (user=%user.name command=%proc.cmdline)"
    priority: WARNING

  # Auto-remediation
  def auto_remediate(alert):
      if alert.type == "unauthorized_access":
          revoke_access(alert.user)
          notify_security_team(alert)
      elif alert.type == "malware_detected":
          isolate_host(alert.host)
          start_forensics(alert.host)

Incident management:
  # PagerDuty integration
  from pagerduty import EventsClient
  
  client = EventsClient(integration_key="...")
  client.trigger(
      summary="Security incident detected",
      source="security-scanner",
      severity="critical",
      custom_details={"incident_type": "unauthorized_access"}
  )
```

---

## Step 10: Security Metrics

### Key Metrics

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
  - Risk score
  - Incident frequency

Process metrics:
  - Security review coverage
  - Security training completion
  - Security tool adoption
  - False positive rate
```

### Implementation

```
Dashboard:
  # Security metrics dashboard
  metrics:
    vulnerabilities:
      critical: 5
      high: 23
      medium: 156
      low: 432
    
    mttr:
      critical: 2.3 days
      high: 8.5 days
      medium: 21 days
      low: 45 days
    
    compliance:
      soc2: 94%
      gdpr: 87%
      hipaa: 92%

Reporting:
  # Weekly security report
  def generate_security_report():
      vulnerabilities = scan_vulnerabilities()
      compliance = check_compliance()
      incidents = get_incidents()
      
      report = {
          "vulnerabilities": summarize(vulnerabilities),
          "compliance": compliance,
          "incidents": incidents,
          "recommendations": generate_recommendations()
      }
      
      return report
```

---

## Related Skills

- [sdlc-adversarial-review](sdlc-adversarial-review): Security review and threat modeling
- [sdlc-cicd-pipeline](sdlc-cicd-pipeline): Secure CI/CD implementation
- [sdlc-deployment](sdlc-deployment): Secure deployment practices
- [sdlc-legal-compliance](sdlc-legal-compliance): Regulatory compliance
