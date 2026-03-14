This log covers the configuration and commands for syncing the vault between the Angel Server and GitHub.
### Server Info

- **Hostname:** angel-node-01 (Tailscale)
    
- **Local IP:** 192.168.0.151
    
- **SSH User:** drew
    
- **Repo Path:** ~/repos/obsidian-vault.git
    

### Remotes

- **lab-server:** Primary private sync (Home Ubuntu Server)
    
- **origin:** Public backup (GitHub)
    

---

### Standard Sync Commands

**To Push Updates:**

1. `git add .`
    
2. `git commit -m "update message"`
    
3. `git push lab-server main`
    
4. `git push origin main`
    

**To Pull Updates:**

1. `git pull lab-server main`
    

---

### Setup / Config Notes

- **Initial Clone:** `git clone drew@angel-node-01:~/repos/obsidian-vault.git`
    
- **Rename Origin (Mac):** `git remote rename origin lab-server`
    
- **Add GitHub (SSH):** `git remote add origin git@github.com:AndrewMacGregor1/Angel-Server-Lab.git`
    

---

### Troubleshooting

- **Connection Timeout:** Ensure Ubuntu VM is active and Tailscale is connected.
    
- **Fat Finger Alert:** Watch for 192.18 (wrong) vs 192.168 (correct) :(.
    
- **SSH Key:** Ensure `id_ed25519.pub` is present in server `authorized_keys`.