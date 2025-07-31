---
layout: default
title: Architecture Overview
permalink: /architecture/
---

# Architecture Overview

## Azure Landing Zone Architecture

Our Azure Landing Zone follows Microsoft's recommended architecture patterns to provide a secure, scalable foundation for enterprise workloads.

## Core Components

### 1. Management Groups Hierarchy

```
Root Management Group
├── Platform Management Group
│   ├── Management Subscription
│   ├── Connectivity Subscription
│   └── Identity Subscription
└── Landing Zones Management Group
    ├── Corp Landing Zones
    └── Online Landing Zones
```

### 2. Network Architecture

- **Hub-and-Spoke Topology**: Centralized connectivity with isolated workload networks
- **Azure Firewall**: Centralized network security and traffic filtering
- **VPN Gateway**: Secure hybrid connectivity
- **ExpressRoute**: Dedicated private connectivity

### 3. Security Framework

- **Azure AD**: Identity and access management
- **Azure Policy**: Governance and compliance
- **Azure Security Center**: Security monitoring and recommendations
- **Key Vault**: Secrets and certificate management

### 4. Monitoring and Logging

- **Azure Monitor**: Comprehensive monitoring solution
- **Log Analytics**: Centralized logging and analysis
- **Application Insights**: Application performance monitoring
- **Azure Sentinel**: Security information and event management (SIEM)

## Design Principles

1. **Security by Default**: All resources are secured by default
2. **Zero Trust**: Never trust, always verify
3. **Least Privilege**: Minimal access rights
4. **Defense in Depth**: Multiple layers of security
5. **Compliance**: Built-in compliance controls

## Network Topology

![Network Architecture](../assets/images/network-architecture.png)

The network architecture implements a hub-and-spoke model with:

- **Hub VNet**: Contains shared services (firewall, VPN gateway, DNS)
- **Spoke VNets**: Isolated workload environments
- **Peering**: Secure connectivity between hub and spokes

## Subscription Organization

| Subscription Type | Purpose | Resource Types |
|------------------|---------|----------------|
| Management | Platform management and monitoring | Log Analytics, Azure Monitor, Automation |
| Connectivity | Network hub and shared connectivity | Virtual Networks, Firewall, VPN Gateway |
| Identity | Identity services | Domain Controllers, ADFS |
| Landing Zone - Corp | Corporate workloads | Applications, databases, VMs |
| Landing Zone - Online | Internet-facing workloads | Web apps, APIs, CDN |

## Compliance and Governance

- **Built-in Policies**: Automatically applied governance policies
- **Resource Naming**: Standardized naming conventions
- **Tagging Strategy**: Consistent resource tagging
- **Cost Management**: Budget controls and cost optimization

---

[← Back to Home](/) | [Next: Deployment Guide →](/deployment-guide/)
