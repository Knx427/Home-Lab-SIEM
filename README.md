### Enterprise SIEM Infrastructure & Distributed Telemetry Matrix

An enterprise-grade Security Operations Center (SOC) engineering framework engineered to deliver centralized security monitoring, endpoint detection, and host integrity auditing across distributed infrastructure assets. Built on a hardened standalone **Wazuh SIEM stack**, this deployment implements multi-layered logging pipelines, automated signature synchronization, and stateful intrusion prevention. 

### 🏗️ Architectural Topology

The security infrastructure is architected across a decoupled master controller node and multi-platform endpoint agents, structured into three distinct deployment phases: 

                  [ LOG SOURCES & CLIENT ENDPOINTS ]
                Windows Clients   │   Linux Server Nodes
                (Sysmon Logging)  │   (Auditd / Syslog)
                                │   │
                                ▼   ▼
                           [ WAZUH AGENTS ]
                    (Secure Ingestion Over Port 1514)
                                   │
                                   ▼
                          [ CENTRAL SIEM NODE ]
          ┌────────────────────────┼────────────────────────┐
          ▼                        ▼                        ▼
    [WAZUH MANAGER]          [WAZUH INDEXER]        [WAZUH DASHBOARD]
    (Engine & Decoders)     (Elastic Database)     (KQL Search Analytics)

1. **Centralized Management Node (wazuh-setup.md):** Provisioning the unified SIEM cluster (Manager, Indexer, Dashboard, and Filebeat data broker) on a minimal Ubuntu Server instance, engineered to bypass Layer 4 socket conflicts on port 443.
2. **Endpoint Client Deployment (agent-setup.md):** Scalable provisioning of lightweight log-shippers on Windows targets using authenticated cryptographic handshakes and custom application log-harvesting streams.
3. **Detection Engineering Core (ossec-config.md):** System configuration matrices enforcing localized File Integrity Monitoring (FIM), anti-rootkit diagnostic hooks, and customized XML intrusion detection signatures.

### 📁 Technical Core Documentation Directory

* **[SIEM Stack Installation & Configuration Guide](wazuh-setup.md):** Sequential deployment protocol for the core manager engine and data storage indexers. Contains technical resolution pathways for socket binding conflicts with existing web assets.
* **[Endpoint Agent Provisioning & Deployment Log](agent-setup.md):** Client-side configuration matrix detailing binary execution, explicit local log path appending, and automated enrollment pipelines via port 1515/TCP.
* **[Central Engine Configurations & Signature Engineering](ossec-config.md):** Deep-dive into host security auditing blocks, log harvesting arrays for authentication loops (auth.log), and custom regex-matched rule development.

### 🔬 Deployed Security Controls & Threat Tracking

### 🛡️ File Integrity Monitoring (FIM) & System Auditing

* **Dynamic Directory Checks:** Continuous real-time checksum tracking across high-leverage system paths (/etc, /usr/bin, /usr/sbin) to trigger alerts instantly upon binary configuration manipulation or drift.
* **Rootkit Diagnostics (rootcheck):** Low-level kernel space evaluations to scan the file system for hidden processes, trojans, and non-compliant permission masks.

### 📊 Multi-Platform Telemetry Ingestion

* **System Event Aggregation:** Continuous, real-time ingestion of host operating system event channels (syslog) and authentication transaction sheets (auth.log) to surface security anomalies instantly.
* **Application Log Harvesting:** Custom agent-side configuration hooks tracking application-layer flat files, allowing individual nodes to stream third-party application logs directly upstream to the central manager.

### 📋 Custom Ruleset Tuning & Signature Engineering

* **Isolated Custom Namespace:** Deployment of custom rule definitions inside the isolated, user-defined index registry (ID: 100001) to protect custom code from upstream package updates.
* **Tactical Event Elevation:** Customized parsing logic built to capture brute-force patterns (such as failed authentication strings), immediately correlating data points and triggering **Level 10 Alarms** across analytical panels.

### 🔍 Analytical Incident Investigation Metrics

The following search logic parameters are mapped into the SIEM Kibana dashboard to give security analysts immediate visibility and triage capabilities over incoming network data: 

|       Security Vector Target        |   Kibana Query Language (KQL) / Lucene Filter Expression   |
|-------------------------------------|------------------------------------------------------------|
|****Active Brute-Force Traversal**** |  rule.description:*authentication failed* OR event.id:5716 | 
|****PowerShell Process Anomalies**** |            data.win.eventdata.image:*powershell*           |
|****System Integrity Violations (FIM)**** | rule.groups:syscheck |
|****Unauthorized Software Provisions**** |rule.description:*Installed application* |
|****Application Layer Compromise Logs**** |location:*nextcloud.log* |
|****System Access & Privilege Sessions**** | rule.description:*logged on* OR *session opened* |

### 🔄 Daemon State Integration

When committing modifications to system decoders, configuration blocks, or custom XML rules, force a structural reload to re-compile the memory tables: 

```bash

sudo systemctl restart wazuh-manager
```

---
## ✍️ Author
Created and maintained by **knx427**  
_Network Technician | Cybersecurity Infrastructure & Monitoring Specialist_  

📫 *Contributions, suggestions, or technical discussions are always welcome!*
