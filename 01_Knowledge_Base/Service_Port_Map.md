## 1. Purpose

This document provides a centralized reference for all port assignments within the Angel Server environment. It is used to prevent port conflicts when deploying new Docker containers and to assist in the configuration of reverse proxy routing.

## 2. Node Reference

The following values represent the active primary node for the lab environment.

| **Attribute**   | **Lab Value (Example)** | **Context**          |
| --------------- | ----------------------- | -------------------- |
| **Node Name**   | `angel-node-01`         | Primary Docker Host  |
| **Internal IP** | `192.168.0.151`         | Static Management IP |
| Tailscale IP    | 100.y.y.y               | Private Mesh VPN IP  |

## 3. Assigned Service Ports

This table tracks the mapping between the host's physical ports and the internal container ports.

| **Service**             | **Host Port** | **Container Port** | **Protocol** | **Reference**                                                                                    |     |
| ----------------------- | ------------- | ------------------ | ------------ | ------------------------------------------------------------------------------------------------ | --- |
| **SSH**                 | 22            | 22                 | **TCP**      | **[SOP-04: Ubuntu Provisioning](SOP-04_Ubuntu_Server_Provisioning.md)**                          |     |
| **Portainer (HTTPS)**   | 9443          | 9443               | **TCP**      | **[SOP-07: Containerization](SOP-07_Containerization_(Docker_&_Portainer).md)**                  |     |
| **Nginx Proxy (HTTP)**  | 80            | 80                 | **TCP**      | **[SOP-08: Reverse Proxy Deployment](SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)** |     |
| **Nginx Proxy (HTTPS)** | 443           | 443                | **TCP**      | **[SOP-08: Reverse Proxy Deployment](SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)** |     |
| **Nginx Admin UI**      | 81            | 81                 | **TCP**      | **[SOP-08: Reverse Proxy Deployment](SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)** |     |
| **Minecraft Bedrock**   | 19132         | 19132              | **UDP**      | **[SOP-10: Minecraft Bedrock Edition](SOP-10_Minecraft_Bedrock_Edition_Deployment.md)**          |     |
| **Immich Web UI**       | 2283          | 2283               | **TCP**      | **[SOP-14: Immich Photo Engine Deployment](SOP-14_Immich_Photo_Engine_Deployment )**             |     |
| **Immich ML **          | 3001          | 3001               | **TCP**      | **[SOP-14: Immich Photo Engine Deployment](SOP-14_Immich_Photo_Engine_Deployment )**             |     |

## 4. Conflict Resolution

Before deploying any new service, this map must be consulted to ensure the desired Host Port is available. If a conflict is identified, the new service must be assigned an alternative port, typically in the `10000-65535` range.