### 1. Purpose

This procedure defines the transition from password-based authentication to **public-key cryptography** for SSH access. This hardening step mitigates the risk of brute-force attacks on the Ubuntu node and ensures that only authorized devices (like your home PC and your laptop at the IT lab assistant job) can establish a secure terminal session.

### 2. Lab Environment Reference

The following values are used for the **Angel Server** environment.

|**Attribute**|**Lab Value (Example)**|**Context**|
|---|---|---|
|**Primary User**|`drew`|Non-root administrative user|
|**Node Name**|`angel-node-01`|Primary Docker Host|
|**Internal IP**|`192.168.0.151`|Static Management IP|
|**SSH Service**|`OpenSSH`|Targeted service for hardening|

---

### 3. Generating the Key Pair

This step must be performed on your **local machine** (the device you are using to connect to the server).

**Step 1: Generate a modern Ed25519 key**

Open your terminal (PowerShell or Bash) and run:

```
ssh-keygen -t ed25519 -C "drew-work-laptop"
```

- **Action:** Press **Enter** to save to the default location.
    
- **Security:** Enter a strong passphrase for the key itself for "double" protection.
    

---

### 4. Deploying the Public Key to the Server

You must now tell `angel-node-01` to recognize your new key.

**Step 1: Copy the key using the automated tool**

```
ssh-copy-id -i ~/.ssh/id_ed25519.pub drew@192.168.0.151
```

- _Note: If you are on Windows without `ssh-copy-id`, you can manually paste the contents of your `id_ed25519.pub` file into `/home/drew/.ssh/authorized_keys` on the server._
    

**Step 2: Test the connection**

Attempt to login. You should be prompted for your **key passphrase**, not your **user password**.

```
ssh drew@192.168.0.151
```

---

### 5. Hardening the SSH Daemon

Once you've confirmed the key works, you must disable password logins entirely to lock the door behind you.

**Step 1: Edit the SSH configuration**

```
sudo nano /etc/ssh/sshd_config
```

**Step 2: Update the following directives**

Ensure these lines match exactly:

```
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no
```

**Step 3: Restart the service**

```
sudo systemctl restart ssh
```

---

### 6. Post-SOP Verification

- **Access Check:** Open a new terminal and verify you can still log in using your key.
    
- **Security Check:** Attempt to log in from a device _without_ the key; the server should immediately refuse the connection without asking for a password.
    
- **Firewall Status:** Confirm UFW still allows OpenSSH by running `sudo ufw status`.