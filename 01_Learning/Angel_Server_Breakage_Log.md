
This document tracks real technical issues encountered during the deployment of the Angel Server home lab. Each incident is assigned an **INC identifier** for easier referencing across SOPs, commits, and troubleshooting documentation.

---

## Incident Index

```
INC-001 – ISO mount lock during VM provisioning  
INC-002 – Docker user permission issue  
INC-003 – Router firmware blocking port forwarding configuration  
INC-004 – ISP port 80 filtering preventing HTTP challenge  
INC-005 – Cloudflare proxy blocking Minecraft server
```

---

## Phase 1 – Infrastructure (Provisioning)

| **Date**       | **The "Break"**                                                         | **Root Cause**                                                                   | **The Fix**                                                                                       | **Video Segment**  |
| -------------- | ----------------------------------------------------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | ------------------ |
| **2026-02-15** | **INC-001 – Ubuntu VM "Failed unmounting cdrom.mount" on first reboot** | **ISO Lock:** The virtual CD/DVD drive was still holding the installation media. | **Post-Install Cleanup:** In Proxmox, set the VM Hardware CD/DVD drive to "Do not use any media." | Provisioning Phase |

---

## Phase 2 – Containerization (Docker & Portainer)

| **Date** | **The "Break"** | **Root Cause** | **The Fix** | **Video Segment** |
|----------|-----------------|---------------|-------------|-------------------|
| **2026-02-28** | **INC-002 – Docker commands required `sudo` despite Docker being installed** | **User Permission Issue:** The primary user had not yet been added to the `docker` group, meaning Docker commands required elevated privileges. | Added the user to the Docker group using `sudo usermod -aG docker $USER` and logged out/in to refresh group membership. | Containerization Phase |

---

## Phase 3 – Edge Networking (Nginx Proxy Manager & DNS)

| **Date** | **The "Break"** | **Root Cause** | **The Fix** | **Video Segment** |
|----------|-----------------|---------------|-------------|-------------------|
| **2026-03-01** | **INC-003 – Router Web UI blocking Port Forwarding settings** | **Firmware Lock:** Cox "Panoramic" routers disable local web UI management for ports once they sync with the ISP cloud. | **App-Web Hybrid:** Used the Web UI for DHCP Reservation (MAC binding) and the mobile app for the actual Port Forwarding rules. | Edge Networking Phase |
| **2026-03-01** | **INC-004 – Port 80 "Closed" despite active port forward** | **ISP Filtering:** Cox residential gateways often block Port 80, preventing standard Let's Encrypt HTTP-01 challenges. | **DNS-01 Challenge:** Migrated DNS management to Cloudflare and used an API token to verify domain ownership via DNS records instead of web traffic. | Edge Networking Phase |

---

## Phase 4 – Service Deployment (Minecraft Server)

| **Date** | **The "Break"** | **Root Cause** | **The Fix** | **Video Segment** |
|----------|-----------------|---------------|-------------|-------------------|
| **2026-03-10** | **INC-005 – Minecraft server unreachable despite container running** | **Cloudflare Proxy Conflict:** The root DNS record (`angelserver.live`) was set to **Proxied (orange cloud)**. Cloudflare only proxies HTTP/HTTPS traffic and does not support UDP game traffic such as Minecraft Bedrock (`19132/udp`). | **DNS Only Mode:** Disabled Cloudflare proxy for the `angelserver.live` A record by switching it to **DNS Only (grey cloud)**, allowing direct connections to the home server IP. | Game Server Deployment |

---

# Lessons Learned

The following observations were extracted from the incidents above and represent general operational lessons for future deployments.

---

## Infrastructure Lessons

- **ISO Media Priority:** Hypervisors often attempt to boot from mounted installation media before local disks. Always unmount ISO files after the first successful OS installation to prevent boot issues.  
- **False Boot Errors:** Some Linux shutdown or reboot errors can simply indicate that virtual hardware is still attached. Checking the hypervisor configuration often resolves these messages quickly.

---

## Containerization Lessons

- **Docker Permission Gotcha:** After installing Docker, commands may still require `sudo` until the user is added to the `docker` group and the session is refreshed. Always verify group membership before troubleshooting Docker itself.

---

## Networking Lessons

- **DHCP vs Static Visibility:** ISP routers may not properly display devices using manually assigned static IP addresses. Always confirm connectivity via SSH before assuming a device is offline.

- **The "Ping Wake-Up":** If a reserved device does not appear in a router’s management interface, sending a `ping` to the gateway (`192.168.0.1`) can force the router to recognize the device.

- **Residential Port Filtering:** Never assume Port 80 is open simply because a port forward exists. Residential ISPs frequently block Port 80 at the network level.

- **DNS-01 Advantage:** DNS-01 challenges allow SSL certificate issuance without exposing web ports, making them ideal for home lab environments.

- **Cloudflare Proxy Limitations:** Cloudflare only proxies HTTP/HTTPS traffic. Services relying on UDP or non-web protocols (such as Minecraft servers) must use **DNS-only mode** for direct client connections.