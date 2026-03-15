## 1. Purpose

This procedure defines the secure backup and redundancy strategy for **Ed25519 SSH private keys**. By establishing "Break Glass" access and off-site key storage, this SOP prevents a permanent lockout from the Angel Server in the event of local hardware failure or loss of the primary management device.

## 2. Lab Environment Reference

The following values are used for the **Angel Server** backup strategy.

|**Attribute**|**Value**|**Context**|
|---|---|---|
|**Primary Key Type**|`Ed25519`|High-security elliptic curve key|
|**Public Key Location**|`~/.ssh/authorized_keys`|Stored on Ubuntu Node (`.151`)|
|**Private Key Name**|`id_ed25519`|The "Key" stored on your client machine|
|**Emergency Access**|Proxmox Console|"Break Glass" physical terminal access|

## 3. The "3-2-1" Key Backup Strategy

To ensure redundancy, your private key should exist in three distinct locations.

**Step 1: Local Secure Backup (Encrypted)**

Do not store your private key in plain text on a cloud drive.

1. Copy your `id_ed25519` (private key) and `id_ed25519.pub` (public key) to a secure, encrypted vault like **Bitwarden** or **1Password**.
    
2. Label it clearly as `Angel-Server-SSH-Key`.
    

**Step 2: Physical Hardware Backup (Offline)**

1. Copy the key pair to a dedicated USB drive used for lab recovery.
    
2. Store this drive in a physical location separate from your Dell OptiPlex 9020.
    

**Step 3: Tailscale Mobile Access**

Since you have **Tailscale** installed, import your private key into a mobile SSH client (like **Termius** or **Blink**) on your phone.

- **Why?** This ensures that even if your main computer dies, you can still manage the server over the Tailscale Mesh VPN from your phone.
    

## 4. "Break Glass" Emergency Recovery

If all keys are lost, you must use the hypervisor's physical authority to regain access.

1. **Access Proxmox UI:** Navigate to `https://192.168.0.150:8006`.
    
2. **Open Console:** Select **VM 100 (angel-node-01)** and click **Console**.
    
3. **Login:** Use your physical user credentials (`drew`) to log in directly via the Proxmox virtual terminal.
    
4. **Restore Access:** - Generate a new key pair on a new device.
    
    - Use the Proxmox console to manually paste the new public key into `/home/drew/.ssh/authorized_keys`.
        

## 5. Post-SOP Verification

- **Redundancy Check:** Confirm the private key is visible in your encrypted vault.
    
- **Mobile Check:** Attempt to SSH into `192.168.0.151` via Tailscale using your phone while Wi-Fi is disabled.
    
- **Console Check:** Verify you can still log in to the Proxmox shell as `root@pam` to manage the VM hardware if needed.