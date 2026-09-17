### ⚙️ Wazuh Core Configuration: Infrastructure Hardening & Custom Rule Engineering

This document details the configuration layers required to enforce host security baselines, establish log ingestion pathways, and engineer custom alert rules within the Wazuh SIEM ecosystem. 

### 🔐 1. Security Hardening Rules (Syscheck & Rootcheck)

### ✅ Architectural Impact

* **File Integrity Monitoring (FIM):** Actively monitors system binaries, configuration directories, and critical system paths for unauthorized additions, modifications, or deletions. This is a foundational control for detecting ransomware encryption or unauthorized backdoor deployments.
* **System Auditing:** Scans the target operating system for rootkits, hidden processes, unsecure permissions, or departures from predefined system security benchmarks.

### ➤ Configuration Execution

Modify the master security runtime configuration file on the central manager ```(/var/ossec/etc/ossec.conf)```: 

```xml

<syscheck>
  <enabled>yes</enabled>
  <frequency>3600</frequency> <!-- Audits system architecture integrity every 60 minutes -->
  <directories check_all="yes">/etc,/usr/bin,/usr/sbin</directories>
  <auto_ignore>no</auto_ignore>
</syscheck>

<rootcheck>
  <enabled>yes</enabled>
</rootcheck>
```

* **Analyst Note:** Setting ```<auto_ignore>``` to no ensures that repeated modifications to crucial files ```(like /etc/passwd)``` continue to fire alerts rather than being automatically whitelisted by the system, preserving continuous visibility over persistent threats.

### 🧠 2. Log Collection from the Agent (Syslog, Auth, etc.)

### ✅ Architectural Impact

* **Authentication Telemetry Ingestion:** Aggregates remote host system tracking data to dynamically expose brute-force attempts, unauthorized administrative transitions (sudo misuses), and anomalous SSH access vectors.

### ➤ Configuration Execution

Inject these specific telemetry ingestion hooks directly into the configuration matrix of the targeted endpoint machine ```(/var/ossec/etc/ossec.conf)```: 

```xml

<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/auth.log</location>
</localfile>

<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/syslog</location>
</localfile>

```

* **Analyst Note:** By mapping the auth.log directly into the syslog log format, the endpoint agent reads lines in real-time as they flush to disk and streams them upstream over port 1514/TCP to be processed by the manager's decoder engine.

### 📋 3. Policy Monitoring & Custom Alert Rule Engineering

### ✅ Architectural Impact

The true value of a SIEM lies in custom rules tailored to an organization's behavior. Writing customized alert decoders allows an analyst to alert on: 

* **System Invariants:** Instant notification if highly restrictive security files or firewalls (iptables / nftables) are altered.
* **Anomalous Access Profiles:** Flagging successful authentication sequences executing outside standard operational hours.
* **Targeted Intrusion Signatures:** Aggregating granular system log strings into high-severity indicators.

### Custom Detection Logic Implementation

Append custom identification structures within the local rule definition workspace on the Wazuh Manager ```(/var/ossec/etc/rules/local_rules.xml)```: 

```xml

<group name="custom,syslog,">
  <rule id="100001" level="10">
    <decoded_as>syslog</decoded_as>
    <match>Failed password</match>
    <description>Possible SSH brute force attempt identified on host asset</description>
  </rule>
</group>
```

* **Analyst Note:** Custom rule IDs are explicitly bound to user-defined spaces (starting at ID 100000+) to guarantee they never collide with stock upstream rules. Setting this rule to **Level 10** flags it as a high-priority tactical event, triggering instant correlation inside the central dashboard.

### 🔄 4. Security Daemon Lifecycle Activation

To commit modifications across your security parameters, parse configurations, and update the live rules engine, trigger a graceful reset of the primary manager daemon: 

```bash

sudo systemctl restart wazuh-manager
```
