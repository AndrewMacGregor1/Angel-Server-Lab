Here is the completely rewritten **Network_Cheat_Sheet.md**. This version incorporates the new **Tailscale** addressing and the **SSH hardening** requirements, ensuring your "Source of Truth" reflects the state of the lab after completing **SOP-11** and **SOP-12**.

---

## 1. Core Infrastructure (Layer 3)

The following table outlines the foundational network addressing for the home lab. All local infrastructure is contained within the `192.168.0.0/24` subnet.

|**Device/Node**|**Hostname**|**Local Static IP**|**Tailscale IP (VPN)**|**Management Port**|
|---|---|---|---|---|
|**Cox Gateway**|`gateway.lan`|`192.168.0.1`|N/A|N/A|
|**Proxmox Host**|`angel-server.lan`|`192.168.0.150`|`100.x.x.x`|`8006` (HTTPS)|
|**Ubuntu VM 01**|`angel-node-01`|`192.168.0.151`|`100.y.y.y`|`22` (SSH)|

## 2. Services & Credentials

Standard access methods for managing the lab environment from both local and remote locations.

- **Proxmox Web UI (Local):** `https://192.168.0.150:8006`.
    
- **Proxmox Web UI (Remote):** `https://[Tailscale-IP]:8006`.
    
    - **Default User:** `root`.
        
- **Ubuntu SSH Access:** `ssh drew@192.168.0.151` (Local) or `ssh drew@[Tailscale-IP]` (Remote).
    
    - **Primary User:** `drew`.
        
    - **Authentication:** Ed25519 Public Key Only (Password Auth: **Disabled**).
        
- **DNS Servers:** `192.168.0.1` (Local Gateway) or Tailscale MagicDNS.
    

## 3. Physical & Virtual Port Map

Mapping the physical hardware to the virtual environment.

- **Dell OptiPlex NIC:** Physically connected via 100ft Ethernet to Port 1 of the Cox Gateway.
    
- **Virtual Bridge (`vmbr0`):** Software-defined bridge mapping the physical NIC to all internal VMs.
    

## 4. Virtual Networking (Tailscale Tailnet)

The following table tracks the private mesh network addresses used for remote administration and bypassing ISP port restrictions.

| **Device Name**   | **Tailscale IP** | **Primary Role**         | **Access Policy**     |
| ----------------- | ---------------- | ------------------------ | --------------------- |
| **proxmox-host**  | `100.x.x.x`      | Bare-Metal Management    | Internal/Mesh Only    |
| **angel-node-01** | `100.y.y.y`      | Primary Ubuntu VM        | Internal/Mesh Only    |
| **work-laptop**   | `100.z.z.z`      | Remote Management Device | Authorized Key-holder |
