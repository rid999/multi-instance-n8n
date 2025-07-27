# multi-instance-n8n

This guide allows you to run multiple isolated n8n instances (e.g. for different clients or projects) on the same VPS using Docker + Docker Compose.

📦 Requirements
VPS with Ubuntu/Debian

Docker & Docker Compose installed

Ports open (e.g. 5678, 5679)

Basic shell access (SSH)

🚀 Installation Steps
1. Install Docker & Docker Compose (if not yet)
bash
Copy
Edit
sudo apt update && sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
2. Directory Structure
bash
Copy
Edit
mkdir -p ~/multi-n8n/projectA ~/multi-n8n/projectB
cd ~/multi-n8n
Each project will have:

Its own data volume

Its own auth credentials

Its own port

3. Create docker-compose.yml
Create the following docker-compose.yml in ~/multi-n8n:

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
Replace your-vps-ip and passwords as needed.

4. Start the Containers
bash
Copy
Edit
docker compose up -d
To check status:

bash
Copy
Edit
docker ps
5. Access Each n8n Instance
🌐 http://your-vps-ip:5678 → Project A

🌐 http://your-vps-ip:5679 → Project B

Login with basic auth credentials you set (admin / projectApass or projectBpass).

🔐 Optional: Setup with NGINX & SSL (Reverse Proxy)
You can route each instance via subdomains:

Instance	Subdomain	Port
Project A	projecta.example.com	5678
Project B	projectb.example.com	5679

Let me know if you want a NGINX + Certbot config for auto-HTTPS + routing.

🔁 Auto-start on Reboot
Docker Compose keeps containers running by default unless stopped manually. You’re good to go.

📂 Data Persistence
Each project’s data is stored in its respective volume:

~/multi-n8n/projectA → /home/node/.n8n in container

~/multi-n8n/projectB → same
