# 🔐 Wazuh SIEM Setup & Monitoring Documentation

This repository contains configuration files, custom rules, integration steps, and notes for setting up and running a Wazuh-based SIEM system. It is designed for use in home labs or small environments to monitor SSH access, file changes, Windows activity, Nextcloud usage, and more.

---


## ⚙️ Setup Overview

1. **Wazuh Stack Installation**
   - Wazuh Manager, Indexer, and Dashboard on Ubuntu Server (8GB RAM minimum)
   - Optionally installed via Docker or package

2. **Agent Deployment**
   - Windows agent with Sysmon, Defender, PowerShell auditing, etc.
   - Linux agent with Auditd, Rootcheck, and Syscollector

3. **Monitoring Targets**
   - SSH logins and brute-force attempts
   - File changes (syscheck)
   - System info, services, open ports (syscollector)
   - Nextcloud access logs via Filebeat

4. **Alert Tuning**
   - Local rules to suppress benign logs (e.g., PowerShell policy test scripts)
   - Use of custom alert levels and `noalert` flags

---

## 🔍 Sample Discover Filters (Kibana/Wazuh Dashboards)

| Use Case                     | Filter Example |
|-----------------------------|----------------|
| SSH Failed Logins           | `rule.description:*authentication failed*` OR `event.id:5716` |
| PowerShell Execution        | `data.win.eventdata.image:*powershell*` |
| File Changes (Syscheck)     | `rule.groups:syscheck` |
| App Install/Uninstall       | `rule.description:*Installed application*` |
| Nextcloud Activity          | `location:*nextcloud.log*` |
| User Logins (Windows/Linux) | `rule.description:*logged on* OR *session opened*` |

---


## 🛡️ License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for more details.

---

## ✍️ Author

Created by Knx427
Cybersecurity & System Monitoring Enthusiast  
Contributions and suggestions welcome!

