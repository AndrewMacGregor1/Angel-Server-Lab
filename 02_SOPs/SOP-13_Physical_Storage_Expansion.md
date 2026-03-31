### 1. Purpose

This procedure defines the initialization of a secondary physical Hard Disk Drive (HDD) within Proxmox and the implementation of a **Virtio-FS Directory Bridge** to the Ubuntu Server guest OS. This method allows the Proxmox host to manage the physical health of the drive while providing the VM high-speed, shared access to data volumes without the overhead of virtual disk images.

### 2. Lab Environment Reference

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**Physical Disk**|500GB HDD|WDC/ST Secondary Drive|
|**Storage Name**|`angel-data`|Logical Proxmox Storage ID|
|**Host Path**|`/mnt/pve/angel-data`|Physical mount point on Proxmox|
|**VM Mount**|`/mnt/data`|Path inside Ubuntu VM (`.151`)|
|**Bridge Tech**|`Virtio-FS`|High-performance host-to-guest directory sharing|

### 3. Physical Initialization (Proxmox UI)

1. **Wipe Disk:** Navigate to **angel-server (node) > Disks**. Select the 500GB drive and click **Wipe Disk**.
    
2. **Initialize:** Click **Initialize Disk with GPT**.
    
3. **Create Directory Storage:** Go to **Disks > Directory > Create: Directory**.
    
    - **Disk:** Select the 500GB drive.
        
    - **File System:** `ext4`.
        
    - **Name:** `angel-data`.
        
    - _Proxmox will now mount this drive at `/mnt/pve/angel-data`_.
        

### 4. Virtio-FS Bridge Configuration

Instead of adding a "Hard Drive" to the VM, we add a "Directory Mapping" to the Datacenter.

**Step 1: Create Datacenter Mapping**

1. Navigate to **Datacenter > Directory Mappings**.
    
2. Click **Add**.
    
3. **ID:** `angel_data_bridge`.
    
4. **Path:** `/mnt/pve/angel-data`.
    

**Step 2: Attach Hardware to VM**

1. Navigate to **VM 100 > Hardware**.
    
2. Click **Add > Virtio-FS**.
    
3. **Tag:** `angel_data_bridge`.
    
4. **Reboot the VM** to "plug in" the new virtual device.
    

### 5. Guest OS Integration (Ubuntu CLI)

**Step 1: Create the Mount Point**

```
sudo mkdir -p /mnt/data
sudo chown -R $USER:$USER /mnt/data
```

**Step 2: Configure Persistent Mounting (`fstab`)**

To ensure the bridge mounts automatically after a reboot:

1. Edit fstab: `sudo nano /etc/fstab`.
    
2. Add the following line to the bottom:
    
    `angel_data_bridge /mnt/data virtiofs defaults 0 0`
    

### 6. Post-SOP Verification

- **Mount Test:** Run `sudo mount -a`.
    
- **Space Check:** Run `df -h /mnt/data` and verify it shows the HDD capacity (approx. 460GB-492GB).