# RAG System Deployment Guide

This repository provides a **step-by-step guide** to deploy a **Retrieval-Augmented Generation (RAG)** system using the open-source engine [RAGFlow](https://github.com/infiniflow/ragflow). This work is part of my internship experience as a **GenAI Engineer**.

## Caution: Architecture Compatibility

The server setup in this repository is based on the **ARM64** architecture (e.g., Apple Silicon Mac).

> If you are using an **x86-based** machine (e.g., Intel or AMD CPUs), please refer to the official [RAGFlow documentation](https://github.com/infiniflow/ragflow) for the appropriate Docker configuration, as the Dockerfiles may differ.

> You may find the website hard to use on mobile devices. Please refer to the `mobile` folder for more information.

## Clone the Repository

```bash
git clone https://github.com/Jayden-Xu/RAG
cd RAG
```

## Step 1: Host RAGFlow Locally

```bash
cd docker
docker compose -f docker-compose-macos.yml build
docker compose -f docker-compose-macos.yml up -d
```

Once the containers are built and started, they should be running in the background.

### Access the Web Interface

Open your browser and visit:

```
https://127.0.0.1
```

You should be redirected to the **RAGFlow frontend**.

### What’s Next? *(to be explained in detail in later parts)*

1. Add your **model APIs** through the interface.
2. Upload your **custom knowledge base**.
3. Start chatting with your local RAG system!

---

## Step 2: Host on a Public Domain and Allow External Access

### 1. Set up a DuckDNS Domain

Go to [DuckDNS](https://www.duckdns.org/) and log in or register. Create a subdomain of your choice.

Then retrieve your **public IP address** using:

```bash
curl ifconfig.me
```

Update the **Current IP** on DuckDNS with the result above.  
A **token** will be generated — keep it safe, as it will be used later with Let's Encrypt for SSL certification.

---

### 2. Configure Port Mapping on the Router

Log in to your router admin panel (e.g., `https://192.168.110.1`) and navigate to:

```
Devices → Manage → Config → Advanced → Port Mapping
```

Click the **Edit** button and ensure the following settings are in place:

- **External Port**: `5000`  
  → This is the port through which your DuckDNS domain (e.g., `yourdomain.duckdns.org:5000`) will be accessed from the internet.

- **Internal Port**: `81`  
  → The port on your local machine (e.g., Mac) that the router will forward requests to.

- **Internal IP Address**: Your local machine’s IP address (e.g., `192.168.110.12`)  
  → This must be the **internal IP** of the machine hosting RAGFlow.

This setup ensures that **external traffic** to your DuckDNS domain is correctly **routed to your machine** where RAGFlow is running.

---

## Step 3: Use Let's Encrypt to Enable HTTPS

To secure your domain with HTTPS, you'll use **Let's Encrypt** via **Certbot** and a DNS challenge script.

---

### 1. Check if Certbot and Nginx are installed

Run the following commands:

```bash
which certbot
which nginx
```

>If Certbot is not installed, install it using:

>```bash
>brew install certbot
>```

>After installation, run the following command to generate your certificate:

>```bash
>sudo certbot certonly --manual \
>  --preferred-challenges dns \
>  --manual-auth-hook ~/duckdns-hook.sh \
>  -d your_domain.duckdns.org
>```

> Replace `your_domain` with your actual DuckDNS subdomain.

>Follow the instructions shown in the terminal, and choose the options as prompted.

---

### 2. Verify Your `duckdns-hook.sh` Script

Make sure your DNS hook script includes the correct DuckDNS token.

Open the script to edit:

```bash
nano ~/duckdns-hook.sh
```

Ensure it contains:

```bash
token=<your-duckdns-token>
```

This script will automatically update the DNS TXT record for validation.

---

Once the certificate is issued, it will be saved under:

```
/etc/letsencrypt/live/your_domain.duckdns.org/
```

These certificate files will be referenced in the next step for NGINX configuration.

---

## Step 4: Configure NGINX for Reverse Proxy and HTTPS

Once your SSL certificate is ready, configure NGINX to forward external HTTPS traffic to your local RAGFlow service.

---

### Edit the NGINX Config File

Open the NGINX configuration file:

```bash
sudo nano /opt/homebrew/etc/nginx/nginx.conf
```

Ensure the following settings are in place:

#### `server` block:

```nginx
server {
    listen 81 ssl;
    server_name your_domain.duckdns.org;

    ssl_certificate /etc/letsencrypt/live/your_domain.duckdns.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your_domain.duckdns.org/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    location / {
        proxy_pass http://127.0.0.1:80;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

> Replace `your_domain.duckdns.org` with your actual DuckDNS domain.  
> Ensure the `listen` port (e.g. `81`) matches the **internal port** configured in your router's port forwarding settings.

---

### Start NGINX and Verify

Start NGINX:

```bash
sudo nginx
```

Check if NGINX is listening correctly on port `81`:

```bash
sudo lsof -i :81
```

If no errors are shown and the port is in use, everything is set up correctly.

---

### Test the Setup

Open your browser and visit:

```
https://your_domain.duckdns.org:5000
```

You should see the RAGFlow interface securely served over HTTPS.
