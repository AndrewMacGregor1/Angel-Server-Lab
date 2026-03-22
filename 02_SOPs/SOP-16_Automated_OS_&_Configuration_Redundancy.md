## 1. Purpose

This procedure defines the multi-layer backup strategy for the **Angel Server** operating system and service configurations. By capturing file-level "blueprints" (`/opt/docker`), disk-level images (VZDump), and offsite synchronization, the lab achieves a Near-Zero Recovery Time Objective (RTO).

## 2. Lab Environment Reference

The following values represent the recovery architecture for the **angel-node-01** Ubuntu node.

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**Config Source**|`/opt/docker`|Directory for NPM, Portainer, and Minecraft YAMLs|
|**Image Source**|VM 100 (angel-node-01)|The full virtual machine disk (OS only)|
|**Backup Tool**|`Rclone` / `vzdump`|Synchronization and image extraction|
|**Offsite Target**|`encrypted_gdrive`|Encrypted Google Drive "Crypt" remote|

---

## 3. Layer 1: Configuration "Blueprints" & System Images

### Step 1: Update the Automation Script

The backup script must be configured to handle three distinct sync jobs to ensure total coverage. Run `nano ~/scripts/offsite-backup.sh` and ensure it contains:


```
#!/bin/bash
# JOB 1: Sync Photos (SOP-15)
rclone sync /mnt/data/immich-photos encrypted_gdrive:immich_backups --progress

# JOB 2: Sync Docker Blueprints (SOP-16)
rclone sync /opt/docker encrypted_gdrive:docker_configs --progress

# JOB 3: Sync Proxmox VM Images (SOP-16)
rclone sync /var/lib/vz/dump encrypted_gdrive:vm_images --progress
```

### Step 2: Configure Proxmox Local Backups

1. **Exclude Data**: In **VM 100 > Hardware**, select the **500GB HDD**, click **Edit**, and **Uncheck "Backup"**.
    
2. **Storage**: In **Datacenter > Storage**, add a directory at `/var/lib/vz/dump` labeled `local-backups` with content type **Backup**.
    
3. **Schedule**: In **Datacenter > Backup**, schedule a weekly snapshot of **VM 100** for **Saturday at 03:00**.
    

---

## 4. Recovery Procedures (The Restore Drill)

### Scenario A: Accidental Deletion (Short-term)

If a configuration is broken or a container is deleted but the hardware is fine:

- Navigate to **VM 100 > Snapshots**.
    
- Select the most recent stable snapshot and click **Rollback**.
    

### Scenario B: SSD Failure / OS Corruption (Mid-term)

If the Ubuntu OS is unbootable but the Proxmox host is functional:

1. Navigate to your `local-backups` storage in the Proxmox UI.
    
2. Select the latest `.vma.zst` image and click **Restore**.
    
3. Ensure the restored VM points to the correct 500GB physical HDD.
    

### Scenario C: Total Hardware Loss (Disaster Recovery)

If the physical Dell OptiPlex is destroyed or lost:

1. **Provision Hardware**: Install Proxmox on a new machine.
    
2. **Initialize Rclone**: Install Rclone on the new host and re-authorize the `encrypted_gdrive` remote using the saved **Password and Salt**.
    
3. **Restore Configs**: Run `rclone copy encrypted_gdrive:docker_configs /opt/docker` to recover your service blueprints.
    
4. **Restore VM**: Download the latest image from `vm_images` and use the Proxmox **Restore** feature to recreate the node.
    

---

## 5. Post-SOP Verification

- **Daily Check**: Verify that `docker_configs` in Google Drive updates daily at 02:00.
    
- **Weekly Check**: Confirm a new encrypted image appears in `vm_images` every Sunday morning.
    
- **Password Audit**: Ensure the Crypt passwords are stored in a secure, off-network location.