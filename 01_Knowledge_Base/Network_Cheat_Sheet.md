## 1. Core Infrastructure (The "Truth" Table)

| **Device/Node**      | **Access Method** | **Local IP**        | **Role**                        |
| -------------------- | ----------------- | ------------------- | ------------------------------- |
| **Cox Modem**        | Bridge Mode       | `192.168.0.1`       | ISP Handoff (Passthrough)       |
| **Ubiquiti Gateway** | Web UI / App      | `192.168.0.1`       | Main Router & Firewall          |
| **Cisco Switch**     | Physical Only     | `No IP`             | Layer 2 Distribution (Upstairs) |
| **Proxmox Host**     | Web UI            | **`192.168.0.150`** | Physical Hypervisor             |


## 2. Quick Access & Admin

- **Proxmox Management:** `https://192.168.0.150:8006`.
    
- **SSH Entry:** `ssh drew@192.168.0.150`.
    
- **WiFi SSIDs:** * `Mac_WiFi`: Standard internet (VLAN 1).
    
    - `Mac_WiFi_VPN`: Tunneled via Wireguard (VLAN 10).
        

