---
layout: default
title: Monitoring and Observability
permalink: /monitoring/
---

# Monitoring and Observability

Comprehensive monitoring and observability strategy for Azure Landing Zone infrastructure.

## Overview

Our monitoring solution provides end-to-end visibility across all Azure resources, applications, and services using Azure Monitor, Log Analytics, and Application Insights.

## Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Applications  │───▶│  Azure Monitor  │───▶│   Dashboards    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
┌─────────────────┐           │                ┌─────────────────┐
│ Infrastructure  │───────────┼───────────────▶│     Alerts      │
└─────────────────┘           │                └─────────────────┘
┌─────────────────┐           ▼                ┌─────────────────┐
│    Security     │    ┌─────────────────┐    │   Automation    │
└─────────────────┴───▶│ Log Analytics   │───▶│   Responses     │
                        └─────────────────┘    └─────────────────┘
```

## Components

### 1. Azure Monitor

Central monitoring service that collects, analyzes, and acts on telemetry from cloud and on-premises environments.

**Key Features:**
- Metrics collection and analysis
- Log aggregation and querying
- Alerting and notifications
- Workbooks and dashboards

### 2. Log Analytics Workspace

Centralized repository for log data with powerful query capabilities.

```kusto
// Example: Failed login attempts
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(1h)
| summarize count() by Account, Computer
| order by count_ desc
```

### 3. Application Insights

Application performance monitoring (APM) service for web applications.

**Monitored Metrics:**
- Response times
- Failure rates
- Dependency calls
- User sessions
- Custom metrics

### 4. Azure Sentinel

Security Information and Event Management (SIEM) solution.

## Monitoring Strategy

### Infrastructure Monitoring

#### Virtual Machines
```json
{
  "metrics": [
    "Percentage CPU",
    "Available Memory Bytes",
    "Disk Read Bytes/sec",
    "Disk Write Bytes/sec",
    "Network In Total",
    "Network Out Total"
  ],
  "alertRules": [
    {
      "name": "High CPU Usage",
      "condition": "Percentage CPU > 80",
      "duration": "5 minutes",
      "severity": "Warning"
    }
  ]
}
```

#### Storage Accounts
- Availability metrics
- Transaction metrics
- Capacity metrics
- Authentication metrics

#### Network Resources
- Bandwidth utilization
- Packet drops
- Connection metrics
- DDoS attack metrics

### Application Monitoring

#### Web Applications
```javascript
// Application Insights configuration
import { ApplicationInsights } from '@microsoft/applicationinsights-web';

const appInsights = new ApplicationInsights({
  config: {
    instrumentationKey: 'your-instrumentation-key',
    enableAutoRouteTracking: true,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true
  }
});

appInsights.loadAppInsights();
appInsights.trackPageView();
```

#### Database Monitoring
- Query performance
- Blocking processes
- Connection pool metrics
- Storage utilization

## Dashboards

### Executive Dashboard

Key metrics for leadership:
- Overall system health
- Cost trends
- Security incidents
- Compliance status

### Operations Dashboard

Technical metrics for operations teams:
- Resource utilization
- Performance metrics
- Alert status
- Deployment status

### Security Dashboard

Security-focused metrics:
- Threat detection
- Vulnerability assessments
- Access patterns
- Compliance violations

## Alerting Strategy

### Alert Categories

1. **Critical Alerts** (P0)
   - Service outages
   - Security breaches
   - Data loss incidents

2. **High Priority** (P1)
   - Performance degradation
   - Failed deployments
   - Capacity warnings

3. **Medium Priority** (P2)
   - Configuration drift
   - Cost anomalies
   - Compliance violations

4. **Low Priority** (P3)
   - Informational alerts
   - Maintenance reminders
   - Optimization suggestions

### Alert Rules Configuration

```json
{
  "alertRules": [
    {
      "name": "VM High CPU",
      "description": "Alert when VM CPU usage exceeds 80%",
      "condition": {
        "metric": "Percentage CPU",
        "operator": "GreaterThan",
        "threshold": 80,
        "timeAggregation": "Average",
        "windowSize": "PT5M"
      },
      "actions": [
        {
          "actionGroupId": "/subscriptions/.../actionGroups/ops-team",
          "webhookProperties": {}
        }
      ]
    }
  ]
}
```

### Action Groups

```bash
# Create action group for operations team
az monitor action-group create \
  --resource-group rg-monitoring \
  --name ops-team \
  --short-name opstm \
  --action email ops-lead ops-lead@company.com \
  --action sms ops-oncall +1234567890
