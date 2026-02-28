## 1. Purpose

This procedure defines the deployment of Nginx Proxy Manager (NPM) and the associated edge network configuration. A reverse proxy acts as a centralized gateway, directing incoming external traffic to specific internal containers based on domain names while providing SSL/TLS encryption.

## 2. Lab Environment Reference

The following values are used as the baseline for the reference lab environment.

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**Data Root**|`/opt/docker/npm`|Persistent storage location|
|**Internal IP**|`192.168.0.151`|Static IP of the Ubuntu VM|
|**HTTP Port**|`80`|Standard web traffic|
|**HTTPS Port**|`443`|Encrypted web traffic|
|**Admin UI Port**|`81`|NPM Management Interface|

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

Paste the following configuration into the editor:

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

## 4. Edge Network Configuration (Router Port Forwarding)

For the proxy to receive traffic from the internet (required for SSL certificate generation), the local router must be configured to pass traffic to the Ubuntu VM.

**Step 1: Access the Router Gateway**

Navigate to the router's local IP (e.g., `192.168.0.1`) and authenticate.

**Step 2: Create Port Forwarding Rules**

Create two new rules in the "Port Forwarding" or "Virtual Server" section:

1. **Rule 1:** Protocol: TCP | External Port: 80 | Internal Port: 80 | Internal IP: `192.168.0.151`
    
2. **Rule 2:** Protocol: TCP | External Port: 443 | Internal Port: 443 | Internal IP: `192.168.0.151`
    

**Step 3: Update Firewall**

Ensure the Ubuntu firewall (UFW) is configured to allow these new traffic types.

```
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 81/tcp
```

## 5. Post-SOP Verification

- **Admin Access:** Navigate to `http://192.168.0.151:81` and log in with the default credentials:
    
    - **Email:** `admin@example.com`
        
    - **Password:** `changeme`
        
- **External Connectivity:** Use an external tool (like a port checker) to verify that ports 80 and 443 are "Open" on your public IP address.
    
- **SSL Readiness:** Confirm the "SSL Certificates" tab is accessible in NPM; this is required for the upcoming Minecraft and documentation services.