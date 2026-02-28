## 1. Purpose

This procedure defines the deployment of a dedicated Minecraft Bedrock Edition server using Docker. This allows players on Windows, Android, iOS, and consoles to connect to a persistent world hosted on the lab hardware.

## 2. Lab Environment Reference

The following values are used as the baseline for the reference lab environment.

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**Service Image**|`itzg/minecraft-bedrock-server`|Optimized Bedrock Docker image|
|**Data Root**|`/opt/docker/minecraft`|Persistent world data location|
|**Default Port**|`19132`|Standard Bedrock UDP port|
|**Protocol**|UDP|Required protocol for Bedrock traffic|

## 3. Directory and Data Preparation

To ensure world progress is saved during container updates, a dedicated data volume must be created.

**Step 1: Create the application directory**
```
mkdir -p /opt/docker/minecraft
```

## 4. Deployment via Docker Compose

Using Docker Compose allows for easy management of server environment variables such as difficulty and game mode.

**Step 1: Create the configuration file**

```
nano /opt/docker/minecraft/docker-compose.yml
```

**Step 2: Define the Minecraft service**

Paste the following configuration into the editor:

```
services:
  mc:
    image: itzg/minecraft-bedrock-server
    container_name: minecraft-bedrock
    environment:
      EULA: "TRUE"
      GAMEMODE: survival
      DIFFICULTY: normal
      SERVER_NAME: "My-Minecraft-Server"
    ports:
      - 19132:19132/udp
    volumes:
      - ./data:/data
    restart: unless-stopped
```

**Step 3: Launch the server**

```
docker compose -f /opt/docker/minecraft/docker-compose.yml up -d
```

## 5. Edge Network Configuration (Router)

Unlike web traffic, Minecraft Bedrock uses the UDP protocol. The router must be told to send this specific traffic to the server.

**Step 1: Create a Port Forwarding Rule**

Navigate to the router settings and create a new rule:

- **Protocol:** UDP (Important: do not use TCP)
    
- **External Port:** 19132
    
- **Internal Port:** 19132
    
- **Internal IP:** 192.168.0.151

**Step 2: Update the Host Firewall**

Allow the Minecraft port through the Ubuntu firewall.

```
sudo ufw allow 19132/udp
```

## 6. Post-SOP Verification

- **Console Logs:** Run `docker logs -f minecraft-bedrock` to confirm the server has finished generating the world and is "Started."
    
- **External Connection:** Have a friend on mobile or PC enter the custom DDNS hostname (e.g., `[YOUR-SUBDOMAIN].duckdns.org`) and port `19132` in the "Servers" tab.
    
- **Xbox/Console Connection:** Since consoles do not natively support custom IPs, the player must use a DNS workaround (like BedrockConnect) or a mobile app to make the server appear in their "Friends" tab.