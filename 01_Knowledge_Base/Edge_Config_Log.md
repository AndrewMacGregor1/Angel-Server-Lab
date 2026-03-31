## 1. Lab Environment Reference

These values represent the current baseline for the server's network presence.

| **Attribute**     | **Value**          | **Context**                           |
| ----------------- | ------------------ | ------------------------------------- |
| **Gateway IP**    | `192.168.0.1`      | Cox Panoramic Router Management       |
| **Primary Node**  | `192.168.0.151`    | Ubuntu VM (NFS Server / NPM / Immich) |
| **DNS Provider**  | Cloudflare         | DNS-01 Challenge & Proxy Provider     |
| **Active Domain** | `angelserver.live` | Primary FQDN                          |

---

## 2. Port Forwarding Rules (Router Level)

These rules dictate which "doors" are open on the Cox gateway to allow traffic to reach the Proxmox environment.

|**Service**|**External Port**|**Internal Port**|**Target IP**|**Protocol**|**Status**|
|---|---|---|---|---|---|
|**HTTPS**|443|443|`192.168.0.151`|TCP|**Open** (NPM Traffic)|
|**NPM Admin**|81|81|`192.168.0.151`|TCP|**Open** (Management)|
|**Minecraft**|**19132**|**19132**|**`192.168.0.160`**|**UDP**|**Open (LXC 101)**|

---

## 3. SSL & DNS Configuration

**Security Protocol:** Sensitive API tokens are managed via Cloudflare and injected into Nginx Proxy Manager (NPM) to bypass Port 80 requirements.

**Step 1: DNS-01 Challenge Verification**

- NPM uses a Cloudflare API Token to verify domain ownership instead of the HTTP-01 challenge.
    
- This allows for the generation of Wildcard Certificates (`*.angelserver.live`) while Port 80 remains closed.
    

**Step 2: Dynamic IP Management**

- The home public IP is dynamic; synchronization is handled via a Cloudflare API integration.
    
- **Config Path:** `/opt/docker/npm/config/cloudflare.ini` (Reference for API token storage).
    

---

## 4. Maintenance & Verification

The following checks ensure continued external access after network interruptions:

1. **Cloudflare Proxy Status:** Ensure the "Orange Cloud" is active to hide the home public IP.
    
2. **Port Verification:** Periodically use an external probe to verify Port 443 and 19132 are responding.
    
3. **Internal Sync:** Verify the **NFS Master Share** is active on `.151` so all service data remains available to the LXCs.
    

---

## 5. Mesh VPN Configuration (Tailnet)

|**Device Name**|**Tailscale IP**|**Role**|**Status**|
|---|---|---|---|
|**proxmox-host**|`100.x.x.x`|Bare-Metal Management|**Active**|
|**angel-node-01**|`100.y.y.y`|**NFS Master Storage / NPM**|**Active**|
|**minecraft-lxc**|`100.a.a.a`|Dedicated Game Server|**Active**|
|**jellyfin-lxc**|`100.b.b.b`|Media Server (QSV)|**Active**|
|**ingest-vm**|`100.c.c.c`|VPN Protected Ingest|**Active**|

---

## 6. Management Access Hardening

| **Service**    | **Internal IP** | **Protocol** | **Access Policy**           | **Verification**          |
| -------------- | --------------- | ------------ | --------------------------- | ------------------------- |
| **SSH**        | `ALL`           | TCP          | **Tailnet Only** / Key-Auth | Password Auth Disabled    |
| **NPM Admin**  | `192.168.0.151` | TCP          | **Tailnet Only**            | IP Restricted / Mesh Only |
| **Proxmox UI** | `192.168.0.150` | TCP          | **Tailnet Only**            |                           |