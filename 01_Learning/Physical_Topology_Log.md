## 1. Site Survey & Infrastructure Constraints

This section documents the physical environment and the logical solutions implemented to overcome onsite networking limitations.

- **Constraint:** Existing interior coax ports and legacy wiring were found to be non-functional or unreliable for high-bandwidth server traffic.
    
- **Environmental Gap:** The primary gateway is located in the basement, while the server node is situated in a third-floor workspace.
    
- **Deployment Solution:** A dedicated 100ft Category-rated Ethernet run was deployed to establish a direct, low-latency path between the node and the gateway.

## 2. Layer 1 Component Specifications

The following physical components comprise the Layer 1 foundation of the Angel Server lab.

| **Component**     | **Specification**            | **Role**                        |
| ----------------- | ---------------------------- | ------------------------------- |
| **Primary Media** | 100ft Cat5e/Cat6 Patch Cable | Physical Data Path              |
| **Gateway**       | Cox Panoramic Box            | Primary ISP Layer 3 Termination |
| **Server Node**   | Dell OptiPlex 9020 SFF       | Physical Hypervisor Host        |

> **Note:** "If the cable is bad, the config doesn't matter." Physical layer integrity is the first priority in troubleshooting.

## 3. Cable Routing & Pathing

The cable run follows a documented path to ensure safety and signal integrity over the 100ft distance.

1. **Origin:** Dell OptiPlex 9020 NIC.
    
2. **Transition:** Secured along hallway walls using adhesive cable clips to prevent trip hazards.
    
3. **Vertical Drop:** Routed through a banister and across the kitchen ceiling to a service penetration located behind the refrigerator.
    
4. **Termination:** Connected directly to **Port 1** of the Cox Gateway in the basement.

## 4. Maintenance & Connectivity Verification

Standard verification tests were performed to ensure the physical run supports the required data rates.

- **2026-02-08:** Physical deployment completed; verified "Link Up" status and 1Gbps negotiation on the host NIC.
    
- **Functional Testing:** Executed `ping -c 4 google.com` to confirm successful data flow across the 100ft run.
    
- **Signal Integrity:** Confirmed the run is well within the 100-meter (328ft) maximum limit for Ethernet to prevent attenuation.