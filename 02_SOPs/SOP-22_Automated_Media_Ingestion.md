## 1. Purpose

This note covers how we set up our automated media downloading stack (qBittorrent, Radarr, Sonarr, and Prowlarr) using Docker. The main goal here is to use a single folder path (`/data`) across all our apps. This lets Linux use **Hardlinks**, which means a movie can sit in our download folder and our clean Jellyfin library folder at the exact same time without taking up double the hard drive space.

## 2. Lab Environment Reference

| **Attribute** | **Value** | **Context** |
| :--- | :--- | :--- |
| **Ingest Host VM** | `VM 103 (DOWNLOAD)` (`192.168.20.6`) | Our dedicated download machine |
| **Shared App Path** | `/data` | The inside folder all the apps use to talk |
| **Physical HDD Path** | `/mnt/data/jellymain` | Where the actual 500GB Toshiba drive is mounted |
| **VPN Network Layer** | `gluetun` (WireGuard) | Keeps our torrent traffic hidden through ProtonVPN |

## 3. Step 1: Making the Hard Drive Folders

Before launching our apps, we need to create the actual folders on our hard drive. If the folders don't match up exactly on the host system, the apps will copy files instead of linking them, wasting our storage space.

SSH into your download machine and run these commands to create your library folders:

```bash
# Create the raw torrent download folders
mkdir -p /mnt/data/jellymain/torrents/movies
mkdir -p /mnt/data/jellymain/torrents/tv

# Create the clean folders that Jellyfin will actually look at
mkdir -p /mnt/data/jellymain/movies
mkdir -p /mnt/data/jellymain/tv

# Fix permissions so our regular 'drew' user owns the files
sudo chown -R 1000:1000 /mnt/data/jellymain