```

## Log Management

### Log Categories

1. **Activity Logs**
   - Administrative operations
   - Service health events
   - Resource health events

2. **Diagnostic Logs**
   - Application logs
   - Security logs
   - Audit logs

3. **Metrics Logs**
   - Performance counters
   - Custom metrics
   - Platform metrics

### Log Retention Policies

| Log Type | Retention Period | Storage Tier |
|----------|------------------|--------------|
| Security Logs | 2 years | Premium |
| Audit Logs | 7 years | Standard |
| Performance Logs | 90 days | Standard |
| Debug Logs | 30 days | Cool |

### Sample Kusto Queries

#### Security Events
```kusto
SecurityEvent
| where TimeGenerated > ago(24h)
| where EventLevelName == "Error"
| summarize count() by EventID, Activity
| order by count_ desc
```

#### Performance Analysis
```kusto
Perf
| where TimeGenerated > ago(1h)
| where ObjectName == "Processor"
| where CounterName == "% Processor Time"
| summarize avg(CounterValue) by Computer
| order by avg_CounterValue desc
```

#### Application Insights
```kusto
requests
| where timestamp > ago(1d)
| where success == false
| summarize count() by resultCode, name
| order by count_ desc
```

## Automation and Response

### Auto-scaling Rules

```json
{
  "profiles": [
    {
      "name": "Scale based on CPU",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      },
      "rules": [
        {
          "scaleAction": {
            "direction": "Increase",
            "type": "ChangeCount",
            "value": "1",
            "cooldown": "PT5M"
          },
          "metricTrigger": {
            "metricName": "Percentage CPU",
            "operator": "GreaterThan",
            "threshold": 70,
            "timeAggregation": "Average",
            "timeWindow": "PT5M"
          }
        }
      ]
    }
  ]
}
```

### Automated Remediation

Logic Apps for common remediation scenarios:
- Restart failed services
- Scale resources automatically
- Create support tickets
- Send notifications

## Cost Monitoring

### Budget Alerts

```bash
# Create budget with alerts
az consumption budget create \
  --resource-group rg-monitoring \
  --budget-name monthly-budget \
  --amount 1000 \
  --time-period start-date=2024-01-01 \
  --notifications \
    contact-emails="finance@company.com" \
    threshold=80 \
    threshold-type=Actual
```

### Cost Analysis Queries

```kusto
Usage
| where TimeGenerated > startofmonth(now())
| where MeterCategory contains "Virtual"
| summarize TotalCost = sum(PretaxCost) by MeterSubCategory
| order by TotalCost desc
```

## Best Practices

### 1. Monitoring Design
- Monitor user experience first
- Use structured logging
- Implement distributed tracing
- Set up synthetic monitoring

### 2. Alert Fatigue Prevention
- Use appropriate alert thresholds
- Implement alert suppression rules
- Group related alerts
- Regular alert rule reviews

### 3. Data Management
- Implement proper retention policies
- Use appropriate storage tiers
- Regular data archival
- Monitor storage costs

### 4. Security Monitoring
- Monitor privileged access
- Track configuration changes
- Implement behavioral analytics
- Regular security reviews

---

[← Security Policies](/security-policies/) | [Back to Home](/)
