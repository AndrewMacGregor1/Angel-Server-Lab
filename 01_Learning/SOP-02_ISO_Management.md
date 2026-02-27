## 1. Purpose

This procedure outlines the process for acquiring, transferring, and verifying Operating System (OS) images (ISOs) within the Proxmox Virtual Environment (PVE). This step is required before any Virtual Machines can be provisioned on the **Angel Server** hardware.

## 2. Lab Environment Reference

The following values are used as examples for the Angel Server environment.

| **Attribute**      | **Lab Value (Example)**              | **Context**                    |
| ------------------ | ------------------------------------ | ------------------------------ |
| **Reference ISO**  | `ubuntu-24.04-live-server-amd64.iso` | Ubuntu 24.04 LTS               |
| **Target Storage** | `local`                              | Default ISO/Template directory |
| **Node Name**      | `angel-server`                       | Primary PVE Node               |
| **Boot Drive**     | 128GB SSD                            | Physical OS/Storage drive      |
|                    |                                      |                                |

## 3. Image Acquisition

1. **Source Selection:** Download the intended ISO (e.g., **Ubuntu Server LTS**) from the official distribution website to a local workstation.
    
2. **Subnet Verification:** Ensure the workstation is on the same subnet as the Proxmox host to allow for a successful network transfer (e.g., both on `192.168.0.x`).
    

## 4. Establishing the Hypervisor Connection

1. **Authentication:** Access the Proxmox Web UI via browser (e.g., `https://192.168.0.150:8006`) and log in with the `root` administrative account.
    
2. **Node Navigation:** In the **Datacenter** resource tree on the left, expand the target node (e.g., `angel-server`).
    
3. **Storage Identification:** Select the storage volume designated for ISO images.
    
    - **Note:** In default Proxmox installations, **local** is typically configured for ISOs and templates, while **local-lvm** is reserved for virtual disk images.
    

## 5. The Upload Process

1. **Selection:** In the center menu, navigate to the **ISO Images** tab.
    
2. **Initiation:** Click the **Upload** button at the top of the interface.
    
3. **File Mapping:** Click **Select File**, browse to the ISO downloaded in Section 3, and confirm the selection.
    
4. **Execution:** Click **Upload**. Monitor the transfer progress until the **Task Viewer** returns a status of `TASK OK`.
    

## 6. Post-Upload Verification

1. **Integrity Check:** Verify that the ISO filename and size appear correctly within the **ISO Images** list.
    
2. **Capacity Check:** Monitor the "local" storage utilization. While a 128GB SSD can store several dozen ISOs, it is best practice to keep this directory organized and remove unused images to preserve space.