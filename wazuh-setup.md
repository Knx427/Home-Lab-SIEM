# Wazuh Installation Guide (Ubuntu/Debian)

This guide outlines the steps to install Wazuh Manager, Indexer, Dashboard, and Filebeat on a single instance.

---

## 1. Import Wazuh GPG Key
```
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh.gpg
```

### 2. Add Wazuh Repository
```bash
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | \
sudo tee /etc/apt/sources.list.d/wazuh.list
```

### 3. Update Package Lists
```sudo apt update```

### 4. Port Conflict Notice (Important)
If your system already has another service running on port 443, edit the Wazuh installation YAML file to change the Dashboard port before installation.
This will avoid conflicts with existing HTTPS services (That happened to me, a reinstallation was required).

### 5. Install Wazuh Components
```
sudo apt install wazuh-manager wazuh-indexer wazuh-dashboard filebeat
```

### 6. Enable and Start Services
```bash
sudo systemctl daemon-reload

sudo systemctl enable wazuh-dashboard
sudo systemctl start wazuh-dashboard

sudo systemctl enable wazuh-manager
sudo systemctl start wazuh-manager

sudo systemctl enable wazuh-indexer
sudo systemctl start wazuh-indexer

sudo systemctl enable filebeat
sudo systemctl start filebeat
```

### 7. Verify Services Are Running
```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
sudo systemctl status filebeat
```

### 8. Wazuh Manager Self-Monitoring
The Wazuh Manager automatically monitors the host it runs on using internal modules.
No agent installation is required on the same machine.

---
### Next Steps:
- Setup Agent. 
- Config Rules / Logs. 
