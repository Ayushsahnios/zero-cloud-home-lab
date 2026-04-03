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
![k3s](https://img.shields.io/badge/k3s-Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)

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
| 🔝 Top | Chromebook #1 | Ubuntu Server 24.04 (CLI) | k3s Control Plane + Cloudflare Tunnel + NFS | 4GB | 16GB eMMC + 500GB SSD |
| 🖥️ Middle | Chromebook #2 | Zorin OS 24 | k3s Worker #1 + Pi-hole + DHCP | 4GB | 16GB eMMC |
| 💻 Bottom | Chromebook #3 | PostmarketOS (ARM) | k3s Worker #2 | 4GB | 28GB eMMC |

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
| 4 | Week 4 | k3s Kubernetes — 3 Node Cluster + Service Migration | ✅ Complete |
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
| Filebrowser | CB#1 | 192.168.0.103 | `:8085` | [ayush1.xyz/filesystem](https://ayush1.xyz/filesystem) | File upload/download UI |
| Node Exporter | ALL | all nodes | `:9100` | - | Linux metrics (DaemonSet) |
| Cloudflared | CB#1 | 192.168.0.103 | systemd | - | Cloudflare Tunnel — always on |
| Gitea | CB#2 | 192.168.0.192 | `:3001` | [gitea.ayush1.xyz](https://gitea.ayush1.xyz) | Self-hosted Git server |
| Drone CI | CB#2 | 192.168.0.192 | `:8080` | [drone.ayush1.xyz](https://drone.ayush1.xyz) | CI/CD pipelines |
| Pi-hole | CB#2 | 192.168.0.192 | `:80` | - | Network-wide ad blocking + DHCP |
| NFS Server | CB#1 | 192.168.0.103 | `2049` | - | Shared storage for Gitea + Drone |
| k3s Server | CB#1 | 192.168.0.103 | `6443` | - | Kubernetes control plane |
| k3s Agent | CB#2 | 192.168.0.192 | - | - | Kubernetes worker node #1 |
| k3s Agent | CB#3 | 192.168.0.64 | - | - | Kubernetes worker node #2 |

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

### Mount 500GB SSD (3 Partitions)
```bash
# Partition the SSD
sudo fdisk /dev/sda
# sda1 → 250GB → /home
# sda2 → 200GB → /mnt/media (Jellyfin)
# sda3 →  17GB → swap

# Format
sudo mkfs.ext4 -F /dev/sda1
sudo mkfs.ext4 -F /dev/sda2
sudo mkswap /dev/sda3
sudo swapon /dev/sda3

# Mount
sudo mount /dev/sda1 /home
sudo mkdir -p /mnt/media
sudo mount /dev/sda2 /mnt/media

# Make permanent
echo '/dev/sda1 /home ext4 defaults,commit=1,barrier=1 0 2' | sudo tee -a /etc/fstab
echo '/dev/sda2 /mnt/media ext4 defaults,commit=1,barrier=1 0 2' | sudo tee -a /etc/fstab
echo '/dev/sda3 none swap sw 0 0' | sudo tee -a /etc/fstab

df -h
```

### SSD Layout
```
sda (500GB SSD)
├── sda1  250GB → /home          (Docker, configs, gitea NFS share)
├── sda2  200GB → /mnt/media     (Jellyfin movies — future)
└── sda3   17GB → swap           (RAM overflow)
```

### Move Containerd to SSD (free up root partition)
```bash
sudo systemctl stop docker && sudo systemctl stop containerd
sudo mv /var/lib/containerd /home/containerd
sudo ln -s /home/containerd /var/lib/containerd
sudo systemctl start containerd && sudo systemctl start docker
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

## ☸️ Week 4 — k3s Kubernetes (3 Node Cluster)

### Why k3s?
k3s is lightweight Kubernetes — perfect for low-resource machines like Chromebooks. All services migrated from Docker to k3s for auto-healing, scheduling and centralized management.

### Cluster Layout
```
CB#1 (ayush1)        → Control Plane — API Server, Scheduler, etcd
CB#2 (addblock-paine) → Worker Node #1 — x86_64
CB#3 (ayush2)        → Worker Node #2 — ARM (PostmarketOS)
```

### Step 1: Install k3s on CB#1 (Control Plane)
```bash
curl -sfL https://get.k3s.io | sh -

# Check it's running
sudo systemctl status k3s
sudo kubectl get nodes

# Get join token for worker nodes
sudo cat /var/lib/rancher/k3s/server/node-token

# Disable Traefik (using Cloudflare tunnel instead)
sudo nano /etc/systemd/system/k3s.service
# Add --disable traefik to ExecStart
sudo systemctl daemon-reload && sudo systemctl restart k3s
```

### Step 2: Join Worker Nodes (CB#2 and CB#3)
```bash
# Run on CB#2 and CB#3
curl -sfL https://get.k3s.io | \
  K3S_URL=https://192.168.0.103:6443 \
  K3S_TOKEN=<token-from-cb1> sh -

# Verify all nodes joined
sudo kubectl get nodes
# NAME             STATUS   ROLES           VERSION
# ayush1           Ready    control-plane   v1.34.5+k3s1
# addblock-paine   Ready    <none>          v1.34.5+k3s1
# ayush2           Ready    <none>          v1.34.5+k3s1
```

### Step 3: Label Worker Nodes
```bash
sudo kubectl label node addblock-paine node-role.kubernetes.io/worker=worker
sudo kubectl label node ayush2 node-role.kubernetes.io/worker=worker
```

### Step 4: Migrate Services from Docker to k3s

Used **Kompose** to convert docker-compose files to Kubernetes manifests:
```bash
# Install kompose
curl -L https://github.com/kubernetes/kompose/releases/download/v1.32.0/kompose-linux-amd64 -o kompose
chmod +x kompose && sudo mv kompose /usr/local/bin/

# Convert monitoring stack
cd ~/server/monitoring
kompose convert -o ~/k3s/monitoring/

# Convert gitea stack
kompose convert -f ~/k3s/gitea-compose.yml -o ~/k3s/gitea/
```

Key fixes needed after kompose conversion:
- Add `hostNetwork: true` → pods bind directly to host ports
- Add `nodeSelector` → pin services to correct nodes
- Replace PVC volumes with `hostPath` → use existing data

### Step 5: Deploy Everything
```bash
sudo kubectl apply -f ~/k3s/monitoring/
sudo kubectl apply -f ~/k3s/nginx.yaml
sudo kubectl apply -f ~/k3s/filebrowser.yaml
sudo kubectl apply -f ~/k3s/gitea/

# Watch pods come up
sudo kubectl get pods -o wide -w
```

### Step 6: Node Exporter DaemonSet (monitors ALL nodes)
```bash
cat > ~/k3s/node-exporter-daemonset.yaml << 'EOF'
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
  namespace: default
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      hostNetwork: true
      hostPID: true
      containers:
        - image: prom/node-exporter
          name: node-exporter
          args:
            - --web.listen-address=0.0.0.0:9100
          ports:
            - containerPort: 9100
              hostPort: 9100
EOF

sudo kubectl apply -f ~/k3s/node-exporter-daemonset.yaml
# Automatically runs on ALL 3 nodes ✅
```

### Useful kubectl Commands
```bash
sudo kubectl get pods -o wide --all-namespaces    # all pods + nodes
sudo kubectl get nodes                             # cluster status
sudo kubectl describe pod <name>                  # pod details
sudo kubectl logs <pod-name>                      # pod logs
sudo kubectl apply -f file.yaml                   # deploy
sudo kubectl delete deployment <name>             # remove
sudo kubectl get pods -o wide -w                  # watch live
sudo kubectl top node                             # resource usage
sudo kubectl rollout restart deployment <name>    # restart pods
sudo kubectl get pods --field-selector spec.nodeName=ayush1  # pods on specific node
```

---

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
├── ~/.cloudflared/
│   ├── cert.pem
│   └── <UUID>.json
├── /home/                          ← 250GB SSD partition (sda1)
│   ├── ayush1/                     ← user home
│   ├── containerd/                 ← symlinked from /var/lib/containerd
│   └── gitea-storage/
│       ├── gitea/                  ← Gitea repos + DB (NFS share)
│       └── drone/                  ← Drone build data (NFS share)
├── /mnt/media/                     ← 200GB SSD partition (sda2) — Jellyfin future
└── swap                            ← 17GB SSD partition (sda3)

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
<summary><b>Hardware failure — SD card died after power cut</b></summary>

```bash
# Signs of dying SD card
# - Emergency mode on boot
# - "mmcblk1: recovery failed" in dmesg
# - "Card stuck being busy" errors
# - Buffer I/O errors on multiple blocks

# Fix 1: Comment out dead device in fstab
nano /etc/fstab
# Add # to the failing UUID line

# Fix 2: Force reboot
echo b > /proc/sysrq-trigger

# Fix 3: After reboot with new SSD
sudo mkfs.ext4 -F /dev/sda1
sudo mount /dev/sda1 /home

# Fix 4: Free up root partition
sudo mv /var/lib/containerd /home/containerd
sudo ln -s /home/containerd /var/lib/containerd
snap list --all | awk '/disabled/{print $1, $3}' | while read snapname revision; do sudo snap remove "$snapname" --revision="$revision"; done
sudo journalctl --vacuum-size=50M
```
</details>

<details>
<summary><b>Pi-hole blocking your own domains</b></summary>

```bash
# Whitelist your domains
sudo pihole allow ayush1.xyz
sudo pihole allow grafana.ayush1.xyz
sudo pihole allow prometheus.ayush1.xyz
sudo pihole allow portainer.ayush1.xyz
sudo pihole allow gitea.ayush1.xyz
sudo pihole allow drone.ayush1.xyz

# Add Google DNS as upstream backup
# Pi-hole admin → Settings → DNS → add 8.8.8.8, 8.8.4.4

sudo pihole reloaddns
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
- [x] Docker containerd on separate 500GB SSD partition
- [x] NFS storage isolated to LAN only
- [x] Gitea + Drone CI OAuth integration
- [x] Pi-hole domain whitelist for lab domains
- [x] k3s Kubernetes managing all services
- [x] PostmarketOS firewall on CB#3
- [ ] UPS battery backup (prevent power cut failures)
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
