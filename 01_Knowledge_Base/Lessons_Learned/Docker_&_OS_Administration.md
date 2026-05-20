# Lessons Learned: Docker & OS Administration

This document tracks all the random operating system bugs, user privilege issues, and terminal roadblocks we hit while managing packages and setting up secure remote access.

## INC-002: The Docker Group Privilege Check

### The Symptom

Right after setting up Docker, trying to run a basic command like `docker ps` or spinning up a container threw a permission error unless you smacked `sudo` in front of every single command.

### The Core Problem

By default, the Docker daemon binds to a Unix socket that is owned by the `root` user. If you are logged in as a normal user (like `drew`), the system blocks you from touching that socket for security reasons.

### The Takeaway

To run Docker commands without constantly needing root privileges, your user account must be explicitly added to the system's `docker` security group using `sudo usermod -aG docker $USER`. **Crucial step:** The group change won't actually apply until you completely log out of your SSH session and log back in to refresh your user tokens.

## INC-006: The Tailscale School Domain Lockout

### The Symptom

While trying to log into Tailscale on a new node to set up a secure remote tailnet, the client threw a completely random "Your account is unpaid" or seat-limit error and refused to authorize.

### The Core Problem

Many massive SaaS platforms (like Tailscale) use the domain name of your email address (like `@email.vccs.edu`) to automatically group users together into a single "Organization" account. Because thousands of students share that school domain, Tailscale thought you were trying to join the official college corporate network, which had already hit its maximum user seat limit.

### The Takeaway

Never use institutional, school, or work emails to manage your private home lab infrastructure. Stick to personal emails or separate GitHub accounts so you have total, independent administrative control over your networks. If you accidentally authenticate a machine to the wrong network, you can use the terminal command `tailscale up --force-reauth` to wipe the local key and force a fresh login screen.

## INC-007: The Windows OpenSSH Parity Gap

### The Symptom

You were trying to follow a standard Linux guide to push your workstation's public SSH key over to the server, but typing `ssh-copy-id drew@192.168.0.151` into PowerShell threw a red "command not recognized" error.

### The Core Problem

The `ssh-copy-id` tool is a built-in shell script found natively on macOS and Linux systems, but Microsoft's native OpenSSH port for Windows PowerShell completely leaves it out.

### The Takeaway

Don't assume your admin tools are identical across different operating systems. If you are on a Windows machine, you have to use a PowerShell pipe trick to manually read your local key file and append it directly to the remote server's file system:

PowerShell

```
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh drew@192.168.0.151 "cat >> ~/.ssh/authorized_keys"
```

## INC-008 & INC-009: The Rclone Shell Path & Session Crash

### The Symptom

While trying to configure Rclone for your offsite backups, PowerShell claimed the command didn't exist even though you just unzipped it. Shortly after, during the automated browser authentication step, your active terminal window randomly vanished into thin air.

### The Core Problem

Windows zip utilities love to create a "double folder" structure when extracting tools (e.g., nesting an `rclone-vX/` folder inside _another_ folder named `rclone-vX/`). If you don't look closely at your directory path, you'll be calling a binary that is sitting one level deeper. Second, when Rclone launched your default web browser to handle the Google Drive/Proton OAuth handshake, the quick redirect caused a minor desktop resource spike that crashed the active Windows Terminal process, killing the background SSH session instantly.

### The Takeaway

1. Always run a quick `ls` or `dir` to verify where your executable file is actually sitting before running it.
    
2. OAuth security tokens are strictly one-time-use. If your terminal window crashes mid-handshake, that specific token is completely dead. You have to log back into your server, clean up any ghost processes, and run the setup script fresh to get a brand-new handshake redirect link.
    

## Long-Term Administration Guidelines

1. **The Lockout Rule:** Before you go into your server settings and completely disable password authentication to harden the machine, always verify that at least **two separate admin devices** (like your MacBook over Tailscale and your main PC over the local LAN) can successfully log in using their cryptographic SSH keys. If you skip this, a single typo in a config file can lock you out of your own hardware permanently.
    
2. **Centralized User Mapping:** When mapping persistent folders for apps, align everything to a single, non-root system user UID (like `drew` at `1000`). This stops different containers from picking random permission masks and locking each other out of shared directories on your data drives.