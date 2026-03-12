## 1. Lab Environment Reference

These values represent the current baseline for the server's network presence.

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**Gateway IP**|`192.168.0.1`|Cox Panoramic Router Management|
|**Target Node IP**|`192.168.0.151`|Primary Ubuntu VM (Angel-node-01)|
|**DNS Provider**|Cloudflare|DNS-01 Challenge & Proxy Provider|
|**Active Domain**|`angelserver.live`|Primary FQDN|

---

## 2. Port Forwarding Rules (Router Level)

These rules dictate which "doors" are open on the Cox gateway to allow traffic to reach the Optiplex.

| **Service**   | **External Port** | **Internal Port** | **Protocol** | **Status**                          |
| ------------- | ----------------- | ----------------- | ------------ | ----------------------------------- |
| **HTTP**      | 80                | 80                | TCP          | **Closed** (ISP Blocked/Not Needed) |
| **HTTPS**     | 443               | 443               | TCP          | **Open** (Production Traffic)       |
| **NPM Admin** | 81                | 81                | TCP          | **Open** (Remote Management)        |
| **Minecraft** | 25565             | 25565             | TCP/UDP      | **Open** (Game Server)              |

---
## 3. SSL & DNS Configuration

**Security Protocol:** Sensitive API tokens are managed via Cloudflare and injected into Nginx Proxy Manager (NPM) to bypass Port 80 requirements.

**Step 1: DNS-01 Challenge Verification**

- Instead of the HTTP-01 challenge, NPM uses a Cloudflare API Token to verify domain ownership.
    
- This allows for the generation of Wildcard Certificates (`*.angelserver.live`) while Port 80 remains closed.
    

**Step 2: Dynamic IP Management**

- The home public IP is dynamic; synchronization is handled via a Cloudflare API integration.
    
- **Config Path:** `/opt/docker/npm/config/cloudflare.ini` (Reference for API token storage).
    
---
## 4. Maintenance & Verification

The following checks ensure continued external access after network interruptions:

1. **Cloudflare Proxy Status:** Ensure the "Orange Cloud" is active to hide the home public IP.
    
2. **Port Verification:** Periodically use an external probe to verify Port 443 and 25565 are responding.
    
3. **SSL Renewal:** Confirm that NPM successfully renews certificates 30 days before expiration via the DNS challenge.
___
## 5. Mesh VPN Configuration (Tailnet)

|**Device Name**|**Tailscale IP**|**Role**|**Status**|
|---|---|---|---|
|**proxmox-host**|`100.x.x.x`|Bare-Metal Management|**Active**|
|**angel-node-01**|`100.y.y.y`|Ubuntu VM / Docker Host|**Active**|
|**work-laptop**|`100.z.z.z`|Remote Admin Access|**Active**|

---
## 6. Management Access Hardening

|**Service**|**Public Port**|**Protocol**|**Access Policy**|**Verification**|
|---|---|---|---|---|
|**SSH**|22|TCP|**Tailnet Only** / Key-Auth|Password Auth Disabled|
|**NPM Admin**|81|TCP|**Tailnet Only**|IP Restricted / Mesh Only|
|**Proxmox UI**|8006|TCP|**Tailnet Only**|
