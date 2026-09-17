### 🔐 Wazuh SIEM Component Installation & Core Configuration

This document outlines the procedural steps required to deploy a centralized **Wazuh Security Operations Center (SOC)** instance on a Debian/Ubuntu infrastructure. This implementation aggregates the Wazuh Manager, Indexer, Dashboard, and Filebeat data shipper onto a unified node. 

### 1. Cryptographic Authentication & GPG Key Ingestion

Import the official Wazuh public GPG key to verify package signature integrity prior to system installation: 

```bash

curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh.gpg
```


### 2. Package Repository Provisioning

Append the authenticated, stable Wazuh 4.x package repository channel to the local Advanced Package Tool (APT) sources matrix: 

```bash

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | \
sudo tee /etc/apt/sources.list.d/wazuh.list
```


### 3. Local Ingestion Synchronization

Synchronize the local package index cache with the newly provisioned remote repository metadata: 

```bash

sudo apt update
```

### 4. Architectural Engineering Warning: Layer 4 Port Conflicts

[!WARNING]
**Network Interface Conflict Handling (Port 443 / HTTPS):**
If the deployment target system hosts pre-existing web services or infrastructure assets (e.g., Nextcloud, Apache, Nginx) binding to default TLS/HTTPS **Port 443**, the wazuh-dashboard initialization will fail. 

*Mitigation Protocol:* Prior to execution, the installation orchestration YAML configuration must be edited to re-bind the Wazuh Web Dashboard service interface to a non-standard alternative ingress port to prevent socket binding collisions. 

### 5. Security Component Orchestration & Installation

Install the full security information and event management (SIEM) architecture stack natively via the package manager: 

```bash

sudo apt install -y wazuh-manager wazuh-indexer wazuh-dashboard filebeat
```

### 6. Service Initialization & Daemon Management

Reload the systemd manager configuration to capture binary updates, enable persistent boot states, and initialize all SIEM daemons sequentially: 

```bash

# Reload system systemd configuration
sudo systemctl daemon-reload

# Enable and spin up data ingestion and storage backend
sudo systemctl enable --now wazuh-indexer
sudo systemctl enable --now wazuh-manager

# Enable and spin up log broker and web management interface
sudo systemctl enable --now filebeat
sudo systemctl enable --now wazuh-dashboard
```

### 7. Verification & Telemetry Audit

Validate the active operational socket runtime status of each distinct sub-component service layer: 

```bash

sudo systemctl status wazuh-indexer --no-pager
sudo systemctl status wazuh-manager --no-pager
sudo systemctl status filebeat --no-pager
sudo systemctl status wazuh-dashboard --no-pager
```

### 8. Host Security Loopback & Self-Monitoring

The Wazuh Manager daemon automatically hooks into local system event loops using native internal telemetry modules. Consequently, explicit loopback host agent deployment is omitted on the primary master infrastructure node. 

### 🚀 Post-Deployment Engineering Tasks

* [ Provision and Link Endpoint Monitoring Agents ](agent-setup.md)
* [ Refine Rule Classifications & Custom Log Parsers ](ossec-config.md)
