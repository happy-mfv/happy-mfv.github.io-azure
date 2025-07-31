# Azure Landing Zone

[![GitHub Pages](https://img.shields.io/badge/docs-GitHub%20Pages-blue)](https://happy-mfv.github.io/azure-happy)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Terraform](https://img.shields.io/badge/terraform->=1.0-purple.svg)](https://www.terraform.io/)

## Project Overview

Azure Landing Zone is a foundational architecture designed to provide a secure, scalable, and compliant Azure environment for enterprise applications and workloads.

📖 **[View Full Documentation](https://happy-mfv.github.io/azure-happy)**

## Project Objectives

- Build standardized Azure infrastructure foundation
- Ensure security and regulatory compliance
- Create scalable and reusable environments
- Apply best practices in cloud resource management

## Architecture Overview

### Core Components

1. **Management Groups**: Organize and manage subscriptions
2. **Subscriptions**: Environment separation (Dev, Test, Prod)
3. **Resource Groups**: Group related resources
4. **Virtual Networks**: Virtual networks and connectivity
5. **Security & Compliance**: Security policies and compliance
6. **Monitoring & Logging**: Monitoring and logging

## Directory Structure

```
azure-happy/
├── docs/                   # Project documentation
├── terraform/              # Infrastructure as Code
├── policies/               # Azure Policies
├── scripts/                # Automation scripts
├── templates/              # ARM/Bicep templates
└── monitoring/             # Monitoring configurations
```

## System Requirements

- Azure CLI >= 2.0
- Terraform >= 1.0
- PowerShell Core or Bash
- Git

## Getting Started

### 1. Environment Setup

```bash
# Install Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Login to Azure
az login

# Install Terraform
# (Instructions vary by OS)
```

### 2. Clone repository

```bash
git clone https://github.com/happy-mfv/azure-happy.git
cd azure-happy
```

### 3. Initial Configuration

```bash
# Create service principal
az ad sp create-for-rbac --name "azure-happy-sp"

# Configure Terraform backend
# (Details in docs/terraform-setup.md)
```

## Deployment

### Development Environment

```bash
cd terraform/environments/dev
terraform init
terraform plan
terraform apply
```

### Production Environment

```bash
cd terraform/environments/prod
terraform init
terraform plan
terraform apply
```

## Monitoring and Maintenance

- **Azure Monitor**: Performance and availability monitoring
- **Azure Security Center**: Security assessment
- **Azure Policy**: Compliance enforcement
- **Cost Management**: Cost control

## Contributing

1. Fork repository
2. Create feature branch
3. Commit changes
4. Push to branch
5. Create Pull Request

## Contact

- **Team**: MFV Happy Team
- **Email**: [team email]
- **Project Owner**: [Owner name]

---

## Changelog

### v1.0.0 (Planned)
- Initial Azure Landing Zone setup
- Basic security policies
- Network configuration
- Monitoring setup

---

*This document was last updated: $(date)*
