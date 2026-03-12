## 1. Project Overview

This is a documentation-first home lab project centered around a Dell OptiPlex 9020 SFF. This repository tracks the transition from classroom theory at Virginia Western Community College (VWCC) to hands-on Linux Administration and Enterprise Networking experience.

## 2. Infrastructure Architecture

The following diagram represents the planned architecture of the Angel Server lab as the project progresses through the YouTube series.

```mermaid
graph TD

subgraph External_World [External Access]
    RemoteDevice["Remote Device<br>(Work/Laptop)"]
    Internet((Internet))
end

subgraph Cloud_Services [Cloud Logic]
    Cloudflare["Cloudflare DNS<br>(DDNS & SSL)"]
    Tailscale["Tailscale Control Plane<br>(Mesh VPN)"]
end

subgraph Home_Network [Home Lab: Dell OptiPlex 9020]
    Router["Cox Gateway (192.168.0.1)"]
    
    subgraph Hypervisor_Layer [Physical Host]
        Proxmox["Proxmox Host<br>192.168.0.150<br>(Tailscale Active)"]
        
        subgraph Guest_OS [Virtual Node]
            Ubuntu["Ubuntu Server VM<br>angel-node-01<br>192.168.0.151<br>(Tailscale Active)"]
            
            subgraph Docker_Platform [Containers]
                NPM["Nginx Proxy Manager<br>Reverse Proxy :80 :443"]
                Portainer["Portainer UI :9443"]
                Minecraft["Minecraft Bedrock :19132"]
                Future["Future: Plex & DNS Sinkhole"]
            end
        end
    end
end

%% Connections
RemoteDevice -- "Encrypted Tunnel" --> Tailscale
Tailscale -- "Bypass Firewall" --> Proxmox
Tailscale -- "Bypass Firewall" --> Ubuntu

Internet --> Cloudflare
Cloudflare --> Router
Router --> NPM
NPM --> Portainer
NPM --> Future
```

## 3. Technical Stack

- **Hypervisor:** Proxmox VE 9.1 (Bare-Metal).
    
- **Guest OS:** Ubuntu Server 24.04 LTS.
    
- **Hardware:** Intel Core i5-4590, 16GB DDR3 RAM, 128GB SSD (OS), and 500GB HDD (Data).
    
- **Networking:** Static IPv4 configuration via a dedicated 100ft physical Ethernet run.

## 4. Current Project Standing

The lab has completed its core infrastructure and containerization phases. The next stage of the project focuses on edge networking, external access, and service exposure using a reverse proxy and dynamic DNS.

### Completed (Episode 01: The Foundation)

- **[SOP-01](./01_Learning/SOP-01_Proxmox_Installation.md) to [SOP-04](./01_Learning/SOP-04_Ubuntu_Server_Provisioning.md)**: Successful bare-metal installation and provisioning of a hardened, headless Ubuntu VM.
    
-  **[Breakage Log](./01_Learning/Angel_Server_Breakage_Log.md)**: Resolved the ISO Lock boot error encountered during initial provisioning.

### Completed (Episode 02: The Container Engine)

- **[SOP-05:](./01_Learning/SOP-05_Guest_Services_&_Data_Integrity.md)**  Guest Agent integration and Proxmox snapshot baseline established.
    
- **[SOP-06:](./01_Learning/SOP-06_System_Hardening_&_Directory_Structure.md)** Standardized directory structure at /opt/docker and UFW hardening.
    
- **[SOP-07:](./01_Learning/SOP-07_Containerization_(Docker_&_Portainer).md)**  Deployment of the Docker Engine and Portainer management interface.

### Planned (Episode 03: Opening the Gates)

- **[SOP-08:](./01_Learning/SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)** and **[SOP-09:](./01_Learning/SOP-09_Dynamic_DNS_(DDNS)_Configuration)** Implementation of Nginx Proxy Manager and Dynamic DNS for secure remote access.
    
- **[SOP-10:](./01_Learning/SOP-10_Minecraft_Bedrock_Edition_Deployment.md)** Deployment of a persistent Minecraft Bedrock Edition server.

### Planned (Episode 04: The Secure Link)

- **[SOP-11:](./01_Learning/SOP-11_Secure_Remote_Access.md)**: Implementation of a Tailscale Mesh VPN to bypass ISP port filtering and enable full remote management of the Proxmox host and Ubuntu node from external networks.
    
- **[SOP-12:](./01_Learning/SOP-12_SSH_Key-Based_Authentication.md)**: Transitioning from password-based logins to Ed25519 public-key cryptography and disabling password authentication to harden the server against brute-force attacks.

## Repository Structure

- **[01_Learning:](./01_Learning/SOP-01_Proxmox_Installation.md)** Technical SOPs, Physical Topology Logs, and the Service Port Map.
    
- **[Angel Server MOC:](./Angel_Server_MOC.md)** The Map of Content for quick navigation across the entire project.