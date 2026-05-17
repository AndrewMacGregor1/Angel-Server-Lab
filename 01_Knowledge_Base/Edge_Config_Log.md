## 1. Environment Baseline

| **Attribute**    | **Value**          | **Context**                  |
| ---------------- | ------------------ | ---------------------------- |
| **Gateway IP**   | `192.168.0.1`      | Ubiquiti Cloud Gateway Ultra |
| **DNS Provider** | Cloudflare         | DNS-01 Auth / Wildcard SSL   |
| **FQDN**         | `angelserver.live` | Primary Domain               |

## 2. Active Port Forwarding

Traffic entering via the Ubiquiti Gateway.

| **Service**     | **Ext. Port** | **Int. IP**     | **Int. Port** | **Protocol**  |
| --------------- | ------------- | --------------- | ------------- | ------------- |
| **HTTPS (NPM)** | 443           | `192.168.0.151` | 443           | TCP           |
| **Minecraft**   | 19132         | `192.168.0.89`  | 19132         | UDP (LXC 101) |

## 3. Remote Access & Hardening

|**Target**|**Access Rule**|**Verification**|
|---|---|---|
|**Proxmox UI**|Tailscale Only (`100.x.x.x:8006`)|No WAN Access|
|**NPM Admin**|Tailscale Only (`192.168.0.151:81`)|Port 81 Closed on Router|
|**SSH**|Tailscale + Key-Auth Only|Passwords Disabled|

## 4. Edge Service Summary

- **SSL Challenge:** DNS-01 via Cloudflare (Bypasses Port 80 blocking).
    
- **Proxy Status:** "Orange Cloud" active for web services; "Grey Cloud" for Minecraft UDP.
    
- **VLAN 10:** Dedicated SSID for Wireguard-tunneled traffic.