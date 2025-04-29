---
title: "Replace Dropbox With Seafile to Self-Host a Cloud Storage Solution in Proxmox LXC Container"
date: 2025-01-25T06:48:16Z
draft: false
---

## Official web pages
**[Seafile Docker Overview](https://manual.seafile.com/latest/setup/overview/)**

**[https://www.seafile.com/en/home/](https://www.seafile.com/en/home/)**

**[Clients: https://www.seafile.com/en/download/](https://www.seafile.com/en/download/)**

**[https://manual.seafile.com/11.0/docker/deploy_seafile_with_docker/](https://manual.seafile.com/11.0/docker/deploy_seafile_with_docker/)**

**[https://manual.seafile.com/12.0/setup/setup_ce_by_docker/#getting-started](https://manual.seafile.com/12.0/setup/setup_ce_by_docker/#getting-started)**

## In stallation
Run these commands:
```bash
sudo su 
mkdir /opt/stacks/seafile -p
cd /opt/stacks/seafile
```

Run This command:
```bash
nano seafile-server.yml
```
Put this in the file:
```Yaml
services:
  db:
    image: ${SEAFILE_DB_IMAGE:-mariadb:10.11}
    container_name: seafile-mysql
    environment:
      - MYSQL_ROOT_PASSWORD=${INIT_SEAFILE_MYSQL_ROOT_PASSWORD:-}
      - MYSQL_LOG_CONSOLE=true
      - MARIADB_AUTO_UPGRADE=1
    volumes:
      - "${SEAFILE_MYSQL_VOLUME:-/opt/seafile-mysql/db}:/var/lib/mysql"
    networks:
      - seafile-net
    healthcheck:
      test:
        [
          "CMD",
          "/usr/local/bin/healthcheck.sh",
          "--connect",
          "--mariadbupgrade",
          "--innodb_initialized",
        ]
      interval: 20s
      start_period: 30s
      timeout: 5s
      retries: 10

  memcached:
    image: ${SEAFILE_MEMCACHED_IMAGE:-memcached:1.6.29}
    container_name: seafile-memcached
    entrypoint: memcached -m 256
    networks:
      - seafile-net

  seafile:
    image: ${SEAFILE_IMAGE:-seafileltd/seafile-mc:12.0-latest}
    container_name: seafile
     ports:
       - "7380:80"
    volumes:
      - ${SEAFILE_VOLUME:-/opt/seafile-data}:/shared
    environment:
      - DB_HOST=${SEAFILE_MYSQL_DB_HOST:-db}
      - DB_PORT=${SEAFILE_MYSQL_DB_PORT:-3306}
      - DB_USER=${SEAFILE_MYSQL_DB_USER:-seafile}
      - DB_ROOT_PASSWD=${INIT_SEAFILE_MYSQL_ROOT_PASSWORD:-}
      - DB_PASSWORD=${SEAFILE_MYSQL_DB_PASSWORD:?Variable is not set or empty}
      - SEAFILE_MYSQL_DB_CCNET_DB_NAME=${SEAFILE_MYSQL_DB_CCNET_DB_NAME:-ccnet_db}
      - SEAFILE_MYSQL_DB_SEAFILE_DB_NAME=${SEAFILE_MYSQL_DB_SEAFILE_DB_NAME:-seafile_db}
      - SEAFILE_MYSQL_DB_SEAHUB_DB_NAME=${SEAFILE_MYSQL_DB_SEAHUB_DB_NAME:-seahub_db}
      - TIME_ZONE=${TIME_ZONE:-Etc/UTC}
      - INIT_SEAFILE_ADMIN_EMAIL=${INIT_SEAFILE_ADMIN_EMAIL:-me@example.com}
      - INIT_SEAFILE_ADMIN_PASSWORD=${INIT_SEAFILE_ADMIN_PASSWORD:-asecret}
      - SEAFILE_SERVER_HOSTNAME=${SEAFILE_SERVER_HOSTNAME:?Variable is not set or empty}
      - SEAFILE_SERVER_PROTOCOL=${SEAFILE_SERVER_PROTOCOL:-http}
      - SITE_ROOT=${SITE_ROOT:-/}
      - NON_ROOT=${NON_ROOT:-false}
      - JWT_PRIVATE_KEY=${JWT_PRIVATE_KEY:?Variable is not set or empty}
      - SEAFILE_LOG_TO_STDOUT=${SEAFILE_LOG_TO_STDOUT:-false}
      - ENABLE_SEADOC=${ENABLE_SEADOC:-false}
      - SEADOC_SERVER_URL=${SEADOC_SERVER_URL:-http://seafile.example.com/sdoc-server}
    labels:
      caddy: ${SEAFILE_SERVER_PROTOCOL:-http}://${SEAFILE_SERVER_HOSTNAME:?Variable is not set or empty}
      caddy.reverse_proxy: "{{upstreams 80}}"
    depends_on:
      db:
        condition: service_healthy
      memcached:
        condition: service_started
    networks:
      - seafile-net

networks:
  seafile-net:
    name: seafile-net
```
Run This command:

```bash
nano .env
```
Put this in the file:
```
COMPOSE_FILE='seafile-server.yml,caddy.yml'
COMPOSE_PATH_SEPARATOR=','


SEAFILE_IMAGE=seafileltd/seafile-mc:12.0-latest
SEAFILE_DB_IMAGE=mariadb:10.11
SEAFILE_MEMCACHED_IMAGE=memcached:1.6.29
SEAFILE_CADDY_IMAGE=lucaslorentz/caddy-docker-proxy:2.9-alpine

SEAFILE_VOLUME=./seafile-data
SEAFILE_MYSQL_VOLUME=./seafile-mysql/db
SEAFILE_CADDY_VOLUME=./seafile-caddy

SEAFILE_MYSQL_DB_HOST=db
INIT_SEAFILE_MYSQL_ROOT_PASSWORD=Password@Zs6bvr
SEAFILE_MYSQL_DB_USER=seafile
SEAFILE_MYSQL_DB_PASSWORD=Pasword@Zs6bvr

TIME_ZONE=Etc/UTC

JWT_PRIVATE_KEY=

SEAFILE_SERVER_HOSTNAME=seafile.example.com
SEAFILE_SERVER_PROTOCOL=http

INIT_SEAFILE_ADMIN_EMAIL=me@example.com
INIT_SEAFILE_ADMIN_PASSWORD=Password@Zs6bvr


SEADOC_IMAGE=seafileltd/sdoc-server:1.0-latest
SEADOC_VOLUME=/opt/seadoc-data

ENABLE_SEADOC=false
SEADOC_SERVER_URL=http://seafile.example.com/sdoc-server


NOTIFICATION_SERVER_IMAGE=seafileltd/notification-server:12.0-latest
NOTIFICATION_SERVER_VOLUME=./notification-data
```


Run This command:

```bash
nano caddy.yml
```
Put this in the file:
```yaml
services:

  caddy:
    image: ${SEAFILE_CADDY_IMAGE:-lucaslorentz/caddy-docker-proxy:2.9-alpine}
    restart: unless-stopped
    container_name: seafile-caddy
    ports:
      - 80:80
      - 443:443
    environment:
      - CADDY_INGRESS_NETWORKS=seafile-net
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ${SEAFILE_CADDY_VOLUME:-/opt/seafile-caddy}:/data/caddy
    networks:
      - seafile-net
    healthcheck:
      test: ["CMD-SHELL", "curl --fail http://localhost:2019/metrics || exit 1"]
      start_period: 20s
      interval: 20s
      timeout: 5s
      retries: 3

networks:
  seafile-net:
    name: seafile-net
```
## Starting the server
Run this command:
```bash
docker compose -f seafile-server.yml -d
```

## Starting Caddy
Run this command:
```
docker compose -f caddy.yml
```

