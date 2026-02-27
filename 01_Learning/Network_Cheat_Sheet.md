## 1. Core Infrastructure (Layer 3)

The following table outlines the foundational network addressing for the home lab. All infrastructure is contained within the `192.168.0.0/24` subnet.

|**Device/Node**|**Hostname**|**Static IP**|**Management Port**|
|---|---|---|---|
|**Cox Gateway**|`gateway.lan`|`192.168.0.1`|N/A|
|**Proxmox Host**|`angel-server.lan`|`192.168.0.150`|`8006` (HTTPS)|
|**Ubuntu VM 01**|`angel-node-01`|`192.168.0.151`|`22` (SSH)|

> **Note:** Assign `.151` to the Ubuntu VM during the Netplan configuration to maintain an organized IP range.

## 2. Services & Credentials

Standard access methods for managing the lab environment.

- **Proxmox Web UI:** `https://192.168.0.150:8006`
    
    - **Default User:** `root`
        
- **Ubuntu SSH Access:** `ssh drew@192.168.0.151`
    
    - **Default User:** `drew`
        
- **DNS Servers:** `192.168.0.1` (Local Gateway) or `8.8.8.8` (Google Public DNS)
    

## 3. Physical & Virtual Port Map

Mapping the physical hardware to the virtual environment.

- **Dell OptiPlex NIC:** Physically connected via 100ft Ethernet to Port 1 of the Cox Gateway.
    
- **Virtual Bridge (`vmbr0`):** Software-defined bridge that maps the physical NIC to all internal Virtual Machines.