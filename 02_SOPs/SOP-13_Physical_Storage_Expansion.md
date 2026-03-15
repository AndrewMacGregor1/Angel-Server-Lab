## 1. Purpose

This procedure defines the initialization of a secondary physical Hard Disk Drive (HDD) within the Proxmox hypervisor and the subsequent mounting of that storage to the Ubuntu Server guest OS. This expansion provides a dedicated high-capacity volume for persistent data, such as photo backups or media, while preserving the primary SSD for the operating system.

## 2. Lab Environment Reference

The following values are used for the **Angel Server** storage expansion.

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**Physical Disk**|500GB HDD|WDC/ST Secondary Drive|
|**Storage Name**|`angel-data`|Logical Proxmox Storage ID|
|**Target Mount**|`/mnt/data`|Path inside Ubuntu VM (`.151`)|
|**File System**|`ext4`|Standard Linux journaled file system|

## 3. Physical Initialization (Proxmox UI)

Before the VM can see the drive, it must be wiped and initialized by the hypervisor host (`192.168.0.150`).

1. **Wipe Disk:** Navigate to **angel-server (node) > Disks**. Select the 500GB drive and click **Wipe Disk**.
    
2. **Initialize:** Click **Initialize Disk with GPT**.
    
3. **Create Storage:** Go to **Disks > Directory > Create: Directory**.
    
    - **Disk:** Select the 500GB drive.
        
    - **File System:** `ext4`.
        
    - **Name:** `angel-data`.
        
4. **Attach to VM:** Navigate to **VM 100 (angel-node-01) > Hardware**.
    
    - Click **Add > Hard Disk**.
        
    - **Storage:** Select `angel-data`.
        
    - **Disk Size:** `500` (or maximum available).
        
    - Click **Add**.
        

## 4. Guest OS Integration (Ubuntu CLI)

Now that the "virtual cable" is plugged in, the guest OS must be told where to put the data.

**Step 1: Identify the new disk**

```
lsblk
```

_(Look for a new disk, likely `/dev/sdb`, with a size of ~500G)._

**Step 2: Create a partition and format (if not passed as raw)**

```
sudo mkfs.ext4 /dev/sdb1
```

**Step 3: Create the mount point and set permissions**

```
sudo mkdir -p /mnt/data
sudo chown -R $USER:$USER /mnt/data
```

**Step 4: Configure Persistent Mounting (`fstab`)**

To ensure the drive mounts automatically after a reboot, it must be added to the file system table.

1. Get the drive UUID: `sudo blkid /dev/sdb1`
    
2. Edit fstab: `sudo nano /etc/fstab`
    
3. Add the following line to the bottom:
    
    `UUID=[YOUR_UUID_HERE] /mnt/data ext4 defaults 0 2`
    

## 5. Post-SOP Verification

- **Mount Test:** Run `sudo mount -a`. If no errors appear, the config is correct.
    
- **Space Check:** Run `df -h` and verify that `/mnt/data` shows ~460GB of available space.
    
- **Persistence Test:** Reboot the VM (`sudo reboot`) and verify the drive is still mounted at `/mnt/data` upon login.