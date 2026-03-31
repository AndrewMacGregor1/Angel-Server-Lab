## 1. Purpose

This document provides a centralized reference for all port assignments within the Angel Server environment. It is used to prevent port conflicts when deploying new Docker containers and to assist in the configuration of reverse proxy routing.

## 2. Node Reference

The following values represent the active nodes for the lab environment.

| **Node Name**   | **Internal IP** | **Role / Context**             |
| --------------- | --------------- | ------------------------------ |
| `angel-node-01` | `192.168.0.151` | Primary VM (NFS Server / NPM)  |
| `minecraft-lxc` | `192.168.0.160` | Game Server (LXC 101)          |
| `jellyfin-lxc`  | `192.168.0.161` | Media Server (LXC 102)         |
| `ingest-vm`     | `192.168.0.162` | VPN Download VM (VM 103)       |
| `Proxmox Host`  | `192.168.0.150` | Physical Hypervisor Management |
|                 |                 |                                |

## 3. Assigned Service Ports

This table tracks the mapping between the service's physical ports and the internal container ports across the specific nodes.

| **Service**             | **Node IP**     | **Host Port** | **Internal Port** | **Protocol** | **Reference**                                                                                                                      |
| ----------------------- | --------------- | ------------- | ----------------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| **SSH (Management)**    | `ALL`           | 22            | 22                | **TCP**      | **[SOP-04: Ubuntu Provisioning](02_SOPs/SOP-04_Ubuntu_Server_Provisioning.md)**                                                    |
| **Nginx Proxy (HTTP)**  | `192.168.0.151` | 80            | 80                | **TCP**      | **[SOP-08: Reverse Proxy Deployment](02_SOPs/SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)**                           |
| **Nginx Proxy (HTTPS)** | `192.168.0.151` | 443           | 443               | **TCP**      | **[SOP-08: Reverse Proxy Deployment](02_SOPs/SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)**                           |
| **Nginx Admin UI**      | `192.168.0.151` | 81            | 81                | **TCP**      | **[SOP-08: Reverse Proxy Deployment](02_SOPs/SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)**                           |
| **Immich Web UI**       | `192.168.0.151` | 2283          | 2283              | **TCP**      | **[SOP-14: Immich Photo Engine Deployment](02_SOPs/SOP-14_Immich_Photo_Engine_Deployment.md )**                                    |
| **Minecraft Bedrock**   | `192.168.0.160` | 19132         | 19132             | **UDP**      | **[SOP-17: Dedicated Minecraft LXC & Traffic Redirection ](02_SOPs/SOP-17_Dedicated_Minecraft_LXC_&_Traffic_Redirection.md)**      |
| **Jellyfin Web UI**     | `192.168.0.161` | 8096          | 8096              | **TCP**      | **[SOP-18: Jellyfin Media Server LXC + Hardware Transcoding](02_SOPs/SOP-18_Jellyfin_Media_Server_LXC_+_Hardware_Transcoding.md)** |
| **qBittorrent Web UI**  | `192.168.0.162` | 8080          | 8080              | **TCP**      | **[SOP-19 VPN Protected Ingest VM](02_SOPs/SOP-19_VPN_Protected_Ingest_VM.md)**                                                    |

## 4. Conflict Resolution

Before deploying any new service, this map must be consulted to ensure the desired Host Port is available on the target Node IP. If a conflict is identified, the new service must be assigned an alternative port, typically in the `10000-65535` range.