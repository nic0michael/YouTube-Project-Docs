---
title: "Advanced Docker Workshop"
date: 2025-02-04T15:25:29Z
draft: false
---


## 1. Basic Services
We start with the most fundamental part of a `docker-compose.yml` file: defining services. Here, we define two services: a database (`db`) and an application (`app`).

```yaml
version: "3"
services:
  db:
    image: postgres:15
  app:
    image: myapp:latest
```

## 2. Adding Ports
Next, we expose necessary ports to allow communication between containers and external systems.

```yaml
version: "3"
services:
  db:
    image: postgres:15
    ports:
      - "5432:5432"
  app:
    image: myapp:latest
    ports:
      - "8080:80"
```

## 3. Adding Networks Inside Services
We define internal networks for communication between services.

```yaml
version: "3"
services:
  db:
    image: postgres:15
    ports:
      - "5432:5432"
    networks:
      - backend
  app:
    image: myapp:latest
    ports:
      - "8080:80"
    networks:
      - backend
```

## 4. Defining the Main Networks section
A network outside the services section provides better control.

```yaml
version: "3"
services:
  db:
    image: postgres:15
    ports:
      - "5432:5432"
    networks:
      - mynetwork
  app:
    image: myapp:latest
    ports:
      - "8080:80"
    networks:
      - mynetwork

networks:
  mynetwork:
```

## 5. Types of Networks
Different network types can be defined.

```yaml
networks:
  mynetwork:
    driver: bridge  # Default option
  myoverlay:
    driver: overlay
  myhost:
    driver: host
```

## 6. Adding Volumes Inside Services
Volumes store persistent data.

```yaml
services:
  db:
    image: postgres:15
    volumes:
      - ./db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

## 7. Defining a Main Volumes Section

```yaml
volumes:
  db_data:
    driver: local
```

## 8. Adding Restart Policies
Ensuring services restart under different conditions.

```yaml
services:
  db:
    image: postgres:15
    restart: unless-stopped
  app:
    image: myapp:latest
    restart: always
```

## 9. Other Common Start Options
Other useful configurations.

```yaml
services:
  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=mydatabase
    depends_on:
      - app
```

## 10. Environment Variables for Credentials
Using environment variables improves security.

```yaml
services:
  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=${DB_USER}
      - POSTGRES_PASSWORD=${DB_PASS}
      - POSTGRES_DB=${DB_NAME}
```

## 11. Using a `.env` File
We replace hardcoded credentials with variables stored in a `.env` file.

`.env` file:
```ini
DB_USER=admin
DB_PASS=secret
DB_NAME=mydatabase
```

`docker-compose.yml`:
```yaml
services:
  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=${DB_USER}
      - POSTGRES_PASSWORD=${DB_PASS}
      - POSTGRES_DB=${DB_NAME}
```

## Now we have some fun
We install Joplin \
**[http://rino.kozow.com/devops/posts/replace-onenote-self--host-joplin-docker-container-in-proxmox-lxc-container/](http://rino.kozow.com/devops/posts/replace-onenote-self--host-joplin-docker-container-in-proxmox-lxc-container/)**

## The joplin docker compose file
**Run these commands :**
```bash
sudo su -

mkdir /opt/stacks/joplin -p

cd /opt/stacks/joplin/

nano compose.yaml
```
put this in the file:
```yaml
version: '3'
services:
    db:
        image: postgres:16
        volumes:
            - ./data/postgres:/var/lib/postgresql/data
        ports:
            - "5432:5432"
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
            - "22300:22300"
        restart: unless-stopped
        environment:
            - APP_PORT=22300
            - APP_BASE_URL=${APP_BASE_URL}
            - DB_CLIENT=pg
            - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
            - POSTGRES_DATABASE=${POSTGRES_DB}
            - POSTGRES_USER=${POSTGRES_USER}
            - POSTGRES_PORT=5432
            - POSTGRES_HOST=db


```
**Run this command:**
```bash
nano .env
```
Put this in the file
```env
POSTGRES_PASSWORD=testing123
POSTGRES_USER=joplin
POSTGRES_DB=joplindb
APP_BASE_URL=http://192.168.68.105:22300

```
## Lets start this container
**run this command:**
```bash
docker compose up
```
## we can see the server is running in the browser
**[http://your-server-ip:22300](http://your-server-ip:22300)**

**[http://tiger.loseyourip.com:22300/](http://tiger.loseyourip.com:22300/)**

**Default email : admin@localhost**

**Defailt password : admin**
