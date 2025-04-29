---
title: "Install Nextcloud in Docker"
date: 2024-09-21T14:22:27Z
draft: false
---
## Create Nextcloud folder
```
sudo su -

mkdir /opt/stacks/nextcloud/ -p

cd /opt/stacks/nextcloud/

```

## Create Compose file
```
nano compose.yaml
```

Put this in the file:
```
services:
  nc:
    image: nextcloud:apache
    environment:
      - POSTGRES_HOST=db
      - POSTGRES_PASSWORD=nextcloud
      - POSTGRES_DB=nextcloud
      - POSTGRES_USER=nextcloud
    ports:
      - 8088:80
    restart: always
    volumes:
      - ./nc_data:/var/www/html
  db:
    image: postgres:alpine
    environment:
      - POSTGRES_PASSWORD=nextcloud
      - POSTGRES_DB=nextcloud
      - POSTGRES_USER=nextcloud
    restart: always
    volumes:
      - ./db_data:/var/lib/postgresql/data
    expose:
      - 5432
networks: {}

```
