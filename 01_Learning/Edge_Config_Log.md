## 1. Purpose

This log documents the specific configurations applied to the network edge (router) to facilitate external access to internal services. It serves as a historical record for port forwarding rules and firewall exceptions required to maintain external connectivity.

## 2. Lab Environment Reference

The following values represent the baseline configuration for the reference lab environment.

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**Gateway IP**|`192.168.0.1`|Local Router Management Interface|
|**Target Node IP**|`192.168.0.151`|Primary Linux Host/VM|
|**DDNS Provider**|DuckDNS|External Domain Provider|
|**DDNS Domain**|`[SUBDOMAIN].duckdns.org`|Active FQDN|

## 3. Port Forwarding Rules

These rules direct specific external traffic from the public internet to the internal server.

|**Service**|**External Port**|**Internal Port**|**Protocol**|**Target IP**|
|---|---|---|---|---|
|**HTTP (Web)**|80|80|TCP|`192.168.0.151`|
|**HTTPS (SSL)**|443|443|TCP|`192.168.0.151`|
|**Minecraft**|19132|19132|**UDP**|`192.168.0.151`|

## 4. Dynamic DNS Credentials

**Security Protocol:** All API tokens and sensitive credentials must be stored in a local `.env` file and excluded from version control via `.gitignore`.

**Step 1: Token Management**

API tokens associated with the DDNS subdomain should be stored in the local environment file on the host system. Hard-coding these values into the documentation is strictly prohibited.

**Step 2: Client Integration**

The token is pulled into the container environment using the following configuration path:

```
/opt/docker/ddns/config/ddclient.conf
```

## 5. Maintenance & Verification

The following checks ensure continued external access after network interruptions:

1. **DDNS Synchronization:** Confirm the DDNS provider reflects the current public IP of the network.
    
2. **Port Verification:** Utilize an external port-probing tool to verify that ports 80, 443, and 19132 are in the "Open" state.
    
3. **Service Logs:** Verify "heartbeat" updates in the container logs for the DDNS client.