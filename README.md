## 1. Project Overview

This is a documentation-first home lab project centered around a Dell OptiPlex 9020 SFF. This repository tracks the transition from classroom theory at Virginia Western Community College (VWCC) to hands-on Linux Administration and Enterprise Networking experience.

## 2. Infrastructure Architecture

The following diagram represents the planned architecture of the Angel Server lab as the project progresses through the YouTube series.

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': {
  'primaryColor': '#534AB7',
  'primaryTextColor': '#fff',
  'primaryBorderColor': '#3C3489',
  'lineColor': '#888780',
  'secondaryColor': '#0F6E56',
  'tertiaryColor': '#1a1a2e'
}}}%%

flowchart TD

    %% ── External ──
    RemoteUser(["Remote user"])
    PublicGuest(["Public guest"])

    %% ── Cloud ──
    TS["Tailscale VPN<br/>Encrypted mesh"]
    CF["Cloudflare<br/>DNS / DDNS"]

    %% ── Gateway ──
    GW["Cox gateway<br/>192.168.0.1"]

    %% ── Hypervisor ──
    PVE["Proxmox VE<br/>192.168.0.150"]

    %% ── VM & Docker ──
    NODE["angel-node-01<br/>192.168.0.151"]
    NPM["Nginx Proxy Manager<br/>Reverse proxy · HTTPS :443"]
    PORT["Portainer<br/>Docker UI"]
    MC["Minecraft Bedrock<br/>UDP :19132"]
    IMMICH["Immich<br/>Photo engine"]
    HDD[("500GB HDD<br/>Photo storage")]

    %% ── Backup ──
    CRON["Cron job<br/>Nightly 2:00 AM"]
    RCLONE["Rclone<br/>AES-256 crypt"]
    VZDUMP["VZDump<br/>Weekly VM images"]
    GDRIVE[("Google Drive<br/>Encrypted offsite")]

    %% ── Traffic flows ──
    RemoteUser -->|Tailscale tunnel| TS
    PublicGuest --> CF

    TS -->|Secure tunnel| PVE
    TS -->|Secure tunnel| NODE
    CF --> GW

    GW -->|HTTPS :443| NPM
    GW -->|UDP :19132| MC

    PVE --> NODE

    NODE --> NPM
    NPM --> PORT
    NPM --> IMMICH
    IMMICH <--> HDD

    %% ── Backup pipeline ──
    NODE -.->|triggers| CRON
    CRON --> RCLONE
    VZDUMP -.->|weekly| RCLONE
    RCLONE ==>|AES-256 encrypted sync| GDRIVE

    %% ── Styles ──
    classDef external  fill:#993C1D,stroke:#F0997B,color:#fff
    classDef cloud     fill:#185FA5,stroke:#85B7EB,color:#fff
    classDef gateway   fill:#444441,stroke:#B4B2A9,color:#fff
    classDef hyperv    fill:#3C3489,stroke:#AFA9EC,color:#fff
    classDef vm        fill:#085041,stroke:#5DCAA5,color:#fff
    classDef service   fill:#27500A,stroke:#97C459,color:#fff
    classDef backup    fill:#854F0B,stroke:#EF9F27,color:#fff
    classDef storage   fill:#3C3489,stroke:#AFA9EC,color:#fff

    class RemoteUser,PublicGuest external
    class TS,CF cloud
    class GW gateway
    class PVE hyperv
    class NODE,NPM,PORT,IMMICH vm
    class MC service
    class CRON,RCLONE,VZDUMP backup
    class HDD,GDRIVE storage
```

## 3. Technical Stack

- **Hypervisor:** Proxmox VE 9.1 (Bare-Metal).
    
- **Guest OS:** Ubuntu Server 24.04 LTS.
    
- **Hardware:** Intel Core i5-4590, 16GB DDR3 RAM, 128GB SSD (OS), and 500GB HDD (Data).
    
- **Networking:** Static IPv4 configuration via a dedicated 100ft physical Ethernet run.

## 4. Project Roadmap & Video Series

The following table tracks the transition from foundational infrastructure to high-level service deployment. Each milestone corresponds to a technical phase and its associated documentation.

| **Episode** | **Phase**                    | **Primary Milestones**                                                | **Documentation**                                                                                                                         | **Video Link**                              |     |
| ----------- | ---------------------------- | --------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- | --- |
| 01          | The Foundation               | Proxmox Installation, VM Provisioning                                 | **[SOP-01](02_SOPs/SOP-01_Proxmox_Installation.md) to [SOP-04](02_SOPs/SOP-04_Ubuntu_Server_Provisioning.md)**                            | https://www.youtube.com/watch?v=c6ZbCqxJuvM |     |
| 02          | Containerization             | Docker Engine, Portainer, Snapshots                                   | **[SOP-05](02_SOPs/SOP-05_Guest_Services_&_Data_Integrity.md)** to **[SOP-07:](02_SOPs/SOP-07_Containerization_(Docker_&_Portainer).md)** | https://www.youtube.com/watch?v=fCr6QeNjRn0 |     |
| 03          | Edge Networking and Services | Nginx Proxy Manager, Cloudflare DNS, Minecraft Bedrock, Tailscale VPN | **[SOP-08](02_SOPs/SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)** to **[SOP-11](02_SOPs/SOP-11_Secure_Remote_Access.md)**    | https://www.youtube.com/watch?v=fT8O6tvYz6E |     |
| 04          | System Hardening             | SSH Keys, Password Disable, Git                                       | **[SOP-12](02_SOPs/SOP-12_SSH_Key-Based_Authentication.md)**                                                                              | https://www.youtube.com/watch?v=_-WZi9831JQ |     |
| 05          | Personal Cloud               | Storage Expansion, Immich, 3-2-1 Backup                               | **[SOP-13](02_SOPs/SOP-13_Physical_Storage_Expansion.md)** to **[SOP-16](SOP-16_Automated_OS_&_Configuration_Redundancy.md)**             | Pending                                     |     |
|             |                              |                                                                       |                                                                                                                                           |                                             |     |
|             |                              |                                                                       |                                                                                                                                           |                                             |     |

## Repository Structure

- **[01_Knowledge_Base:](01_Knowledge_Base/Angel_Server_Breakage_Log.md)** Technical SOPs, Physical Topology Logs, and the Service Port Map.
    
- **[Angel Server MOC:](./Angel_Server_MOC.md)** The Map of Content for quick navigation across the entire project.