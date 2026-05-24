## 1. Purpose

This procedure details the deployment and configuration of the full-stack automation architecture within the dedicated Ingest VM. By decoupling container lifetimes from persistent data and implementing a unified path root (`/data`), this layout forces atomic hardlinks across the hard disk drive (HDD) filesystem partition. This yields zero-space, instant file organization: qBittorrent seeds permanently from a raw download directory while Radarr and Sonarr maintain a beautifully structured, metadata-rich media library for Jellyfin.

## 2. Lab Environment Reference

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**Ingest Host VM**|`angel-node-01` (`192.168.0.151`)|Core Download/Automation Engine|
|**Unified Container Path**|`/data`|Top-level pointer mapped to host mount|
|**Physical Storage Root**|`/mnt/data/jellymain`|Core storage path on Host HDD|
|**VPN Routing Gateway**|`gluetun` (WireGuard)|Network namespace provider for qBit|

## 3. Step 1: Establish Core Physical Directories

Before modifying the composition configurations, the explicit directory architecture must match on the underlying Linux host filesystem to enable seamless hardlinking and prevent software fallback copies.

Log into `angel-node-01` via SSH and initialize the targeted folder partitions:

```
# Create the raw torrenting workspace areas
mkdir -p /mnt/data/jellymain/torrents/movies
mkdir -p /mnt/data/jellymain/torrents/tv

# Create the clean display library destinations for Jellyfin
mkdir -p /mnt/data/jellymain/movies
mkdir -p /mnt/data/jellymain/tv

# Enforce explicit Drew user permissions over the target paths
sudo chown -R 1000:1000 /mnt/data/jellymain
```

## 4. Step 2: Rewrite the Integrated Docker Compose Stack

To ensure all media handlers share an identical layout view of the storage partition, we discard old independent directories and map the absolute storage baseline `/mnt/data/jellymain` directly to a single inner root directory named `/data`.

Navigate to your workspace configuration context and open the primary compose file:

```
cd /opt/docker/ingest
nano docker-compose.yml
```

Replace the internal stack structure with this production-hardened design:

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
      - 9696:9696 # Prowlarr Web UI
      - 7878:7878 # Radarr Web UI
      - 8989:8989 # Sonarr Web UI
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
      - /opt/docker/ingest/config/qbittorrent:/config
      - /mnt/data/jellymain:/data
    depends_on:
      gluetun:
        condition: service_healthy
    restart: unless-stopped

  prowlarr:
    image: lscr.io/linuxserver/prowlarr:latest
    container_name: prowlarr
    network_mode: "service:gluetun"
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
    volumes:
      - /opt/docker/ingest/config/prowlarr:/config
    restart: unless-stopped

  radarr:
    image: lscr.io/linuxserver/radarr:latest
    container_name: radarr
    network_mode: "service:gluetun"
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
    volumes:
      - /opt/docker/ingest/config/radarr:/config
      - /mnt/data/jellymain:/data
    restart: unless-stopped

  sonarr:
    image: lscr.io/linuxserver/sonarr:latest
    container_name: sonarr
    network_mode: "service:gluetun"
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
    volumes:
      - /opt/docker/ingest/config/sonarr:/config
      - /mnt/data/jellymain:/data
    restart: unless-stopped
```

Deploy the updated architecture cleanly:

```
docker compose up -d --remove-orphans
```

## 5. Step 3: Application Optimization Mapping

### qBittorrent Configuration Alignment

1. Open the qBittorrent dashboard (`http://192.168.0.151:8080`).
    
2. Navigate to **Tools > Options > Downloads**.
    
3. Change the **Default Save Path** to exactly: `/data/torrents/`
    
4. Under **Web UI > Authentication**, check **Bypass authentication for clients on local subnet** to secure automated network hooks. Save configuration changes.
    

### Centralized Tracker Routing (Prowlarr)

1. Open Prowlarr (`http://192.168.0.151:9696`).
    
2. Navigate to **Settings > Apps** and click the **+** icon to register Radarr and Sonarr separately.
    
3. Use the local internal loopback URL `http://localhost:7878` for Radarr and `http://localhost:8989` for Sonarr (since they share Gluetun’s local network workspace).
    
4. Paste the respective API keys obtained from Radarr and Sonarr’s **Settings > General** sub-menus. Click **Sync App Indexers** to force cross-app tracker populating.
    

### Hardlink Library Binding (Radarr & Sonarr)

1. Open **Radarr** (`:7878`) and **Sonarr** (`:8989`).
    
2. Navigate to **Settings > Media Management > Advanced** and ensure **Use Hardlinks instead of Copy** is marked **ON**.
    
3. Under the **Root Folders** management frame at the bottom of the page:
    
    - For Radarr, declare your root library path as: `/data/movies/`
        
    - For Sonarr, declare your root library path as: `/data/tv/`
        
4. Under **Settings > Download Clients**, append qBittorrent to both applications (`Host: localhost`, `Port: 8080`). Set the operational tracker category to `radarr` inside Radarr, and `tv-sonarr` inside Sonarr.
    

## 6. Post-SOP Verification

- **API Handshake Verification:** Check your app integrations within Prowlarr. Every card must display a solid green status bar, verifying continuous internal API mesh capability.
    
- **Download Automation Test:** Add a test item inside Radarr or Sonarr and click **Interactive Search**. Trigger a download and confirm the object enters the queue with the correct targeted category tag applied.
    
- **Zero-Space Allocation Verification:** Once a transfer hits completion status, log back into the host command terminal and query the file indices directly:
    
    ```
    ls -i /mnt/data/jellymain/torrents/movies/
    ls -i /mnt/data/jellymain/movies/
    ```
    
    Verify that the payload files inside both separate storage paths contain identical filesystem **Inode integers**. This confirms they occupy only a single storage footprint on the physical disk block layer.