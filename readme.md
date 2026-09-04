# 🏠 Home Media Server

Lightweight Docker-based home media server running on an Intel NUC.

## 🔖 Service Bookmarks

| Service            | URL                    |
| ------------------ | ---------------------- |
| 🎬 **Jellyfin**    | http://nuc.local:8096  |
| 📥 **qBittorrent** | http://nuc.local:8080  |
| 📋 **Dozzle**      | http://nuc.local:9999  |
| 📊 **Glances**     | http://nuc.local:61208 |

---

## 🐳 Services

### 🎬 Jellyfin

Media server for movies and TV shows.

**URL:** http://nuc.local:8096

Features:

* Intel Quick Sync hardware transcoding
* Intel GPU passthrough via `/dev/dri`
* Direct Play support
* Movies and TV shows stored separately

Media:

```text
/home/nuc/media/movies
/home/nuc/media/tv
```

Configuration:

```text
/home/nuc/jellyfin/config
/home/nuc/jellyfin/cache
```

---

### 📥 qBittorrent

Torrent client with a web interface.

**URL:** http://nuc.local:8080

Downloads:

```text
/home/nuc/media:/downloads
```

The common `/home/nuc/media` parent directory keeps downloads and media on the same filesystem, allowing hardlinks to be used later if required.

---

### 📋 Dozzle

Lightweight Docker container log viewer.

**URL:** http://nuc.local:9999

Dozzle uses the Docker socket in read-only mode:

```text
/var/run/docker.sock:/var/run/docker.sock:ro
```

---

### 📊 Glances

Lightweight system monitoring dashboard.

**URL:** http://nuc.local:61208

Monitors:

* CPU
* RAM
* Disk
* Network
* Processes
* Docker containers
* System information

---

## ⚡ Hardware Transcoding

Jellyfin has access to the Intel GPU through:

```yaml
devices:
  - /dev/dri:/dev/dri
```

Check GPU availability:

```bash
ls -l /dev/dri
```

Expected:

```text
card0
renderD128
```

In Jellyfin:

**Dashboard → Playback → Transcoding**

Select:

```text
Intel QuickSync (QSV)
```

Enable hardware encoding.

To monitor Intel GPU activity:

```bash
sudo intel_gpu_top
```

---

## 🚀 Installation

Clone the repository:

```bash
git clone <YOUR-REPOSITORY-URL>
cd <REPOSITORY-DIRECTORY>
```

Start all services:

```bash
docker compose up -d
```

Check status:

```bash
docker compose ps
```

---

## 🔄 Updating

Pull the latest images:

```bash
docker compose pull
```

Recreate containers:

```bash
docker compose up -d
```

---

## 🛠️ Useful Commands

### View status

```bash
docker compose ps
```

### View logs

```bash
docker compose logs -f
```

### Restart a service

```bash
docker compose restart jellyfin
```

### Check resource usage

```bash
docker stats
```

### Check Intel GPU

```bash
sudo intel_gpu_top
```

### Stop everything

```bash
docker compose down
```

---

## 📁 Directory Structure

```text
/home/nuc/
├── jellyfin/
│   ├── config/
│   └── cache/
│
├── qbittorrent/
│   └── config/
│
└── media/
    ├── movies/
    └── tv/
```

---

## 🔁 Container Restart Policy

All services use:

```yaml
restart: unless-stopped
```

Containers will automatically start again after the NUC reboots.

---

## 🔐 Security

This setup is intended for use on a trusted home LAN.

The Compose file does **not** contain passwords, API keys, or other credentials.

If exposing services outside the LAN, use appropriate authentication, HTTPS, and preferably a VPN or reverse proxy.

---

## 📌 Quick Access

**Media**

🎬 [Jellyfin](http://nuc.local:8096)

**Downloads**

📥 [qBittorrent](http://nuc.local:8080)

**Logs**

📋 [Dozzle](http://nuc.local:9999)

**Monitoring**

📊 [Glances](http://nuc.local:61208)
