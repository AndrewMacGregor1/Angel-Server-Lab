## **1. Purpose**

This procedure outlines the bare-metal installation of Proxmox VE 9.1 on the N100 Mini PC and the initial OS-level hardening required before it joins the Angel Server cluster.

## **2. Lab Environment Reference**

| **Attribute**     | **Value**                | **Context**            |
| ----------------- | ------------------------ | ---------------------- |
| **Hardware**      | MOREFINE M9 (Intel N100) | Secondary Cluster Node |
| **Primary Disk**  | 500GB NVMe SSD           | OS & Local VM Storage  |
| **Hostname**      | `angel-node-02`          | DNS/Cluster Identity   |
| **Management IP** | `192.168.0.152/24`       | Static Assignment      |

---
## **3. Step 1: Proxmox Installation**

1. **Boot Media:** Flash the Proxmox VE 9.1 ISO to a USB drive and boot the N100.
    
2. **Target Disk:** Select the **500GB NVMe**. Use `ext4` or `ZFS (RAID0)` depending on your preference for snapshots.
    
3. **Network Configuration:**
    
    - **Management Interface:** Select the 2.5G Ethernet port.
        
    - **Hostname:** `angel-node-02.local`
        
    - **IP Address:** `192.168.0.152`
        
    - **Gateway:** `192.168.0.1 prime` (Cox Gateway).
        

---

## **4. Step 2: Post-Install Hardening (Host Level)**

Just like the Ubuntu VM, the Proxmox host itself needs to be updated and secured.

1. **Disable Subscription Nag:** (Optional but helpful for home labs)
    
    
    ```
   sed -i "s/^data.status === 'Active'/true/" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js
    ```
    
2. **Update Repository:** Switch to the "No-Subscription" list to get updates without a license.
    
3. **Install Essential Tools:**
    
    ```
    apt update && apt install -y htop micro screen intel-gpu-tools
    ```
    
    - _Note: `intel-gpu-tools` is critical here to monitor the N100's QuickSync engine later._
        

---

### **5. Step 3: Establishing the Communication Bridge**

For the N100 to work seamlessly in the cluster, the **QEMU Guest Agent** and **SSH keys** must be ready.

1. **Enable SSH:** Ensure your `drew` user or root can SSH into the node.
    
2. **Verify Link:** Confirm you can ping the OptiPlex (`192.168.0.151`) from the N100 terminal.
    

---

### **6. Post-SOP Verification**

- **Web UI Access:** Navigate to `https://192.168.0.152:8006` and log in successfully.
    
- **Storage Check:** Verify the `local` and `local-lvm` (or `local-zfs`) storages show the correct ~500GB capacity.
    
- **GPU Visibility:** Run `ls /dev/dri`. You should see `card0` and `renderD128`. If these aren't there, the N100 iGPU isn't being recognized by the kernel.
    
- **Network Integrity:** Run `ethtool enp1s0` (or your interface name) to confirm it has negotiated at 1000Mb/s (or 2500Mb/s if your switch supported it).