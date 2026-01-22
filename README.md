# Litmus Edge Manager - Deploy to Azure

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdmzaytsev%2Ftest1%2Fmain%2Fazuredeploy.json)

## Overview

Litmus Edge Manager is an integrated edge-to-cloud Industrial IoT platform that provides everything you need to put industrial data to work for smart manufacturing. The product is purpose-built to collect, process and analyze data at the edge, then rapidly integrate the data with any cloud, database, or message broker for data storage, analytics, and AI/ML.

Litmus Edge is a flexible and scalable edge platform that collects, analyzes, manages and integrates data from all industrial assets. Purpose-built for Industry 4.0, the Litmus Edge platform provides the edge connectivity and intelligence needed to enable dozens of use cases ranging from predictive maintenance and asset condition monitoring to machine learning and industrial IoT.

## What This Template Deploys

- **Virtual Machine** with Litmus Edge Manager pre-installed (Azure Marketplace image)
- **Network Security Group** with rules for required ports
- **Virtual Network and Subnet** (optional - can use existing)
- **Public IP Address** (optional - can use existing or deploy with private IP only)
- **Network Interface**

### Network Security Group Rules

| Port | Protocol | Description |
|------|----------|-------------|
| 80 | TCP | HTTP web interface |
| 443 | TCP | HTTPS secure web interface |
| 8446 | TCP | LEM administration console |
| 51820 | UDP | Remote edge connections |
| 8883 | TCP | MQTT SSL for IoT device communication |
| 123 | UDP | NTP time synchronization |

## Parameters

### Required Parameters

| Parameter | Description |
|-----------|-------------|
| `sshPublicKey` | SSH public key for VM administrator account |

### VM Configuration

| Parameter | Default | Description |
|-----------|---------|-------------|
| `projectName` | `lem` | Name prefix for generating resource names (3-10 chars) |
| `location` | Resource group location | Azure region for deployment |
| `vmSize` | `Standard_B4ms` | VM size (minimum recommended: Standard_B4ms) |
| `adminUsername` | `lemadmin` | VM administrator username |
| `osDiskSizeGB` | `160` | OS disk size in GB (128-1024) |

### Network Configuration

| Parameter | Default | Description |
|-----------|---------|-------------|
| `virtualNetworkNewOrExisting` | `new` | Create new or use existing VNet |
| `virtualNetworkName` | `lem-vnet` | Name of virtual network |
| `virtualNetworkResourceGroup` | Current RG | Resource group of existing VNet |
| `virtualNetworkAddressPrefix` | `10.0.0.0/16` | Address prefix for new VNet |
| `subnetName` | `lem-subnet` | Name of subnet |
| `subnetAddressPrefix` | `10.0.0.0/24` | Address prefix for new subnet |

### Public IP Configuration

| Parameter | Default | Description |
|-----------|---------|-------------|
| `publicIpNewOrExistingOrNone` | `new` | Create new, use existing, or none (private only) |
| `publicIpName` | `lem-pip` | Name of public IP address |
| `publicIpResourceGroup` | Current RG | Resource group of existing public IP |

### Tags

| Parameter | Description |
|-----------|-------------|
| `vmTags` | Tags for the virtual machine |
| `networkSecurityGroupTags` | Tags for the NSG |
| `virtualNetworkTags` | Tags for the VNet |
| `publicIpTags` | Tags for the public IP |
| `networkInterfaceTags` | Tags for the NIC |

Tags are specified as JSON objects. Examples:

**Azure Portal (Deploy to Azure button)**

Enter tags in the parameter field as JSON:
```json
{"environment": "production", "costCenter": "IT-12345", "owner": "admin@company.com"}
```

**Azure CLI**

```bash
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json \
  --parameters sshPublicKey="ssh-rsa AAAA..." \
               vmTags='{"environment":"production","application":"lem"}'
```

**Parameters file**

```json
"vmTags": {
    "value": {
        "environment": "production",
        "application": "litmus-edge-manager",
        "costCenter": "IT-12345"
    }
}
```

## Outputs

| Output | Description |
|--------|-------------|
| `vmName` | Name of the deployed VM |
| `privateIpAddress` | Private IP address |
| `publicIpAddress` | Public IP address (if configured) |
| `adminConsoleUrl` | URL to LEM admin console (https://IP:8446) |
| `webInterfaceUrl` | URL to web interface (https://IP) |
| `adminUsername` | Admin username for SSH |
| `sshCommand` | SSH command to connect to VM |

## Deployment

### Using the Deploy to Azure Button

1. Click the **Deploy to Azure** button above
2. Fill in the required parameters
3. Review and create the deployment

### Using Azure CLI

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file azuredeploy.json \
  --parameters sshPublicKey="<your-ssh-public-key>"
```

### Using Azure PowerShell

```powershell
New-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -TemplateFile azuredeploy.json `
  -sshPublicKey "<your-ssh-public-key>"
```

## Post-Deployment

After deployment completes:

1. Access the LEM administration console at `https://<public-ip>:8446`
2. Access the web interface at `https://<public-ip>`
3. SSH to the VM using: `ssh <adminUsername>@<public-ip>`

## Requirements

- Azure subscription
- SSH key pair for VM authentication

## License

Litmus Edge Manager is a commercial product. Contact [Litmus Automation](https://litmus.io) for licensing information.
