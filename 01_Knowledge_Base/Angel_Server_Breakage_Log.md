
This document tracks real technical issues encountered during the deployment of the Angel Server home lab. Each incident is assigned an **INC identifier** for easier referencing across SOPs, commits, and troubleshooting documentation.

---

## Incident Index

```
INC-001 – ISO mount lock during VM provisioning  
INC-002 – Docker user permission issue  
INC-003 – Router firmware blocking port forwarding configuration  
INC-004 – ISP port 80 filtering preventing HTTP challenge  
INC-005 – Cloudflare proxy blocking Minecraft server
INC-006 – Tailnet "Unpaid" error due to organizational domain conflict
INC-007 – Windows PowerShell missing native 'ssh-copy-id' utility
INC-008 – Rclone command "not recognized" in PowerShell
INC-009 – Windows terminal closed during Rclone authorization

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
## Phase 5 – Remote Access & Hardening (Episode 04)

| **Date**       | **The "Break"**                                                     | **Root Cause**                                                                                                                                       | **The Fix**                                                                                                            | **Video Segment**   |
| -------------- | ------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------- |
| **2026-03-11** | **INC-006 – Tailscale "Your account is unpaid" / User limit error** | **Domain Conflict:** Using a VCCS student email caused Tailscale to attempt to join a shared organizational tailnet that had reached its seat limit. | **Personal Pivot:** Migrated to a personal email/GitHub account to establish a private "Personal" tailnet.             | Remote Access Phase |
| 2026-03-11     | **INC-007 – `ssh-copy-id` missing on Windows**                      | **Utility Parity:** The standard Windows OpenSSH client does not include the `ssh-copy-id` script found in Linux/macOS.                              | **Manual Pipe:** Used a PowerShell one-liner to manually append the public key to the server's `authorized_keys` file. | Remote Access Phase |
|                |                                                                     |                                                                                                                                                      |                                                                                                                        |                     |
___
## Phase 6 – Disaster Recovery & Automation (SOP-15/16)

| **Date**       | **The "Break"**                                                   | **Root Cause**                                                                                                                                                              | **The Fix**                                                                                                                          | **Video Segment** |
| -------------- | ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- |
| **2026-03-22** | **INC-008 – Rclone command "not recognized" in PowerShell**       | **Nested Folder Structure:** The Rclone zip extracts into a folder containing _another_ folder of the same name. The `.exe` was one level deeper than the active directory. | **Directory Navigation:** Used `cd` to enter the inner versioned folder before running the `./rclone` command.                       | Disaster Recovery |
| **2026-03-22** | **INC-009 – Windows terminal closed during Rclone authorization** | **OS Resource Spike/Crash:** The browser "Success" redirect triggered a desktop environment refresh or minor crash on the Windows host, closing active SSH sessions.        | **Session Persistence:** Re-established SSH to the OptiPlex (which remained running). Re-ran the Rclone config to get a fresh token. | Disaster Recovery |

___
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
___
### **Remote Access & Identity Lessons**

- **SaaS Domain Multi-Tenancy**: Many SaaS platforms (like Tailscale) use the email domain (e.g., `@email.vccs.edu`) to automatically group users into "Organizations."

- **Personal vs. Institutional Accounts**: For home lab infrastructure, personal identity providers (GitHub, personal Gmail) are preferred over institutional accounts to avoid seat-limit conflicts and ensure long-term administrative control.

- **Force Re-authentication**: When switching between Tailscale accounts on a Linux host, use the `--force-reauth` flag to ensure the node's key is correctly associated with the new tailnet.

- **Utility Parity across OS:** Do not assume standard Linux/macOS scripts (like `ssh-copy-id`) exist on Windows PowerShell; always verify your toolchain on all admin devices.

- **Manual Identity Deployment:** In the absence of automated scripts, a PowerShell pipe (`type | ssh`) is a reliable way to deploy public keys while maintaining strict file permissions (`chmod 600`) on the host.

- **Redundant Access Points:** Always verify that at least two independent devices (e.g., MacBook via Tailscale and Main PC via LAN) have working SSH keys before disabling password authentication to prevent a permanent lockout.
___
## Disaster Recovery & Tooling Lessons

- **The "Double-Folder" Zip Trap:** Many Windows zip utilities create a top-level container folder. Always verify the location of the `.exe` with `ls` or `dir` before assuming a path is correct.

- **Token One-Time Use:** Authentication tokens provided via browser redirects are often one-time use. If the terminal session closes before the token is pasted, the process must be restarted to generate a new valid handshake.

- **Snapshot vs. Backup Utility:** Snapshots are for "Undo" (fast, local); Backups are for "Disaster" (slow, offsite). Keeping one stable snapshot is an efficient middle ground for active development.

- **Data Exclusion in VM Images:** To prevent ballooning backup sizes, always exclude bulk data drives (HDDs) from Proxmox VM backups if that data is already being synced via other methods (Rclone).