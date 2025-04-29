---
title: "Replace Google Photos With PhotoPrism: Self Host Your Memories in Docker on Proxmox LXC Container"
date: 2024-12-28T09:54:17Z
draft: false
---

In this video, we show you how to install and self-host PhotoPrism in a Proxmox LXC container using Docker.

## 1. Links
**[https://www.photoprism.app/](https://www.photoprism.app/)**

**[https://docs.photoprism.app/](https://docs.photoprism.app/)**

## 2. Benefits of PhotoPrism Over Google Photos**  
- **Privacy**: Your photos stay on your server, not on Google's cloud, ensuring complete control and privacy.  
- **No Subscription Fees**: Avoid recurring charges with this free, open-source solution.  
- **Custom Storage**: Store photos on your preferred hardware or external storage solutions.  
- **Open-Source Advantage**: Regular updates and community support without vendor lock-in.  
- **Full Resolution**: Maintain the original quality of your photos without compression.  

## 3. Benefits of PhotoPrism Over Facebook**  
- **No Ads or Tracking**: Share your photos without intrusive ads or privacy compromises.  
- **Data Ownership**: Retain full ownership of your photos, with no risk of platform bans or content removal.  
- **Selective Sharing**: Share specific photos or albums privately without exposing your entire library.  
- **Better Organization**: Advanced photo tagging and search features for streamlined management.  
- **No Social Pressure**: Focus on your memories without likes, comments, or algorithm distractions.  

## 4. PhotoPrism and WhatsApp**  
- Share photo links directly via WhatsApp, making it easy to send albums or individual pictures without large file transfers.  

## 5. Docker Compose file
**[https://dl.photoprism.app/docker/compose.yaml](https://dl.photoprism.app/docker/compose.yaml)**

## 6. Installation Instructions
Run these commands in LXC Server
```bash
sudo su -

mkdir /opt/stacks/photoprism -p

cd /opt/stacks/photoprism

```

To add an `.env` file to store sensitive credentials and make the `docker-compose.yml` cleaner and more secure, you can use the `env_file` option in Docker Compose. Here's how you can modify the setup:

### 6.1 Step 1: Create the `.env` file
Create a file named `.env` in the same directory as the `docker-compose.yml` file, and add the following entries:
```bash
nano .env
```

Put this in the file:
```env
# PhotoPrism Admin Credentials
PHOTOPRISM_ADMIN_USER=admin
PHOTOPRISM_ADMIN_PASSWORD=securepassword123

# Database Credentials
PHOTOPRISM_DATABASE_USER=photoprism
PHOTOPRISM_DATABASE_PASSWORD=securedbpassword456
PHOTOPRISM_DATABASE_NAME=photoprism
PHOTOPRISM_DATABASE_SERVER=mariadb:3306

# Other Configurations
PHOTOPRISM_SITE_URL=http://localhost:2342/
PHOTOPRISM_AUTH_MODE=password
PHOTOPRISM_LOG_LEVEL=info
PHOTOPRISM_SITE_CAPTION=AI-Powered Photos App
PHOTOPRISM_BACKUP_SCHEDULE=daily
```

Replace `securepassword123` and `securedbpassword456` with your desired secure passwords.


### Advantages of using the .env file
1. **Separation of Concerns:** Sensitive credentials are moved to a dedicated file.
2. **Security:** Easier to protect sensitive information by excluding the `.env` file from version control (add `.env` to `.gitignore`).
3. **Reusability:** The `.env` file can be reused across multiple services or projects.




### 6.2 Step 2: Update `docker-compose.yml`
We modify the `docker compose` file to include the `env file` directive. Here's the updated version:
```bash
nano compose.yaml
```

Put this in the	file:
```yaml
services:
  photoprism:
    image: photoprism/photoprism:latest
    stop_grace_period: 10s
    depends_on:
      - mariadb
    security_opt:
      - seccomp:unconfined
      - apparmor:unconfined
    ports:
      - "2342:2342"
    env_file: 
      - .env
    environment:
      PHOTOPRISM_DISABLE_TLS: "false"
      PHOTOPRISM_DEFAULT_TLS: "true"
      PHOTOPRISM_ORIGINALS_LIMIT: 5000
      PHOTOPRISM_HTTP_COMPRESSION: "gzip"
      PHOTOPRISM_READONLY: "false"
      PHOTOPRISM_EXPERIMENTAL: "false"
      PHOTOPRISM_DISABLE_CHOWN: "false"
      PHOTOPRISM_DISABLE_WEBDAV: "false"
      PHOTOPRISM_DISABLE_SETTINGS: "false"
      PHOTOPRISM_DISABLE_TENSORFLOW: "false"
      PHOTOPRISM_DISABLE_FACES: "false"
      PHOTOPRISM_DISABLE_CLASSIFICATION: "false"
      PHOTOPRISM_DISABLE_VECTORS: "false"
      PHOTOPRISM_DISABLE_RAW: "false"
      PHOTOPRISM_RAW_PRESETS: "false"
      PHOTOPRISM_SIDECAR_YAML: "true"
      PHOTOPRISM_BACKUP_ALBUMS: "true"
      PHOTOPRISM_BACKUP_DATABASE: "true"
      PHOTOPRISM_AUTO_INDEX: 300
      PHOTOPRISM_AUTO_IMPORT: -1
      PHOTOPRISM_DETECT_NSFW: "false"
      PHOTOPRISM_UPLOAD_NSFW: "true"
    working_dir: "/photoprism"
    volumes:
      - "~/Pictures:/photoprism/originals"
      - "./storage:/photoprism/storage"

  mariadb:
    image: mariadb:11
    restart: unless-stopped
    stop_grace_period: 5s
    security_opt:
      - seccomp:unconfined
      - apparmor:unconfined
    command: --innodb-buffer-pool-size=512M --transaction-isolation=READ-COMMITTED --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --max-connections=512 --innodb-rollback-on-timeout=OFF --innodb-lock-wait-timeout=50
    env_file:
      - .env
    volumes:
      - "./database:/var/lib/mysql"
```

### 6.3 Step 3: Run the Setup
Run the following commands to bring up the stack with the new configuration:

```bash
docker compose up -d
```
## 6.4 Open in browser

**[Dockage: http://tiger.loseyourip.com:5001/](http://tiger.loseyourip.com:5001/)**

**[http://localhost:2342/](http://localhost:2342/)**


**[http://10.154.2.87:2342/](http://10.154.2.85:2342/)**


**[http://tiger.loseyourip.com:2342/](http://tiger.loseyourip.com:2342/)**

