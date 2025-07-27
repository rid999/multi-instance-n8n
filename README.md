###🔁 Multi-instance n8n Setup on VPS (Docker-based)
Panduan ini menyediakan solusi tangguh untuk menjalankan beberapa instance n8n yang terisolasi pada satu Virtual Private Server (VPS) menggunakan Docker dan Docker Compose. Pengaturan ini ideal untuk mengelola lingkungan n8n yang terpisah untuk klien, proyek, atau tujuan pengujian yang berbeda, memastikan isolasi penuh data dan konfigurasi.

📦 Persyaratan
Sebelum memulai, pastikan Anda memiliki hal-hal berikut:

VPS: Sebuah Virtual Private Server yang menjalankan Ubuntu atau Debian.

Docker & Docker Compose: Keduanya sudah terinstal di VPS Anda.

Port Terbuka: Pastikan port yang diinginkan untuk instance n8n (misalnya, 5678, 5679) terbuka di firewall VPS Anda.

Akses SSH: Akses Secure Shell (SSH) ke VPS Anda.

🚀 Langkah-langkah Instalasi
Ikuti langkah-langkah ini untuk menyiapkan lingkungan n8n multi-instance Anda:

1. Instal Docker & Docker Compose
Jika Anda belum menginstal Docker dan Docker Compose, jalankan perintah berikut:

Bash

sudo apt update && sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
2. Buat Struktur Direktori Proyek
Atur direktori proyek n8n Anda. Setiap proyek akan memiliki direktori khusus sendiri untuk penyimpanan data.

Bash

mkdir -p ~/multi-n8n/projectA ~/multi-n8n/projectB
cd ~/multi-n8n
Struktur ini memastikan:

Volume Data Terisolasi: Setiap instance n8n akan memiliki volume datanya sendiri.

Autentikasi Terpisah: Kredensial unik untuk setiap instance.

Port Khusus: Setiap instance akan dapat diakses melalui port yang berbeda.

3. Buat docker-compose.yml
Buat file docker-compose.yml di direktori ~/multi-n8n dengan konten berikut:

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
      - N8N_BASIC_AUTH_PASSWORD=projectApass # <--- GANTI DENGAN SANDI ANDA
      - WEBHOOK_TUNNEL_URL=http://your-vps-ip:5678 # <--- GANTI DENGAN IP VPS ANDA
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
      - N8N_BASIC_AUTH_PASSWORD=projectBpass # <--- GANTI DENGAN SANDI ANDA
      - WEBHOOK_TUNNEL_URL=http://your-vps-ip:5679 # <--- GANTI DENGAN IP VPS ANDA
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5679
🔧 Penting: Ingatlah untuk mengganti your-vps-ip dengan alamat IP VPS Anda yang sebenarnya dan perbarui N8N_BASIC_AUTH_PASSWORD untuk setiap proyek dengan sandi yang kuat dan unik.

4. Mulai Kontainer
Arahkan ke direktori ~/multi-n8n dan mulai instance n8n Anda menggunakan Docker Compose:

Bash

docker compose up -d
Untuk memverifikasi bahwa kontainer Anda berjalan, jalankan:

Bash

docker ps
5. Akses Setiap Instance n8n
Anda sekarang dapat mengakses instance n8n Anda menggunakan URL dan kredensial berikut:

Instance	URL	Kredensial Autentikasi
Project A	http://your-vps-ip:5678	admin / projectApass
Project B	http://your-vps-ip:5679	admin / projectBpass

Export to Sheets
✅ Catatan: Gunakan kredensial yang Anda definisikan di file docker-compose.yml Anda.

🔐 Opsional: Pengaturan dengan NGINX & SSL (Reverse Proxy)
Untuk pengaturan yang lebih aman dan mudah digunakan, Anda dapat mengekspos setiap instance n8n melalui subdomain dengan HTTPS menggunakan NGINX dan Certbot. Ini memungkinkan Anda menggunakan URL yang ramah seperti projecta.example.com alih-alih alamat IP dan port.

Instance	Subdomain	Port
Project A	projecta.example.com	5678
Project B	projectb.example.com	5679

Export to Sheets
Jika Anda memerlukan bantuan untuk mengonfigurasi NGINX sebagai reverse proxy dengan SSL, jangan ragu untuk meminta contoh konfigurasi lengkap.

🔁 Auto-start pada Reboot VPS
Secara default, Docker Compose dikonfigurasi untuk secara otomatis memulai ulang kontainer n8n Anda jika VPS di-reboot atau kontainer berhenti secara tak terduga.

Untuk memulai ulang semua instance n8n secara manual:

Bash

docker compose restart
Jika Anda ingin menonaktifkan perilaku auto-restart untuk kontainer tertentu, gunakan:

Bash

docker update --restart=no [container_name]
📂 Persistensi Data
Setiap instance n8n menyimpan data pentingnya (termasuk kredensial, alur kerja, dan riwayat eksekusi) dalam volume khusus di VPS Anda:

~/multi-n8n/projectA → Memetakan ke /home/node/.n8n di dalam kontainer n8n_projectA.

~/multi-n8n/projectB → Memetakan ke /home/node/.n8n di dalam kontainer n8n_projectB.
