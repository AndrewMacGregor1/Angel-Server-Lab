### 1. Purpose

This procedure covers the initial setup of the **Ubiquiti Cloud Gateway Ultra** and the creation of **VLAN 10 (VPN_Net)**. This is the "brain" that tells the rest of the lab which traffic is normal and which traffic gets sent through the Wireguard tunnel.

### 2. Lab Environment Reference

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**Native VLAN**|`VLAN 1`|Default Network (`192.168.0.x`)|
|**VPN VLAN**|`VLAN 10`|VPN Network (`192.168.10.x`)|
|**Router IP**|`192.168.0.1`|Management Gateway|

### 3. Step 1: Create the Virtual Networks

1. **Login:** Hit the UniFi dashboard at `192.168.0.1`.
    
2. **Add Network:** Go to **Settings > Networks > New Virtual Network**.
    
    - **Name:** `Angel_VPN`.
        
    - **VLAN ID:** `10`.
        
    - **Subnet:** `192.168.10.1/24`.
        
3. **DHCP:** Enable the DHCP server so devices on this VLAN get their own `.10.x` IPs.
    

### 4. Step 2: The Wireguard Tunnel & Routing Rule

1. **VPN Client:** Go to **Settings > VPN > VPN Client** and upload your Wireguard `.conf` file from Proton.
    
2. **Policy-Based Routing:** Go to **Settings > Routing > Policy-Based Routing**.
    
    - **What:** `All Traffic`.
        
    - **Source:** Select `Angel_VPN (VLAN 10)`.
        
    - **Interface:** Select your `Wireguard Tunnel`.
        
3. **The Result:** Any device we put on VLAN 10 is now physically incapable of seeing the "regular" internet—it’s tunnel or nothing.
    

