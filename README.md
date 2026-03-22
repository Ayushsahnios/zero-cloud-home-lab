# Zero Cloud Home Lab

<div align="center">

![Ubuntu](https://img.shields.io/badge/Ubuntu_Server-24.04-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Zorin](https://img.shields.io/badge/Zorin_OS-Linux-0CC1F3?style=for-the-badge&logo=zorin&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)
![Cloudflare](https://img.shields.io/badge/Cloudflare-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white)
![Pi-hole](https://img.shields.io/badge/Pi--hole-96060C?style=for-the-badge&logo=pi-hole&logoColor=white)
![Gitea](https://img.shields.io/badge/Gitea-609926?style=for-the-badge&logo=gitea&logoColor=white)
![Drone CI](https://img.shields.io/badge/Drone_CI-212121?style=for-the-badge&logo=drone&logoColor=white)

**3 Chromebooks. No AWS. No cloud bills. Just raw metal on the internet.**

*Self-hosted. Zero cloud. Always on. 100% yours.*

</div>

---

## 🌍 Live Services

| URL | Service | Machine | Status |
|-----|---------|---------|--------|
| [ayush1.xyz](https://ayush1.xyz) | Main website (Nginx) | CB#1 | ✅ Live |
| [grafana.ayush1.xyz](https://grafana.ayush1.xyz) | Grafana Monitoring | CB#1 | ✅ Live |
| [prometheus.ayush1.xyz](https://prometheus.ayush1.xyz) | Prometheus Metrics | CB#1 | ✅ Live |
| [portainer.ayush1.xyz](https://portainer.ayush1.xyz) | Portainer Docker UI | CB#1 | ✅ Live |
| [gitea.ayush1.xyz](https://gitea.ayush1.xyz) | Gitea Git Server | CB#2 | ✅ Live |
| [drone.ayush1.xyz](https://drone.ayush1.xyz) | Drone CI Pipelines | CB#2 | ✅ Live |

> All services exposed via Cloudflare Tunnel — zero open inbound ports, free HTTPS, works behind double NAT.

---

## 📸 The Setup

![Zero Cloud Home Lab](setup.jpeg)

| Shelf | Machine | OS | Role | RAM | Storage |
|-------|---------|-----|------|-----|---------|
| 🔝 Top | Chromebook #1 | Ubuntu Server 24.04 (CLI) | Web Server + Docker + Cloudflare Tunnel | 4GB | 16GB eMMC + 32GB `/home` |
| 🖥️ Middle | Chromebook #2 | Zorin OS 24 | Gitea + Drone CI + Pi-hole + DHCP | 4GB | 16GB eMMC |
| 💻 Bottom | Chromebook #3 | ChromeOS | Daily driver — SSH control panel | 4GB | 16GB eMMC |

> 3 Chromebooks on a rack shelf. Pi-hole network firewall. Cloudflare tunnel. Zero cloud. 💪

---

## 🌐 Network Architecture

```
┌─────────────────────┐        ┌─────────────────────┐
│  CB#1 Ubuntu Server │        │  CB#2 Zorin OS      │
│  192.168.0.103      │        │  192.168.0.192      │
│                     │        │                     │
│  Docker Engine      │        │  Docker Engine      │
│  ├── Nginx    :80   │        │  ├── Gitea   :3001  │
│  ├── Portainer:9000 │        │  ├── Drone   :8080  │
│  ├── Grafana  :3000 │        │  └── Pi-hole :80    │
│  ├── Prometheus:9090│        │                     │
│  ├── Node Exp :9100 │        │  NFS Client         │
│  └── Cloudflared    │◄───────┤  /mnt/gitea-storage │
│                     │  NFS   │  → CB#1 storage     │
│  NFS Server         │        └─────────────────────┘
│  /home/gitea-storage│
│  ├── gitea/         │  ← Gitea repos + DB
│  └── drone/         │  ← Drone build data
└─────────────────────┘
    │
    │ Outbound QUIC (4 connections)
    ▼
Cloudflare Edge
    │
    ├── ayush1.xyz            → CB#1 :80   (Nginx)
    ├── grafana.ayush1.xyz    → CB#1 :3000 (Grafana)
    ├── prometheus.ayush1.xyz → CB#1 :9090 (Prometheus)
    ├── portainer.ayush1.xyz  → CB#1 :9000 (Portainer)
    ├── gitea.ayush1.xyz      → CB#2 :3001 (Gitea)
    └── drone.ayush1.xyz      → CB#2 :8080 (Drone CI)
    │
    ▼
Internet (HTTPS, free, no port forwarding needed)
```

---

## ✅ Roadmap

| # | Week | Topic | Status |
|---|------|-------|--------|
| 1 | Week 1 | Ubuntu Server + Docker + Nginx + Cloudflare Tunnel | ✅ Complete |
| 2 | Week 2 | Prometheus + Grafana Monitoring Stack | ✅ Complete |
| 3 | Week 3 | Gitea + Drone CI (Self-hosted GitOps) | ✅ Complete |
| 4 | Week 4 | k3s Kubernetes + App Manifests | ⏳ Pending |
| 5 | Week 5 | Terraform (Infrastructure as Code) | ⏳ Pending |
| 6 | Week 6 | SSL via Let's Encrypt + Domain Routing | ⏳ Pending |

---

## 🚀 Running Stack

| Service | Machine | Local IP | Port | Public URL | Purpose |
|---------|---------|----------|------|------------|---------|
| Nginx | CB#1 | 192.168.0.103 | `:80` | [ayush1.xyz](https://ayush1.xyz) | Web server / reverse proxy |
| Grafana | CB#1 | 192.168.0.103 | `:3000` | [grafana.ayush1.xyz](https://grafana.ayush1.xyz) | Monitoring dashboards |
| Prometheus | CB#1 | 192.168.0.103 | `:9090` | [prometheus.ayush1.xyz](https://prometheus.ayush1.xyz) | Metrics collection |
| Portainer | CB#1 | 192.168.0.103 | `:9000` | [portainer.ayush1.xyz](https://portainer.ayush1.xyz) | Docker management GUI |
| Node Exporter | CB#1 | 192.168.0.103 | `:9100` | - | Linux system metrics |
| Cloudflared | CB#1 | 192.168.0.103 | systemd | - | Cloudflare Tunnel — always on |
| Gitea | CB#2 | 192.168.0.192 | `:3001` | [gitea.ayush1.xyz](https://gitea.ayush1.xyz) | Self-hosted Git server |
| Drone CI | CB#2 | 192.168.0.192 | `:8080` | [drone.ayush1.xyz](https://drone.ayush1.xyz) | CI/CD pipelines |
| Pi-hole | CB#2 | 192.168.0.192 | `:80` | - | Network-wide ad blocking + DHCP |
| NFS Server | CB#1 | 192.168.0.103 | `2049` | - | Shared storage for Gitea + Drone |

---

## 📦 Week 1 — Web Server Setup (Chromebook #1)

### Install Ubuntu Server
```bash
sudo apt update && sudo apt upgrade -y

# Prevent sleep on lid close
sudo nano /etc/systemd/logind.conf
# Set: HandleLidSwitch=ignore
sudo systemctl restart systemd-logind
```

### Mount 32GB Extended Storage
```bash
lsblk
sudo mkfs.ext4 /dev/mmcblk1p1
sudo mount /dev/mmcblk1p1 /home
echo '/dev/mmcblk1p1 /home ext4 defaults 0 2' | sudo tee -a /etc/fstab
df -h
```

### Move Docker Storage to /home
```bash
sudo systemctl stop docker && sudo systemctl stop docker.socket
sudo mkdir -p /home/docker
sudo mv /var/lib/docker /home/docker/lib
sudo nano /etc/docker/daemon.json
```
```json
{ "data-root": "/home/docker/lib" }
```
```bash
sudo systemctl start docker
docker info | grep "Docker Root Dir"
```

### Install Docker
```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
newgrp docker
```

### Deploy Nginx
```bash
docker cp nginx:/usr/share/nginx/html/index.html ~/index.html
nano ~/index.html

docker run -d \
  --name nginx \
  --restart always \
  -p 80:80 \
  -v ~/index.html:/usr/share/nginx/html/index.html \
  nginx:alpine
```

### Deploy Portainer
```bash
docker volume create portainer_data
docker run -d \
  --name portainer \
  --restart always \
  -p 9000:9000 \
  -p 9443:9443 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce
```

---

## ☁️ Cloudflare Tunnel Setup (Named Tunnel — Permanent)

This lab runs behind **double NAT** (AT&T ISP router → Tenda router), making port forwarding impossible. Cloudflare Tunnel bypasses this with outbound-only connections — no open ports needed.

### Install cloudflared
```bash
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null
echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared focal main' | sudo tee /etc/apt/sources.list.d/cloudflared.list
sudo apt update && sudo apt install cloudflared -y
```

### Fix IPv6 Issues (if tunnel create times out)
```bash
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
echo "net.ipv6.conf.all.disable_ipv6=1" | sudo tee -a /etc/sysctl.conf
echo "net.ipv6.conf.default.disable_ipv6=1" | sudo tee -a /etc/sysctl.conf
```

### Authenticate and Create Tunnel
```bash
cloudflared tunnel login
cloudflared tunnel --no-autoupdate create zero-cloud
```

### Config File (`/etc/cloudflared/config.yml`)
```yaml
tunnel: <YOUR-TUNNEL-UUID>
credentials-file: /etc/cloudflared/<YOUR-TUNNEL-UUID>.json

ingress:
  - hostname: ayush1.xyz
    service: http://localhost:80
  - hostname: www.ayush1.xyz
    service: http://localhost:80
  - hostname: grafana.ayush1.xyz
    service: http://localhost:3000
  - hostname: prometheus.ayush1.xyz
    service: http://localhost:9090
  - hostname: portainer.ayush1.xyz
    service: http://localhost:9000
  - hostname: gitea.ayush1.xyz
    service: http://192.168.0.192:3001
  - hostname: drone.ayush1.xyz
    service: http://192.168.0.192:8080
  - service: http_status:404
```

### Add DNS Routes
```bash
cloudflared tunnel route dns zero-cloud ayush1.xyz
cloudflared tunnel route dns zero-cloud www.ayush1.xyz
cloudflared tunnel route dns zero-cloud grafana.ayush1.xyz
cloudflared tunnel route dns zero-cloud prometheus.ayush1.xyz
cloudflared tunnel route dns zero-cloud portainer.ayush1.xyz
cloudflared tunnel route dns zero-cloud gitea.ayush1.xyz
cloudflared tunnel route dns zero-cloud drone.ayush1.xyz
```

### Install as systemd Service
```bash
sudo cloudflared --config /etc/cloudflared/config.yml service install
sudo systemctl enable cloudflared
sudo systemctl start cloudflared
sudo systemctl status cloudflared
```

---

## 📊 Week 2 — Monitoring Stack (Chromebook #1)

### `docker-compose.yml`
```yaml
services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    restart: always
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus

  grafana:
    image: grafana/grafana
    container_name: grafana
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=Newpassword123

  node-exporter:
    image: prom/node-exporter
    container_name: node-exporter
    restart: always
    ports:
      - "9100:9100"

volumes:
  prometheus_data:
  grafana_data:
```

### `prometheus.yml`
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']
```

### Configure Grafana
```
1. Open https://grafana.ayush1.xyz
2. Login: admin / Newpassword123
3. Connections → Data Sources → Prometheus
4. URL: http://prometheus:9090 → Save & Test ✅
5. Dashboards → Import → ID: 1860 → Import
```

### Reset Grafana Password
```bash
docker exec -it grafana grafana-cli admin reset-admin-password Newpassword123
```

---

## 🔀 Week 3 — Gitea + Drone CI (Chromebook #2)

Gitea and Drone run on CB#2 but store all data on CB#1 via NFS.

### Step 1: NFS Server on CB#1
```bash
sudo apt install nfs-kernel-server -y
sudo mkdir -p /home/gitea-storage/gitea
sudo mkdir -p /home/gitea-storage/drone
sudo chown -R nobody:nogroup /home/gitea-storage
echo '/home/gitea-storage 192.168.0.192(rw,sync,no_subtree_check,no_root_squash)' | sudo tee -a /etc/exports
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
```

### Step 2: NFS Client on CB#2
```bash
sudo apt install nfs-common -y
sudo mkdir -p /mnt/gitea-storage
sudo mount 192.168.0.103:/home/gitea-storage /mnt/gitea-storage
echo '192.168.0.103:/home/gitea-storage /mnt/gitea-storage nfs defaults 0 0' | sudo tee -a /etc/fstab
sudo chown -R addblock:addblock /mnt/gitea-storage
```

### Step 3: docker-compose.yml on CB#2
```yaml
services:
  gitea:
    image: gitea/gitea:latest
    container_name: gitea
    restart: always
    ports:
      - "3001:3000"
      - "222:22"
    volumes:
      - /mnt/gitea-storage/gitea:/data
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__database__DB_TYPE=sqlite3
      - GITEA__server__DOMAIN=gitea.ayush1.xyz
      - GITEA__server__ROOT_URL=https://gitea.ayush1.xyz
      - GITEA__server__SSH_DOMAIN=gitea.ayush1.xyz

  drone:
    image: drone/drone:2
    container_name: drone
    restart: always
    ports:
      - "8080:80"
    volumes:
      - /mnt/gitea-storage/drone:/data
    environment:
      - DRONE_GITEA_SERVER=https://gitea.ayush1.xyz
      - DRONE_GITEA_CLIENT_ID=<your-oauth-client-id>
      - DRONE_GITEA_CLIENT_SECRET=<your-oauth-client-secret>
      - DRONE_SERVER_HOST=drone.ayush1.xyz
      - DRONE_SERVER_PROTO=https
      - DRONE_RPC_SECRET=<your-rpc-secret>
```

### Step 4: Register Drone OAuth App in Gitea
```
1. https://gitea.ayush1.xyz → Profile → Settings → Applications
2. Create OAuth2 Application:
   Name: Drone CI
   Redirect URI: https://drone.ayush1.xyz/login
3. Copy Client ID + Secret → add to docker-compose.yml
4. docker compose down && docker compose up -d
```

### Step 5: First CI Pipeline
```yaml
# .drone.yml in any repo
kind: pipeline
type: docker
name: default

steps:
  - name: build
    image: alpine
    commands:
      - echo "Zero Cloud Lab CI 🚀"
      - echo "Build successful!"
```

---

## 🕳️ Pi-hole Setup (Chromebook #2)

```bash
curl -sSL https://install.pi-hole.net | bash
# Settings → DHCP → Enable as DHCP server
pihole -c              # stats
pihole updateGravity   # update blocklists
```

---

## 🐳 Docker Cheatsheet

```bash
docker ps                            # running containers
docker ps -a                         # all containers
docker logs -f nginx                 # follow logs
docker stats                         # live CPU/RAM
docker exec -it nginx sh             # shell into container
docker cp file.html nginx:/usr/share/nginx/html/
docker compose up -d                 # start stack
docker compose down                  # stop stack
docker system prune -a               # cleanup
```

---

## 🖥️ System Cheatsheet

```bash
df -h && free -h && lsblk           # disk, RAM, storage
ip addr show | grep "192.168"       # find local IP
sudo ss -tlnp                       # listening ports

# SSH
ssh ayush1@192.168.0.103            # CB#1
ssh addblock@192.168.0.192          # CB#2

# Cloudflare
sudo systemctl status cloudflared
sudo systemctl restart cloudflared
sudo journalctl -u cloudflared -f

# NFS
sudo exportfs -v                    # show exports (CB#1)
df -h | grep gitea                  # check mount (CB#2)
```

---

## 📁 Folder Structure

```
CB#1 Ubuntu Server (192.168.0.103)
├── ~/index.html                    ← nginx landing page
├── ~/server/monitoring/
│   ├── docker-compose.yml
│   └── prometheus.yml
├── /etc/cloudflared/
│   ├── config.yml
│   └── <UUID>.json
├── /home/docker/lib/               ← Docker storage (32GB)
└── /home/gitea-storage/
    ├── gitea/                      ← repos + DB (NFS share)
    └── drone/                      ← build data (NFS share)

CB#2 Zorin OS (192.168.0.192)
├── ~/gitea/docker-compose.yml
└── /mnt/gitea-storage/             ← NFS mount from CB#1
```

---

## 🔧 Troubleshooting

<details>
<summary><b>Tunnel shows error 1033</b></summary>

```bash
sudo systemctl status cloudflared
sudo systemctl start cloudflared
sudo cloudflared --config /etc/cloudflared/config.yml service install
```
</details>

<details>
<summary><b>cloudflared tunnel create times out</b></summary>

```bash
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
cloudflared tunnel --no-autoupdate create zero-cloud
```
</details>

<details>
<summary><b>Grafana not showing data</b></summary>

```bash
# Connections → Data Sources → Prometheus
# URL: http://prometheus:9090 → Save & Test
```
</details>

<details>
<summary><b>Gitea/Drone down after CB#2 reboot</b></summary>

```bash
df -h | grep gitea
sudo mount 192.168.0.103:/home/gitea-storage /mnt/gitea-storage
cd ~/gitea && docker compose up -d
```
</details>

<details>
<summary><b>502 Bad Gateway</b></summary>

```bash
docker ps && curl http://localhost:80
docker logs nginx && docker restart nginx
```
</details>

<details>
<summary><b>32GB /home not mounted after reboot</b></summary>

```bash
sudo mount /dev/mmcblk1p1 /home
echo '/dev/mmcblk1p1 /home ext4 defaults 0 2' | sudo tee -a /etc/fstab
```
</details>

---

## 🔐 Security Checklist

- [x] SSH key auth only (no passwords)
- [x] Root SSH login disabled
- [x] Cloudflare Named Tunnel (zero open inbound ports)
- [x] Free HTTPS on all subdomains via Cloudflare
- [x] Pi-hole network-wide ad blocking + DHCP
- [x] Docker storage on separate 32GB partition
- [x] NFS storage isolated to LAN only
- [x] Gitea + Drone CI OAuth integration
- [ ] Cloudflare Access in front of Prometheus + Portainer
- [ ] Fail2ban on all machines (Week 6)
- [ ] UFW hardening on CB#2 (Week 6)

---

## 📚 Resources

- [Cloudflare Tunnel Docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Gitea Docs](https://docs.gitea.com/)
- [Drone CI Docs](https://docs.drone.io/)
- [Pi-hole Docs](https://docs.pi-hole.net/)
- [Prometheus Docs](https://prometheus.io/docs/)
- [Grafana Docs](https://grafana.com/docs/)
- [Docker Docs](https://docs.docker.com/)
- [roadmap.sh/devops](https://roadmap.sh/devops)

---

## 🐙 Push to GitHub

```bash
mkdir zero-cloud-home-lab && cd zero-cloud-home-lab
cp ~/server/monitoring/docker-compose.yml ./monitoring/
cp ~/server/monitoring/prometheus.yml ./monitoring/
# Remove secrets from gitea/docker-compose.yml before pushing!

git init
git add .
git commit -m "feat: week 3 complete — gitea + drone CI + NFS"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/zero-cloud-home-lab.git
git push -u origin main
```

---

<div align="center">

**Built by Ayush** · March 2026 · Zero Cloud Home Lab

*3 Chromebooks. 1 rack. Zero AWS. No AWS was harmed in the making of this project.*

*Leave a ⭐ if this inspired you.*

</div>
