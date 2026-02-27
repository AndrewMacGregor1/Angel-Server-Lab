## 1. Purpose

This procedure defines the configuration steps for creating a Virtual Machine (VM) "shell" within Proxmox VE. This provides the virtualized hardware environment necessary to install an Operating System (OS).

## 2. Lab Environment Reference

The following values are used for the **Angel Server** lab environment. These values are optimized for the reference hardware (i5-4590, 16GB RAM).

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**VM ID**|`100`|Primary VM Identifier|
|**VM Name**|`ubuntu-server-01`|Inventory Name|
|**CPU Cores**|`2`|Resource allocation|
|**Memory**|`4096 MiB`|4GB RAM allocation|
|**Disk Size**|`32 GB`|Virtual disk capacity|

## 3. The "Create VM" Wizard

Access the Proxmox Web UI (e.g., `https://192.168.0.150:8006`) and click the **Create VM** button in the top right corner.

### **A. General Tab**

- **Node:** Select the target node (e.g., `angel-server`).
    
- **VM ID:** Assign a unique ID (defaults start at `100`).
    
- **Name:** Enter a descriptive hostname (e.g., `ubuntu-server-01`).
    

### **B. OS Tab**

- **ISO Image:** Select the storage location (e.g., `local`) and the ISO file uploaded in [SOP-02](https://www.google.com/search?q=./SOP-02_ISO_Management.md).
    
- **Type:** Ensure "Linux" is selected with the appropriate kernel version.
    

### **C. System Tab**

- **Qemu Agent:** **Check** this box. This allows the host and guest OS to communicate status information, such as IP addresses.
    
- **Graphics card:** Keep as default (Default) unless specific PCIe pass-through is required.
    

### **D. Disks Tab**

- **Storage:** Select the volume designated for virtual disks (e.g., `local-lvm`).
    
- **Disk Size:** Allocate the required space (e.g., `32 GB`).
    

### **E. CPU Tab**

- **Cores:** Allocate CPU resources (e.g., `2`).
    
- **Type:** Select `host`. This passes the specific instruction sets of the physical CPU directly to the VM for optimal performance.
    

### **F. Memory Tab**

- **Memory:** Enter the desired RAM allocation (e.g., `4096` for 4GB).
    

### **G. Network Tab**

- **Bridge:** Select `vmbr0`. This links the virtual network interface to the physical Ethernet port on the host.
    

## 4. Finalization

- **Confirm:** Review the configuration summary.
    
- **Execution:** Click **Finish** to provision the VM.
    
- **Verification:** Ensure the new VM appears in the resource tree under the host node.