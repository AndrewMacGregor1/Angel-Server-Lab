## 1. Project Overview

This is a documentation-first home lab project centered around a Dell OptiPlex 9020 SFF. This repository tracks the transition from classroom theory at Virginia Western Community College (VWCC) to hands-on Linux Administration and Enterprise Networking experience.

## 2. Technical Stack

- **Hypervisor:** Proxmox VE 9.1 (Bare-Metal).
    
- **Guest OS:** Ubuntu Server 24.04 LTS.
    
- **Hardware:** Intel Core i5-4590, 16GB DDR3 RAM, 128GB SSD (OS), and 500GB HDD (Data).
    
- **Networking:** Static IPv4 configuration via a dedicated 100ft physical Ethernet run.

## 3. Current Project Standing

The lab is currently entering Phase 2, shifting focus from core infrastructure provisioning to service containerization and external edge networking.

### Completed (Episode 01: The Foundation)

- **[SOP-01](./01_Learning/SOP-01_Proxmox_Installation.md) to [SOP-04](./01_Learning/SOP-04_Ubuntu_Server_Provisioning.md)**: Successful bare-metal installation and provisioning of a hardened, headless Ubuntu VM.
    
-  **[Breakage Log](./01_Learning/Angel_Server_Breakage_Log.md)**: Resolved the ISO Lock boot error encountered during initial provisioning.

### In Progress (Episode 02: The Container Engine)

- **[SOP-05:](./01_Learning/SOP-05_Guest_Services_&_Data_Integrity.md)**  Guest Agent integration and Proxmox snapshot baseline established.
    
- **[SOP-06:](./01_Learning/SOP-06_System_Hardening_&_Directory_Structure.md)** Standardized directory structure at /opt/docker and UFW hardening.
    
- **[SOP-07:](./01_Learning/SOP-07_Containerization_(Docker_&_Portainer).md)**  Deployment of the Docker Engine and Portainer management interface.
    

### Planned (Episode 03: Opening the Gates)

- **[SOP-08:](./01_Learning/SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)** and **[SOP-09:](./01_Learning/SOP-09_Dynamic_DNS_(DDNS)_Configuration)** Implementation of Nginx Proxy Manager and Dynamic DNS for secure remote access.
    
- **[SOP-10:](./01_Learning/SOP-10_Minecraft_Bedrock_Edition_Deployment.md)** Deployment of a persistent Minecraft Bedrock Edition server.

## 4. Repository Structure

- **[01_Learning:](./01_Learning/SOP-01_Proxmox_Installation.md)** Technical SOPs, Physical Topology Logs, and the Service Port Map.
    
- **[02_Archive:](./02_Archive/Angel_Server_Hardware.md)** Hardware Bill of Materials (BOM) and retired configuration milestones.
    
- **[Angel Server MOC:](./Angel_Server_MOC.md)** The Map of Content for quick navigation across the entire project.