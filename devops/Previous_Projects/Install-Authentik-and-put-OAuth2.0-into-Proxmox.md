---
title: "Install Authentik and put OAuth2.0 Into Proxmox"
date: 2024-10-19T12:25:48Z
draft: false
---
## 1. What is OAuth2
1. Open standard for authorization used to grant limited access to resources.  
2. Allows third-party applications to access user data without exposing credentials.  
3. Utilizes access tokens issued by an authorization server.  
4. Supports various flows like authorization code, implicit, and client credentials.  
5. Widely used in web, mobile, and API security for secure access delegation.  

## 2. What is Authentik
1. Open-source identity provider for authentication, authorization, and user management.  
2. Supports OAuth2, OpenID Connect, SAML, and LDAP protocols.  
3. Enables single sign-on (SSO) with multiple services for improved security.  
4. Includes advanced access control policies and multi-factor authentication (MFA).  
5. Flexible deployment options for on-premises or cloud environments.  

## The Authentk site 
**[https://goauthentik.io/](https://goauthentik.io/)**

**[Getting Started](https://docs.goauthentik.io/docs/install-config/)**

**[Configuration Information](https://docs.goauthentik.io/docs/install-config/configuration/)**

## 3. Installation instructions

Run these commands
```
# become Sudo
sudo su -

# Create Authentik folder
mkdir /opt/stacks/authentik -p

cd /opt/stacks/authentik/
```

Create Cocker Compose file
```
nano compose.yaml
```

Put this in the file:
```
version: "3.4"

services:
  postgresql:
    image: docker.io/library/postgres:12-alpine
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -d $${POSTGRES_DB} -U $${POSTGRES_USER}"]
      start_period: 20s
      interval: 30s
      retries: 5
      timeout: 5s
    volumes:
      - ./database:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: ${PG_PASS:?database password required}
      POSTGRES_USER: ${PG_USER:-authentik}
      POSTGRES_DB: ${PG_DB:-authentik}
    env_file:
      - .env
  redis:
    image: docker.io/library/redis:alpine
    command: --save 60 1 --loglevel warning
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "redis-cli ping | grep PONG"]
      start_period: 20s
      interval: 30s
      retries: 5
      timeout: 3s
    volumes:
      - ./redis:/data
  server:
    image: ${AUTHENTIK_IMAGE:-ghcr.io/goauthentik/server}:${AUTHENTIK_TAG:-2023.8.3}
    restart: unless-stopped
    command: server
    environment:
      AUTHENTIK_REDIS__HOST: redis
      AUTHENTIK_POSTGRESQL__HOST: postgresql
      AUTHENTIK_POSTGRESQL__USER: ${PG_USER:-authentik}
      AUTHENTIK_POSTGRESQL__NAME: ${PG_DB:-authentik}
      AUTHENTIK_POSTGRESQL__PASSWORD: ${PG_PASS}
    volumes:
      - ./media:/media
      - ./custom-templates:/templates
    env_file:
      - .env
    ports:
      - "${COMPOSE_PORT_HTTP:-9000}:9000"
      - "${COMPOSE_PORT_HTTPS:-9443}:9443"
    depends_on:
      - postgresql
      - redis
  worker:
    image: ${AUTHENTIK_IMAGE:-ghcr.io/goauthentik/server}:${AUTHENTIK_TAG:-2023.8.3}
    restart: unless-stopped
    command: worker
    environment:
      AUTHENTIK_REDIS__HOST: redis
      AUTHENTIK_POSTGRESQL__HOST: postgresql
      AUTHENTIK_POSTGRESQL__USER: ${PG_USER:-authentik}
      AUTHENTIK_POSTGRESQL__NAME: ${PG_DB:-authentik}
      AUTHENTIK_POSTGRESQL__PASSWORD: ${PG_PASS}
    # `user: root` and the docker socket volume are optional.
    # See more for the docker socket integration here:
    # https://goauthentik.io/docs/outposts/integrations/docker
    # Removing `user: root` also prevents the worker from fixing the permissions
    # on the mounted folders, so when removing this make sure the folders have the correct UID/GID
    # (1000:1000 by default)
    user: root
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./media:/media
      - ./certs:/certs
      - ./custom-templates:/templates
    env_file:
      - .env
    depends_on:
      - postgresql
      - redis
volumes:
  database:
    driver: local
  redis:
    driver: local
```
Create Environment file
```
nano .env
```

Put this in the file
```
PG_USER=authentik
PG_PASS=aReallyLongStrongPasswordShouldBePutHere
AUTHENTIK_SECRET_KEY=someincrediblylongcomplexkeygoeshere
AUTHENTIK_ERROR_REPORTING__ENABLED=true
# SMTP Host Emails are sent to
AUTHENTIK_EMAIL__HOST=smtp.example.com
AUTHENTIK_EMAIL__PORT=587
# Optionally authenticate (don't add quotation marks to your password)
AUTHENTIK_EMAIL__USERNAME=auth@example.com
AUTHENTIK_EMAIL__PASSWORD=a-L0n6-Strong_password_should_go_here
# Use StartTLS
AUTHENTIK_EMAIL__USE_TLS=true
# Use SSL
AUTHENTIK_EMAIL__USE_SSL=false
AUTHENTIK_EMAIL__TIMEOUT=10
# Email address authentik will send from, should have a correct @domain
AUTHENTIK_EMAIL__FROM=auth@example.com
COMPOSE_PORT_HTTP=8700
COMPOSE_PORT_HTTPS=1443
# Authentik Version to Pull
ATHENTIK_TAG=2023.8.3
```
## Start docker container in detached mode
```
docker compose up -d
```

## Open in Browser 
**[http://localhost:9700](http://localhost:9700)**

in or case :
 
[http://tiger.loseyourip.com:9700](http://tiger.loseyourip.com:9700)

## Initial Setup
[http://tiger.loseyourip.com:9700/if/flow/initial-setup](http://tiger.loseyourip.com:9700/if/flow/initial-setup)

in our case :

**[http://tiger.loseyourip.com:8700/if/flow/initial-setup](http://tiger.loseyourip.com:8700/if/flow/initial-setup)**


## Bibliography

https://wiki.opensourceisawesome.com/books/authentik/page/install-and-setup-authentik \
https://www.youtube.com/watch?v=KlDJ4K45M_o&t=2272s \
https://www.youtube.com/watch?v=enwFWELCYJo \
