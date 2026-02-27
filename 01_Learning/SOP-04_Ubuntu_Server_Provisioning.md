## 1. Purpose

This procedure defines the steps for installing the Ubuntu Server Operating System onto a virtualized shell and performing initial security hardening. This transition moves the project from physical hardware setup to a "headless" remote management environment.

## 2. Lab Environment Reference

The following network parameters are required for the **Angel Server** lab environment.

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**Target Subnet**|`192.168.0.0/24`|Local Lab Subnet|
|**Static IP Address**|`192.168.0.151`|Reserved for Ubuntu VM|
|**Local Gateway**|`192.168.0.1`|Router Interface|
|**Primary DNS**|`8.8.8.8`|Google Public DNS|

## 3. Initial Boot & Installation Wizard

1. **Start VM:** Select the target VM in the Proxmox sidebar and click **Start**, then open the **Console** tab.
    
2. **Language/Keyboard:** Select the preferred language and keyboard layout.
    
3. **Install Type:** Choose **Ubuntu Server** (Standard).
    
4. **Static Networking:**
    
    - Highlight the network interface (e.g., `enp1s0`) and select **Edit IPv4**.
        
    - Change the method to **Manual** and enter the lab values defined in Section 2.
        
5. **Storage:**
    
    - Select **Use an entire disk** (utilizing the virtual disk provisioned in SOP-03).
        
    - Ensure **Set up this disk as an LVM group** is selected for flexible volume management.
        

## 4. Profile & SSH Configuration

1. **User Profile:**
    
    - **Your Name:** Use a preferred display name.
        
    - **Server Name:** Enter the hostname (e.g., `angel-node-01`).
        
    - **Username:** Create a primary user (e.g., `drew`).
        
2. **SSH Setup:** Select **Install OpenSSH server**. This is critical for remote CLI management.
    

## 5. Base System Hardening (Post-Install)

Once the installation is complete and the system reboots, log in via the console and execute the following maintenance commands to finalize the setup.

**1. Update System Repositories** Synchronize the local package index with the remote repositories and apply all available security patches.

```
sudo apt update && sudo apt upgrade -y
```

**2. Install Core Utilities** Install essential tools for networking, text editing, and system monitoring.

```
sudo apt install -y curl git vim htop net-tools
```

**3. Verify Connectivity** Confirm the network stack is configured correctly.

- **IP Assignment:** Run `ip addr show` to verify the static `192.168.0.151` assignment.
    
- **External Routing:** Run `ping -c 4 google.com` to verify DNS resolution and gateway communication.
## 6. Remote Management Transition

1. **Shutdown:** Power off the Virtual Machine via the command `sudo poweroff`.
    
2. **Remove Media:** In the Proxmox **Hardware** tab, unmount the Ubuntu installation ISO by setting the CD/DVD drive to **"Do not use any media"**.
    
3. **Headless Operation:** The physical host (e.g., Dell OptiPlex) can now be disconnected from local peripherals such as the monitor and keyboard. All future administrative access is conducted via SSH from a remote workstation:

```
ssh username@192.168.0.151
```