### 1. Purpose

This procedure establishes a secure, encrypted Zero-Tier Mesh VPN using **Tailscale**. This allows for full administrative access to the Proxmox Web UI, SSH, and Portainer from external networks without exposing the lab to the public internet via traditional port forwarding, bypassing residential ISP restrictions.

### 2. Lab Environment Reference

The following values are used as the baseline for the **Angel Server** environment.

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**Primary Node**|`angel-node-01`|Ubuntu VM / Docker Host|
|**Node IP**|`192.168.0.151`|Static Management IP|
|**Hypervisor**|`Proxmox VE`|Physical OptiPlex Host|
|**External Access**|Tailnet|Private encrypted network|

---

### 3. Installing Tailscale on the Ubuntu Node

Since this node houses your Docker Engine and Portainer, it is the primary target for remote management.

**Step 1: Install Tailscale using the official script**

```
curl -fsSL https://tailscale.com/install.sh | sh
```

**Step 2: Authenticate the node**

Run the login command and follow the URL provided in the terminal to link the server to your Tailscale account.

```
sudo tailscale up
```

---

### 4. Enabling Access to Proxmox (The Hypervisor)

To manage the "bare metal" host from your job, you should install Tailscale directly on the Proxmox host.

1. **SSH into Proxmox** (192.168.0.150) or use the physical console.
    
2. **Run the same install script** used in Section 3.
    
3. **Authenticate** by running `tailscale up`.
    
4. **Verification:** You should now see both `proxmox` and `angel-node-01` in your Tailscale Admin Console with new **100.x.x.x** IP addresses.
    

---

### 5. Remote Management Setup

To work from your IT lab assistant desk:

1. **Install Tailscale** on your remote laptop or mobile device.
    
2. **Log in** with your credentials.
    
3. **Access via Tailscale IP:** Instead of using the local `192.168.0.151` address, use the Tailscale assigned IP to reach your services.
    

---

### 6. Post-SOP Verification

- **Connectivity Test:** Disconnect your remote device from the local network and attempt to `ping` the Tailscale IP of your server node.
    
- **Web UI Access:** Confirm you can log into **Portainer (:9443)** and **Proxmox (:8006)** using the Tailscale IPs.
    
- **Security Check:** Verify that you can access these services without needing to open additional ports on your Cox Gateway.