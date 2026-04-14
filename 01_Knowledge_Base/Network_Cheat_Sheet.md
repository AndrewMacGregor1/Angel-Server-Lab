
---
## 1. Core Infrastructure (Layer 3)

This table outlines the  network addressing for my home lab.

|**Device/Node**|**Hostname**|**Local Static IP**|**Tailscale IP (VPN)**|**Management Port**|
|---|---|---|---|---|
|**Cox Gateway**|`gateway.lan`|`192.168.0.1`|N/A|N/A|
|**Proxmox Host**|`angel-server.lan`|`192.168.0.150`|`100.x.x.x`|`8006` (HTTPS)|
|**Ubuntu VM 01**|`angel-node-01`|`192.168.0.151`|`100.y.y.y`|`22` (SSH)|
|**Minecraft LXC**|`minecraft-lxc`|`192.168.0.160`|`100.a.a.a`|`22` (SSH)|
|**Jellyfin LXC**|`jellyfin-lxc`|`192.168.0.161`|`100.b.b.b`|`22` (SSH)|
|**Ingest VM**|`ingest-vm`|`192.168.0.162`|`100.c.c.c`|`22` (SSH)|

## 2. Services & Credentials

Standard access methods for managing the lab environment from both local and remote locations.

- **Proxmox Web UI:** `https://192.168.0.150:8006` (Local) or `https://[Tailscale-IP]:8006` (Remote).
    
    - **Default User:** `root`.
        
- **Ubuntu SSH Access:** `ssh drew@192.168.0.151` (Local) or `ssh drew@[Tailscale-IP]` (Remote).
    
    - **Authentication:** Ed25519 Public Key Only (**Password Auth: Disabled**).
        
- **LXC/VM Management:** Use Proxmox Console or SSH via Tailscale IPs.
    
- **DNS Servers:** `192.168.0.1` (Local Gateway) or Tailscale MagicDNS.
    

## 3. Physical & Virtual Port Map

Mapping the physical hardware to the virtual environment.

- **Dell OptiPlex NIC:** Physically connected via 100ft Ethernet to Port 1 of the Cox Gateway.
    
- **Virtual Bridge (`vmbr0`):** Software-defined bridge mapping the physical NIC to all internal VMs and LXCs.
    
    

## 4. Virtual Networking (Tailscale Tailnet)

The following table tracks the private mesh network addresses used for remote administration and bypassing ISP port restrictions.

| **Device Name**   | **Tailscale IP** | **Primary Role**         | **Access Policy**   |
| ----------------- | ---------------- | ------------------------ | ------------------- |
| **proxmox-host**  | `100.x.x.x`      | Bare-Metal Management    | Internal/Mesh Only  |
|                   |                  |                          |                     |
| **minecraft-lxc** | `100.a.a.a`      | Dedicated Game Server    | Internal/Mesh Only  |
| **jellyfin-lxc**  | `100.b.b.b`      | Media Server (QSV)       | Internal/Mesh Only  |
| **ingest-vm**     | `100.c.c.c`      | VPN Protected Downloads  | Internal/Mesh Only  |
| **work-laptop**   | `100.z.z.z`      | Remote Management Device | Authorized Key-hold |