## 1. Purpose

This procedure defines the setup of a Dynamic DNS (DDNS) client to maintain a consistent Fully Qualified Domain Name (FQDN) for the lab environment. This ensures that external services and users can reach the server using a custom hostname even if the ISP assigns a new public IP address.

## 2. Lab Environment Reference

The following values are used as the baseline for the reference lab environment.

| **Attribute**        | **Lab Value (Example)**        | **Context**                |
| -------------------- | ------------------------------ | -------------------------- |
| **Service Provider** | DuckDNS / Cloudflare           | DDNS Hosting Provider      |
| **Custom Hostname**  | `[YOUR-SUBDOMAIN].duckdns.org` | The external access URL    |
| **Data Root**        | `/opt/docker/ddns`             | Persistent script location |
| **Update Interval**  | 5 Minutes                      | Frequency of IP checks     |

## 3. Provider Setup

Before configuring the server, an account must be established with a DDNS provider to reserve a hostname.

**Step 1: Register Hostname**

1. Visit a DDNS provider (e.g., DuckDNS.org).
    
2. Create a subdomain (e.g., `angel-server`).
    
3. Note the **API Token** provided by the service; this is required for the server to authenticate updates.
    

## 4. Deploying the DDNS Client via Docker

Using a containerized client ensures the update script runs reliably in the background without manual intervention.

**Step 1: Create the directory structure**

```
mkdir -p /opt/docker/ddns
```

**Step 2: Create the Docker Compose file**

```
nano /opt/docker/ddns/docker-compose.yml
```

**Step 3: Define the DDNS service**

_(Example using the popular `linuxserver/ddclient` image which supports multiple providers)_

```
services:
  ddclient:
    image: lscr.io/linuxserver/ddclient:latest
    container_name: ddclient
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
    volumes:
      - ./config:/config
    restart: unless-stopped
```

**Step 4: Launch the container**

```
docker compose -f /opt/docker/ddns/docker-compose.yml up -d
```

## 5. Configuration and Verification

The client must be pointed toward the specific DDNS provider and API token.

**Step 1: Edit the configuration file** Modify the auto-generated config file (located in `/opt/docker/ddns/config/ddclient.conf`) to include the domain and API token following the provider's specific syntax.

**Step 2: Post-SOP Verification** Check the container logs to confirm a "SUCCESS" message or an "IP updated" notification.

```
docker logs ddclient
```

**Step 3: DNS Resolution Test** Open a terminal on an external network (or use a phone on cellular data) and run a ping against the custom hostname.

```
ping [YOUR-SUBDOMAIN].duckdns.org
```

**Step 4: Result** Confirm the ping returns the current public IP address of the home network.