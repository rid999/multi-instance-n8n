# multi-instance-n8n

# 🔁 Multi-instance n8n Setup on VPS (Docker-based)

This guide allows you to run multiple isolated **n8n** instances (e.g. for different clients or projects) on the same VPS using **Docker + Docker Compose**.

---

## 📦 Requirements

- VPS with Ubuntu/Debian 20,21,22,24
- Docker & Docker Compose installed
- Ports open (e.g. `5678`, `5679`)
- Basic shell access (SSH)

---

## 🚀 Installation Steps

### 1. Install Docker & Docker Compose (if not yet)

```bash
sudo apt update && sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker


**### 2. Create Project Directory Structure**
mkdir -p ~/multi-n8n/projectA ~/multi-n8n/projectB
cd ~/multi-n8n

Each project will have:

Its own data volume

Its own auth credentials

Its own exposed port


3. Create docker-compose.yml
Create the following docker-compose.yml file inside the ~/multi-n8n directory:

yaml
Copy
Edit
version: '3.7'

services:
  n8n_projectA:
    image: n8nio/n8n
    container_name: n8n_projectA
    ports:
      - "5678:5678"
    volumes:
      - ./projectA:/home/node/.n8n
    environment:
      - GENERIC_TIMEZONE=Asia/Jakarta
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=projectApass
      - WEBHOOK_TUNNEL_URL=http://your-vps-ip:5678
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5678

  n8n_projectB:
    image: n8nio/n8n
    container_name: n8n_projectB
    ports:
      - "5679:5679"
    volumes:
      - ./projectB:/home/node/.n8n
    environment:
      - GENERIC_TIMEZONE=Asia/Jakarta
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=projectBpass
      - WEBHOOK_TUNNEL_URL=http://your-vps-ip:5679
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5679
🔧 Replace your-vps-ip and passwords as needed.

4. Start the Containers
bash
Copy
Edit
docker compose up -d
Check running containers:

bash
Copy
Edit
docker ps
5. Access Each n8n Instance
Instance	URL	Auth (default)
Project A	http://your-vps-ip:5678	admin / projectApass
Project B	http://your-vps-ip:5679	admin / projectBpass

✅ Login with basic auth credentials you configured in docker-compose.yml.

🔐 Optional: Setup with NGINX & SSL (Reverse Proxy)
You can route each instance via subdomains (optional):

Instance	Subdomain	Port
Project A	projecta.example.com	5678
Project B	projectb.example.com	5679

Let me know if you want a pre-made NGINX + Certbot config for automatic HTTPS routing.

🔁 Auto-start on Reboot
Docker Compose will auto-restart containers unless manually stopped:

bash
Copy
Edit
docker compose restart
To disable auto-start, use:

bash
Copy
Edit
docker update --restart=no [container_name]
📂 Data Persistence
Each instance stores workflows and credentials in its own volume:

~/multi-n8n/projectA → /home/node/.n8n (in container)

~/multi-n8n/projectB → /home/node/.n8n (in container)

You can back these up or mount to external storage if needed.
