## 1. Project Overview

- **Goal:** Documenting the transition from theory to hands-on networking and Linux experience through a home lab environment.
- **Hardware Specs:** Node 01: Dell OptiPlex 9020 SFF Profile.

## 2. The Documentation Pipeline (SOPs)

These Standard Operating Procedures define the repeatable steps used to build and scale the Angel Server infrastructure.

- **[SOP-01: Proxmox Installation](02_SOPs/SOP-01_Proxmox_Installation.md)** — Hardware prep and bare-metal hypervisor install on Dell OptiPlex 9020 SFF.
- **[SOP-1.5: N100 Node Provisioning](02_SOPs/SOP-1.5_N100_Node_Provisioning.md)** — Hardware prep and bare-metal hypervisor install on n100 mini PC.
- **[SOP-02: ISO Management](02_SOPs/SOP-02_ISO_Management.md)** — Network transfer of OS images to the server.
- **[SOP-03: VM Shell Creation](02_SOPs/SOP-03_VM_Shell_Creation.md)** — Resource allocation and virtual hardware configuration.
- **[SOP-04: Ubuntu Provisioning](02_SOPs/SOP-04_Ubuntu_Server_Provisioning.md)** — Finalizing the primary node and base hardening.
- **[SOP-05: Guest Services & Data Integrity](02_SOPs/SOP-05_Guest_Services_&_Data_Integrity.md)** — QEMU Agent setup and Proxmox snapshotting.
- **[SOP-06: System Hardening & Directory Structure](02_SOPs/SOP-06_System_Hardening_&_Directory_Structure.md)** — UFW firewall and /opt/docker organization.
- **[SOP-07: Containerization](02_SOPs/SOP-07_Containerization_(Docker_&_Portainer).md)** — Deploying Docker Engine and Portainer UI.
- **[SOP-08: Reverse Proxy Deployment](02_SOPs/SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)** — Centralized traffic management and SSL.
- **[SOP-09: Dynamic DNS Configuration](02_SOPs/SOP-09_Dynamic_DNS_(DDNS)_Configuration.md)** — Maintaining a persistent external FQDN.
- **[SOP-10: Minecraft Bedrock Edition](02_SOPs/SOP-10_Minecraft_Bedrock_Edition_Deployment.md)** — Deploying a persistent game world for cross-platform play.
- **[SOP-11: Secure Remote Access](02_SOPs/SOP-11_Secure_Remote_Access.md)** — Establishing a Mesh VPN via Tailscale for off-site management.
- **[SOP-12: SSH Key-Based Authentication](02_SOPs/SOP-12_SSH_Key-Based_Authentication.md)** — Transitioning to public-key cryptography and disabling password logins.
- **[SOP-12.1: SSH Key Backups](02_SOPs/SOP-12.1_Break_Glass.md)** — Establishing "Break Glass" procedures and secure off-site key backups (Not for video).
- **[SOP-13: Physical Storage Expansion](02_SOPs/SOP-13_Physical_Storage_Expansion.md)** — - Initializing and mounting the internal 500GB HDD to `/mnt/data`.
- **[SOP-14: Immich Photo Engine Deployment](02_SOPs/SOP-14_Immich_Photo_Engine_Deployment.md )** — Deploying a self-hosted photo/video cloud using the new HDD mount.
- **[SOP-15: Automated Offsite Redundancy](02_SOPs/SOP-15_Automated_Offsite_Data_Redundancy.md)** — Configuring Rclone for encrypted, automated 3-2-1 backups.
- **[SOP-16: Automated OS & Configuration Redundancy](02_SOPs/SOP-16_Automated_OS_&_Configuration_Redundancy)** — Configuring Rclone for backups of OS.
- **[SOP-17: Dedicated Minecraft LXC & Traffic Redirection ](02_SOPs/SOP-17_Dedicated_Minecraft_LXC_&_Traffic_Redirection.md)** — Moves Minecraft container to an independent LXC and sets up drive sharing with NFS and bind mounts.
- **[SOP-18: Jellyfin Media Server LXC + Hardware Transcoding](02_SOPs/SOP-18_Jellyfin_Media_Server_LXC_+_Hardware_Transcoding.md)** — Setting up jellyfin in it's own independent LXC.
- **[SOP-19: VPN Protected Ingest VM](02_SOPs/SOP-19_VPN_Protected_Ingest_VM.md)** —  Setting up a VM for torrenting with a kill switch.
- **[SOP-20: Catalyst 2960 Configuration](02_SOPs/SOP-20_Catalyst_2960_Configuration.md)** —  Basic Layer 2 configuration of new Cisco Catalyst switch.
- **[SOP-21: NFS Storage Bridge & N100 Service Deployment](02_SOPs/SOP-21_NFS_Storage_Bridge_&_N100_Service_Deployment.md)** —  Establishes a NFS bridge between the Dell OptiPlex and the new n100 mini PC.

## 3. Network Topology & Infrastructure

This section serves as the "Source of Truth" for the physical and logical layout of the lab. It provides a historical log of the physical deployment, current internal IP assignments, and external edge configurations required for remote access and service delivery.

-  [Physical Topology & Deployment Log](01_Knowledge_Base/Physical_Topology_Log.md) — Details on the 100ft Ethernet run and site constraints.
-  [Current Network Cheat Sheet](01_Knowledge_Base/Network_Cheat_Sheet.md) — Static IP assignments and gateway mapping.
-  [Edge Config Log](01_Knowledge_Base/Edge_Config_Log.md) — Record of router port forwarding rules and DDNS tokens.
-  [Service Port Map](01_Knowledge_Base/Service_Port_Map.md) — Quick-reference list of container-to-port mappings.

## 4. Maintenance & Versioning

Documentation in this section tracks the operational health and evolution of the server. The Breakage Log acts as a troubleshooting repository for technical hurdles.

-  [Angel Server Breakage Log](01_Knowledge_Base/Angel_Server_Breakage_Log.md) — Documenting technical hurdles and their subsequent resolutions.

## 5. Media & Content Tracking

This section bridges the gap between technical implementation and public documentation for the YouTube series. It provides a direct mapping of SOPs to specific video episodes and acts as a scratchpad for future laboratory expansions and educational content ideas.

- **[Episode Brainstorming](03_Content/Episode_Brainstorming.md)** — Ideas for future lab expansions.