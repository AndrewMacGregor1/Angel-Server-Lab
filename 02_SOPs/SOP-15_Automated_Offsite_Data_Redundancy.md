### 1. Purpose

This procedure defines the automation of encrypted offsite backups for the **Angel Server** data volume. By utilizing **Rclone**, critical data stored on the 500GB HDD is synchronized to a remote "Cold Storage" tier, fulfilling the 3-2-1 backup standard: 3 copies of data, 2 different media, 1 offsite.

### 2. Lab Environment Reference

The following values represent the backup architecture for the **angel-node-01** Ubuntu node.

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**Source Path**|`/mnt/data/immich-photos`|Local HDD storage for originals|
|**Backup Tool**|`Rclone`|Command-line program to manage cloud storage|
|**Remote Target**|`Backblaze B2 / AWS S3`|Example offsite "Cold Storage" provider|
|**Encryption**|`Rclone Crypt`|Client-side encryption before data leaves the lab|

### 3. Rclone Installation and Remote Configuration

Rclone acts as the "bridge" between your local HDD and the cloud.

**Step 1: Install Rclone on the Ubuntu Node**

```
sudo apt install rclone -y
```

**Step 2: Configure the Remote Connection**

1. Run `rclone config`.
    
2. Create a new remote (e.g., `remote_storage`) and follow the prompts for your specific provider (B2, S3, etc.).
    
3. **Critical:** Create a second "Crypt" remote that wraps your primary remote. This ensures that even if the cloud provider is hacked, your photos are unreadable without your local password.
    

### 4. Automation via Cron Job

Backups should be "Set and Forget" to ensure consistency.

**Step 1: Create the Backup Script**

```
nano ~/scripts/offsite-backup.sh
```

**Step 2: Define the Sync Command**

Paste the following logic (adjusting names to match your config):

```
#!/bin/bash
# Sync local HDD to Encrypted Cloud Storage
rclone sync /mnt/data/immich-photos encrypted_remote:backup-bucket --progress
```

**Step 3: Schedule the Task**

1. Open the crontab editor: `crontab -e`.
    
2. Add the following line to run the backup every night at 2:00 AM:
    
    `00 02 * * * /bin/bash /home/drew/scripts/offsite-backup.sh`
    

### 5. Post-SOP Verification

- **Manual Execution:** Run the script manually (`./offsite-backup.sh`) and verify files appear in your cloud provider’s web UI as encrypted "gibberish."
    
- **Log Review:** Redirect script output to a log file (`>> /var/log/backup.log`) and check it weekly for errors.
    
- **Restore Drill:** Periodically attempt to download and "Decrypt" a single photo from the cloud to your MacBook to ensure the recovery path works.