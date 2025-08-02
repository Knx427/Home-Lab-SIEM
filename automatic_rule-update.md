## 🔄 Wazuh Ruleset Auto-Updates
-- *Purpose: Keep decoders, rules, and CDB lists current.*
📦 Options:
- Pull rules from Wazuh Rules repo.

- Create a cron job or systemd timer to sync the rules folder:
```
git clone https://github.com/wazuh/wazuh-ruleset.git /var/ossec/etc/ruleset
cp -r /var/ossec/etc/ruleset/rules/* /var/ossec/etc/rules/
systemctl restart wazuh-manager
```

### 🛠️ 1. Clone the ruleset repo (or pull updates)
```
git clone https://github.com/wazuh/wazuh-ruleset.git /var/ossec/etc/ruleset
```

- ***You may want to backup existing folders before overwriting them:***
```
cp -r /var/ossec/etc/rules /var/ossec/etc/rules.bak.$(date +%F)
```

### 🔁 2. Copy updated files to the active config
```
cp -r /var/ossec/etc/ruleset/rules/* /var/ossec/etc/rules/
cp -r /var/ossec/etc/ruleset/decoders/* /var/ossec/etc/decoders/
cp -r /var/ossec/etc/ruleset/sca/* /var/ossec/etc/shared/default/sca/
```

### 🔄 3. Restart manager to apply:
```
systemctl restart wazuh-manager
```

### 🕑 4. Automate with cron:
```
sudo crontab -e
```
Add something like:

```
0 4 * * * /usr/local/bin/update-wazuh-rules.sh
```
Where update-wazuh-rules.sh contains:

```
#!/bin/bash
cd /var/ossec/etc/ruleset || exit
git pull origin main
cp -r rules/* /var/ossec/etc/rules/
cp -r decoders/* /var/ossec/etc/decoders/
cp -r sca/* /var/ossec/etc/shared/default/sca/
systemctl restart wazuh-manager
```
Also make sure its executable:
```
chmod +x /usr/local/bin/update-wazuh-rules.sh
```
---
### Store your *local_rules.xml* in Git and periodically pull from it:
```
git clone https://github.com/yourrepo/wazuh-rules.git /opt/wazuh-custom
cp /opt/wazuh-custom/local_rules.xml /var/ossec/etc/rules/local_rules.xml
systemctl restart wazuh-manager
```
