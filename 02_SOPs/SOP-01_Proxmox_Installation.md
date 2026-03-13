## 1. Purpose

This procedure outlines the steps for a standard bare-metal installation of Proxmox Virtual Environment (PVE) on x86 hardware. This serves as the foundational hypervisor for the **Angel Server** home lab project.

## 2. Lab Environment Reference

The following values are used as examples throughout this guide based on the specific Angel Server environment. Use these as a template for your own network configuration.

| **Attribute**          | **Lab Value (Example)** | **Context**           |
| ---------------------- | ----------------------- | --------------------- |
| **Reference Hardware** | Dell OptiPlex 9020 SFF  | i5-4590, 16GB RAM     |
| **Boot Drive**         | 128GB SSD (`/dev/sda`)  | Target OS drive       |
| **Static IP Address**  | `192.168.0.150`         | Primary Management IP |
| **Local Gateway**      | `192.168.0.1`           | Local Router Gateway  |
| **Lab Domain**         | `angel-server.lan`      | Local FQDN            |

## 3. Installation Media Preparation

1. **Download ISO:** Obtain the latest Proxmox VE ISO from the official Proxmox website.
    
2. **Flash Drive:** Use a utility like **Rufus** or **Etcher** to write the ISO to a USB drive.
    
3. **Compatibility:** If using Rufus, select **"DD Image Mode"** when prompted to ensure the bootloader is correctly written for both UEFI and Legacy BIOS systems.
    

## 4. Pre-Flight Hardware Configuration (BIOS)

Access the system BIOS (e.g., tap **F12** or **Del** on startup) and verify the following settings to ensure hypervisor compatibility:

- **Virtualization Support:** Enable **Intel® VT-x** or **AMD-V** to allow the hypervisor to run guest VMs.
    
- **I/O Virtualization:** Enable **Intel® VT-d** or **AMD-Vi** for PCIe pass-through capabilities.
    
- **Secure Boot:** **Disable** Secure Boot to prevent kernel-level authentication conflicts during the Proxmox installation.
    

## 5. OS Installation & Partitioning

1. **Boot:** Select the USB installation media from the system's one-time boot menu.
    
2. **Installer Mode:** Select **Install Proxmox VE (Graphical)**.
    
3. **Disk Selection:** Choose the target drive for the OS (e.g., `/dev/sda`).
    
    - **Note:** Use **ext4** for simple, single-disk setups or **ZFS** for environments requiring data redundancy and higher integrity.
        
4. **Networking:**
    
    - **Hostname:** Enter a **Fully Qualified Domain Name (FQDN)** (e.g., `angel-server.lan`).
        
    - **Management IP:** Assign a **Static IP address** (e.g., `192.168.0.150`).
        
    - **Gateway/DNS:** Set this to the local router’s IP address (e.g., `192.168.0.1`).
        

## 6. Post-Install Verification

1. **Web Interface:** From a workstation on the same network, navigate to the management URL:
    
    - `https://192.168.0.150:8006`
        
2. **Subscription Management:** Upon login, a "No Subscription" dialogue will appear. This is expected for community users and can be safely dismissed.
    
3. **Repository Configuration:** To enable system updates without a paid license, navigate to **Node > Updates > Repositories**:
    
    - **Disable** the `pve-enterprise` repository.
        
    - **Enable** the `pve-no-subscription` repository.
        
4. **System Update:** Open the **Shell** and execute the following command to ensure the node is running the latest security patches:

```
apt update && apt dist-upgrade
```
