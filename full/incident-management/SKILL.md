# Incident Management Skill

**Version:** 5.1.1
**Author:** Dinoudon
**Level:** Full (~2,500 lines)

## Overview

Comprehensive incident management covering all aspects from detection to resolution and learning.

---

## 1. On-Call Best Practices

### Rotation Design

```yaml
# PagerDuty rotation example
rotation:
  name: "Primary Engineering On-Call"
  type: "weekly"
  start_time: "2024-01-01T09:00:00Z"
  users:
    - user: "alice@company.com"
      order: 1
    - user: "bob@company.com"
      order: 2
    - user: "charlie@company.com"
      order: 3
  handoff_day: "monday"
  handoff_time: "09:00"
  timezone: "UTC"
```

### Fairness Metrics

Track per-engineer:
- Total hours on-call per month
- Number of incidents handled
- After-hours pages (nights/weekends)
- Time to first response

Target: ≤40 hours/month, ≤8 pages/week average.

### Alert Hygiene Checklist

- [ ] Every alert has runbook link
- [ ] Alert thresholds reviewed quarterly
- [ ] Flapping alerts fixed within 48 hours
- [ ] No duplicate alerts firing
- [ ] Alert severity matches actual impact
- [ ] Alert message includes: what, why, runbook link, dashboard

### Tools Comparison

| Feature          | PagerDuty | Opsgenie | Rootly |
|------------------|-----------|----------|--------|
| Web UI           | ★★★★★    | ★★★★☆   | ★★★★★ |
| Mobile App       | ★★★★☆    | ★★★★★   | ★★★★☆ |
| Escalation       | ★★★★★    | ★★★★★   | ★★★★☆ |
| Analytics        | ★★★★☆    | ★★★★☆   | ★★★★★ |
| Slack Integration| ★★★★☆    | ★★★★★   | ★★★★★ |
| Price            | $$$       | $$       | $$$    |

---

## 2. Severity Levels

### SEV Definitions

```yaml
severity_levels:
  SEV1:
    name: "Critical"
    description: "Complete service outage affecting all users"
    examples:
      - "Production database down"
      - "Payment processing completely failed"
      - "Security breach confirmed"
    response_sla: "15 minutes to acknowledge, 1 hour to mitigation"
    escalation_trigger: "5 minutes without IC assignment"
    stakeholders: "CTO, VP Engineering, CEO"
    update_frequency: "Every 15 minutes"

  SEV2:
    name: "High"
    description: "Major feature broken, significant user impact"
    examples:
      - "Search functionality down"
      - "API latency >5 seconds"
      - "30% of users affected"
    response_sla: "30 minutes to acknowledge, 2 hours to mitigation"
    escalation_trigger: "15 minutes without response"
    stakeholders: "Engineering Manager, Product Lead"
    update_frequency: "Every 30 minutes"

  SEV3:
    name: "Medium"
    description: "Feature degradation, limited user impact"
    examples:
      - "Slow page loads"
      - "Email delivery delays"
      - "Non-critical service intermittent"
    response_sla: "1 hour to acknowledge, 8 hours to mitigation"
    escalation_trigger: "4 hours without resolution"
    stakeholders: "Team Lead"
    update_frequency: "Every 2 hours"

  SEV4:
    name: "Low"
    description: "Minor issue, minimal user impact"
    examples:
      - "UI glitch"
      - "Non-critical log errors"
      - "Slow admin panel"
    response_sla: "4 hours to acknowledge, 24 hours to resolution"
    escalation_trigger: "24 hours without resolution"
    stakeholders: "Assigned engineer"
    update_frequency: "Daily"

  SEV5:
    name: "Informational"
    description: "No user impact, maintenance or observation"
    examples:
      - "Certificate expiring in 30 days"
      - "Capacity planning alert"
      - "Non-critical dependency update"
    response_sla: "24 hours to acknowledge, 1 week to resolution"
    escalation_trigger: "1 week without action"
    stakeholders: "None required"
    update_frequency: "Weekly"
```

### Decision Tree for Severity Assignment

```
Is the service completely down for all users?
├── YES → SEV1
└── NO
    ├── Are >50% of users affected?
    │   ├── YES → SEV2
    │   └── NO
    │       ├── Is a major feature broken?
    │       │   ├── YES → SEV2
    │       │   └── NO
    │       │       ├── Is performance degraded >50%?
    │       │       │   ├── YES → SEV3
    │       │       │   └── NO
    │       │       │       ├── Is there measurable user impact?
    │       │       │       │   ├── YES → SEV4
    │       │       │       │   └── NO → SEV5
```

