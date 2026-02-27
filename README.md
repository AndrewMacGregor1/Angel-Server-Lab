## 1. Project Overview

This is a **documentation-first** home lab project centered around a **Dell OptiPlex 9020 SFF**. This repository tracks my transition from classroom theory at Virginia Western Community College to hands-on **Linux Administration** and **Enterprise Networking** experience.

## 2. Technical Stack

- **Hypervisor:** Proxmox VE 9.1 (Bare-Metal)
- **Guest OS:** Ubuntu Server 24.04 LTS
- **Hardware:** Intel Core i5-4590 | 16GB DDR3 RAM | 128GB SSD + 500GB HDD
- **Networking:** Static IPv4 Configuration via a dedicated 100ft physical Layer 1 run

## 3. Key Milestones (Episode 01)

- **[SOP-01](./01_Learning/SOP-01_Proxmox_Installation.md)**: Bare-metal hypervisor installation and non-subscription repository configuration.
- **[SOP-04](./01_Learning/SOP-04_Ubuntu_Server_Provisioning.md)**: Successful provisioning of a hardened, headless Ubuntu VM with static networking.
- **[Breakage Log](./01_Learning/Angel_Server_Breakage_Log.md)**: Troubleshooting and resolving the "ISO Lock" boot error encountered during the initial VM reboot.

## 4. Repository Structure

-  **[01_Learning](./01_Learning/SOP-01_Proxmox_Installation.md)**:  Technical Standard Operating Procedures (**SOPs**), physical topology logs, and troubleshooting records.
- **[02_Archive](./02_Archive/Angel_Server_Hardware.md)**:  Hardware Bill of Materials (BOM) and historical project milestone configurations.
- **[Angel Server MOC](./Angel_Server_MOC.md)**: The "Map of Content" for quick navigation across the entire project.