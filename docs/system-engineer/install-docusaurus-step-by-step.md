---
title: Install Docusaurus Step by Step
sidebar_position: 1
---

# Install Docusaurus Step by Step

**Internal Knowledge Base – Production Ready (Nginx)**

---

---

## 1. Purpose

Dokumen ini menjelaskan **step-by-step instalasi Docusaurus**, sinkronisasi ke **GitHub**, workflow **edit & push via VS Code**, dan **deployment production internal menggunakan Nginx**.

Target audience:

- System Engineer
- Infra Engineer
- Platform Engineer

---

## 2. Architecture Overview

```
[ Developer Laptop ]
      |
      |  (git push)
v
[ GitHub Repository ]
      |
      |  (git pull)
v
[ Internal Server ]
      |
      |  (npm run build)
v
[ Nginx Static Web ]

```

---

## 3. Prerequisites

### 3.1 Local Machine (Developer)

- OS: Windows / macOS / Linux
- Node.js >= 18
- Git
- VS Code
- GitHub account (SSH access recommended)

### 3.2 Internal Server (Production)

- Ubuntu 20.04 / 22.04
- Internet access (for npm install)
- Nginx
- Git
- Node.js (build only, not runtime)

---

## 4. Local Environment Setup (Developer Laptop)

### 4.1 Install Node.js

📍 **Execute on: Local Machine**

```bash
node -v

```

Jika belum ada:

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x |sudo -E bash -
sudo apt install -y nodejs

```

---

### 4.2 Install Git

📍 **Execute on: Local Machine**

```bash
sudo apt install -y git
git --version

```

---

### 4.3 Configure Git Identity

📍 **Execute on: Local Machine**

```bash
git config --global user.name"yourname"
git config --global user.email"your@email.com"

```

---

## 5. Create GitHub Repository

📍 **Execute on: GitHub Web UI**

1. Create new repository
2. Name example:
    
    ```
    infra-knowledge-base
    
    ```
    
3. Visibility: Private (recommended)
4. ❌ Do NOT initialize README

Copy repository SSH URL:

```
git@github.com:USERNAME/infra-knowledge-base.git

```

---

## 6. Create Docusaurus Project

📍 **Execute on: Local Machine**

```bash
npx create-docusaurus@latest infra-kb classic
cd infra-kb

```

Start dev server:

```bash
npm start

```

Access:

```
http://localhost:3000

```

---

## 7. Initialize Git & Push to GitHub

📍 **Execute on: Local Machine**

```bash
git init
git branch -M main
git remote add origin git@github.com:USERNAME/infra-knowledge-base.git
git add .
git commit -m"Initial Docusaurus setup"
git push -u origin main

```

✅ Source of truth sekarang ada di GitHub

---

## 8. VS Code Workflow (Daily Usage)

📍 **Execute on: Local Machine**

Open project:

```bash
code .

```

### 8.1 Create Documentation Page

📍 **File location:**

```
docs/network/topology.md

```

Example:

```markdown
# Network Topology

This document describes internal network topology.

```

---

### 8.2 Live Preview

📍 **Execute on: Local Machine**

```bash
npm start

```

---

### 8.3 Commit & Push Changes

📍 **Execute on: Local Machine**

```bash
git status
git add .
git commit -m"Add network topology documentation"
git push

```

---

## 9. Build Static Site (Production Mode)

📍 **Execute on: Local Machine OR Server**

```bash
npm run build

```

Output directory:

```
build/

```

---

## 10. Deploy to Internal Server

### 10.1 Install Dependencies

📍 **Execute on: Internal Server**

```bash
sudo apt update
sudo apt install -y git nginx curl

```

Install Node.js:

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x |sudo -E bash -
sudo apt install -y nodejs

```

---

### 10.2 Clone Repository

📍 **Execute on: Internal Server**

```bash
cd /sdi
gitclone git@github.com:USERNAME/infra-knowledge-base.git
cd infra-knowledge-base

```

---

### 10.3 Install & Build

📍 **Execute on: Internal Server**

```bash
npm install
npm run build

```

---

## 11. Nginx Configuration (Production)

### 11.1 Create Site Config

📍 **Execute on: Internal Server**

```bash
sudo nano /etc/nginx/sites-available/infra-kb

```

```
server {
listen80;
server_name kb.internal.local;

root /opt/infra-knowledge-base/build;
index index.html;

location / {
try_files$uri$uri/ /index.html;
    }
}

```

---

### 11.2 Enable Site

📍 **Execute on: Internal Server**

```bash
sudoln -s /etc/nginx/sites-available/infra-kb /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

```

---

### 11.3 DNS / Hosts Entry

📍 **Execute on: Client / DNS Server**

```
10.10.10.10   kb.internal.local

```

---

## 12. Auto Update from GitHub

### 12.1 Update Script

📍 **Execute on: Internal Server**

```bash
nano /opt/update-infra-kb.sh

```

```bash
#!/bin/bash
cd /opt/infra-knowledge-base ||exit 1
git pull origin main
npm run build

```

```bash
chmod +x /opt/update-infra-kb.sh

```

---

### 12.2 Cron Job

📍 **Execute on: Internal Server**

```bash
crontab -e

```

```
*/10 * * * * /opt/update-infra-kb.sh >> /var/log/infra-kb.log 2>&1

```

---

## 13. Recommended Docs Structure

```
docs/
├─ overview/
├─ network/
├─server/
├─ virtualization/
├─security/
├─ sop/
└─ dr/

```

---

## 14. Security Notes

- Nginx only (Node not exposed)
- Internal access only
- Optional: basic auth / firewall allowlist
- Static content = low attack surface

---

## 15. Done 🎉

Internal Knowledge Base:

```
http://kb.internal.local

```