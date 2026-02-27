
## Resolved Issues

This log tracks actual technical hurdles encountered during the deployment of the Angel Server lab.

| **Date**       | **The "Break"**                                           | **Root Cause**                                                                   | **The Fix**                                                                                       | **Video Segment**  |
| -------------- | --------------------------------------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | ------------------ |
| **2026-02-15** | Ubuntu VM "Failed unmounting cdrom.mount" on first reboot | **ISO Lock:** The virtual CD/DVD drive was still holding the installation media. | **Post-Install Cleanup:** In Proxmox, set the VM Hardware CD/DVD drive to "Do not use any media." | Provisioning Phase |

## Lessons Learned

- **Hypervisor Priority:** Proxmox will attempt to boot from the "CD/DVD" drive first if an ISO is still mounted. Always unmount installation media immediately after the first successful install to ensure a smooth transition to the local disk.
    
- **Don't Panic on "Failed" Messages:** Some Linux error screens during reboot are simply the system waiting for the virtual media to be "ejected." Checking the hypervisor status first often reveals a simple fix.