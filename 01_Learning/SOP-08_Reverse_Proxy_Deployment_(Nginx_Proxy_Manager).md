## 1. Purpose

This procedure defines the deployment of **Nginx Proxy Manager (NPM)** as a centralized gateway for the lab environment. By utilizing the **DNS-01 challenge** via Cloudflare, this strategy bypasses ISP-level port filtering (specifically Port 80 blocks) to provide valid SSL/TLS encryption for all internal services.

## 2. Lab Environment Reference

The following values are used as the baseline for the **Angel Server** reference environment.

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**Data Root**|`/opt/docker/npm`|Persistent storage location|
|**Internal IP**|`192.168.0.151`|Static IP of the Ubuntu VM|
|**Admin UI Port**|`81`|NPM Management Interface|
|**HTTPS Port**|`443`|Encrypted web traffic|
|**DNS Provider**|Cloudflare|External DNS Manager|

## 3. Directory and Compose Configuration

To ensure data persistence, the container must be mapped to specific directories on the host system.

**Step 1: Create application directories**

```
mkdir -p /opt/docker/npm/data /opt/docker/npm/letsencrypt
```

**Step 2: Create the Docker Compose file**

```
nano /opt/docker/npm/docker-compose.yml
```

**Step 3: Define the service stack**

Paste the following configuration into the editor. Note that **Port 80** is mapped internally for the proxy's function but does **not** need to be opened on your router.

```
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```

**Step 4: Launch the container**

```
docker compose -f /opt/docker/npm/docker-compose.yml up -d
```

## 4. Edge Network & Security Configuration

Since we are using a DNS challenge, we only need to open the ports required for actual traffic and management.

**Step 1: Router Port Forwarding**

Access your router gateway (e.g., `192.168.0.1`) and create the following rules:

1. **HTTPS:** External Port `443` -> Internal Port `443` -> IP `192.168.0.151`
    
2. **NPM Admin:** External Port `81` -> Internal Port `81` -> IP `192.168.0.151` (Optional for remote management)
    

**Step 2: Update VM Firewall (UFW)**

```
sudo ufw allow 443/tcp
sudo ufw allow 81/tcp
```

## 5. SSL Configuration (DNS-01 Challenge)

This is the critical "pivot" to bypass Port 80 blocking.

1. **Access Admin UI:** Navigate to `http://192.168.0.151:81`.
    
2. **Add Certificate:** Go to **SSL Certificates** > **Add SSL Certificate** > **Let's Encrypt**.
    
3. **DNS Challenge Setup:**
    
    - **Domain Names:** `*.angelserver.live` (Wildcard) and `angelserver.live`.
        
    - **Use a DNS Challenge:** Toggle **ON**.
        
    - **DNS Provider:** Select **Cloudflare**.
        
    - **Credentials:** Paste your **Cloudflare API Token** into the configuration box.
        
    - **Propagation Seconds:** Set to `120`.
        

## 6. Post-SOP Verification

- **Admin Access:** Confirm you can log in to the management console.
    
- **SSL Certificate:** Verify the new certificate appears in the list with an "Active" status.
    
- **Security Check:** Run `sudo ufw status` to ensure Port 80 remains closed to the public internet.