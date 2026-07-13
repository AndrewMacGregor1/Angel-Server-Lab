### 1. Purpose

Now that the router knows about the VLANs, we have to teach the **Cisco Catalyst 2960** switch how to pass them. We’re configuring a "Trunk" port so that one 100ft cable can carry both networks up to the 3rd floor.

### 2. Lab Environment Reference

| **Interface** | **Target Device**  | **Role**                                |
| ------------- | ------------------ | --------------------------------------- |
| **Gig0/1**    | Ubiquiti Gateway   | **Trunk Port** (Incoming from Basement) |
| **Gig0/2**    | Upstairs nanoHD AP | **Trunk Port** (Broadcast)              |
| **Fast0/1**   | Dell OptiPlex 9020 | **Access Port** (VLAN 1 Only)           |

### 3. Step 1: Configure the Switch Backbone (CLI)

Use the console cable to connect to the console port in order to configure the switch
1. **Set the Trunk:** We need to tell the switch that Port `Gig0/1` is the main highway.

    ```
    interface GigabitEthernet0/1
    switchport mode trunk
    switchport trunk native vlan 1
    switchport trunk allowed vlan 1,10
    ```
2. **Configure the AP Port:** The Access Point also needs to see both VLANs so it can broadcast both SSIDs.
  
    ```
    interface GigabitEthernet0/2
    switchport mode trunk
    ```
  
3. **Configure the Server Port:** The Proxmox node only needs the default network for now.

    ```
    interface FastEthernet0/1
    switchport mode access
    switchport access vlan 1
    ```

### 4. Step 2: SSID Broadcasting (UniFi Controller)

1. **WiFi Setup:** In the UniFi dashboard, go to **Settings > WiFi**.
    
2. **SSID 1:** `Mac_WiFi` -> Map to **Default Network**.
    
3. **SSID 2:** `Mac_WiFi_VPN` -> Map to **Angel_VPN (VLAN 10)**.
    
4. **Provision:** Hit "Apply." Both APs will restart and start broadcasting your secure VPN network.
    

