### **1. Purpose**

This procedure establishes a Network File System (NFS) bridge between the OptiPlex 9020 (Storage Provider) and the N100 Mini PC (Service Host). This allows the N100 to utilize the 500GB Toshiba HDD for media and downloads without physical attachment.

### **2. Lab Environment Reference**

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**NFS Server**|`192.168.0.151`|OptiPlex 9020 (Proxmox Host)|
|**NFS Client**|`192.168.0.152`|N100 Mini PC (Proxmox Host)|
|**Export Path**|`/mnt/pve/toshiba-storage`|Physical HDD Mount Point|
|**Jellyfin LXC**|ID `102` / `.161`|New N100 Container|
|**Ingest VM**|ID `103` / `.162`|New N100 Virtual Machine|

---

### **3. Step 1: Configure NFS Export (OptiPlex Host)**

We must tell the OptiPlex to share its HDD with the new N100 node.

1. **Install Server Tools:** `apt update && apt install nfs-kernel-server -y`
    
2. **Define the Export:** Edit `/etc/exports` and add the following line:
    
    `/mnt/pve/toshiba-storage 192.168.0.152(rw,sync,no_subtree_check,all_squash,anonuid=1000,anongid=1000)`
    
    - _Note: This maps all N100 requests to UID 1000, matching your existing file permissions._
        
3. **Apply Changes:** `sudo exportfs -ra`
    

---

### **4. Step 2: Mount the Share (N100 Host)**

Now we make the N100 "see" that storage as if it were a local folder.

1. **Install Client Tools:** `apt update && apt install nfs-common -y`
    
2. **Create Mount Point:** `mkdir -p /mnt/pve/toshiba-storage`
    
3. **Configure FSTAB:** Add this to `/etc/fstab` on the N100:
    
    `192.168.0.151:/mnt/pve/toshiba-storage /mnt/pve/toshiba-storage nfs defaults,_netdev,soft 0 0`
    
4. **Mount All:** `mount -a`
    

---

### **5. Step 3: Deploy Jellyfin LXC (N100)**

Provision a new Unprivileged LXC on the N100 using the **Alder Lake iGPU** for transcoding.

1. **Create Container:** Use Ubuntu 24.04, ensure "Unprivileged" is checked.
    
2. **Add Bind Mount:** In **LXC > Resources**, add a mount point:
    
    - **Path:** `/mnt/pve/toshiba-storage/jellymain`
        
    - **Destination:** `/media`
        
3. **GPU Passthrough:** In the Proxmox UI, add Device Passthrough for `/dev/dri/renderD128`.
    
4. **Hardware Acceleration:** Inside the LXC, install the media drivers:
    
    `apt update && apt install -y intel-media-va-driver-non-free intel-gpu-tools`.
    

---

### **6. Step 4: Deploy Ingest VM (N100)**

Provision a new Ubuntu VM for your VPN-protected downloads.

1. **Storage:** Store the OS disk on the N100's **NVMe SSD** for speed.
    
2. **Data Bridge:** Mount the NFS share from the N100 host into the VM using **Virtio-FS**.
    
3. **Stack:** Deploy your **Gluetun + ProtonVPN + qBittorrent** stack as per **SOP-19**.
    

---

### **7. Post-SOP Verification**

- **NFS Check:** On the N100, run `ls -l /mnt/pve/toshiba-storage`. You should see your `jellymain` and `immich` folders.
    
- **Transcoding Check:** Open Jellyfin, play a 4K file, and run `intel_gpu_top` inside the LXC. You should see the "Video" bar moving, confirming **QuickSync** is working on the N100.
    
- **VPN Kill-Switch:** Inside the Ingest VM, run `docker exec qbittorrent curl ifconfig.me` to confirm you are using a ProtonVPN IP.
    
- **Disk Speed:** Confirm the Ingest VM boots in seconds now that it is on the NVMe instead of the old HDD.