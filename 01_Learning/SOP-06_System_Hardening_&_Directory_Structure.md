## 1. Purpose

This procedure outlines the steps for securing the Ubuntu Server guest OS and establishing a standardized file structure for persistent data. This ensures that all future containerized services have a consistent "home" for their configuration files.

## 2. Lab Environment Reference

The following values are used for the **Angel Server** lab environment.

| **Attribute**    | **Lab Value (Example)** | **Context**                         |
| ---------------- | ----------------------- | ----------------------------------- |
| **Primary User** | `drew`                  | Non-root administrative user        |
| **Data Root**    | `/opt/docker`           | Centralized Docker config directory |
| **Firewall**     | `UFW`                   | Uncomplicated Firewall              |
|                  |                         |                                     |

## 3. Basic Security Hardening

Before installing any public-facing services, the "Attack Surface" of the VM should be minimized.

**Step 1: Enable Unattended Upgrades**

This ensures the server automatically downloads and applies security patches.

```
sudo apt install unattended-upgrades -y
```

**Step 2: Configure the Firewall (UFW)**

By default, we want to deny all incoming traffic except for the services we explicitly allow.

```
sudo ufw allow OpenSSH
sudo ufw enable
```

## 4. Organizational Structure

Establishing a dedicated directory for Docker prevents configuration files from being scattered across the user's home folder.

**Step 1: Create the Docker Root directory**

We use `/opt/docker` as it is a standard location for "optional" or add-on software.

```
sudo mkdir -p /opt/docker
```

**Step 2: Assign ownership to the primary user**

This allows the `drew` user to manage files without constantly needing `sudo`.

```
sudo chown -R $USER:$USER /opt/docker
```

## 5. Post-SOP Verification

- **Security Check:** Run `sudo ufw status` to confirm the firewall is active and protecting the VM.
    
- **Directory Check:** Run `ls -ld /opt/docker` to verify the folder exists and is owned by your user.