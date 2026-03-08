## 1. Purpose

This procedure defines the integration of Cloudflare as the authoritative DNS provider for the **Angel Server**. It establishes two critical functions:

1. **SSL/TLS via DNS-01:** Bypassing ISP-level Port 80 blocking by verifying domain ownership through DNS records.
    
2. **DDNS Automation:** Ensuring the `angelserver.live` A-record automatically updates whenever the residential public IP changes.
    

## 2. Lab Environment Reference

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**DNS Provider**|Cloudflare|Authoritative DNS & Proxy|
|**Domain**|`angelserver.live`|Primary FQDN|
|**Data Root**|`/opt/docker/cloudflare-ddns`|DDNS Configuration Directory|
|**API Token**|[Cloudflare Token]|Permissions: Zone:DNS:Edit|

## 3. Cloudflare & Registrar Handshake

1. **Nameserver Migration:** Update the domain registrar (e.g., Namecheap) to use Cloudflare’s assigned nameservers.
    
2. **API Token Generation:** Create a "Zone:DNS:Edit" token in the Cloudflare Profile settings to allow the server to modify records programmatically.
    

## 4. Configuring DNS-01 in Nginx Proxy Manager

1. **Access NPM Admin:** Navigate to `http://192.168.0.151:81`.
    
2. **Request Certificate:** Under **SSL Certificates**, select **Add Let's Encrypt Certificate**.
    
3. **Challenge Setup:**
    
    - **Domain Names:** `*.angelserver.live`, `angelserver.live`
        
    - **Use DNS Challenge:** Toggle **ON**
        
    - **DNS Provider:** Select **Cloudflare**
        
    - **Credentials:** Enter the Cloudflare API Token
        
    - **Propagation Seconds:** Set to `120`
        

## 5. Deploying DDNS Automation (Docker)

This ensures the server "phones home" to Cloudflare to update your IP address automatically.

**Step 1: Create the directory**

```
mkdir -p /opt/docker/cloudflare-ddns
cd /opt/docker/cloudflare-ddns
```

**Step 2: Create the Docker Compose file**

```
nano docker-compose.yml
```

**Step 3: Define the service**

Paste the following, replacing `YOUR_API_TOKEN` with your actual token:

```
services:
  cloudflare-ddns:
    image: oznu/cloudflare-ddns:latest
    restart: always
    environment:
      - API_KEY=YOUR_API_TOKEN
      - ZONE=angelserver.live
      - PROXIED=true
```

**Step 4: Launch the container**

```
docker compose up -d
```

## 6. Post-SOP Verification

1. **SSL Validity:** Confirm the certificate status is "Active" in the NPM dashboard.
    
2. **Automation Logs:** Run `docker logs cloudflare-ddns` to verify the IP was detected and synced successfully.
    
3. **Proxy Status:** Verify the "Orange Cloud" (Proxy) is enabled in the Cloudflare dashboard to mask the home public IP.
    
4. **Resolution Test:** Run `ping angelserver.live` to confirm it resolves to a Cloudflare IP rather than your local gateway.