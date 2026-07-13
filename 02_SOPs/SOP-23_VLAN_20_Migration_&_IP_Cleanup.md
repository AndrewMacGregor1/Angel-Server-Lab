## 1. Purpose

We noticed that having all of our home lab containers and test environments sitting on the main home network (VLAN 1) was a bit messy, and the random DHCP IP addresses were getting annoying to track. This procedure details how we moved the entire computing stack over to a dedicated server network (VLAN 20) to isolate our lab services and clean up our IP addressing scheme.

## 2. Lab Environment Reference

| **Device Name** | **New Subnet IP Assignment** | **VLAN Configuration Type** |
| :--- | :--- | :--- |
| **Proxmox VE Host** | `192.168.20.2` | Native Untagged (Cisco Port Control) |
| **MC-LXC (101)** | `192.168.20.3` | Untagged Guest (Inherited) |
| **JELLY-LXC (102)** | `192.168.20.4` | Untagged Guest (Inherited) |
| **VM 100 (Docker Host)** | `192.168.20.5` | Untagged Guest (Inherited) |
| **VM 103 (DOWNLOAD)** | `192.168.20.6` | Untagged Guest (Inherited) |

## 3. Step 1: Creating the Network Highway (UniFi & Cisco)

Before our servers could talk on the new subnet, we had to tell our core router and switch infrastructure that VLAN 20 existed.

1. **UniFi Gateway Ultra Setup:** We logged into the UniFi dashboard and added a new network named `Lab_Services` on **VLAN 20**. We turned off Auto-Scale and manually assigned the gateway to `192.168.20.1`, setting the dynamic DHCP range to start much higher up at `.100` so we could keep `.2` through `.99` completely open for static assignments.
2. **Cisco 2960 Database Configuration:** We hopped onto the Cisco switch using our serial console cable and typed these commands to create the VLAN database entry:
    ```text
    enable
    configure terminal
    vlan 20
     name LabServices
    exit
    ```
3. **Switch Port Trunking:** We made sure the main uplink port going down to our router (`g0/1`) allowed the new VLAN 20 tags.

## 4. Step 2: The Proxmox Host Pivot (Fixing the Lockout)

We ran into a major learning moment when moving the Proxmox host. Because the host OS sends out its management web panel traffic without any internal VLAN tags, the Cisco switch didn't know what network to map it to. To fix the web panel lockout, we changed our port behavior.

1. **Cisco Port Native VLAN Adjustment:** We dropped into the switch interface plugged into our physical server (`f0/2`) and told it to treat all untagged traffic coming out of the host as native **VLAN 20** traffic:
    ```text
    interface fastethernet 0/2
     switchport mode trunk
     switchport trunk allowed vlan 1,20
     switchport trunk native vlan 20
    end
    write memory
    ```
2. **The Result:** This instantly restored access to our Proxmox Web UI dashboard at its new management home: `https://192.168.20.2:8006`.

## 5. Step 3: Container Migration & Clearing Tags

Because our Cisco switch port is now doing the heavy lifting of forcing all untagged traffic from the server directly into VLAN 20, we had to change how our individual VMs and LXCs were configured. If they tagged themselves, the switch got confused and dropped their traffic.

1. **Clearing Proxmox Tags:** Inside the Proxmox interface, we selected every VM and LXC network device and **cleared out the VLAN tag field completely so it was blank**.
2. **Assigning the New IP Scheme:** For the LXCs, we adjusted their static hardware network profiles to map to our clean new incremental IPs (`.3`, `.4`, etc.) and set their gateway target to `192.168.20.1`.
3. **Updating Ubuntu Netplan Profiles:** For our full VMs (like `VM 100` and `VM 103`), we logged in and opened their Netplan YAML files (`/etc/netplan/50-cloud-init.yaml`) to swap out their hardcoded static blocks to match the new subnet:
    ```yaml
    network:
      version: 2
      renderer: networkd
      ethernets:
        ens18:
          dhcp4: false
          addresses:
            - 192.168.20.5/24
          routes:
            - to: default
              via: 192.168.20.1
          nameservers:
            addresses: [1.1.1.1, 8.8.8.8]
    ```
    We used `sudo netplan apply` to push the new network profiles live.

## 6. Post-SOP Verification

- **Ping Connectivity Test:** Open up a terminal session inside any migrated service and ping the UniFi gateway (`ping -c 3 192.168.20.1`) to ensure data flows out of the Proxmox host interface smoothly.
- **Cross-Subnet Access Verification:** Confirm that your personal computer sitting on the main home Wi-Fi (VLAN 1) can cleanly open the Proxmox Web panel and initiate SSH shell connections to the servers across the new boundary.