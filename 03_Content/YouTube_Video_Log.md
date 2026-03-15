## **1. Published Episodes**

#### **Episode 1: The Foundation**

- **Release Date:** 2026-02-27
    
- **SOPs Covered:**
    
    - **[SOP-01: Proxmox Installation](SOP-01_Proxmox_Installation.md)**  — Hardware prep and bare-metal hypervisor install.
        
    - **[SOP-02: ISO Management](SOP-02_ISO_Management.md)** — Network transfer of OS images to the server.
        
    - **[SOP-03: VM Shell Creation](SOP-03_VM_Shell_Creation.md)** — Resource allocation and virtual hardware configuration.
        
    - **[SOP-04: Ubuntu Provisioning](SOP-04_Ubuntu_Server_Provisioning.md)** — Finalizing the primary node and base hardening.
        
- **Status:** Published

#### **Episode 2: The Container Engine**

- **Release Date:** 2026-03-06
    
- **Goal:** Moving from a bare OS to a managed container environment.
    
- **SOPs Covered:**
    
    - **[SOP-05: Guest Services & Data Integrity](SOP-05_Guest_Services_&_Data_Integrity.md)** — QEMU Agent setup and Proxmox snapshotting.
        
    - **[SOP-06: System Hardening & Directory Structure](SOP-06_System_Hardening_&_Directory_Structure.md)** — UFW firewall and /opt/docker organization.
        
    - **[SOP-07: Containerization](SOP-07_Containerization_(Docker_&_Portainer).md)** — Deploying Docker Engine and Portainer UI.
    
- **Status:** Published

#### **Episode 3: Opening the Gates**

- **Goal:** Connecting the local lab to the outside world securely.
    
- **SOPs Covered:**
    
    - **[SOP-08: Reverse Proxy Deployment](SOP-08_Reverse_Proxy_Deployment_(Nginx_Proxy_Manager).md)** — Centralized traffic management and SSL.
        
    - **[SOP-09: Dynamic DNS Configuration](SOP-09_Dynamic_DNS_(DDNS)_Configuration.md)** — Maintaining a persistent external FQDN.
        
    - **[SOP-10: Minecraft Bedrock Edition](SOP-10_Minecraft_Bedrock_Edition_Deployment.md)** — Deploying a persistent game world for cross-platform play.
        
    - **[SOP-11: Secure Remote Access](SOP-11_Secure_Remote_Access.md)** — Establishing a Mesh VPN via Tailscale for off-site management.
    
- **Status:** Published

---
## **2. Production Pipeline**

#### **Episode 4: The Secure Link**

- **Goal**: Hardening host access.
    
- **SOPs Covered**:
      
    - **[SOP-12: SSH Key-Based Authentication](SOP-12_SSH_Key-Based_Authentication.md)** — Transitioning to public-key cryptography and disabling password logins.
	    
    - **[SOP-12.1: SSH Key Backups](SOP-12.1_Break_Glass)**  — Establishing "Break Glass" procedures and secure off-site key backups (Not for video).
	    
    - **Git Implementation** — Setting up local and remote Git repositories for tracking `/opt/docker` and documentation changes.
    
- **Status**: Planned.

#### **Episode 5: The Personal Cloud**

- **Goal**: Utilizing secondary hardware for private data storage and redundancy.
    
- **SOPs Covered**:
    
    - **[SOP-13: Physical Storage Expansion](SOP-13_Physical_Storage_Expansion)** — Initializing and mounting the internal 500GB HDD to `/mnt/data`.
        
    - **[SOP-14: Immich Photo Engine Deployment](SOP-14_Immich_Photo_Engine_Deployment )**  — Deploying a self-hosted photo/video cloud using the new HDD mount.
        
    - **[SOP-15: Automated Offsite Redundancy](SOP-15_Automated_Offsite_Data_Redundancy)** — Configuring Rclone for encrypted, automated 3-2-1 backups.
        
- **Status**: Planned.



