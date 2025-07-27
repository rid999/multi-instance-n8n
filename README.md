Here's a refined and formatted README.md for your GitHub repository, making it clearer and more engaging:

🔁 Multi-instance n8n Setup on VPS (Docker-based)
This guide provides a robust solution for running multiple isolated n8n instances on a single Virtual Private Server (VPS) using Docker and Docker Compose. This setup is ideal for managing separate n8n environments for different clients, projects, or testing purposes, ensuring complete isolation of data and configurations.

📦 Requirements
Before you begin, make sure you have the following:

VPS: A Virtual Private Server running Ubuntu or Debian.

Docker & Docker Compose: Both installed on your VPS.

Open Ports: Ensure the desired ports for n8n instances (e.g., 5678, 5679) are open in your VPS firewall.

SSH Access: Secure Shell (SSH) access to your VPS.

🚀 Installation Steps
Follow these steps to set up your multi-instance n8n environment:

1. Install Docker & Docker Compose
If you don't already have Docker and Docker Compose installed, run the following commands:

Bash

sudo apt update && sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
2. Create Project Directory Structure
Organize your n8n project directories. Each project will have its own dedicated directory for data storage.

Bash

mkdir -p ~/multi-n8n/projectA ~/multi-n8n/projectB
cd ~/multi-n8n
This structure ensures:

Isolated Data Volumes: Each n8n instance will have its own data volume.

Separate Authentication: Unique credentials for each instance.

Dedicated Ports: Each instance will be accessible via a distinct port.

3. Create docker-compose.yml
Create a docker-compose.yml file in the ~/multi-n8n directory with the following content:

YAML

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
      - N8N_BASIC_AUTH_PASSWORD=projectApass # <--- CHANGE THIS PASSWORD
      - WEBHOOK_TUNNEL_URL=http://your-vps-ip:5678 # <--- REPLACE WITH YOUR VPS IP
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
      - N8N_BASIC_AUTH_PASSWORD=projectBpass # <--- CHANGE THIS PASSWORD
      - WEBHOOK_TUNNEL_URL=http://your-vps-ip:5679 # <--- REPLACE WITH YOUR VPS IP
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5679
🔧 Important: Remember to replace your-vps-ip with your actual VPS IP address and update the N8N_BASIC_AUTH_PASSWORD for each project with strong, unique passwords.

4. Start the Containers
Navigate to the ~/multi-n8n directory and start your n8n instances using Docker Compose:

Bash

docker compose up -d
To verify that your containers are running, execute:

Bash

docker ps
5. Access Each n8n Instance
You can now access your n8n instances using the following URLs and credentials:

Instance	URL	Auth Credentials
Project A	http://your-vps-ip:5678	admin / projectApass
Project B	http://your-vps-ip:5679	admin / projectBpass

Export to Sheets
✅ Note: Use the credentials you defined in your docker-compose.yml file.

🔐 Optional: Setup with NGINX & SSL (Reverse Proxy)
For a more secure and user-friendly setup, you can expose each n8n instance via a subdomain with HTTPS using NGINX and Certbot. This allows you to use friendly URLs like projecta.example.com instead of IP addresses and ports.

Instance	Subdomain	Port
Project A	projecta.example.com	5678
Project B	projectb.example.com	5679

Export to Sheets
If you need assistance configuring NGINX as a reverse proxy with SSL, please feel free to ask for a full configuration example.

🔁 Auto-start on VPS Reboot
By default, Docker Compose is configured to automatically restart your n8n containers if the VPS reboots or the containers stop unexpectedly.

To manually restart all n8n instances:

Bash

docker compose restart
If you wish to disable the auto-restart behavior for a specific container, use:

Bash

docker update --restart=no [container_name]
📂 Data Persistence
Each n8n instance stores its critical data (including credentials, workflows, and execution history) in its own dedicated volume on your VPS:

~/multi-n8n/projectA → Maps to /home/node/.n8n inside the n8n_projectA container.

~/multi-n8n/projectB → Maps to /home/node/.n8n inside the n8n_projectB container.

You can easily back up these directories or bind-mount them to external storage solutions for enhanced data management and recovery.
