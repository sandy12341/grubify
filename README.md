# Grubify - Food Delivery App

A modern food delivery application built with React TypeScript frontend and .NET backend, designed for deployment to Azure Container Apps using Azure Developer CLI (azd).

## Starter demo source

The `demo/starter-source` branch adds Azure Monitor OpenTelemetry request and
dependency instrumentation when `APPLICATIONINSIGHTS_CONNECTION_STRING` is set.
Without that environment variable, local behavior is unchanged. The intentional
cart analytics-cache leak is preserved for the Starter demonstration. Restarting
all active API revisions clears that process-local cache and in-memory carts;
deleting a cart alone does not reset the leaked buffers.

The frontend Docker build and package engine use Node.js 24 LTS. The production
image still serves static files from nginx; the runtime upgrade does not change
the intentional API fault. The Starter source workflow checks engine alignment
and builds the complete frontend container without Azure credentials.

## 🍕 Features

- **Modern UI**: Beautiful, responsive design inspired by popular food delivery apps
- **Real Food Content**: Sample restaurants and food items with real images from Unsplash
- **Complete Food Delivery Flow**: Browse restaurants → Add to cart → Checkout → Track orders
- **Azure Container Apps**: Scalable, serverless container hosting
- **Azure Developer CLI**: One-command deployment and management

## 🏗️ Architecture

- **Frontend**: React 18 + TypeScript + Material-UI
- **Backend**: .NET 9 Web API with RESTful endpoints
- **Infrastructure**: Azure Container Apps + Container Registry
- **Deployment**: Azure Developer CLI (azd)

## 🚀 Complete Deployment Guide

This guide shows how to deploy Grubify with **both backend versions** (v1 with memory leak, v2 with payment failures) for testing Azure SRE Agent scenarios.

## 📋 Prerequisites

Before deploying Grubify, ensure you have the following tools installed and running:

### Required Tools
- **[Azure Developer CLI (azd)](https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/install-azd)** - Latest version
- **[Docker Desktop](https://www.docker.com/products/docker-desktop/)** - Must be **running** before deployment
- **[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)** - For additional Azure operations
- **Azure Subscription** - With Contributor/Owner permissions

### ⚠️ Important: Docker Desktop
**Docker Desktop must be running before executing `azd up`**. The deployment will fail if Docker is not started.

To start Docker Desktop:
- **macOS/Windows**: Launch Docker Desktop application
- **Linux**: Run `sudo systemctl start docker`

Verify Docker is running:
```bash
open -a Docker
docker --version
docker ps
```

### Prerequisites

## 🚀 Quick Start

### 1. Prerequisites Check
Before starting, run our prerequisites check script:

```bash
# Run the automated prerequisites check
./scripts/check-prerequisites.sh


### 2. Initial Azure Setup

```bash
# Clone the repository
git clone https://github.com/dm-chelupati/grubify.git
cd grubify

# ⚠️ IMPORTANT: Start Docker Desktop before proceeding
# Verify Docker is running
docker ps

# Login to Azure
azd auth login
az login --use-device-code

# Initialize azd project (if not already done)
azd init

# Set Azure location
azd env set AZURE_LOCATION eastus2
```

### 3. Deploy Infrastructure & Applications

```bash
# Deploy infrastructure and frontend first
azd up
```

This creates:
- **Resource Group**: `rg-grubify-app`
- **Container Registry**: `crgrubify`
- **Container Apps Environment**: `cae-grubify`
- **API Container App**: `ca-grubify-api`
- **Frontend Container App**: `ca-grubify-frontend`
- **Log Analytics Workspace**: `log-grubify`

### 6. Ready for SRE Scenarios

Now you have:
- ✅ **Frontend deployed** and working
- ✅ **Backend deployed** and working
- ✅ **Infrastructure configured** for testing scenarios

**SRE Agent Setup:**
1. **Create agent** - ([Azure SRE Agent Usage Guide](https://learn.microsoft.com/en-us/azure/sre-agent/usage))
2. **Map GitHub repo** that you cloned this to: **https://github.com/dm-chelupati/grubify.git**
3. **Connect Service Now** to your SRE agent
4. **Setup incident handler** with custom instructions for automated diagnosis and mitigation
5. **Simulate memory leak** using the deployed application endpoints
6. **Create incident in Service Now** to trigger SRE agent response
