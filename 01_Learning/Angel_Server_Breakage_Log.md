## Resolved Issues

This log tracks actual technical hurdles encountered during the deployment of the Angel Server lab.

| **Date**       | **The "Break"**                                                | **Root Cause**                                                                                                                                  | **The Fix**                                                                                                                                          | **Video Segment**      |
| -------------- | -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------- |
| **2026-02-15** | Ubuntu VM "Failed unmounting cdrom.mount" on first reboot      | **ISO Lock:** The virtual CD/DVD drive was still holding the installation media.                                                                | **Post-Install Cleanup:** In Proxmox, set the VM Hardware CD/DVD drive to "Do not use any media."                                                    | Provisioning Phase     |
| **2026-02-28** | Docker commands required `sudo` despite Docker being installed | **User Permission Issue:** The primary user had not yet been added to the `docker` group, meaning Docker commands required elevated privileges. | Added the user to the Docker group using `sudo usermod -aG docker $USER` and logged out/in to refresh group membership.                              | Containerization Phase |
| **2026-03-01** | Router Web UI blocking Port Forwarding settings.               | **Firmware Lock:** Cox "Panoramic" routers disable local web UI management for ports once they sync with the ISP cloud.                         | **App-Web Hybrid:** Used the Web UI for DHCP Reservation (MAC binding) and the mobile app for the actual Port Forwarding rules.                      | Edge Networking Phase  |
| **2026-03-01** | Port 80 "Closed" despite active port forward.                  | **ISP Filtering:** Cox residential gateways often block Port 80, preventing standard Let's Encrypt HTTP-01 challenges.                          | **DNS-01 Challenge:** Migrated DNS management to Cloudflare and used an API Token to verify domain ownership via DNS records instead of web traffic. | Edge Networking Phase  |


## Lessons Learned

- **Hypervisor Priority:** Proxmox will attempt to boot from the "CD/DVD" drive first if an ISO is still mounted. Always unmount installation media immediately after the first successful install to ensure a smooth transition to the local disk.
    
- **Don't Panic on "Failed" Messages:** Some Linux error screens during reboot are simply the system waiting for the virtual media to be "ejected." Checking the hypervisor status first often reveals a simple fix.
	
- **Docker Permissions Gotcha:** After installing Docker, commands may still require sudo until the user is added to the docker group and the session is refreshed. Always verify group membership before troubleshooting Docker itself.
	
- **DHCP vs. Static Clash:** ISP routers (like the Panoramic) can be "blind" to devices that use internal static IPs. Always verify connectivity via SSH before trusting the router's "Online/Offline" status list.
    
- **The "Ping" Wake-Up:** If a device with a reserved IP won't show up in the ISP app for port forwarding, forcing a `ping` from the server to the gateway (`192.168.0.1`) is the fastest way to force the router to recognize the device.
    
- **Residential Port Filtering:** Never assume Port 80 is open just because a port forward is active. Residential ISPs frequently block Port 80 at the headend to discourage web hosting.
    
- **DNS-01 Superiority:** The DNS-01 challenge is the ultimate "cheat code" for home labs. It allows you to get valid SSL certificates and wildcard domains without ever having to open a single web port on your home router.