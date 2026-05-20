## 1. Purpose

This procedure outlines the decommissioning of the residential Cox Panoramic gateway as the primary Layer 3 router, migrating the network edge to an enterprise-grade Ubiquiti Cloud Gateway. By maintaining the legacy `192.168.0.0/24` IP schema and SSID credentials, this migration establishes a managed firewall environment without requiring IP re-allocations across active hypervisor nodes or containerized services.

## 2. Lab Environment Reference

The following values represent the architectural change at the network edge.

| **Attribute**        | **Old Value**         | **New Value**           | **Context**                         |
| :------------------- | :-------------------- | :---------------------- | :---------------------------------- |
| **Primary Gateway**  | Cox Panoramic Gateway | Ubiquiti Cloud Gateway  | Core Layer 3 Routing & Firewall     |
| **Gateway Local IP** | `192.168.0.1`         | `192.168.0.1`           | Retained Primary Management Gateway |
| **Subnet Scope**     | `192.168.0.0/24`      | `192.168.0.0/24`        | Preserved Subnet Baseline           |
| **ISP Mode**         | Routed Routing Engine | **Bridge Mode**         | Cox box acts as a transparent modem |
| **WAN Interface**    | Inbound Coax          | DHCP Assigned Public IP | External Handshake via Ubiquiti WAN |

---

## 3. Pre-Migration Checklist

Before disabling the Cox routing engine, ensure your primary environment data is documented to prevent IP conflicts when the new DHCP pool initializes.

1. **Audit Active Allocations:** Confirm all active IP reservations from your `Service_Port_Map.md` match the current network state:
   * `192.168.0.150` -> Proxmox VE Host
   * `192.168.0.151` -> `angel-node-01` (Ubuntu VM)
   * `192.168.0.160` -> Minecraft LXC
   * `192.168.0.161` -> Jellyfin LXC
2. **Document MAC Addresses:** Copy the physical MAC addresses of these hosts directly from the Cox App or Proxmox hardware tabs to allow immediate MAC-to-IP binding inside the UniFi controller.

---

## 4. Configuring Cox Bridge Mode

To prevent a Double NAT condition where both routing engines attempt packet translation, the ISP hardware must be stripped down to a layer 2 pass-through modem.

**Step 1: Access the Cox Management Interface**
Connect a laptop directly to a LAN port on the Cox Gateway via Ethernet and navigate to `http://192.168.0.1`.

**Step 2: Enable Bridge Mode**
1. Navigate to **Gateway > At a Glance** in the left-hand navigation pane.
2. Locate the **Bridge Mode** field and toggle the setting to **Enable**.
3. Confirm the warning prompt. The gateway will automatically disable its internal DHCP engine and deactivate its integrated Wi-Fi radios.

**Step 3: Hard Power Cycle**
Disconnect the power cable from the back of the Cox Gateway completely and leave it unpowered for 60 seconds to clear the MAC binding cache at the ISP headend.

---

## 5. Ubiquiti Gateway Provisioning

**Step 1: Physical Handshake**
Connect a Category-rated patch cable from **Port 1** of the unpowered Cox Gateway directly to the dedicated **WAN Port** of the new Ubiquiti Cloud Gateway. 

**Step 2: Sequential Boot Sequence**
1. Reconnect power to the Cox Gateway and wait for the front status LED to show a solid "Online" state.
2. Connect power to the Ubiquiti Cloud Gateway and allow the system initialization routine to complete.

**Step 3: Initial Subnet & Wi-Fi Matching**
1. Connect to the default UniFi initialization network via Bluetooth or a temporary LAN connection.
2. During the setup wizard, alter the default LAN configuration from `192.168.1.1` to exactly **`192.168.0.1`**.
3. Configure your primary Wi-Fi Network Name (SSID) and Password to match your legacy Wi-Fi credentials exactly. 

---

## 6. Re-establishing Static DHCP Reservations

Because your hosts utilize static IP assignments, they will immediately connect, but their leases must be locked inside the UniFi OS Network controller to prevent future pool overlapping.

**Step 1: Assign Fixed IPs in UniFi**
1. Open the UniFi Network dashboard and navigate to **Statistics > Client Devices**.
2. Locate `angel-node-01` and your standalone LXCs.
3. Select each device, navigate to **Settings**, toggle **Fixed IP Address** to **ON**, and input their designated IP targets:
   * `192.168.0.151` for the Ubuntu VM.
   * `192.168.0.160` for the Minecraft LXC.
   * `192.168.0.161` for the Jellyfin LXC.

---

## 7. Post-SOP Verification

- **Subnet Continuity:** Run `ping 192.168.0.151` from your main PC. The host must respond instantly, confirming zero path disruption.
- **Double-NAT Validation:** Check the WAN interface status on the Ubiquiti dashboard dashboard. It must display a public WAN address, confirming the Cox box is operating successfully as a transparent bridge.
- **Service Verification:** Launch a browser and attempt to reach `http://192.168.0.151:81`. The Nginx Proxy Manager administration interface must render normally.