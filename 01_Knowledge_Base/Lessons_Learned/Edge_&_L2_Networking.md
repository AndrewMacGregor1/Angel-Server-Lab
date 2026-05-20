# Lessons Learned: Edge & L2 Networking

This document outlines everything we learned while fighting residential ISP locks, configuring managed switches, handling external domain routing, and getting standalone containers to play nice with the local router.

## INC-003 & INC-004: Bypassing the ISP Gateway Wall

### The Symptom

While setting up Nginx Proxy Manager to host services externally, the Cox Panoramic router web UI completely blocked access to the port forwarding menus. Even after using their clunky mobile app to force a Port 80 forward, Let's Encrypt SSL certificates completely failed to generate.

### The Core Problem

Residential ISP gear is intentionally locked down. Cox disables local browser management for port forwarding once the router syncs with their cloud platform. Worse, residential ISPs block inbound traffic on Port 80 at the network level to stop people from running web servers from home. This completely kills standard Let's Encrypt HTTP-01 challenges, which _require_ an open path over Port 80 to verify you own the domain.

### The Takeaway

Stop fighting the ISP's routing engine and bypass it entirely.

1. **Bridge Mode:** Buy a dedicated, enterprise-grade router (like a Ubiquiti Cloud Gateway) and flip the Cox box into **Bridge Mode**. This strips the Cox box down to a basic modem, gives your new router a public WAN IP, and removes their firmware restrictions from your life.
    
2. **DNS-01 Challenges:** Switch your domain's nameservers to Cloudflare and use a **DNS-01 challenge** inside Nginx Proxy Manager instead of an HTTP challenge. By creating a secure Cloudflare API token, NPM can verify you own the domain by placing a temporary TXT record directly into your DNS dashboard. This lets you generate wildcard SSL certificates (`*.angelserver.live`) without ever opening Port 80 to the public internet.
    

## INC-005: The Cloudflare Proxy Game Server Block

### The Symptom

The Minecraft Bedrock server container was up and running, and the local port forward was active, but friends trying to connect using the root domain `angelserver.live` kept hitting a connection timeout error.

### The Core Problem

When you add a DNS record to Cloudflare, the default setting is **Proxied (Orange Cloud)**. This is amazing for web servers because it hides your home IP behind Cloudflare's network, protecting you from DDoS attacks. However, Cloudflare's standard proxy service _only_ handles web traffic (HTTP/HTTPS). Minecraft Bedrock runs entirely on the **UDP** protocol via port `19132`. Because Cloudflare's web proxy doesn't know how to route raw UDP packets, it drops them on the floor.

### The Takeaway

Any DNS records used for non-web services (like game servers, custom voice servers, or SSH entry points) must bypass the Cloudflare web proxy. Switch that specific DNS record from Proxied to **DNS Only (Grey Cloud)** in your Cloudflare dashboard. This tells Cloudflare to act as a normal phonebook, resolving the domain directly to your home's public IP so game clients can establish a direct UDP handshake.

## INC-013: The Silent LXC ARP Ghosting Trick

### The Symptom

After deploying the Minecraft server inside its own dedicated, unprivileged Proxmox LXC container, the container could talk to the internet, but it was completely missing from the network client list, making it impossible to map a port forwarding rule to it.

### The Core Problem

Modern routers rely heavily on the **Address Resolution Protocol (ARP)** table and DHCP requests to discover what devices are plugged into the network. If you provision an LXC container and manually assign it a static IP right out of the gate, the container boots up quietly. Because it never explicitly broadcasts a standard DHCP lease request to the gateway, the router has no idea the device exists on that IP address, essentially turning the container into a network "ghost".

### The Takeaway

If a static host is invisible to your gateway, you have to force a network wake-up call.

1. Temporarily flip the container's network interface configuration in Proxmox to **DHCP**. This forces the container to broadcast its MAC address to the router to ask for an IP, instantly registering it in the router's client database.
    
2. Once the router logs the MAC address, lock it down using a **DHCP Reservation (Fixed IP)** inside your router settings rather than setting a hard static IP on the client OS itself. This keeps your lab organized while ensuring the gateway always recognizes the host.
    

## Long-Term Layer 2 Guidelines

1. **Physical First:** "If the cable is bad, the config doesn't matter." Always verify link negotiation speeds (e.g., confirming a solid 1Gbps link on your 100ft vertical run) before spending hours debugging software configurations or firewall tables.
    
2. **Trunking vs Access Ports:** When running multiple subnets across your infrastructure—like your management network and your segmented **VLAN 10 Home Wireless network**—any port connecting your routers, managed switches, and access points must be configured as an **802.1Q Trunk Port**. If you leave a port set as a standard access port, it will strip away the VLAN tags, dropping your isolated wireless traffic entirely.