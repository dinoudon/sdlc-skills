# Incident Management Skill

**Version:** 5.1.1
**Author:** Dinoudon
**Level:** Moderate (~1,500 lines)

## Overview

Focused incident management with actionable content for quick reference.

---

## 1. On-Call Best Practices

### Rotation Design

```yaml
rotation:
  name: "Primary Engineering On-Call"
  type: "weekly"
  handoff_day: "monday"
  handoff_time: "09:00"
  timezone: "UTC"
  users:
    - user: "alice@company.com"
      order: 1
    - user: "bob@company.com"
      order: 2
```

### Alert Hygiene Checklist

- [ ] Every alert has runbook link
- [ ] Alert thresholds reviewed quarterly
- [ ] Flapping alerts fixed within 48 hours
- [ ] No duplicate alerts firing
- [ ] Alert severity matches actual impact

### Tools Quick Compare

| Feature          | PagerDuty | Opsgenie | Rootly |
|------------------|-----------|----------|--------|
| Web UI           | ★★★★★    | ★★★★☆   | ★★★★★ |
| Escalation       | ★★★★★    | ★★★★★   | ★★★★☆ |
| Slack Integration| ★★★★☆    | ★★★★★   | ★★★★★ |
| Price            | $$$       | $$       | $$$    |

---

## 2. Severity Levels

### SEV Definitions

```yaml
severity_levels:
  SEV1:
    name: "Critical"
    description: "Complete outage, all users affected"
    response_sla: "15 min acknowledge, 1 hour mitigate"
    update_frequency: "Every 15 minutes"
    
  SEV2:
    name: "High"
    description: "Major feature broken, significant impact"
    response_sla: "30 min acknowledge, 2 hours mitigate"
    update_frequency: "Every 30 minutes"
    
  SEV3:
    name: "Medium"
    description: "Feature degradation, limited impact"
    response_sla: "1 hour acknowledge, 8 hours mitigate"
    update_frequency: "Every 2 hours"
    
  SEV4:
    name: "Low"
    description: "Minor issue, minimal impact"
    response_sla: "4 hours acknowledge, 24 hours resolve"
    update_frequency: "Daily"
```

### Decision Tree

```
All users affected? → SEV1
>50% users or major feature broken? → SEV2
Performance degraded >50%? → SEV3
Measurable user impact? → SEV4
No impact, informational? → SEV5
```

---

## 3. Incident Commander Role

### Key Responsibilities

**IC:** Owns incident, coordinates responders, makes escalation decisions, declares resolution
**Scribe:** Documents timeline, records decisions, updates status page
**Comms Lead:** Updates stakeholders, manages customer communications

### IC Checklist

