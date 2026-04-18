## 1. Purpose

This procedure migrates the Minecraft Bedrock server from a VM to a standalone **Unprivileged Proxmox LXC**. This setup optimizes resource usage and utilizes **NFS-based shared storage** with **UID mapping** to allow a sandboxed container to read/write to the central HDD.

## 2. Lab Environment Reference

| **Attribute**  | **Value**                 | **Context**                    |
| -------------- | ------------------------- | ------------------------------ |
| **New LXC ID** | `101`                     | Proxmox Container ID           |
| **New LXC IP** | `192.168.0.160`           | Static IP via DHCP Reservation |
| **NFS Source** | `192.168.0.151:/mnt/data` | Master Export from Ubuntu VM   |
| **Port**       | `19132 (UDP)`             | Bedrock Default                |

---
## 3. Step 1: Storage Preparation (Ubuntu VM .151)

1. **Stop Old Service:** Run `docker compose down` in the old VM directory to prevent data corruption.
    
2. **Migrate Data:** Use `rsync -avzP` to move world files to the HDD path: `/mnt/data/minecraft-data/`.
    
3. **Configure Master Export:** Add this line to `/etc/exports`:
    
    `/mnt/data 192.168.0.150(rw,sync,no_subtree_check,all_squash,anonuid=1000,anongid=1000)`.
    
4. **Apply Changes:** Run `sudo exportfs -ra`.
    

---

## 4. Step 2: Bridge the Storage (Proxmox Host)

1. **Mount NFS:** Add the following to `/etc/fstab` on the Proxmox Host:
    
    `192.168.0.151:/mnt/data /mnt/pve/angel-data nfs defaults,_netdev,soft,nfsvers=3 0 0`.
    
2. **Verify:** Run `mount -a` and ensure files are visible in `/mnt/pve/angel-data/minecraft-data`.
    

---

## 5. Step 3: Create & Map the LXC

1. **Create CT:** Use `ubuntu-24.04-standard`. Ensure **Unprivileged** is checked.
    
2. **Add Bind Mount:** In **LXC 101 > Resources > Add > Mount Point**:
    
    - **Path:** `/mnt/pve/angel-data/minecraft-data`
        
    - **Mount Point:** `/data`.
        
3. **Configure ID Mapping:** Edit `/etc/pve/lxc/101.conf` on the Host to map UID 1000:
    
    
    
    ````
    lxc.idmap: u 0 100000 1000
    lxc.idmap: g 0 100000 1000
    lxc.idmap: u 1000 1000 1
    lxc.idmap: g 1000 1000 1
    lxc.idmap: u 1001 101001 64535
    lxc.idmap: g 1001 101001 64535
    ```.
    ````
    
4. **Grant Host Permissions:** Add `root:1000:1` to `/etc/subuid` and `/etc/subgid`.
    

---

## 6. Step 4: Service Deployment (Inside LXC 101)

1. **Set Network to DHCP:** In Proxmox, set the LXC network to DHCP so the router recognizes the MAC reservation.
    
2. **Install Docker:** `curl -fsSL https://get.docker.com | sh`.
    
3. **Deploy:** Create `docker-compose.yml` in `/opt/minecraft`:
    
    ````
    services:
      mc:
        image: itzg/minecraft-bedrock-server
        container_name: minecraft-bedrock
        environment:
          EULA: "TRUE"
        ports:
          - 19132:19132/udp
        volumes:
          - /data:/data
        restart: unless-stopped
    ```.
    
    ````
    

---

## 7. Step 5: Network & Edge Config

1. **Static Reservation:** Use the LXC's MAC address to reserve `192.168.0.160` in the Cox Router.
    
2. **Port Forwarding:** Point UDP port **19132** directly to **192.168.0.160**.
    
3. **DDNS:** Ensure the Ubuntu VM (.151) is still updating `angelserver.live`.
    

---

## 8. Post-SOP Verification

- **Internal Check:** Run `ls -ln /data` inside the LXC; files must show owner **1000**.
    
- **External Check:** Connect to `angelserver.live` from an external network.