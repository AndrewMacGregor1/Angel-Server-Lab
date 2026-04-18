## **1. Purpose**

This procedure outlines the initialization and basic Layer 2 configuration of the Cisco Catalyst 2960 switch. The goal is to transition the lab from a point-to-point connection to a star topology, providing a dedicated high-speed backplane for the Proxmox cluster.

## **2. Lab Environment Reference**

| **Attribute**     | **Value**           | **Context**                  |
| ----------------- | ------------------- | ---------------------------- |
| **Switch Model**  | Cisco Catalyst 2960 | Primary Layer 2 Distribution |
| **Management IP** | `192.168.0.2`       | Static Switch Management IP  |
| **Uplink Port**   | `FastEthernet 0/1`  | Connection to Cox Gateway    |
| **Node 01 Port**  | `FastEthernet 0/2`  | Connection to OptiPlex 9020  |
| **Node 02 Port**  | `FastEthernet 0/3`  | Connection to N100 Mini PC   |

---

## **3. Initial Hardware Reset**

To ensure no "ghost" configurations or old VLAN databases interfere with the cluster communication, the switch must be factory reset.

**Step 1: Access the Console**

Connect the console cable and open a terminal (9600 Baud). Type `enable` to enter privileged mode.

**Step 2: Wipe Configuration and VLANs**

```
write erase
delete flash:vlan.dat
```

_Note: Confirm both prompts by pressing Enter._

**Step 3: Reboot the Switch**

```
reload
```

_When prompted to enter the "Initial Configuration Dialog," type **no**._

---

## **4. Basic Management Configuration**

Establishing a management identity allows the switch to be identified on the network and reached via the static IP assigned.

**Step 1: Set Identity and IP**

```
conf t
hostname Angel-Switch-01
interface vlan 1
 ip address 192.168.0.2 255.255.255.0
 no shut
exit
```

**Step 2: Configure Default Gateway**

This allows the switch to communicate with the internet for time syncing or updates

```
ip default-gateway 192.168.0.1
```

---

## **5. Physical Connectivity (Star Topology)**

Assigning the devices to their respective physical ports establishes the Layer 1 foundation for the new cluster architecture.

1. **Port 0/1:** Connect the 100ft Ethernet run from the Cox Gateway.
    
2. **Port 0/2:** Connect the OptiPlex 9020 (`192.168.0.151`).
    
3. **Port 0/3:** Connect the N100 Mini PC (`192.168.0.152`).
    

**Step 1: Save the Running Configuration**

Cisco configurations are volatile until saved to NVRAM.

```
do copy run start
```

---

## **6. Post-SOP Verification**

- **Physical Link Check:** Run `show interfaces status` to verify Ports 1, 2, and 3 are `connected` and showing `a-full` (auto-full duplex).
    
- **Management Access:** Perform a `ping 192.168.0.2` from your Windows PC to ensure the switch is reachable.
    
- **Internet Pass-through:** From the OptiPlex shell (`.151`), run `ping 8.8.8.8` to verify the switch is successfully bridging traffic to the Cox Gateway.
    
- **Node Discovery:** From the N100 shell (`.152`), run `ping 192.168.0.151` to verify the two nodes can communicate across the switch backplane for the future NFS bridge.