- [ ] Acknowledge alert within SLA
- [ ] Open incident channel (#inc-YYYY-MM-DD-XXXX)
- [ ] Assign roles (scribe, comms lead)
- [ ] Assess severity
- [ ] Notify stakeholders per matrix
- [ ] Lead troubleshooting
- [ ] Declare resolution
- [ ] Hand off to postmortem owner

---

## 4. Runbook Automation

### Semi-Automated Example

```yaml
runbook:
  name: "Database High CPU"
  automation_tool: "shoreline"
  triggers:
    - metric: "cpu_usage"
      threshold: "> 80%"
      duration: "5m"
  actions:
    - name: "collect_diagnostics"
      command: |
        SELECT pid, now() - pg_stat_activity.query_start AS duration, query
        FROM pg_stat_activity
        WHERE (now() - pg_stat_activity.query_start) > interval '5 minutes';
      auto_execute: true
      
    - name: "suggest_kill_queries"
      type: "approval_required"
      condition: "diagnostics.duration > 10m"
      action: "SELECT pg_terminate_backend(pid);"
```

### Tool Comparison

| Tool          | Best For                  | Pricing     |
|---------------|---------------------------|-------------|
| Rundeck       | Complex workflows         | Open source |
| Shoreline     | Cloud-native automation   | $$$         |
| incident.io   | Integrated incident mgmt  | $$          |

---

## 5. Incident Communication

### Stakeholder Matrix

| Stakeholder        | Notify At | Channel      | Frequency     |
|--------------------|-----------|--------------|---------------|
| Engineering Team   | SEV3+     | Slack        | Real-time     |
| Product Team       | SEV2+     | Slack/Email  | Every 30 min  |
| Executive Team     | SEV1      | Email/SMS    | Every 15 min  |
| Customers          | SEV1-2    | Status Page  | Every 30 min  |

### Templates

**Initial:**
```
[INVESTIGATING] We are investigating issues with [SERVICE].
Impact: [DESCRIPTION]
Next update: [TIME]
```

**Resolution:**
```
[RESOLVED] Issue with [SERVICE]
Root cause: [BRIEF DESCRIPTION]
Duration: [START] to [END] ([TOTAL])
Postmortem within 48 hours.
```

---

## 6. Blameless Postmortems

### Template Structure

```yaml
postmortem:
  metadata:
    incident_id: "INC-2024-001"
    date: "2024-01-15"
    duration: "2 hours 15 minutes"
    severity: "SEV2"
    
  impact:
    users_affected: "15% of payment attempts"
    revenue_impact: "$45,000"
    duration: "2h 15m"
    
  timeline:
    - time: "14:30 UTC"
      event: "Alert triggered"
    - time: "14:32 UTC"
      event: "IC assigned"
    - time: "14:45 UTC"
      event: "Root cause identified"
    - time: "16:45 UTC"
      event: "Resolution deployed"
    
  root_cause:
    five_whys:
      - why: "Service failed"
        because: "DB connections exhausted"
      - why: "Connections exhausted"
        because: "Memory leak in handler"
      - why: "Memory leak existed"
        because: "Missing cleanup in error path"
    
  action_items:
    - title: "Fix memory leak"
      owner: "Bob"
      priority: "P0"
      due: "2024-01-16"
    - title: "Add connection pool alert"
      owner: "Charlie"
      priority: "P1"
      due: "2024-01-20"
    
  lessons:
    went_well:
      - "Fast detection"
      - "Good collaboration"
    went_wrong:
      - "Missing runbook checks"
      - "Monitoring gap"
```

### 5 Whys Template

```
Problem: [STATE PROBLEM]

Why 1: [WHY?] → Because: [ANSWER]
Why 2: [WHY?] → Because: [ANSWER]
Why 3: [WHY?] → Because: [ANSWER]
Why 4: [WHY?] → Because: [ANSWER]
Why 5: [WHY?] → Because: [ROOT CAUSE]
```

---

## 7. Incident Metrics

### Key Formulas

```yaml
metrics:
  MTTD: "Mean Time to Detect"
  formula: "Sum(detection times) / Incidents"
  target: "< 5 min (SEV1), < 15 min (SEV2)"
  
  MTTA: "Mean Time to Acknowledge"
  formula: "Sum(acknowledgment times) / Incidents"
  target: "< 5 min (SEV1), < 15 min (SEV2)"
  
  MTTR: "Mean Time to Resolve"
  formula: "Sum(resolution times) / Incidents"
  target: "< 1 hour (SEV1), < 4 hours (SEV2)"
  
  MTBF: "Mean Time Between Failures"
  formula: "Total uptime / Number of failures"
  target: "> 720 hours (30 days)"
```

### Monthly Report Template

```
# Incident Report - [MONTH]

## Summary
- Total: [X] | SEV1: [X] | SEV2: [X] | SEV3: [X]
- MTTR: [X] (target: [Y])
- MTBF: [X days] (target: [Y days])

## Top Incidents
1. [INC-XXX] - [Description] - [Duration]

## Action Items
- Total: [X] | Completed: [Y] | Overdue: [Z]

## Recommendations
1. [Based on data]
```

---

## 8. Chaos Engineering

### LitmusChaos Example

```yaml
apiVersion: litmuschaos.io/v1alpha1
kind: ChaosEngine
metadata:
  name: pod-delete-chaos
spec:
  appinfo:
    appns: production
    applabel: app=nginx
  experiments:
    - name: pod-delete
      spec:
        components:
          env:
            - name: TOTAL_CHAOS_DURATION
              value: '30'
            - name: CHAOS_INTERVAL
              value: '10'
```

### Checklist

- [ ] Start in staging
- [ ] Have rollback plan
- [ ] Notify team before experiment
- [ ] Monitor closely during
- [ ] Document findings
- [ ] Increase blast radius gradually

---

## 9. Incident Tooling

### Quick Comparison

| Feature            | PagerDuty | Opsgenie | incident.io | Rootly |
|--------------------|-----------|----------|-------------|--------|
| Alerting           | ★★★★★    | ★★★★★   | ★★★★☆      | ★★★★☆ |
| Incident Response  | ★★★★☆    | ★★★★☆   | ★★★★★      | ★★★★★ |
| Postmortem         | ★★★☆☆    | ★★★☆☆   | ★★★★★      | ★★★★★ |
| Slack Integration  | ★★★★☆    | ★★★★★   | ★★★★★      | ★★★★★ |
| Pricing            | $$$       | $$       | $$$        | $$$    |

### Slack Integration Example

```yaml
# incident.io Slack config
incident_io:
  slack:
    channels:
      - prefix: "inc-"
        visibility: "private"
    automations:
      - trigger: "incident_created"
        action: "post_message"
        template: |
          :siren: New incident: {{incident.title}}
          Severity: {{incident.severity}}
```

---

## 10. Incident Response Playbooks

### Service Playbook Template

```yaml
service: "payment-service"
owner: "payments-team"
playbooks:
  - name: "High Error Rate"
    trigger: "error_rate > 5% for 5m"
    steps:
      - action: "Check pods"
        command: "kubectl get pods -n payments"
      - action: "Check recent deploys"
        command: "kubectl rollout history deployment/payment-service"
      - action: "Check logs"
        command: "kubectl logs -n payments -l app=payment-service --tail=100"
      - action: "Mitigation options"
        options:
          - "Rollback if recent deploy"
          - "Scale up if resource issue"
          - "Enable circuit breaker if dependency failing"
    escalation: "If unresolved 30 min, escalate to team lead"
```

### Decision Tree

```
API Latency High?
├── P95 high, P50 normal → Tail latency
│   ├── Single endpoint → Service issue (follow playbook)
│   └── All endpoints → Infrastructure
│       ├── High CPU/Memory → Scale
│       └── Database issues → DB playbook
└── Both P95 and P50 high → System-wide
    ├── Recent deploy → Rollback
    └── Resource exhaustion → Scale resources
```

### Escalation Paths

```yaml
escalation:
  - level: 1
    name: "On-Call Engineer"
    timeout: "15 minutes"
  - level: 2
    name: "Team Lead"
    timeout: "30 minutes"
  - level: 3
    name: "Engineering Manager"
    timeout: "1 hour"
    conditions: ["SEV1 unresolved after 1 hour"]
  - level: 4
    name: "VP Engineering"
    conditions: ["SEV1 > 1 hour", "Multiple services", "Data loss"]
```

---

## Common Pitfalls

### Incident Response
1. **No clear IC** → Assign immediately
2. **Too many in war room** → Essential personnel only
3. **No communication** → Update stakeholders regularly
4. **Jumping to solutions** → Understand problem first
5. **Not documenting** → Scribe captures everything

### Postmortems
1. **Blaming individuals** → Focus on systems
2. **Too many action items** → Top 3-5 only
3. **No ownership** → Every item needs owner
4. **No due dates** → Set realistic deadlines
5. **Not following up** → Review in standups

---

## Quick Reference Commands

### PagerDuty CLI
```bash
pd incident list --status triggered
pd incident update --incident-id PXXXXXX --status acknowledged
pd incident create --title "Test" --service-id PXXXXXX --priority P1
```

### Kubernetes
```bash
kubectl get pods -n production -l app=payment-service
kubectl logs -n production -l app=payment-service --tail=100 -f
kubectl rollout undo deployment/payment-service -n production
kubectl scale deployment/payment-service -n production --replicas=5
```

### PostgreSQL
```sql
SELECT count(*) FROM pg_stat_activity;
SELECT pid, now() - query_start AS duration, query FROM pg_stat_activity 
WHERE (now() - query_start) > interval '5 minutes';
SELECT * FROM pg_locks WHERE NOT granted;
```

---

*Document Version: 5.1.1*
*Author: Dinoudon*