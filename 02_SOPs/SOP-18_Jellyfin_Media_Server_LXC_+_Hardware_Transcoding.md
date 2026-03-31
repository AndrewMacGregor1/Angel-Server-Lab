### 1. Purpose

Establish a high-performance media server using an **Unprivileged LXC**. By mapping the **Intel iGPU** and utilizing **UID mapping** to the NFS Master Share, we enable **Intel QuickSync (QSV)** for 4K transcoding while maintaining seamless access to the central media library.

### 2. Lab Environment Reference

| **Attribute**    | **Value**                   | **Context**                    |
| ---------------- | --------------------------- | ------------------------------ |
| **New LXC ID**   | `102`                       | Proxmox Container ID           |
| **New LXC IP**   | `192.168.0.161`             | Static IP (Reserved in Router) |
| **Media Source** | `/mnt/pve/angel-data/media` | Sub-folder of NFS Master Share |
| **GPU Tech**     | `Intel QuickSync (QSV)`     | i5-4590 Hardware Engine        |

---

### 3. Step 1: Host & ID Preparation (Proxmox Node)

1. **Install Drivers:** `apt update && apt install -y intel-media-va-driver-non-free intel-gpu-tools`.
    
2. **Permission Setup:** Ensure `root:1000:1` exists in `/etc/subuid` and `/etc/subgid` (already done for Minecraft).
    
3. **Identify GIDs:** Run `getent group render | cut -d: -f3` (e.g., `108`).
    

---

### 4. Step 2: Create & Map the LXC

1. **Create CT:** Use `ubuntu-24.04-standard`. **Unprivileged: Checked**.
    
2. **Add Bind Mount:** In **Resources > Add > Mount Point**:
    
    - **Path:** `/mnt/pve/angel-data/media`
        
    - **Mount Point:** `/media`.
        
3. **Apply UID Mapping:** Edit `/etc/pve/lxc/102.conf` and paste the UID 1000 mapping block:
    
    ```
    lxc.idmap: u 0 100000 1000
    lxc.idmap: g 0 100000 1000
    lxc.idmap: u 1000 1000 1
    lxc.idmap: g 1000 1000 1
    lxc.idmap: u 1001 101001 64535
    lxc.idmap: g 1001 101001 64535
    ```
    
4. **GPU Passthrough:** In the Proxmox GUI for LXC 102, go to **Resources > Add > Device Passthrough** and add `/dev/dri/renderD128`.
    

---

### 5. Step 3: Software & Acceleration (Inside LXC 102)

1. **Install Jellyfin:** `curl https://repo.jellyfin.org/install-debuntu.sh | bash`.
    
2. **Assign User Permissions:**
    
    ```
    # Add Jellyfin to the render group (use the GID from Step 1 if it differs)
    usermod -aG render,video jellyfin
    # Ensure Jellyfin owns its config but can read the mapped media
    chown -R jellyfin:jellyfin /var/lib/jellyfin
    ```
    
3. **Enable QSV:** In the Jellyfin Web UI (**Dashboard > Playback**), select **Intel QuickSync**.