### 1. Purpose

This procedure defines the deployment of **Immich** as a self-hosted alternative to iCloud and Google Photos. By utilizing the secondary 500GB HDD for the "Upload" directory, the primary 128GB SSD is protected from storage saturation while maintaining high performance for the application database.

### 2. Lab Environment Reference

The following values align with the **Angel Server** Docker standards.

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**App Root**|`/opt/docker/immich`|Configuration and Docker Compose root|
|**Library Root**|`/mnt/data/immich-photos`|High-capacity HDD storage for originals|
|**Internal Port**|`2283`|Default Immich Web UI port|
|**Admin IP**|`192.168.0.151`|Static Ubuntu Node IP|

### 3. Directory and Environment Preparation

Immich requires specific folders on both the SSD (for speed) and the HDD (for capacity).

**Step 1: Create the Application Directory (SSD)**

```
mkdir -p /opt/docker/immich
cd /opt/docker/immich
```

**Step 2: Create the Library Directory (HDD)**

```
sudo mkdir -p /mnt/data/immich-photos
sudo chown -R $USER:$USER /mnt/data/immich-photos
```

**Step 3: Download the Template Environment File**

```
wget -O .env https://github.com/immich-app/immich/releases/latest/download/example.env
```

_Note: Edit the `.env` file to set your `UPLOAD_LOCATION=/mnt/data/immich-photos`._

### 4. Deployment via Docker Compose

Immich uses a multi-container stack including PostgreSQL (Database) and Redis (Cache).

**Step 1: Download the Compose File**

```
wget -O docker-compose.yml https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml
```

**Step 2: Launch the Stack**

```
docker compose up -d
```

### 5. Edge Network Configuration

To access Immich securely via your custom domain (`angelserver.live`), we must update the Reverse Proxy.

1. **Nginx Proxy Manager:** Log in to the Admin UI at `192.168.0.151:81`.
    
2. **Add Proxy Host:** * **Domain Name:** `photos.angelserver.live`.
    
    - **Forward IP:** `192.168.0.151`.
        
    - **Forward Port:** `2283`.
        
3. **SSL:** Select the Cloudflare Wildcard certificate and enable **Websockets Support**.
    

### 6. Post-SOP Verification

- **Container Health:** Run `docker ps` to verify five Immich-related containers are `Up`.
    
- **Internal Access:** Navigate to `http://192.168.0.151:2283` and complete the initial Admin setup.
    
- **HDD Confirmation:** Upload a single large video and run `du -sh /mnt/data/immich-photos` to confirm the file was written to the HDD, not the SSD.