## 🔐 1. Security Hardening Rules (Syscheck & Rootcheck)
✅ What it does:
Monitors for unauthorized file changes (integrity)
Checks for insecure configurations or rootkits

### ➤ Configure:
Edit the /var/ossec/etc/ossec.conf (on manager):
```
<syscheck>
  <enabled>yes</enabled>
  <frequency>3600</frequency> <!-- Check every hour -->
  <directories check_all="yes">/etc,/usr/bin,/usr/sbin</directories>
  <auto_ignore>no</auto_ignore>
</syscheck>

<rootcheck>
  <enabled>yes</enabled>
</rootcheck>
```

## 🧠 2. Log Collection from the Agent (Syslog, Auth, etc.)
✅ What it does:
Monitors system logs for brute force, sudo, ssh, etc.

### ➤ Ensure these are enabled on agent:
Edit /var/ossec/etc/ossec.conf (on the client/agent machine):
```
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/auth.log</location>
</localfile>

<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/syslog</location>
</localfile>
```

## 3. Policy Monitoring (Custom rules)
You can write custom rules to alert you when:

-A specific file is modified

-A user logs in at odd hours

-iptables or firewall config changes

Example custom rule (in /var/ossec/etc/rules/local_rules.xml):
```
<group name="custom,syslog,">
  <rule id="100001" level="10">
    <decoded_as>syslog</decoded_as>
    <match>Failed password</match>
    <description>Possible SSH brute force</description>
  </rule>
</group>
```

## ✅ Then restart the manager:
```
sudo systemctl restart wazuh-manager
```
