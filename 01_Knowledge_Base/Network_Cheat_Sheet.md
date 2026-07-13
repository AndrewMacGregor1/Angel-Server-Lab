## 1. Core Infrastructure (Our "Truth" Table)

| **Device/Node Name** | **How We Access It** | **Local IP Address** | **What It Actually Does** |
| :--- | :--- | :--- | :--- |
| **Cox Modem** | Box in Bridge Mode | `192.168.0.1` | Passes internet straight to our router |
| **Ubiquiti Gateway** | Web UI / Mobile App | `192.168.20.1` | Our main router, firewall, and DHCP server |
| **Cisco Switch** | Console Cable Only | `No Management IP` | Splits one ethernet cable into multiple lines upstairs |
| **Proxmox Host** | Web Browser UI | **`192.168.20.2`** | The physical Dell computer running our VMs |

## 2. Quick Access Commands & Wi-Fi

- **Proxmox Web UI Panel:** `https://192.168.20.2:8006`.
- **SSH Host Entry:** `ssh drew@192.168.20.2`.
- **Active Wi-Fi Networks:**
    - `Mac_WiFi`: Our standard home internet (VLAN 1).
    - `Mac_WiFi_VPN`: Traffic permanently hidden through WireGuard (VLAN 10).
    

