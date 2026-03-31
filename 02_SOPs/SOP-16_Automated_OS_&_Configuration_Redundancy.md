### 1. Purpose

This procedure defines the multi-layer backup strategy for the **Angel Server**. By utilizing a high-capacity HDD for local image storage and an encrypted offsite sync, we achieve redundancy without sacrificing primary SSD lifespan or performance.

### 2. Lab Environment Reference

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**Config Source**|`/opt/docker`|Service blueprints (NPM, Portainer, etc.)|
|**Image Source**|VM 100 (angel-node-01)|Full VM disk image (VZDump)|
|**Backup Target**|`/mnt/proxmox-hdd-backups`|HDD-based Virtio-FS bridge|
|**Offsite Target**|`encrypted_gdrive`|Encrypted Google Drive "Crypt" remote|
|**Alerting**|`Discord Webhook`|Real-time success/failure notifications|

---

### 3. Layer 1: Configuration & Image Automation

**Step 1: Create the Discord Webhook**

1. In Discord, go to **Server Settings > Integrations > Webhooks**.
    
2. Create a new Webhook, name it "Server Bot," and copy the **Webhook URL**.
    

**Step 2: Update the Automation Script**

Run `nano ~/scripts/offsite-backup.sh` and update the logic to point to the new HDD bridge and include the Discord notification:


```
#!/bin/bash
# Angel Server Multi-Layer Backup Script
CONFIG="/home/drew/.config/rclone/rclone.conf"
WEBHOOK_URL="YOUR_DISCORD_WEBHOOK_URL_HERE"

# JOB 1: Sync Photos (HDD)
rclone --config $CONFIG sync /mnt/data/immich-photos/ encrypted_gdrive:immich/ --progress

# JOB 2: Sync Nextcloud (HDD)
rclone --config $CONFIG sync /mnt/data/nextcloud-data/ encrypted_gdrive:nextcloud/ --progress

# JOB 3: Sync Docker Blueprints (SSD)
rclone --config $CONFIG sync /opt/docker/ encrypted_gdrive:docker-configs/ --progress

# JOB 4: Sync Proxmox VM Images (New HDD Bridge)
rclone --config $CONFIG sync /mnt/proxmox-hdd-backups/ encrypted_gdrive:vm-images/ --progress

# Discord Notification
curl -H "Content-Type: application/json" -X POST -d "{\"content\": \"🚀 **Backup Successful:** Angel Server has finished syncing to Google Drive. SSD space is optimized.\"}" $WEBHOOK_URL
```

**Step 3: Update Proxmox Backup Target**

1. Navigate to **Datacenter > Backup**.
    
2. Edit the existing job for **VM 100**.
    
3. Change **Storage** to `angel-data`.
    
4. Set **Retention** to `Keep Last: 5` (Since you now have 500GB of space).
    

---

### 4. Recovery Procedures

**Scenario A: SSD Failure / OS Corruption**

1. In the Proxmox UI, navigate to the **`angel-data`** storage.
    
2. Select the latest `.vma.zst` image from the **Backups** tab.
    
3. Click **Restore**.
    

**Scenario B: Total Hardware Loss**

1. Reinstall Proxmox on new hardware.
    
2. Re-mount the 500GB HDD or pull the `.vma.zst` file down from Google Drive using `rclone copy`.
    
3. Use the Proxmox **Restore** feature to recreate the VM.
    

---

### 5. Post-SOP Verification

- **Discord Check:** Manually run the script (`sudo ~/scripts/offsite-backup.sh`) and verify the notification appears in your Discord channel.
    
- **Path Check:** Run `ls -lh /mnt/proxmox-hdd-backups` to confirm Ubuntu can see the Proxmox-generated images.