### 💻 Wazuh Endpoint Agent: Windows Client Deployment & Ingestion Matrix

This technical guide outlines the procedures for installing, configuring, and verifying the lightweight Wazuh security agent on a Windows endpoint. This setup establishes a secure telemetry stream from local client event logs to a centralized Wazuh Manager instance. 

### 1. Binary Acquisition & Privileged Installation

1. **Download the MSI Infrastructure Installer**
Acquire the official authenticated Microsoft Installer (MSI) package from the Wazuh distribution server:
[Wazuh Windows Agent MSI Installer](/goto?url=CAESdAHrOzAVdetYzpsx0yuEjv-6uJ_dPRI9ovv7YIAvqWnbv7BxJFPSl7y4X_Qydc3-xzXz3OX-8JX6zTvfXppcYlPwc1L4zLjQmH9IWZEGUOD4LJ_7UB2icJSTxnYdkSe8ez2VBbC4t6AhqYvz0yCK20G11voI)
2. **Privileged Execution**
Right-click the downloaded .msi file and select **Run as Administrator**. 

  * **Analyst Note:** This elevation is required to grant the agent service the necessary system permissions to hook into protected local Windows Event Channels and security hives.
3. **Locate the Installation Root Directory**
The application binaries, logs, and configuration state files populate inside the standard system path: 

```cmd

C:\Program Files (x86)\ossec-agent
```

4. **Initialize the Graphical Management Interface**
To manage local agent states and visually verify parameters, launch the local control agent UI: 

```cmd

win32ui.exe
```

### 2. Telemetry & Ingestion Configuration

1. **Manager IP Alignment (Agent UI Method)**
Open the Wazuh Agent UI on the local machine: 

  * Populate the **"Manager IP"** field with your dedicated SIEM manager's IPv4 address (e.g., 192.168.1.100).
  * Commit the network change by selecting **"Save"**.
2. **Advanced Agent-Side Local Log Harvesting (Optional)**
To capture custom or proprietary logs from individual third-party applications running on the client, open the configuration editor via the menu: View → View Config. Append an explicit XML log harvesting definition block: 

```xml

<localfile>
  <location>C:\logs\custom_app.log</location>
  <log_format>syslog</log_format>
</localfile>
```

  * **Analyst Note:** This allows the agent to actively parse custom flat-file syslog outputs on the host and forward them upstream for central SIEM parsing.
3. **Service State Activation**
Initialize the underlying background daemon by navigating to Manager → clicking **"Start"**.

### 3. Automated Endpoint Enrollment & Handshake

If the upstream Wazuh Manager is active and actively listening for connection requests, the local host will initialize an automated deployment sequence: 

* **Protocol Handshake:** The client initiates an outbound connection on port 1515/TCP to auto-authenticate and generate unique host cryptographic cryptographic keys.
* **Stream Registration:** Once authorized, the agent automatically pivots to port 1514/TCP to begin shipping compressed, secure system event telemetry.
* **Efficiency:** This native process completely removes the requirement for tedious manual command-line enrollment tokens (agent-auth.exe).

### 4. 🔍 Security Analyst Verification (Manager Auditing)

To confirm that the host is actively communicating and that data is successfully reaching the SIEM backend, pivot to your Linux Wazuh Manager terminal and query the operational agent control index utility: 

```bash

# Query the live control matrix to verify active data ingestion
/var/ossec/bin/agent-control -lc
```


### 🛡️ Host Hardening & Maintenance Operations

* **Antivirus & EDR Exception Tuning:** Active security monitoring agents read deep system files and can occasionally trigger false-positive alerts inside endpoint defense tools. To maintain continuous telemetry flow, consider configuring structural path exclusions inside Windows Defender for the core binary directory: ```C:\Program Files (x86)\ossec-agent```
* **Administrative Lifecycle Reset:** If local settings are modified or telemetry stalls, trigger an active service lifecycle reset via the UI (win32ui → Manager → Restart) or cleanly from an elevated PowerShell console: 

```powershell

Restart-Service -Name Wazuh
```
