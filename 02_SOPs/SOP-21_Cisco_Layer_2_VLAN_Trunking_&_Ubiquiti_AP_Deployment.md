## 1. Purpose

This procedure outlines the logical configuration of an upstairs Cisco managed switch to support multi-SSID broadcasting from a Ubiquiti Access Point. It details the creation of Layer 2 Virtual Local Area Networks (VLANs), the configuration of 802.1Q trunk ports to handle tagged wireless traffic, and the maintenance of the untagged management network control plane.

## 2. Lab Environment Reference

The following architectural values represent the Layer 2 distribution layer of the lab.

| **Attribute** | **Value** | **Context** |
| :--- | :--- | :--- |
| **Native/Management Subnet** | `192.168.0.0/24` (Untagged) | Primary server, host, and switch management |
| **Home Wireless Subnet** | `192.168.10.0/24` (VLAN 10) | Segmented SSID for residential client traffic |
| **Uplink Port (Cisco)** | `Gi0/1` | Physical path to the primary routing gateway |
| **AP Downlink Port (Cisco)** | `Gi0/2` | Physical path to the upstairs Ubiquiti AP |

---

## 3. Step 1: VLAN Creation on the Cisco Switch

Before a Cisco switch can forward tagged frames for an alternate subnet, the VLAN ID must exist within its local broadcast domain database.

**Step 1: Access the Cisco Command Line Interface (CLI)**
Establish a console or SSH session to the Cisco switch, input your credentials, and enter privileged EXEC mode:
```text
enable
configure terminal
````

**Step 2: Initialize and Name the VLAN**
```
vlan 10
 name HOME_WIRELESS
exit
```

## 4. Step 2: Configuring the Gateway Uplink Trunk

The physical connection from your primary Ubiquiti Cloud Gateway to the Cisco switch must pass traffic for all subnets simultaneously.

**Step 1: Enter the Uplink Interface** Select the specific port connecting down to your main gateway router (e.g., GigabitEthernet 0/1):
```
interface GigabitEthernet0/1
 description UPLINK_TO_UBIQUITI_GATEWAY
```

**Step 2: Apply 802.1Q Trunking Parameters** Configure the port to pass native management traffic untagged, while explicitly tagging and forwarding VLAN 10:

```
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk native vlan 1
switchport trunk allowed vlan 1,10
exit
```

_(Note: Cisco switches treat VLAN 1 as the default native untagged VLAN out of the box, mapping perfectly to your untagged `192.168.0.0/24` gateway baseline)._

## 5. Step 3: Configuring the Access Point Downlink Port

The port connecting directly to your upstairs Ubiquiti Access Point (e.g., GigabitEthernet 0/2) must replicate the trunk parameters so the AP can communicate with the UniFi controller while dumping wireless clients into VLAN 10.

**Step 1: Enter the AP Interface**

```
interface GigabitEthernet0/2
 description DOWNLINK_TO_UPSTAIRS_UBIQUITI_AP
```

**Step 2: Bind Trunk and Allowed VLAN IDs**

```
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk native vlan 1
switchport trunk allowed vlan 1,10
end
```

**Step 3: Commit Configuration to NVRAM** Save the running configuration to volatile memory to prevent total data loss during a power disruption or cold reboot:

```
copy running-config startup-config
```

## 6. Step 4: UniFi Network Controller Configuration

With Layer 2 pathways open across the Cisco hardware, the UniFi software must be updated to append the appropriate tag IDs to the wireless SSIDs.

**Step 1: Declare the VLAN Network**

1. Log into your UniFi Network Controller (`https://192.168.0.1`).
    
2. Navigate to **Settings > Networks** and click **Create New Network**.
    
3. Name the network `Home_Wireless_VLAN10` and toggle **Advanced Configuration** to **Manual**.
    
4. Set the **VLAN ID** field to **`10`**.
    

**Step 2: Bind the Network to the SSID**

1. Navigate to **Settings > WiFi** and select your dedicated home network profile.
    
2. Under the **Network** dropdown menu, change the target assignment from the default network to your newly declared **`Home_Wireless_VLAN10`**.
    
3. Click **Apply Changes** to force a rolling provision out to your upstairs access points.
    

## 7. Post-SOP Verification

- **Switch Trunk Status:** Execute `show interfaces trunk` on the Cisco switch CLI. Verify that interfaces `Gi0/1` and `Gi0/2` are actively trunking and displaying VLANs `1` and `10` in the "VLANs allowed and active in management domain" block.
    
- **Client IP Verification:** Connect a mobile device or laptop to your dedicated Home SSID. Open the network settings of the client device and confirm it has successfully leased an IP address from the designated VLAN 10 DHCP pool (e.g., `192.168.10.X`), rather than your core server subnet (`192.168.0.X`).
    
- **Core Isolation Test:** From a client device associated with VLAN 10, attempt to ping your Proxmox server (`ping 192.168.0.150`). If your gateway firewall rules are established correctly, the packets should drop, proving layer 3 segmentation works.