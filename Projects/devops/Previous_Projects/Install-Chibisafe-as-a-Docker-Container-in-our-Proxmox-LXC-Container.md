---
title: "Install Chibisafe as a Docker Container in Our Proxmox LXC Container"
date: 2025-02-12T14:52:35Z
draft: false
---

## Web Pages
**[https://chibisafe.moe/](https://chibisafe.moe/)**

**[https://github.com/chibisafe/chibisafe](https://github.com/chibisafe/chibisafe)**

**[https://chibisafe.app/docs/installation/running-with-docker](https://chibisafe.app/docs/installation/running-with-docker)**
## Install Chibisafe

Run these commands:
```bash
sudo su -

mkdir /opt/stacks/chibisafe -p

cd /opt/stacks/chibisafe

nano Caddyfile
```
Put this in the file:
```
{$BASE_URL} {
	route {
		file_server * {
				root /app/uploads
				pass_thru
		}

		@api path /api/*
		reverse_proxy @api http://chibisafe_server:8000 {
				header_up Host {http.reverse_proxy.upstream.hostport}
				header_up X-Real-IP {http.request.header.X-Real-IP}
		}

		@docs path /docs*
		reverse_proxy @docs http://chibisafe_server:8000 {
				header_up Host {http.reverse_proxy.upstream.hostport}
				header_up X-Real-IP {http.request.header.X-Real-IP}
		}

		reverse_proxy http://chibisafe:8001 {
				header_up Host {http.reverse_proxy.upstream.hostport}
				header_up X-Real-IP {http.request.header.X-Real-IP}
		}
	}
}
```

Run this command:
```bash
nano compose.yaml
```

Put this in the file:
```yaml
services:
  chibisafe:
    image: chibisafe/chibisafe:latest
    environment:
      - BASE_API_URL=${CHIBISAFE_BASE_API_URL}
    expose:
      - 8001
    restart: unless-stopped

  chibisafe_server:
    image: chibisafe/chibisafe-server:latest
    volumes:
      - ./database:/app/database:rw
      - ./uploads:/app/uploads:rw
      - ./logs:/app/logs:rw
    expose:
      - 8000
    restart: unless-stopped

  caddy:
    image: caddy:2-alpine
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - ./uploads:/app/uploads:ro
    ports:
      - ${CADDY_PORT}:80
    environment:
      - BASE_URL=${CADDY_BASE_URL}
    restart: unless-stopped

```
Run this command :
```bash
nano .env
```

Put thi in the file
```env
CHIBISAFE_BASE_API_URL=http://chibisafe_server:8000
CADDY_PORT=24424
CADDY_BASE_URL=":80"

```
## Start the server
Run this command:
```
docker compose up -d
```

## Open in the Browser
**[http://your-server-ip:24424/](http://your-server-ip:8080)**

**[http://tiger.loseyourip.com:24424/](http://tiger.loseyourip.com:24424/)**

**User id is: admin**

**Password is : admin**
