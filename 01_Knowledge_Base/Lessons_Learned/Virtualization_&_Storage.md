# Lessons Learned: Virtualization & Storage

This document covers everything we learned the hard way about hypervisors, VM provisioning, unprivileged containers, and network file storage. If the server is acting up during boot or screaming about file permissions, check these notes.

## INC-001: The ISO Media Boot Loop

### The Symptom

After finishing the initial Ubuntu Server installation, hitting "reboot" caused the VM to hang or boot straight back into the installer wizard instead of loading the fresh OS.

### The Core Problem

Proxmox virtual machines treat ISO files exactly like physical CD/DVD discs. By default, the VM's boot order prioritizes the CD drive over the virtual hard drive. If you don't remove the "disc," the VM will just keep booting the installer forever.

### The Takeaway

Always do a post-install cleanup. As soon as an OS finishes installing, go to **Proxmox > Your VM > Hardware > CD/DVD Drive**, edit it, and flip it to **Do not use any media**.

## INC-010: The Unprivileged LXC Permission Wall

### The Symptom

An NFS share was successfully mounted from the storage VM onto the Proxmox host, and then passed into an unprivileged LXC container via a bind mount. Everything looked perfect, but trying to read or write any files inside the container spit out a massive `Permission Denied` error.

### The Core Problem

Unprivileged LXC containers are awesome for security, but they map user IDs weirdly to protect the host. The container shifts all UIDs up by `100000`. This means the standard `drew` user (UID `1000`) inside your container looks like UID `101000` to the NFS server. Because the NFS share only expects a connection from UID `1000`, it slams the door shut.

### The Takeaway

You have to manually punch a hole through the container's security mapping. You must edit the container’s configuration file on the Proxmox host (`/etc/pve/lxc/ID.conf`) to map local UID `1000` directly to host UID `1000`, and update `/etc/subuid` and `/etc/subgid` so root allows the pass-through.

## INC-011: The "Empty Rug" Mount Trap

### The Symptom

The Minecraft Bedrock server container inside the LXC booted up just fine, but players noticed their massive, existing world data was completely gone, replaced by a brand-new, empty starter world.

### The Core Problem

This is a classic boot-order race condition. If an LXC container starts up _before_ the Proxmox host has finished mounting the underlying NFS storage share, Docker sees an empty directory. Instead of waiting, Docker assumes you want a new setup, pulls a default file template, and populates the folder. When the NFS share finally mounts a few seconds later, it overlays the directory, completely "hiding" your actual files underneath the new ghost files.

### The Takeaway

In a decoupled lab setup (where a Storage VM serves files to an App LXC), boot sequencing is mandatory.

1. Make sure your storage provider VM has its Proxmox **Start Order** set to `1` with a startup delay of at least `60` seconds.
    
2. Set your application LXC container's **Start Order** to `2`. This guarantees the files are sitting on the table before the application walks into the room.
    

## INC-012: The Proxmox Infinite Boot Hang

### The Symptom

After restarting the bare-metal Dell OptiPlex host, the entire hypervisor completely froze during the boot sequence and refused to load the Proxmox web interface.

### The Core Problem

The Proxmox host had a static network mount configuration added to its system file system table (`/etc/fstab`) pointing to the Ubuntu VM's NFS export. During a cold boot, Proxmox reads `/etc/fstab` and tries to mount every single drive before doing anything else. Because the Ubuntu VM lives _inside_ Proxmox, the VM wasn't running yet. Proxmox got stuck in a permanent loop waiting for a network share that couldn't start until Proxmox itself finished booting.

### The Takeaway

Never put a standard, aggressive network mount in a hypervisor's `/etc/fstab` without safety nets. Always append the parameters `_netdev` and `soft` to the mount options line.

- `_netdev` tells the host to wait until the network card is fully online before trying the mount.
    
- `soft` tells the host to stop trying and move on if the storage target doesn't respond, keeping a dead share from killing your entire server's boot sequence.
    

## Long-Term Architecture Guidelines

1. **Storage Mapping Efficiency:** Exporting one large, master directory share (like `/mnt/data`) from your main storage pool is way better than managing dozens of tiny individual network shares. You can easily add subfolders for new containers or LXCs later without constantly opening up and messing with `/etc/exports` on the storage controller.
    
2. **Snapshots vs Backups:** Always use Proxmox VM snapshots right before running a sketchy terminal script or doing an upgrade—it acts as an instant "undo" button. But remember, snapshots are not backups. If the local SSD dies, your snapshots die too. Keep offsite rclone tasks handling actual disaster recovery.