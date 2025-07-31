---
layout: default
title: Deployment Guide
---

# Deployment Guide

This guide walks you through deploying the Azure Landing Zone infrastructure using Terraform.

## Prerequisites

Before you begin, ensure you have:

- Azure CLI 2.0 or later
- Terraform 1.0 or later
- PowerShell Core or Bash
- Git
- Appropriate Azure permissions (Owner or Contributor)

## Step 1: Environment Setup

### Install Required Tools

```bash
# Install Azure CLI (Linux/macOS)
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Install Terraform
wget https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip
unzip terraform_1.6.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/
```

### Authenticate with Azure

```bash
# Login to Azure
az login

# Set your subscription
az account set --subscription "your-subscription-id"

# Verify your account
az account show --output table
```

## Step 2: Repository Setup

```bash
# Clone the repository
git clone https://github.com/happy-mfv/azure-happy.git
cd azure-happy

# Create environment-specific configurations
cp terraform/environments/dev/terraform.tfvars.example terraform/environments/dev/terraform.tfvars
```

## Step 3: Configure Variables

Edit the `terraform.tfvars` file for your environment:

```hcl
# terraform/environments/dev/terraform.tfvars

# General Settings
environment         = "dev"
location           = "East US"
organization_name  = "contoso"

# Network Settings
hub_vnet_address_space    = ["10.0.0.0/16"]
spoke_vnet_address_space  = ["10.1.0.0/16"]

# Security Settings
enable_azure_firewall = true
enable_bastion_host   = true

# Tags
default_tags = {
  Environment = "Development"
  Project     = "Azure Landing Zone"
  Owner       = "Platform Team"
}
```

## Step 4: Initialize Terraform

```bash
cd terraform/environments/dev

# Initialize Terraform
terraform init

# Create workspace (optional)
terraform workspace new dev
```

## Step 5: Plan and Deploy

```bash
# Review the deployment plan
terraform plan -out=tfplan

# Apply the configuration
terraform apply tfplan
```

## Step 6: Validate Deployment

After deployment, validate the resources:

```bash
# Check resource groups
az group list --output table

# Verify virtual networks
az network vnet list --output table

# Check Azure Policy assignments
az policy assignment list --output table
```

## Environment-Specific Deployments

### Development Environment

```bash
cd terraform/environments/dev
terraform init
terraform plan -var-file="dev.tfvars"
terraform apply -var-file="dev.tfvars"
```

### Production Environment

```bash
cd terraform/environments/prod
terraform init
terraform plan -var-file="prod.tfvars"
terraform apply -var-file="prod.tfvars"
```

## Post-Deployment Configuration

### 1. Configure Monitoring

```bash
# Deploy monitoring dashboard
az deployment group create \
  --resource-group rg-monitoring-prod \
  --template-file templates/monitoring-dashboard.json
```

### 2. Set up Alerts

```bash
# Create action group
az monitor action-group create \
  --name "LandingZoneAlerts" \
  --resource-group "rg-monitoring-prod"
```

### 3. Configure Backup

```bash
# Enable VM backup
az backup protection enable-for-vm \
  --resource-group myResourceGroup \
  --vault-name myRecoveryServicesVault \
  --vm myVM \
  --policy-name DefaultPolicy
```

## Troubleshooting

### Common Issues

1. **Authentication Errors**
   ```bash
   az login --use-device-code
   ```

2. **Terraform State Lock**
   ```bash
   terraform force-unlock <lock-id>
   ```

3. **Resource Naming Conflicts**
   - Ensure unique resource names
   - Check naming conventions

### Useful Commands

```bash
# View Terraform state
terraform state list

# Import existing resources
terraform import azurerm_resource_group.example /subscriptions/xxx/resourceGroups/mygroup

# Refresh state
terraform refresh
```

## Cleanup

To remove all resources:

```bash
# Destroy resources
terraform destroy

# Confirm by typing 'yes'
```

---

[← Architecture Overview](architecture.md) | [Next: Security Policies →](security-policies.md)
