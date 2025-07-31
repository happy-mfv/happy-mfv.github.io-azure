---
layout: default
title: Security Policies
---

# Security Policies

This document outlines the security policies and compliance controls implemented in our Azure Landing Zone.

## Overview

Our security framework is built on the principle of "Security by Default" and implements multiple layers of protection following the Zero Trust model.

## Azure Policy Definitions

### 1. Network Security Policies

#### Deny Public IP Creation
```json
{
  "displayName": "Deny creation of public IP",
  "description": "This policy denies creation of Public IPs under the assigned scope.",
  "mode": "All",
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.Network/publicIPAddresses"
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

#### Require Network Security Group
```json
{
  "displayName": "Require Network Security Group on subnets",
  "description": "This policy requires a Network Security Group to be associated with subnets.",
  "mode": "All",
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Network/virtualNetworks/subnets"
        },
        {
          "field": "Microsoft.Network/virtualNetworks/subnets/networkSecurityGroup",
          "exists": "false"
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

### 2. Storage Security Policies

#### Secure Transfer Required
```json
{
  "displayName": "Secure transfer to storage accounts should be enabled",
  "description": "Audit requirement of Secure transfer in your storage account.",
  "mode": "Indexed",
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
          "notEquals": "true"
        }
      ]
    },
    "then": {
      "effect": "audit"
    }
  }
}
```

### 3. Compute Security Policies

#### VM Disk Encryption
```json
{
  "displayName": "Virtual machines should encrypt temp disks, caches, data flows",
  "description": "Virtual machines without enabled encryption will be monitored by Azure Security Center",
  "mode": "Indexed",
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
      "effect": "AuditIfNotExists",
      "details": {
        "type": "Microsoft.Security/assessments",
        "name": "d57a4221-a804-52a3-a6f5-7c9a36b9b2c7"
      }
    }
  }
}
```

## Resource Tagging Standards

### Required Tags

All resources must include the following tags:

| Tag Name | Description | Example |
|----------|-------------|---------|
| Environment | Deployment environment | `prod`, `dev`, `test` |
| Project | Project or application name | `azure-landing-zone` |
| Owner | Business owner or team | `platform-team` |
| CostCenter | Cost center for billing | `IT-001` |
| Criticality | Business criticality | `critical`, `high`, `medium`, `low` |

### Tag Policy Example

```json
{
  "displayName": "Require specified tags on resources",
  "description": "Enforces required tags and their values on resources",
  "mode": "Indexed",
  "policyRule": {
    "if": {
      "anyOf": [
        {
          "field": "[concat('tags[', parameters('tagName1'), ']')]",
          "exists": "false"
        },
        {
          "field": "[concat('tags[', parameters('tagName2'), ']')]",
          "exists": "false"
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

## Identity and Access Management

### Azure AD Configuration

1. **Multi-Factor Authentication (MFA)**
   - Required for all privileged accounts
   - Conditional access policies enforced

2. **Privileged Identity Management (PIM)**
   - Just-in-time access for administrative roles
   - Access reviews and approvals

3. **Role-Based Access Control (RBAC)**
   - Principle of least privilege
   - Custom roles for specific requirements

### Service Principal Security

```bash
# Create service principal with limited scope
az ad sp create-for-rbac \
  --name "azure-landing-zone-sp" \
  --role "Contributor" \
  --scopes "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
```

## Network Security

### Network Security Groups (NSG)

Default NSG rules for different tiers:

#### Web Tier NSG Rules
```json
{
  "securityRules": [
    {
      "name": "AllowHTTP",
      "properties": {
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 100,
        "direction": "Inbound"
      }
    },
    {
      "name": "AllowHTTPS",
      "properties": {
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "443",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 101,
        "direction": "Inbound"
      }
    }
  ]
}
```

### Azure Firewall Rules

```json
{
  "networkRuleCollections": [
    {
      "name": "AllowInternetOutbound",
      "priority": 100,
      "action": {
        "type": "Allow"
      },
      "rules": [
        {
          "name": "AllowHTTP",
          "protocols": ["TCP"],
          "sourceAddresses": ["10.0.0.0/16"],
          "destinationAddresses": ["*"],
          "destinationPorts": ["80", "443"]
        }
      ]
    }
  ]
}
```

## Compliance Controls

### Regulatory Compliance

Our landing zone implements controls for:

- **PCI DSS**: Payment card industry standards
- **ISO 27001**: Information security management
- **SOC 2**: Service organization controls
- **GDPR**: General data protection regulation

### Audit and Monitoring

1. **Azure Security Center**
   - Continuous security assessment
   - Security recommendations
   - Threat detection

2. **Azure Sentinel**
   - Security information and event management (SIEM)
   - Security orchestration and automated response (SOAR)

3. **Activity Logs**
   - All administrative actions logged
   - Retention policy: 90 days minimum

## Incident Response

### Security Incident Workflow

1. **Detection**: Automated alerts and monitoring
2. **Analysis**: Security team investigation
3. **Containment**: Isolate affected resources
4. **Eradication**: Remove threats and vulnerabilities
5. **Recovery**: Restore services and operations
6. **Lessons Learned**: Post-incident review

### Emergency Contacts

| Role | Contact | Phone |
|------|---------|-------|
| Security Lead | security@company.com | +1-xxx-xxx-xxxx |
| Platform Team | platform@company.com | +1-xxx-xxx-xxxx |
| Management | management@company.com | +1-xxx-xxx-xxxx |

## Policy Assignment

Policies are assigned at different scopes:

```bash
# Assign policy to management group
az policy assignment create \
  --name "require-tags" \
  --policy "/providers/Microsoft.Authorization/policyDefinitions/xxx" \
  --scope "/providers/Microsoft.Management/managementGroups/mg-landing-zone"

# Assign policy to subscription
az policy assignment create \
  --name "deny-public-ip" \
  --policy "/providers/Microsoft.Authorization/policyDefinitions/xxx" \
  --scope "/subscriptions/{subscription-id}"
```

---

[← Deployment Guide](deployment-guide.md) | [Next: Monitoring →](monitoring.md)
