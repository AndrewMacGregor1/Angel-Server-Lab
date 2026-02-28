## 1. Purpose

This procedure outlines the installation of the Docker Engine and the Portainer management interface. The goal is to establish a containerization layer that allows for the deployment of multiple isolated services on a single Linux host without dependency conflicts.

## 2. Lab Environment Reference

The following values are used as the baseline for the reference lab environment.

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**Target OS**|Ubuntu Server 24.04 LTS|Guest Operating System|
|**Reference Hardware**|Dell OptiPlex 9020 SFF|i5-4590, 16GB RAM|
|**Management IP**|`192.168.0.151`|Static IP of the VM|
|**Web UI Port**|`9443`|Portainer HTTPS port|

## 3. Docker Engine Installation

The installation utilizes the official Docker convenience script to ensure the latest stable version of the container runtime is deployed.

**Step 1: Execute the Docker installation script**

```
curl -fsSL https://get.docker.com | sudo sh
```

**Step 2: Configure non-root user permissions**

To manage containers without requiring elevated privileges for every command, the primary user must be added to the `docker` group.

```
sudo usermod -aG docker $USER
```

_Note: A logout and login are required for group membership changes to take effect._

## 4. Portainer Deployment

Portainer is deployed as a container to provide a web-based dashboard for managing the environment's stack, images, and volumes.

**Step 1: Create a persistent volume for configuration data**

This ensures that the Portainer database and settings persist across container restarts or updates.

```
docker volume create portainer_data
```

**Step 2: Deploy the Portainer Community Edition container**

```
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

## 5. Post-SOP Verification

- **Container Status:** Run `docker ps` to verify the Portainer container status is `Up`.
    
- **Interface Access:** Navigate to `https://[SERVER-IP]:9443` to complete the initial administrator setup.
    
- **Resource Monitoring:** Confirm the dashboard correctly displays the local Docker node and its hardware resources.