## 1. Physical Host Profile

This section defines the primary compute node for the Angel Server home lab.

- **Model:** Dell OptiPlex 9020 SFF (Small Form Factor).
    
- **Chassis:** Compact design optimized for low-power, 24/7 operation in a home environment.
    

## 2. Computational Specs (Layer 1)

Technical specifications of the silicon powering the hypervisor.

|**Component**|**Specification**|**Context**|
|---|---|---|
|**CPU**|Intel® Core™ i5-4590|4th Gen Haswell; 4 Cores / 4 Threads|
|**Clock Speed**|3.3 GHz Base|Sufficient for multiple lightweight Linux VMs|
|**RAM**|16GB DDR3|Primary bottleneck for VM density|
|**Virtualization**|Intel® VT-x / VT-d|Mandatory BIOS flags for Proxmox functionality|

## 3. Storage Architecture

The storage layout is partitioned to balance OS stability with bulk data availability.

- **Boot Drive (OS/ISO):** 128GB SSD.
    
    - _Partitioning:_ Hosted Proxmox PVE + `local` storage for ISO images.
        
- **Bulk Storage:** 500GB HDD.
    
    - _Purpose:_ Intended for future automated backups or large-scale file storage.
        

## 4. Network Interface (Physical)

- **NIC:** Integrated Intel® Gigabit Ethernet.
    
- **Connection:** 100ft Ethernet run terminated at the Cox Panoramic Box.