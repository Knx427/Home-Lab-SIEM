# Wazuh Agent – Minimal Windows Client Setup

This is a lightweight guide for installing and connecting the Wazuh agent on a Windows client to an already-configured Wazuh Manager.

---

## Install the Agent

1. **Download the MSI Installer**  
   [https://packages.wazuh.com/4.x/windows/wazuh-agent-4.x.x.msi](https://packages.wazuh.com/4.x/windows/wazuh-agent-4.x.x.msi)

2. **Run the Installer**  
   Right-click and run the installer **as Administrator**.

3. **Locate the Installation Folder**  
   Navigate to: ```C:\Program Files (x86)\ossec-agent```

4. **Open the Agent UI**
   
   Run: ```win32ui.exe```

---

## Configure the Agent

1. In the Wazuh Agent UI:
- Set your Wazuh Manager IP in the **"Manager IP"** field (e.g. `192.168.1.100`)
- Click **"Save"**

2. *(Optional)* Open the config viewer:
- Menu: `View` → `View Config`
- Edit or review agent-side rules if needed.
- eg. add Syslog:
```
<localfile>
  <location>C:\logs\custom_app.log</location>
  <log_format>syslog</log_format>
</localfile>
```
3. Start the agent:
- Go to `Manager` → click **"Start"**

---

## Automatic Connection

If the Wazuh Manager is already running and listening for agent connections:

- The agent should automatically **connect and authenticate**
- No need for `agent-auth` CLI in this case

---

## ✅ Verify Connection (from Manager side)

On the Wazuh Manager (Linux):

```
/var/ossec/bin/agent-control -lc
```
---

## Notes
You can restart the agent any time via:

win32ui → Manager → Restart

- Optional exclusions for antivirus:

Add C:\Program Files (x86)\ossec-agent to Windows Defender exceptions
