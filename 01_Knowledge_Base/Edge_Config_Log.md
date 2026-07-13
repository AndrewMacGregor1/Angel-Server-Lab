## 1. Our Network Baseline

| **Attribute** | **Value** | **Context** |
| :--- | :--- | :--- |
| **Gateway IP** | `192.168.20.1` | Our main UniFi Cloud Gateway Ultra |
| **DNS Manager** | Cloudflare | Manages our domain names and gives us SSL certificates |
| **Domain Name** | `angelserver.live` | Our primary web address |

## 2. Active Port Forwarding Rules

This table tracks how our UniFi gateway passes traffic coming from the outside internet straight to our servers on VLAN 20. 

*(Note: We updated these destination IPs to match our new subnet layout!)*

| **Service Name** | **Outside Port** | **Inside Server IP** | **Inside Port** | **Protocol** |
| :--- | :--- | :--- | :--- | :--- |
| **HTTPS (Nginx Proxy Manager)** | 443 | `192.168.20.5` | 443 | TCP (VM 100) |
| **Minecraft Server** | 19132 | `192.168.20.3` | 19132 | UDP (LXC 101) |

## 3. Remote Access & Keeping Things Locked Down

We don't expose our administrative dashboards to the public internet. Instead, we use Tailscale to connect securely when we are away from home.

| **Dashboard** | **How We Access It** | **Security Check** |
| :--- | :--- | :--- |
| **Proxmox Web UI** | Tailscale Only (`100.x.x.x:8006`) | Completely blocked from the outside internet |
| **Nginx Proxy Admin UI** | Tailscale Only (`192.168.20.5:81`) | Port 81 is closed on our main router |
| **SSH Terminal Access** | Tailscale + SSH Keys Only | Password login is completely disabled for security |

## 4. Quick Edge Notes

- **Bypassing Port 80 Blocks:** Our home ISP blocks port 80, so we use Cloudflare's DNS API challenge to fetch valid SSL certificates without opening web ports to the public.
- **Cloudflare Proxying:** We keep the "Orange Cloud" turned on for normal web services to hide our home IP address, but we use a "Grey Cloud" (DNS-only) record for Minecraft since Cloudflare doesn't proxy raw UDP game traffic.
- **VPN Wi-Fi:** We keep a dedicated Wi-Fi network (VLAN 10) running for any home devices that we want permanently routed through a WireGuard VPN tunnel.