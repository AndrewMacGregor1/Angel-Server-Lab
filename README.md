## 1. Project Overview

This is a documentation-first home lab project centered around a Dell OptiPlex 9020 SFF. This repository tracks the transition from classroom theory at Virginia Western Community College (VWCC) to hands-on Linux Administration and Enterprise Networking experience.

## 2. Infrastructure Architecture

The following diagram represents the planned architecture of the Angel Server lab as the project progresses through the YouTube series.

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'primaryColor': '#2ecc71', 'edgeLabelBackground':'#282c34', 'tertiaryColor': '#282c34'}}}%%
graph TD
    %% Custom Styles for GitHub Contrast
    classDef internet fill:#e67e22,stroke:#fff,stroke-width:2px,color:#fff;
    classDef cloud fill:#2980b9,stroke:#fff,stroke-width:2px,color:#fff;
    classDef home fill:#27ae60,stroke:#fff,stroke-width:2px,color:#fff;
    classDef node fill:#2c3e50,stroke:#fff,stroke-width:1px,color:#fff;

    subgraph External ["External Access"]
        User((Remote Access)):::internet
        Guest((Public Guest)):::internet
    end

    subgraph Cloud ["Cloud Logic"]
        CF[Cloudflare DNS / DDNS]:::cloud
        TS[Tailscale Mesh VPN]:::cloud
    end

    subgraph Lab ["Home Lab: Dell OptiPlex 9020"]
        Router[Cox Gateway<br>192.168.0.1]:::home
        
        subgraph Physical ["Physical Host (Proxmox)"]
            PVE[Proxmox VE<br>192.168.0.150]:::node
            
            subgraph Virtual ["Virtual Node (Ubuntu)"]
                OS[angel-node-01<br>192.168.0.151]:::node
                
                subgraph Docker ["Docker Platform"]
                    NPM[Nginx Proxy Manager<br>Reverse Proxy]:::node
                    Portainer[Portainer UI<br>:9443]:::node
                    MC[Minecraft Bedrock<br>UDP :19132]:::node
                    Future[Future: Plex / Sinkhole]:::node
                end
            end
        end
    end

    %% Traffic Flows
    User --> TS
    TS -.->|Secure Tunnel| PVE
    TS -.->|Secure Tunnel| OS
    
    Guest --> CF
    CF --> Router
    
    %% Defined Traffic Routing
    Router -->|HTTPS :443| NPM
    Router -->|UDP :19132| MC
    
    NPM --> Portainer
    NPM --> Future
```

## 3. Technical Stack

- **Hypervisor:** Proxmox VE 9.1 (Bare-Metal).
    
- **Guest OS:** Ubuntu Server 24.04 LTS.
    
- **Hardware:** Intel Core i5-4590, 16GB DDR3 RAM, 128GB SSD (OS), and 500GB HDD (Data).
    
- **Networking:** Static IPv4 configuration via a dedicated 100ft physical Ethernet run.

## 4. Project Roadmap & Video Series

The following table tracks the transition from foundational infrastructure to high-level service deployment. Each milestone corresponds to a technical phase and its associated documentation.

| **Episode** | **Phase**                    | **Primary Milestones**                                                | **Documentation**                                                                                                                        | **Video Link**                              |     |
| ----------- | ---------------------------- | --------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- | --- |
| 01          | The Foundation               | Proxmox Installation, VM Provisioning                                 | **[SOP-01](02_SOPs/SOP-01_Proxmox_Installation.md) to [SOP-04](02_SOPs/SOP-04_Ubuntu_Server_Provisioning.md)**                           | https://www.youtube.com/watch?v=c6ZbCqxJuvM |     |
| 02          | Containerization             | Docker Engine, Portainer, Snapshots                                   | **[SOP-05](02_SOPs/SOP-05_Guest_Services_&_Data_Integrity.md)** to **[SOP-07](02_SOPs/SOP-07_Containerization_(Docker_&_Portainer).md)** | https://www.youtube.com/watch?v=fCr6QeNjRn0 |     |
| 03          | Edge Networking and Services | Nginx Proxy Manager, Cloudflare DNS, Minecraft Bedrock, Tailscale VPN | **[SOP-08](02_SOPs/SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)** to **[SOP-11](02_SOPs/SOP-11_Secure_Remote_Access.md)**   | https://www.youtube.com/watch?v=fT8O6tvYz6E |     |
| 05          | System Hardening             | SSH Keys, Password Disable, Git                                       | **[SOP-12](02_SOPs/SOP-12_SSH_Key-Based_Authentication.md)**                                                                             | Pending                                     |     |
| 06          | Personal Cloud               | Storage Expansion, Immich, 3-2-1 Backup                               | **[SOP-13](02_SOPs/SOP-13_Physical_Storage_Expansion.md)** to **[SOP-15](02_SOPs/SOP-15_Automated_Offsite_Data_Redundancy.md)**          | Pending                                     |     |
|             |                              |                                                                       |                                                                                                                                          |                                             |     |

## Repository Structure

- **[01_Knowledge_Base:](01_Knowledge_Base/Angel_Server_Breakage_Log.md)** Technical SOPs, Physical Topology Logs, and the Service Port Map.
    
- **[Angel Server MOC:](./Angel_Server_MOC.md)** The Map of Content for quick navigation across the entire project.