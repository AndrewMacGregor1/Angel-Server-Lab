## 1. Purpose

This procedure establishes a dedicated **Ubuntu VM** for media ingestion. By utilizing **Gluetun** as a sidecar container, all torrent traffic is forced through an encrypted VPN tunnel. If the VPN connection drops, the "Kill-Switch" logic instantly severs the internet connection to prevent your home IP from being exposed.

## 2. Lab Environment Reference

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**New VM ID**|`103`|Proxmox Virtual Machine|
|**New VM IP**|`192.168.0.162`|Static IP for Ingest|
|**VPN Protocol**|`WireGuard`|Modern, high-speed standard|
|**Downloads**|`/mnt/data/torrents`|Shared HDD path|

---

## 3. Step 1: Create the Ingest VM (Proxmox)

1. **Create VM:** Follow **SOP-04** to provision a minimal Ubuntu 24.04 VM.
    
2. **Resources:** 2 Cores and 2GB RAM is plenty for a downloader.
    
3. **Mount Point:** Attach the HDD via **Virtio-FS** (just like in SOP-13).
    
    - **Tag:** `angel_data_bridge`
        
    - **Mount inside VM:** `/mnt/data`
        

---

## 4. Step 2: Configure Gluetun & qBittorrent

We will use a single `docker-compose.yml` file where qBittorrent "piggybacks" on Gluetun's network.

**Create the Directory:**

```
mkdir -p /opt/docker/ingest && cd /opt/docker/ingest
nano docker-compose.yml
```

**Paste the Stack Configuration:**

_(Note: Replace the WireGuard placeholders with the keys from your VPN provider's dashboard)_.

```
services:
  gluetun:
    image: qmcgaw/gluetun:latest
    container_name: gluetun
    cap_add:
      - NET_ADMIN
    devices:
      - /dev/net/tun:/dev/net/tun
    ports:
      - 8080:8080 # qBittorrent Web UI
    environment:
      - VPN_SERVICE_PROVIDER=protonvpn
      - VPN_TYPE=wireguard
      - WIREGUARD_PRIVATE_KEY=YOUR_PRIVATE_KEY
      - WIREGUARD_ADDRESSES=10.2.0.2/32
      - SERVER_COUNTRIES=Netherlands
    restart: unless-stopped

  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:latest
    container_name: qbittorrent
    network_mode: "service:gluetun"
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
      - WEBUI_PORT=8080
    volumes:
      - /opt/docker/ingest/config:/config
      - /mnt/data/torrents:/downloads
    depends_on:
      gluetun:
        condition: service_healthy
    restart: unless-stopped
```

---

## 5. Step 3: Deployment & The "IP Test"

1. **Launch:** `docker compose up -d`
    
2. **Verify the Tunnel:** Run this command to see what IP the **qbittorrent** container is using:
    
    
    ```
      docker exec qbittorrent curl ifconfig.me
    ```
    
    _If the result is your Home IP, **STOP**. If it’s a VPN IP (e.g., in the Netherlands), you are secure._
    

---

## 6. Step 4: Final Permissions

Ensure the qBittorrent user can write to your HDD folder:

```
sudo chown -R 1000:1000 /mnt/data/torrents
```