---
title: "Replace Google Photos Self-Host Immich in Docker in Proxmox LXC"
date: 2025-01-10T16:28:18Z
draft: false
---

## Docker Training URL:
**[http://rino.kozow.com/devops/posts/docker-training/](http://rino.kozow.com/devops/posts/docker-training/)**

## What is Immich?  

1. **Self-Hosted Photo Management**: Immich is an open-source alternative to Google Photos, allowing you to manage and organize your photos and videos on your own server.  

2. **Automatic Uploads**: It supports automatic backups from your devices, ensuring your memories are always safe and accessible.  

3. **Privacy and Control**: With Immich, you retain complete control over your data, keeping it private and away from third-party services.  

4. **Advanced Features**: Includes features like facial recognition, album organization, and seamless sharing capabilities.  

5. **Open-Source and Free**: Fully free to use with active community development and regular updates.  

## Immich Web pages
1. **[https://immich.app/](https://immich.app/)**
2. **[https://immich.app/docs/overview/introduction/](https://immich.app/docs/overview/introduction/)**
3. **[https://github.com/immich-app/immich/releases/tag/v1.124.2](https://github.com/immich-app/immich/releases/tag/v1.124.2)**
4. **[https://github.com/immich-app/immich/blob/main/docker/docker-compose.yml](https://github.com/immich-app/immich/blob/main/docker/docker-compose.yml)**

## Installing Immich
Run these commands:
```bash
sudo su -

mkdir /opt/stacks/immich -p

cd /opt/stacks/immich

nano compose.yaml
```
Put this in the file
```yaml
version: '3.8'

name: immich

services:
  immich-server:
    container_name: immich_server
    image: ghcr.io/immich-app/immich-server:${IMMICH_VERSION:-release}
    volumes:
      - ${UPLOAD_LOCATION}:/usr/src/app/upload
      - /etc/localtime:/etc/localtime:ro
    env_file:
      - .env
    ports:
      - '2283:2283'
    depends_on:
      - redis
      - database
    restart: always
    healthcheck:
      disable: false

  immich-machine-learning:
    container_name: immich_machine_learning
    image: ghcr.io/immich-app/immich-machine-learning:${IMMICH_VERSION:-release}
    volumes:
      - model-cache:/cache
    env_file:
      - .env
    restart: always
    healthcheck:
      disable: false

  redis:
    container_name: immich_redis
    image: docker.io/redis:6.2-alpine@sha256:eaba718fecd1196d88533de7ba49bf903ad33664a92debb24660a922ecd9cac8
    healthcheck:
      test: redis-cli ping || exit 1
    restart: always

  database:
    container_name: immich_postgres
    image: docker.io/tensorchord/pgvecto-rs:pg14-v0.2.0@sha256:90724186f0a3517cf6914295b5ab410db9ce23190a2d9d0b9dd6463e3fa298f0
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_USER: ${DB_USERNAME}
      POSTGRES_DB: ${DB_DATABASE_NAME}
      POSTGRES_INITDB_ARGS: '--data-checksums'
    volumes:
      - immigedb:/var/lib/postgresql/data
    healthcheck:
      test: >-
        pg_isready --dbname="$${POSTGRES_DB}" --username="$${POSTGRES_USER}" || exit 1;
        Chksum="$$(psql --dbname="$${POSTGRES_DB}" --username="$${POSTGRES_USER}" --tuples-only --no-align
        --command='SELECT COALESCE(SUM(checksum_failures), 0) FROM pg_stat_database')";
        echo "checksum failure count is $$Chksum";
        [ "$$Chksum" = '0' ] || exit 1
      interval: 5m
      start_interval: 30s
      start_period: 5m
    command: >-
      postgres
      -c shared_preload_libraries=vectors.so
      -c 'search_path="$$user", public, vectors'
      -c logging_collector=on
      -c max_wal_size=2GB
      -c shared_buffers=512MB
      -c wal_compression=on
    restart: always

volumes:
  model-cache:
  immigedb:

```
Run this command :
```bash
nano .env
```
Put this in the file:
```
# Immich Version
IMMICH_VERSION=release

# Server Configuration
UPLOAD_LOCATION=/path/to/your/upload/location

# Database Configuration
DB_USERNAME=postgres
DB_PASSWORD=yourpassword
DB_DATABASE_NAME=IMMIGEDB

```
## Start Immige
Run this command to start it in detached mode:
```
Docker compose up
```

## Open Browser
**[http://your-server-ip:2283](http://your-server-ip:2283)**

**[http://tiger.loseyourip.com:2283/](http://tiger.loseyourip.com:2283/)**

**[http://tiger.loseyourip.com:5001/compose/dockge](http://tiger.loseyourip.com:5001/compose/dockge)**

