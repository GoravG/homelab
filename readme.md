# 🏠 Home Media Server

Lightweight Docker-based home media server running on an Intel NUC.

## 🔖 Service Bookmarks

| Service              | URL                    |
| -------------------- | ---------------------- |
| 🎬 **Jellyfin**      | http://nuc.local:8096  |
| 📥 **qBittorrent**   | http://nuc.local:8080  |
| 💾 **Scrutiny**      | http://nuc.local:8081  |
| 📋 **Dozzle**        | http://nuc.local:9999  |
| 📊 **Glances**       | http://nuc.local:61208 |

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

### � Scrutiny

Disk health and SMART monitoring for the NUC storage drives.

**URL:** http://nuc.local:8081

Scrutiny watches the SSD and surfaces SMART status, temperature, and drive health metrics. This is useful for early warning signs like wear, reallocated sectors, or impending drive failure.

Configuration and data directories:

```text
/home/nuc/scrutiny/config
/home/nuc/scrutiny/data
```

The container is granted raw I/O access to the physical drive:

```yaml
devices:
  - "/dev/sda:/dev/sda"
```

---

### �📋 Dozzle

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

## ⚙️ Enable Intel Quick Sync

After starting Jellyfin, open the Jellyfin dashboard:

**Dashboard → Playback → Transcoding**

Set:

* **Hardware acceleration:** `Intel QuickSync (QSV)`
* **Enable hardware encoding:** ✅
* Enable the required **hardware decoding** codecs.

Then click **Save**.

### Verify Hardware Transcoding

Start a video that requires transcoding and run on the NUC:

```bash
sudo intel_gpu_top
```

You should see activity on the Intel GPU's **Video** engine during transcoding.

> **Note:** Quick Sync must be enabled from the Jellyfin Dashboard after the container is started. The Docker Compose configuration only provides Jellyfin access to the Intel GPU through `/dev/dri`.

---

## 🚀 Installation

Clone the repository:

```bash
git clone https://github.com/GoravG/homelab.git
cd homelab
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
├── scrutiny/
│   ├── config/
│   └── data/
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

**Storage Health**

💾 [Scrutiny](http://nuc.local:8081)

**Logs**

📋 [Dozzle](http://nuc.local:9999)

**Monitoring**

📊 [Glances](http://nuc.local:61208)
