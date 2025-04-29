---
title: "Replace OneNote Self  Host Joplin Docker Container in Proxmox LXC Container"
date: 2025-02-03T17:11:12Z
draft: false
---

## Web Pages
**[https://joplinapp.org/](https://joplinapp.org/)**

**[https://joplinapp.org/help/install/](https://joplinapp.org/help/install/)**

**[https://joplinapp.org/help/apps/](https://joplinapp.org/help/apps/)**

## Install Joplin

Run these commands:
```bash
sudo su -

mkdir /opt/stacks/joplin -p

cd /opt/stacks/joplin 

nano compose.yaml
```

Put this in the file:
```yaml
version: "3"
services:
  db:
    image: postgres:15
    volumes:
      - /docker/joplindb:/var/lib/postgresql/data
    ports:
      - ${POSTGRES_PORT}:5432
    restart: unless-stopped
    environment:
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_DB=${POSTGRES_DB}
  app:
    image: joplin/server:latest
    depends_on:
      - db
    ports:
      - ${JOPLIN_PORT}:22300
    restart: unless-stopped
    environment:
      - APP_PORT=${JOPLIN_PORT}
      - APP_BASE_URL=${JOPLIN_BASE_URL}
      - DB_CLIENT=pg
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DATABASE=${POSTGRES_DB}
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PORT=${POSTGRES_PORT}
      - POSTGRES_HOST=db
      - MAILER_ENABLED=${MAILER_ENABLED}
      - MAILER_HOST=${MAILER_HOST}
      - MAILER_PORT=${MAILER_PORT}
      - MAILER_SECURE=${MAILER_SECURE}
      - MAILER_AUTH_USER=${MAILER_AUTH_USER}
      - MAILER_AUTH_PASSWORD=${MAILER_AUTH_PASSWORD}
      - MAILER_NOREPLY_NAME=${MAILER_NOREPLY_NAME}
      - MAILER_NOREPLY_EMAIL=${MAILER_NOREPLY_EMAIL}
volumes:
  joplindb: null
networks: {}

```
Run this command :
```bash
nano .env
```

Put thi in the file
```env
# Database Config
POSTGRES_PASSWORD=postgres
POSTGRES_USER=postgres
POSTGRES_DB=joplin
POSTGRES_PORT=5432

# Joplin Config
JOPLIN_PORT=22300
JOPLIN_BASE_URL=http://tiger.loseyourip.com:22300

# Mailer Config
MAILER_ENABLED=1
MAILER_HOST=smtp.gmail.com
MAILER_PORT=465
MAILER_SECURE=1
MAILER_AUTH_USER=nico@gmail.com
MAILER_AUTH_PASSWORD=Y0urP@ssw0rd
MAILER_NOREPLY_NAME=Joplin
MAILER_NOREPLY_EMAIL=email@email.com

```
## Start the server
Run this command:
```
docker compose up -d
```

## Open in the Browser
**[http://your-server-ip:22300](http://your-server-ip:22300)**

**[http://tiger.loseyourip.com:22300](http://tiger.loseyourip.com:22300)**

**Default email : admin@localhost**

**Defailt password : admin**