---

## 3. Incident Commander Role

### Responsibilities

**Incident Commander (IC):**
- Owns the incident from declaration to resolution
- Coordinates responders
- Makes decisions on escalation/mitigation
- Ensures communication happens
- Declares incident resolved

**Scribe:**
- Documents timeline in incident channel
- Records key decisions and actions
- Updates status page
- Prepares postmortem draft

**Communications Lead:**
- Updates stakeholders
- Posts to status page
- Coordinates with support team
- Manages customer communications

### Escalation Model

```yaml
escalation_paths:
  technical:
    level1: "On-call engineer"
    level2: "Senior engineer / Team lead"
    level3: "Principal engineer / Architect"
    level4: "VP Engineering"
    
  business:
    level1: "Product manager"
    level2: "VP Product"
    level3: "C-level executives"
    
  external:
    level1: "Customer support"
    level2: "Account management"
    level3: "Legal / PR"
```

### IC Checklist

- [ ] Acknowledge alert within SLA
- [ ] Open incident channel (#inc-YYYY-MM-DD-XXXX)
- [ ] Assign roles (scribe, comms lead)
- [ ] Assess severity and set initial level
- [ ] Identify impacted services
- [ ] Notify stakeholders per severity matrix
- [ ] Lead troubleshooting war room
- [ ] Make escalation decisions
- [ ] Declare incident resolved
- [ ] Hand off to postmortem owner

---

## 4. Runbook Automation

### Phased Approach

**Phase 1: Manual with Documentation**
```yaml
runbook:
  name: "Database High CPU"
  service: "postgres-primary"
  triggers:
    - "CPU > 80% for 5 minutes"
  steps:
    - action: "Check active queries"
      command: "SELECT * FROM pg_stat_activity WHERE state = 'active';"
      expected: "Identify long-running queries"
    - action: "Check for locks"
      command: "SELECT * FROM pg_locks WHERE NOT granted;"
      expected: "No lock contention"
    - action: "Check replication lag"
      command: "SELECT * FROM pg_stat_replication;"
      expected: "lag < 100MB"
  automatable: false
```

**Phase 2: Semi-Automated**
```yaml
runbook:
  name: "Database High CPU - Automated"
  automation_tool: "shoreline"
  triggers:
    - metric: "cpu_usage"
      threshold: "> 80%"
      duration: "5m"
  actions:
    - name: "collect_diagnostics"
      type: "command"
      command: |
        SELECT pid, now() - pg_stat_activity.query_start AS duration, query, state
        FROM pg_stat_activity
        WHERE (now() - pg_stat_activity.query_start) > interval '5 minutes';
      auto_execute: true
      
    - name: "suggest_kill_long_queries"
      type: "approval_required"
      condition: "diagnostics.duration > 10m"
      action: "SELECT pg_terminate_backend(pid);"
      
    - name: "scale_up"
      type: "approval_required"
      condition: "cpu_usage > 90% for 15m"
      action: "aws rds modify-db-instance --db-instance-identifier prod-db --db-instance-class db.r5.2xlarge"
```

**Phase 3: Fully Automated**
```yaml
# incident.io automation
automation:
  name: "Auto-remediate Redis Memory"
  trigger:
    type: "metric_threshold"
    metric: "redis_memory_used_bytes"
    threshold: "> 85%"
    duration: "2m"
  actions:
    - name: "evict_old_keys"
      type: "redis_command"
      command: "CONFIG SET maxmemory-policy allkeys-lru"
      
    - name: "notify_on_call"
      type: "pagerduty"
      severity: "SEV3"
      message: "Redis memory high, auto-eviction enabled"
      
    - name: "scale_if_needed"
      type: "terraform"
      condition: "redis_memory_used_bytes > 90% for 10m"
      workspace: "redis-production"
      variables:
        node_count: 3
```

### Tool Comparison

| Tool          | Best For                  | Pricing     | Learning Curve |
|---------------|---------------------------|-------------|----------------|
| Rundeck       | Complex workflows         | Open source | Medium         |
| Shoreline     | Cloud-native automation   | $$$         | Low            |
| incident.io   | Integrated incident mgmt  | $$          | Low            |
| AWS SSM       | AWS-only environments     | $           | Medium         |

---

## 5. Incident Communication

### Status Page Tools

```yaml
status_pages:
  instatus:
    url: "status.company.com"
    components:
      - name: "API"
        description: "REST API endpoints"
      - name: "Web App"
        description: "Customer-facing web application"
      - name: "Database"
        description: "Primary data store"
    notification_channels:
      - email
      - slack
      - sms
      - webhook
      
  statuspage_io:
    url: "status.company.io"
    integrations:
      - pagerduty
      - slack
      - jira
```

### Stakeholder Matrix

| Stakeholder        | Notify At | Channel      | Frequency     |
|--------------------|-----------|--------------|---------------|
| Engineering Team   | SEV3+     | Slack        | Real-time     |
| Product Team       | SEV2+     | Slack/Email  | Every 30 min  |
| Executive Team     | SEV1      | Email/SMS    | Every 15 min  |
| Customer Support   | SEV2+     | Slack        | Every 30 min  |
| Customers          | SEV1-2    | Status Page  | Every 30 min  |
| Legal              | Security  | Email        | As needed     |
| PR                 | SEV1      | Email        | As needed     |

### Templates

**Initial Update:**
```
[INVESTIGATING] We are investigating issues with [SERVICE]. 
Impact: [DESCRIPTION OF IMPACT]
Current status: Our team is actively investigating.
Next update: [TIME]
```

**Ongoing Update:**
```
[UPDATE] Issue with [SERVICE] - [TIME]
Status: [INVESTIGATING/IDENTIFIED/FIXING/MONITORING]
What we know: [CURRENT FINDINGS]
Actions taken: [WHAT WE'VE DONE]
Next update: [TIME]
```

**Resolution:**
```
[RESOLVED] Issue with [SERVICE] - [TIME]
Root cause: [BRIEF DESCRIPTION]
Resolution: [WHAT WE DID]
Duration: [START TIME] to [END TIME] ([TOTAL TIME])
Postmortem: We will conduct a thorough review and share findings within 48 hours.
```

### Communication Checklist

- [ ] Status page updated within 5 minutes of declaration
- [ ] Internal Slack channel created
- [ ] Stakeholders notified per matrix
- [ ] Customer support briefed with talking points
- [ ] Regular updates posted per frequency schedule
- [ ] Resolution communicated within 10 minutes of fix
- [ ] Postmortem date announced

---

## 6. Blameless Postmortems

### Google SRE Process

```yaml
postmortem_template:
  metadata:
    incident_id: "INC-2024-001"
    date: "2024-01-15"
    duration: "2 hours 15 minutes"
    severity: "SEV2"
    author: "alice@company.com"
    reviewers:
      - "bob@company.com"
      - "charlie@company.com"
    
  summary: |
    On January 15th, the payment processing service experienced 
    intermittent failures for 2 hours and 15 minutes, affecting 
    approximately 15% of transactions.
    
  impact:
    users_affected: "15% of payment attempts"
    revenue_impact: "$45,000 in failed transactions"
    duration: "2h 15m"
    services_affected:
      - "payment-service"
      - "checkout-flow"
    
  timeline:
    - time: "14:30 UTC"
      event: "Alert triggered: payment_service_error_rate > 5%"
      actor: "PagerDuty"
    - time: "14:32 UTC"
      event: "IC assigned: Alice"
      actor: "PagerDuty"
    - time: "14:35 UTC"
      event: "War room opened in #inc-2024-01-15-001"
      actor: "Alice"
    - time: "14:45 UTC"
      event: "Identified: Database connection pool exhaustion"
      actor: "Bob"
    - time: "15:00 UTC"
      event: "Mitigation: Increased connection pool size"
      actor: "Bob"
    - time: "16:45 UTC"
      event: "Root cause confirmed: Memory leak in connection handler"
      actor: "Charlie"
    - time: "16:45 UTC"
      event: "Resolution: Deployed hotfix"
      actor: "Alice"
    
  root_cause_analysis:
    five_whys:
      - why: "Payment service failed"
        because: "Database connection pool exhausted"
      - why: "Connection pool exhausted"
        because: "Connections not being released"
      - why: "Connections not released"
        because: "Memory leak in connection handler"
      - why: "Memory leak existed"
        because: "Missing connection cleanup in error path"
      - why: "Missing cleanup not caught"
        because: "No integration test for connection pool under error conditions"
    
    contributing_factors:
      - "Monitoring didn't alert on connection pool saturation until after failures"
      - "Runbook didn't include connection pool checks"
      - "No circuit breaker for database connections"
    
  action_items:
    - id: "AI-001"
      title: "Fix memory leak in connection handler"
      owner: "Bob"
      priority: "P0"
      due_date: "2024-01-16"
      status: "completed"
      
    - id: "AI-002"
      title: "Add connection pool saturation alert"
      owner: "Charlie"
      priority: "P1"
      due_date: "2024-01-20"
      status: "in_progress"
      
    - id: "AI-003"
      title: "Update runbook with connection pool troubleshooting"
      owner: "Alice"
      priority: "P1"
      due_date: "2024-01-20"
      status: "not_started"
      
    - id: "AI-004"
      title: "Add integration test for connection pool under error conditions"
      owner: "Bob"
      priority: "P2"
      due_date: "2024-02-01"
      status: "not_started"
      
    - id: "AI-005"
      title: "Implement circuit breaker for database connections"
      owner: "Charlie"
      priority: "P2"
      due_date: "2024-02-15"
      status: "not_started"
    
  lessons_learned:
    what_went_well:
      - "Fast detection due to alerting"
      - "Quick IC assignment and war room setup"
      - "Good cross-team collaboration"
      
    what_went_wrong:
      - "Initial runbook missing critical checks"
      - "Monitoring gap for connection pool metrics"
      - "No circuit breaker pattern implemented"
      
    where_we_got_lucky:
      - "Issue occurred during low traffic period"
      - "On-call engineer had recent context on payment service"
    
  metrics:
    mtt_detect: "2 minutes"
    mtt_acknowledge: "2 minutes"
    mtt_mitigate: "30 minutes"
    mtt_resolve: "2 hours 15 minutes"
```

### 5 Whys Template

```
Problem: [STATE THE PROBLEM]

Why 1: [WHY DID THIS HAPPEN?]
  → Because: [ANSWER]

Why 2: [WHY DID THAT HAPPEN?]
  → Because: [ANSWER]

Why 3: [WHY DID THAT HAPPEN?]
  → Because: [ANSWER]

Why 4: [WHY DID THAT HAPPEN?]
  → Because: [ANSWER]

Why 5: [WHY DID THAT HAPPEN?]
  → Because: [ROOT CAUSE]

Root Cause: [SUMMARY]
```

### Action Item Tracking

```yaml
action_item_tracking:
  tool: "Jira"
  labels:
    - "postmortem"
    - "incident-YYYY-MM-DD"
  fields:
    priority:
      P0: "Critical - Fix within 24 hours"
      P1: "High - Fix within 1 week"
      P2: "Medium - Fix within 1 month"
      P3: "Low - Fix within quarter"
    owner: "Responsible engineer"
    due_date: "YYYY-MM-DD"
    status:
      - "not_started"
      - "in_progress"
      - "blocked"
      - "completed"
  review_cadence: "Weekly in engineering standup"
```

---

## 7. Incident Metrics

### Key Metrics Formulas

```yaml
metrics:
  MTTD:
    name: "Mean Time to Detect"
    formula: "Sum(Time to detect for all incidents) / Number of incidents"
    target: "< 5 minutes for SEV1, < 15 minutes for SEV2"
    measurement: "Time from incident start to first alert"
    
  MTTA:
    name: "Mean Time to Acknowledge"
    formula: "Sum(Time to acknowledge for all incidents) / Number of incidents"
    target: "< 5 minutes for SEV1, < 15 minutes for SEV2"
    measurement: "Time from alert to human acknowledgment"
    
  MTTR:
    name: "Mean Time to Resolve"
    formula: "Sum(Time to resolve for all incidents) / Number of incidents"
    target: "< 1 hour for SEV1, < 4 hours for SEV2"
    measurement: "Time from incident start to resolution"
    
  MTBF:
    name: "Mean Time Between Failures"
    formula: "Total operational time / Number of failures"
    target: "> 720 hours (30 days)"
    measurement: "Time between incident starts"
    
  MTTM:
    name: "Mean Time to Mitigate"
    formula: "Sum(Time to mitigate for all incidents) / Number of incidents"
    target: "< 30 minutes for SEV1, < 1 hour for SEV2"
    measurement: "Time from incident start to user impact reduction"
    
  Change Failure Rate:
    name: "Deployment Failure Rate"
    formula: "(Failed deployments / Total deployments) × 100"
    target: "< 5%"
    measurement: "Deployments causing incidents"
```

### Dashboard Example (Grafana)

```yaml
# Grafana dashboard config
dashboard:
  title: "Incident Metrics"
  panels:
    - title: "MTTR Trend"
      type: "graph"
      targets:
        - query: "avg(incident_duration_seconds) by (severity)"
      time_range: "30d"
      
    - title: "Incidents by Severity"
      type: "piechart"
      targets:
        - query: "count(incidents) by (severity)"
        
    - title: "MTTD by Service"
      type: "heatmap"
      targets:
        - query: "avg(time_to_detect_seconds) by (service)"
        
    - title: "Open Incidents"
      type: "stat"
      targets:
        - query: "count(incidents{status='active'})"
      thresholds:
        - value: 0
          color: "green"
        - value: 3
          color: "yellow"
        - value: 5
          color: "red"
```

### Monthly Report Template

```
# Incident Report - [MONTH YEAR]

## Summary
- Total incidents: [X]
- SEV1: [X] | SEV2: [X] | SEV3: [X] | SEV4: [X] | SEV5: [X]
- MTTR: [X hours] (target: [Y hours])
- MTTD: [X minutes] (target: [Y minutes])
- MTBF: [X days] (target: [Y days])

## Top 3 Incidents
1. [INC-XXX] - [Brief description] - [Duration]
2. [INC-XXX] - [Brief description] - [Duration]
3. [INC-XXX] - [Brief description] - [Duration]

## Trends
- [Observation about trends]
- [Comparison to previous month]

## Action Items from Postmortems
- [X] total action items
- [Y] completed
- [Z] in progress
- [W] overdue

## Recommendations
1. [Recommendation based on data]
2. [Recommendation based on data]
```

---

## 8. Chaos Engineering

### Chaos Monkey (Netflix)

```yaml
# Spinnaker Chaos Monkey config
chaos_monkey:
  enabled: true
  mean_time_between_days: 5
  min_applications: 2
  grouping: "cluster"
  regions:
    - "us-east-1"
    - "us-west-2"
  opt_out_labels:
    - "chaos-monkey-opt-out"
  termination:
    enabled: true
    probability: 0.5
    max_terminations_per_day: 2
```

### Gremlin

```yaml
# Gremlin attack config
attacks:
  - name: "CPU Stress"
    type: "cpu"
    target:
      type: "kubernetes"
      namespace: "production"
      labels:
        app: "payment-service"
    parameters:
      cores: 4
      load: 100
      duration: 300
      
  - name: "Network Latency"
    type: "network"
    target:
      type: "host"
      hosts: ["web-01", "web-02"]
    parameters:
      latency: 500
      jitter: 100
      duration: 600
      
  - name: "Pod Delete"
    type: "kubernetes"
    target:
      type: "kubernetes"
      namespace: "production"
      labels:
        app: "redis"
    parameters:
      percentage: 50
```

### LitmusChaos (Kubernetes)

```yaml
# LitmusChaos experiment
apiVersion: litmuschaos.io/v1alpha1
kind: ChaosEngine
metadata:
  name: pod-delete-chaos
  namespace: production
spec:
  appinfo:
    appns: production
    applabel: app=nginx
    appkind: deployment
  chaosServiceAccount: litmus-admin
  experiments:
    - name: pod-delete
      spec:
        components:
          env:
            - name: TOTAL_CHAOS_DURATION
              value: '30'
            - name: CHAOS_INTERVAL
              value: '10'
            - name: FORCE
              value: 'false'
```

### AWS Fault Injection Simulator

```yaml
# AWS FIS experiment template
AWSTemplateFormatVersion: '2010-09-09'
Resources:
  EC2TerminateExperiment:
    Type: AWS::FIS::ExperimentTemplate
    Properties:
      Description: "Terminate random EC2 instances"
      Targets:
        myInstances:
          ResourceTags:
            Environment: production
          SelectionMode: "PERCENT(50)"
      Actions:
        terminateInstances:
          ActionId: "aws:ec2:terminate-instances"
          Target:
            Key: "myInstances"
      StopConditions:
        - Source: "aws:cloudwatch:alarm"
          Value: "arn:aws:cloudwatch:us-east-1:123456789012:alarm:HighErrorRate"
      RoleArn: "arn:aws:iam::123456789012:role/FISRole"
```

### Chaos Engineering Checklist

- [ ] Start in staging environment
- [ ] Have rollback plan ready
- [ ] Notify team before experiment
- [ ] Monitor closely during experiment
- [ ] Document findings
- [ ] Gradually increase blast radius
- [ ] Automate successful experiments
- [ ] Schedule during low-traffic periods initially

### Pitfalls

1. **Testing in production too early** → Always start in staging
2. **No rollback plan** → Always have kill switch
3. **Too large blast radius** → Start small, increase gradually
4. **No monitoring** → Watch all metrics during experiments
5. **Blame culture** → Treat failures as learning opportunities

---

## 9. Incident Tooling Comparison

### Comprehensive Comparison

| Feature                | PagerDuty | Opsgenie | incident.io | Rootly | Firehydrant |
|------------------------|-----------|----------|-------------|--------|-------------|
| Alerting               | ★★★★★    | ★★★★★   | ★★★★☆      | ★★★★☆ | ★★★★☆      |
| On-Call Management     | ★★★★★    | ★★★★★   | ★★★★★      | ★★★★★ | ★★★★☆      |
| Incident Response      | ★★★★☆    | ★★★★☆   | ★★★★★      | ★★★★★ | ★★★★★      |
| Status Page            | ★★★☆☆    | ★★★☆☆   | ★★★★★      | ★★★★☆ | ★★★★☆      |
| Postmortem             | ★★★☆☆    | ★★★☆☆   | ★★★★★      | ★★★★★ | ★★★★★      |
| Analytics              | ★★★★☆    | ★★★★☆   | ★★★★★      | ★★★★★ | ★★★★☆      |
| Slack Integration      | ★★★★☆    | ★★★★★   | ★★★★★      | ★★★★★ | ★★★★★      |
| API                    | ★★★★★    | ★★★★★   | ★★★★☆      | ★★★★☆ | ★★★★☆      |
| Runbook Automation     | ★★★☆☆    | ★★★☆☆   | ★★★★☆      | ★★★★☆ | ★★★☆☆      |
| Pricing                | $$$       | $$       | $$$        | $$$    | $$          |
| Best For               | Enterprise| Mid-size | Modern teams| DevOps | SRE teams   |

### Integration Examples

```yaml
# PagerDuty to Slack
pagerduty:
  slack_integration:
    channel: "#incidents"
    events:
      - incident.triggered
      - incident.acknowledged
      - incident.resolved
    template: |
      :rotating_light: *{{incident.title}}*
      Severity: {{incident.severity}}
      Status: {{incident.status}}
      <{{incident.html_url}}|View in PagerDuty>

# Opsgenie webhook
opsgenie:
  webhooks:
    - url: "https://hooks.slack.com/services/XXX"
      events:
        - "Create"
        - "Acknowledge"
        - "Close"
      filters:
        - type: "priority"
          operation: "match"
          field: "priority"
          expected: "P1"

# incident.io Slack integration
incident_io:
  slack:
    workspace: "company.slack.com"
    channels:
      - prefix: "inc-"
        visibility: "private"
    automations:
      - trigger: "incident_created"
        action: "post_message"
        template: |
          :siren: New incident: {{incident.title}}
          Severity: {{incident.severity}}
          Lead: {{incident.lead}}
```

### Migration Guide

**From PagerDuty to incident.io:**
```yaml
migration:
  steps:
    - export_pagerduty_schedules
    - map_severity_levels
    - configure_webhooks
    - migrate_integrations
    - train_team
    - parallel_run_2_weeks
    - cutover
  tools:
    - "PagerDuty API for export"
    - "incident.io import scripts"
    - "Slack workflow builder for notifications"
```

---

## 10. Incident Response Playbooks

### Per-Service Playbook Template

```yaml
# Service Playbook: payment-service
service: "payment-service"
owner: "payments-team"
slack_channel: "#payments-oncall"
pagerduty_service: "payment-service-prod"

playbooks:
  - name: "High Error Rate"
    trigger: "error_rate > 5% for 5 minutes"
    steps:
      - step: 1
        action: "Check service health"
        command: "kubectl get pods -n payments"
        expected: "All pods Running"
      - step: 2
        action: "Check recent deployments"
        command: "kubectl rollout history deployment/payment-service"
        expected: "Identify recent changes"
      - step: 3
        action: "Check error logs"
        command: "kubectl logs -n payments -l app=payment-service --tail=100"
        expected: "Identify error patterns"
      - step: 4
        action: "Check dependencies"
        checks:
          - "Database connectivity"
          - "Redis connectivity"
          - "External API status (Stripe, PayPal)"
      - step: 5
        action: "Mitigation options"
        options:
          - "Rollback if recent deployment"
          - "Scale up if resource constrained"
          - "Enable circuit breaker if dependency failing"
          - "Failover to secondary if available"
    escalation: "If unresolved after 30 minutes, escalate to payments-team-lead"

  - name: "Database Connection Issues"
    trigger: "db_connection_errors > 0"
    steps:
      - step: 1
        action: "Check connection pool"
        command: "SELECT count(*) FROM pg_stat_activity;"
        expected: "< max_connections"
      - step: 2
        action: "Check for long-running queries"
        command: |
          SELECT pid, now() - pg_stat_activity.query_start AS duration, query
          FROM pg_stat_activity
          WHERE (now() - pg_stat_activity.query_start) > interval '5 minutes';
        expected: "No long-running queries"
      - step: 3
        action: "Check replication lag"
        command: "SELECT * FROM pg_stat_replication;"
        expected: "lag < 100MB"
      - step: 4
        action: "Mitigation"
        options:
          - "Kill long-running queries: SELECT pg_terminate_backend(pid);"
          - "Increase connection pool: Update DATABASE_POOL_SIZE"
          - "Failover to replica if primary overloaded"
```

### Decision Trees

```
API Latency High?
├── Check P95 vs P50
│   ├── P95 high, P50 normal → Tail latency issue
│   │   ├── Check specific endpoints
│   │   │   ├── Single endpoint → Service-specific issue
│   │   │   │   └── Follow service playbook
│   │   │   └── All endpoints → Infrastructure issue
│   │   │       ├── Check CPU/Memory → Scale if needed
│   │   │       ├── Check Network → Contact cloud provider
│   │   │       └── Check Database → Follow DB playbook
│   │   └── Check for slow queries
│   └── Both P95 and P50 high → System-wide issue
│       ├── Check recent deployments → Rollback if needed
│       ├── Check resource exhaustion → Scale resources
│       └── Check external dependencies → Enable circuit breaker
```

### Escalation Path Diagrams

```yaml
# Escalation Matrix for payment-service
escalation_paths:
  - level: 1
    name: "On-Call Engineer"
    timeout: "15 minutes"
    contacts:
      - type: "pagerduty"
        service: "payment-service-primary"
        
  - level: 2
    name: "Team Lead"
    timeout: "30 minutes"
    contacts:
      - type: "pagerduty"
        user: "team-lead@company.com"
      - type: "slack"
        channel: "#payments-leads"
        
  - level: 3
    name: "Engineering Manager"
    timeout: "1 hour"
    contacts:
      - type: "pagerduty"
        user: "eng-manager@company.com"
      - type: "phone"
        number: "+1-555-0123"
        
  - level: 4
    name: "VP Engineering"
    timeout: "2 hours"
    conditions:
      - "SEV1 unresolved after 1 hour"
      - "Multiple services affected"
      - "Data loss or security breach"
    contacts:
      - type: "pagerduty"
        user: "vp-eng@company.com"
      - type: "phone"
        number: "+1-555-0456"
```

### Playbook Creation Checklist

- [ ] Service name and owner identified
- [ ] Common failure modes documented
- [ ] Monitoring and alerting configured
- [ ] Diagnostic commands listed
- [ ] Step-by-step troubleshooting guide
- [ ] Mitigation options with commands
- [ ] Escalation paths defined
- [ ] Runbook links in alert descriptions
- [ ] Tested in staging environment
- [ ] Updated after each incident

---

## Appendix A: Common Pitfalls

### Incident Response Pitfalls

1. **No clear IC** → Always assign IC immediately
2. **Too many people in war room** → Limit to essential personnel
3. **No communication** → Update stakeholders regularly
4. **Jumping to solutions** → Understand problem first
5. **Not documenting** → Scribe should capture everything
6. **Blame culture** → Focus on systems, not people
7. **Skipping postmortem** → Always conduct review
8. **Action items not tracked** → Use ticketing system
9. **Not following severity definitions** → Be consistent
10. **Alert fatigue** → Tune alerts regularly

### Communication Pitfalls

1. **Using jargon in customer comms** → Use plain language
2. **Over-promising timelines** → Be realistic
3. **Not updating status page** → Customers need info
4. **Internal comms in public channels** → Use private channels
5. **Not briefing support team** → They face customers

### Postmortem Pitfalls

1. **Blaming individuals** → Focus on systems
2. **Too many action items** → Prioritize top 3-5
3. **No ownership** → Every item needs an owner
4. **No due dates** → Set realistic deadlines
5. **Not following up** → Review in standups

---

## Appendix B: Templates Library

### Slack Channel Topic

```
🚨 Incident: [TITLE] | Severity: [SEV] | IC: @[NAME] | Status: [STATUS] | 
Updates: Every [X] min | Status page: https://status.company.com
```

### Email to Executives

```
Subject: [SEV1] Service Outage - [SERVICE]

Team,

We are currently experiencing a [SEV1] incident affecting [SERVICE].

Impact: [DESCRIPTION]
Duration: [START TIME] - [CURRENT STATUS]
Current Status: [WHAT WE'RE DOING]
ETA to Resolution: [ESTIMATE]
Next Update: [TIME]

Incident Commander: [NAME]
War Room: #[CHANNEL]

We will provide updates every [X] minutes.

Best,
[INCIDENT COMMANDER]
```

### Customer Support Talking Points

```
# Talking Points for [INCIDENT]

## What happened
- [SIMPLE EXPLANATION]

## Impact
- [WHAT CUSTOMERS EXPERIENCE]

## Current status
- [WHAT WE'RE DOING ABOUT IT]

## Workarounds
- [IF ANY AVAILABLE]

## ETA
- [ESTIMATED RESOLUTION TIME]

## Escalation
- If customer asks for compensation: Route to [TEAM]
- If customer threatens legal: Route to [LEGAL]
- If media inquiry: Route to [PR]
```

---

## Appendix C: Tool Commands Quick Reference

### PagerDuty CLI

```bash
# List incidents
pd incident list --status triggered

# Acknowledge incident
pd incident update --incident-id PXXXXXX --status acknowledged

# Create incident
pd incident create --title "Test incident" --service-id PXXXXXX --priority P1

# List schedules
pd schedule list

# Override schedule
pd schedule override --schedule-id PXXXXXX --start "2024-01-15T09:00:00" --end "2024-01-22T09:00:00" --user-id PXXXXXX
```

### Kubernetes Debugging

```bash
# Check pod status
kubectl get pods -n production -l app=payment-service

# View pod logs
kubectl logs -n production -l app=payment-service --tail=100 -f

# Execute into pod
kubectl exec -it -n production payment-service-xxx -- /bin/sh

# Check events
kubectl get events -n production --sort-by='.lastTimestamp'

# Describe pod for issues
kubectl describe pod -n production payment-service-xxx

# Rollback deployment
kubectl rollout undo deployment/payment-service -n production

# Scale deployment
kubectl scale deployment/payment-service -n production --replicas=5
```

### Database Debugging

```bash
# PostgreSQL
# Active connections
SELECT count(*) FROM pg_stat_activity;

# Long running queries
SELECT pid, now() - pg_stat_activity.query_start AS duration, query, state
FROM pg_stat_activity
WHERE (now() - pg_stat_activity.query_start) > interval '5 minutes';

# Lock contention
SELECT * FROM pg_locks WHERE NOT granted;

# Table bloat
SELECT schemaname, tablename, pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS size
FROM pg_tables
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;

# MySQL
# Process list
SHOW FULL PROCESSLIST;

# InnoDB status
SHOW ENGINE INNODB STATUS;

# Slow queries
SELECT * FROM mysql.slow_log ORDER BY start_time DESC LIMIT 10;
```

---

*Document Version: 5.1.1*
*Last Updated: 2024-01-15*
*Author: Dinoudon*
*Maintainer: SRE Team*