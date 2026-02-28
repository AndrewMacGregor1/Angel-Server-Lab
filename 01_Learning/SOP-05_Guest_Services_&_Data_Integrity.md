##  1. Purpose

This procedure defines the steps for establishing a communication link between the Proxmox host and the guest Operating System (OS) using the **QEMU Guest Agent**. It also outlines the "Snapshot" process to ensure data integrity before performing major system configuration changes.

## 2. Lab Environment Reference

The following values are used as the reference baseline for the **Angel Server** environment.

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**Hypervisor Host**|Proxmox VE (`192.168.0.150`)|The physical OptiPlex hardware|
|**Guest VM**|Ubuntu Server (`192.168.0.151`)|The virtualized "Angel Node"|
|**Service Name**|`qemu-guest-agent`|The host-to-guest bridge|

## 3. Enabling the Guest Agent (Interface)

Before the software can communicate, the virtual hardware "port" must be enabled in the Proxmox Web UI.

1. **Navigate:** Select the target VM (e.g., `100 (ubuntu-server-01)`) from the Proxmox resource tree.
    
2. **Options:** Go to the **Options** tab in the center menu.
    
3. **QEMU Guest Agent:** Locate the setting, click **Edit**, check the **Use QEMU Guest Agent** box, and click **OK**.
    

## 4. Installing the Agent (Guest OS)

For the communication bridge to function, the agent software must be running inside the Linux guest.

**Step 1: Access the guest VM via SSH**

```
ssh drew@192.168.0.151
```

**Step 2: Update the package list and install the agent utility**

```
sudo apt update && sudo apt install qemu-guest-agent -y
```

**Step 3: Start and enable the service to ensure it runs automatically on boot**

```
sudo systemctl enable --now qemu-guest-agent
```

## 5. Creating a "System Baseline" Snapshot

A snapshot provides a point-in-time "save game" that allows for an instant rollback if a configuration error occurs.

1. **Selection:** Select the VM in Proxmox and navigate to the **Snapshots** tab.
    
2. **Take Snapshot:** Click the **Take Snapshot** button.
    
3. **Configuration:**
    
    - **Name:** `Base_OS_Configured`
        
    - **Description:** "Ubuntu 24.04 installed, static IP 151 set, QEMU agent active. Pre-Docker install."
        
4. **Execution:** Click **Take Snapshot** and wait for the `TASK OK` status.
    

## 6. Post-SOP Verification

- **Status Check:** In the Proxmox **Summary** tab for the VM, the "IP Address" field should now automatically display `192.168.0.151`.
    
- **Rollback Readiness:** The snapshot should appear in the visual tree on the Snapshots page.