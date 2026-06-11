# Incident Management Skill

**Version:** 5.1.1
**Author:** Dinoudon
**Level:** Slim (~600 lines)

## Overview

Essential incident management - checklists, examples, pitfalls.

---

## 1. On-Call Essentials

### Alert Hygiene Checklist
- [ ] Every alert has runbook link
- [ ] Thresholds reviewed quarterly
- [ ] No duplicate alerts
- [ ] Severity matches impact

### Tools Quick Compare
| Tool      | Best For      | Price |
|-----------|---------------|-------|
| PagerDuty | Enterprise    | $$$   |
| Opsgenie  | Mid-size      | $$    |
| Rootly    | Modern teams  | $$$   |

---

## 2. Severity Levels

| SEV | Definition                    | Response SLA              |
|-----|-------------------------------|---------------------------|
| 1   | Complete outage, all users    | 15 min ack, 1 hr mitigate |
| 2   | Major feature broken          | 30 min ack, 2 hr mitigate |
| 3   | Feature degradation           | 1 hr ack, 8 hr mitigate   |
| 4   | Minor issue                   | 4 hr ack, 24 hr resolve   |
| 5   | Informational                 | 24 hr ack, 1 week resolve |

---

## 3. Incident Commander Checklist

- [ ] Acknowledge alert within SLA
- [ ] Open incident channel
- [ ] Assign scribe and comms lead
- [ ] Assess severity
- [ ] Notify stakeholders
- [ ] Lead troubleshooting
- [ ] Declare resolution
- [ ] Hand off to postmortem

---

## 4. Runbook Example

```yaml
runbook:
  name: "Database High CPU"
  triggers:
    - metric: "cpu_usage"
      threshold: "> 80%"
  actions:
    - name: "Check queries"
      command: "SELECT pid, duration, query FROM pg_stat_activity WHERE duration > 5m"
      auto_execute: true
    - name: "Kill long queries"
      type: "approval_required"
      action: "SELECT pg_terminate_backend(pid)"
```

---

## 5. Communication Templates

**Initial:**
```
[INVESTIGATING] Issues with [SERVICE]. Impact: [DESC]. Next update: [TIME]
```

**Resolution:**
```
[RESOLVED] [SERVICE] issue fixed. Root cause: [CAUSE]. Duration: [TIME]. Postmortem in 48h.
```

### Stakeholder Matrix
| Stakeholder   | Notify At | Channel | Frequency    |
|---------------|-----------|---------|--------------|
| Engineering   | SEV3+     | Slack   | Real-time    |
| Product       | SEV2+     | Email   | Every 30 min |
| Executives    | SEV1      | SMS     | Every 15 min |
| Customers     | SEV1-2    | Status  | Every 30 min |

---

## 6. Postmortem Template

```yaml
postmortem:
  incident_id: "INC-2024-001"
  date: "2024-01-15"
  duration: "2h 15m"
  severity: "SEV2"
  
  impact:
    users_affected: "15%"
    revenue: "$45K"
    
  timeline:
    - time: "14:30" 
      event: "Alert triggered"
    - time: "14:32"
      event: "IC assigned"
    - time: "16:45"
      event: "Resolved"
    
  root_cause: "Memory leak in connection handler"
  
  action_items:
    - title: "Fix leak"
      owner: "Bob"
      priority: "P0"
    - title: "Add alert"
      owner: "Charlie"
      priority: "P1"
    
  lessons:
    went_well: ["Fast detection", "Good collaboration"]
    went_wrong: ["Missing runbook", "Monitoring gap"]
```

---

## 7. Key Metrics

| Metric | Formula                          | Target            |
|--------|----------------------------------|-------------------|
| MTTD   | Detection time / Incidents       | < 5 min (SEV1)    |
| MTTA   | Acknowledgment time / Incidents  | < 5 min (SEV1)    |
| MTTR   | Resolution time / Incidents      | < 1 hr (SEV1)     |
| MTBF   | Uptime / Failures                | > 30 days         |

---

## 8. Chaos Engineering

### Checklist
- [ ] Start in staging
- [ ] Have rollback plan
- [ ] Notify team first
- [ ] Monitor during experiment
- [ ] Document findings
- [ ] Increase radius gradually

### Example (LitmusChaos)
```yaml
apiVersion: litmuschaos.io/v1alpha1
kind: ChaosEngine
metadata:
  name: pod-delete
spec:
  experiments:
    - name: pod-delete
      spec:
        components:
          env:
            - name: TOTAL_CHAOS_DURATION
              value: '30'
```

---

## 9. Tooling Comparison

| Feature           | PagerDuty | Opsgenie | incident.io | Rootly |
|-------------------|-----------|----------|-------------|--------|
| Alerting          | ★★★★★    | ★★★★★   | ★★★★☆      | ★★★★☆ |
| Incident Response | ★★★★☆    | ★★★★☆   | ★★★★★      | ★★★★★ |
| Postmortem        | ★★★☆☆    | ★★★☆☆   | ★★★★★      | ★★★★★ |
| Price             | $$$       | $$       | $$$        | $$$    |

---

## 10. Service Playbook Template

```yaml
service: "payment-service"
playbooks:
  - name: "High Error Rate"
    trigger: "error_rate > 5% for 5m"
    steps:
      - action: "Check pods"
        command: "kubectl get pods -n payments"
      - action: "Check logs"
        command: "kubectl logs -l app=payment --tail=100"
      - action: "Mitigations"
        options:
          - "Rollback if recent deploy"
          - "Scale up if resource issue"
          - "Circuit breaker if dependency down"
    escalation: "30 min → team lead, 1 hr → manager"
```

### Decision Tree
```
High latency?
├── Single endpoint → Service playbook
└── All endpoints → Infrastructure
    ├── High CPU → Scale
    └── DB issues → DB playbook
```

---

## Common Pitfalls

### Incident Response
1. **No IC assigned** → Assign immediately
2. **Too many responders** → Essential only
3. **No updates** → Communicate regularly
4. **Skip understanding** → Diagnose before fixing
5. **No documentation** → Scribe everything

### Postmortems
1. **Blame people** → Focus on systems
2. **Too many items** → Top 3-5 only
3. **No owner** → Assign responsibility
4. **No deadlines** → Set due dates
5. **No follow-up** → Track in standups

---

## Quick Commands

### Kubernetes
```bash
kubectl get pods -n prod -l app=myapp
kubectl logs -n prod -l app=myapp --tail=100 -f
kubectl rollout undo deployment/myapp -n prod
```

### PostgreSQL
```sql
SELECT count(*) FROM pg_stat_activity;
SELECT pid, duration, query FROM pg_stat_activity WHERE duration > 5m;
```

### PagerDuty
```bash
pd incident list --status triggered
pd incident update --incident-id PXXXXXX --status acknowledged
```

---

*Version: 5.1.1 | Author: Dinoudon*