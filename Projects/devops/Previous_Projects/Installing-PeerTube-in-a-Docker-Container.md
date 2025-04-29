---
title: "Installing PeerTube in a Docker Container"
date: 2024-09-21T08:36:26Z
draft: false
---


## 1. Get the latest Compose file
```
sudo su -

mkdir /opt/stacks/peertube -p

cd /opt/stacks/peertube

curl https://raw.githubusercontent.com/chocobozzz/PeerTube/master/support/docker/production/docker-compose.yml > compose.yaml
```
When this does not work
```
nano compose.yaml
```
Put this in the file
```
services:

  # You can comment this webserver section if you want to use another webserver/proxy or test PeerTube in local
  webserver:
    image: chocobozzz/peertube-webserver:latest
    # If you don't want to use the official image and build one from sources:
    # build:
    #   context: .
    #   dockerfile: Dockerfile.nginx
    env_file:
      - .env
    ports:
     - "80:80"
     - "443:443"
    volumes:
      - type: bind
        # Switch sources if you downloaded the whole repository
        #source: ../../nginx/peertube
        source: ./docker-volume/nginx/peertube
        target: /etc/nginx/conf.d/peertube.template
      - assets:/var/www/peertube/peertube-latest/client/dist:ro
      - ./docker-volume/data:/var/www/peertube/storage
      - certbot-www:/var/www/certbot
      - ./docker-volume/certbot/conf:/etc/letsencrypt
    depends_on:
      - peertube
    restart: "always"

  # You can comment this certbot section if you want to use another webserver/proxy or test PeerTube in local
  certbot:
    container_name: certbot
    image: certbot/certbot
    volumes:
      - ./docker-volume/certbot/conf:/etc/letsencrypt
      - certbot-www:/var/www/certbot
    restart: unless-stopped
    entrypoint: /bin/sh -c "trap exit TERM; while :; do certbot renew --webroot -w /var/www/certbot; sleep 12h & wait $${!}; done;"
    depends_on:
      - webserver

  peertube:
    # If you don't want to use the official image and build one from sources:
    # build:
    #   context: .
    #   dockerfile: ./support/docker/production/Dockerfile.bookworm
    image: chocobozzz/peertube:production-bookworm
    # Use a static IP for this container because nginx does not handle proxy host change without reload
    # This container could be restarted on crash or until the postgresql database is ready for connection
    networks:
      default:
        ipv4_address: 172.18.0.42
        ipv6_address: 2001:db8:2::42
    env_file:
      - .env

    ports:
     - "1935:1935" # Comment if you don't want to use the live feature
    #  - "9000:9000" # Uncomment if you use another webserver/proxy or test PeerTube in local, otherwise not suitable for production
    volumes:
      # Remove the following line if you want to use another webserver/proxy or test PeerTube in local
      - assets:/app/client/dist
      - ./docker-volume/data:/data
      - ./docker-volume/config:/config
    depends_on:
      - postgres
      - redis
      - postfix
    restart: "always"

  postgres:
    image: postgres:13-alpine
    env_file:
      - .env
    volumes:
      - ./docker-volume/db:/var/lib/postgresql/data
    restart: "always"

  redis:
    image: redis:6-alpine
    volumes:
      - ./docker-volume/redis:/data
    restart: "always"

  postfix:
    image: mwader/postfix-relay
    env_file:
      - .env
    volumes:
      - ./docker-volume/opendkim/keys:/etc/opendkim/keys
    restart: "always"

networks:
  default:
    enable_ipv6: true
    ipam:
      driver: default
      config:
      - subnet: 172.18.0.0/16
      - subnet: 2001:db8:2::/64

volumes:
  assets:
  certbot-www:
```


## 2. Get the latest env_file
```
curl https://raw.githubusercontent.com/Chocobozzz/PeerTube/master/support/docker/production/.env > .env
```
When this does not work 
'''
nano .env
'''

Put this in the file
```
# Database / Postgres service configuration
POSTGRES_USER=<MY POSTGRES USERNAME>
POSTGRES_PASSWORD=<MY POSTGRES PASSWORD>
# Postgres database name "peertube"
POSTGRES_DB=peertube
# The database name used by PeerTube will be PEERTUBE_DB_NAME (only if set) *OR* 'peertube'+PEERTUBE_DB_SUFFIX
#PEERTUBE_DB_NAME=<MY POSTGRES DB NAME>
#PEERTUBE_DB_SUFFIX=_prod
# Database username and password used by PeerTube must match Postgres', so they are copied:
PEERTUBE_DB_USERNAME=$POSTGRES_USER
PEERTUBE_DB_PASSWORD=$POSTGRES_PASSWORD
PEERTUBE_DB_SSL=false
# Default to Postgres service name "postgres" in docker-compose.yml
PEERTUBE_DB_HOSTNAME=postgres

# PeerTube server configuration
# If you test PeerTube in local: use "peertube.localhost" and add this domain to your host file resolving on 127.0.0.1
PEERTUBE_WEBSERVER_HOSTNAME=<MY DOMAIN>
# If you just want to test PeerTube on local
#PEERTUBE_WEBSERVER_PORT=9000
#PEERTUBE_WEBSERVER_HTTPS=false
# If you need more than one IP as trust_proxy
# pass them as a comma separated array:
PEERTUBE_TRUST_PROXY=["127.0.0.1", "loopback", "172.18.0.0/16"]

# Generate one using `openssl rand -hex 32`
PEERTUBE_SECRET=<MY PEERTUBE SECRET>

# E-mail configuration
# If you use a Custom SMTP server
#PEERTUBE_SMTP_USERNAME=
#PEERTUBE_SMTP_PASSWORD=
# Default to Postfix service name "postfix" in docker-compose.yml
# May be the hostname of your Custom SMTP server
PEERTUBE_SMTP_HOSTNAME=postfix
PEERTUBE_SMTP_PORT=25
PEERTUBE_SMTP_FROM=noreply@<MY DOMAIN>
PEERTUBE_SMTP_TLS=false
PEERTUBE_SMTP_DISABLE_STARTTLS=false
PEERTUBE_ADMIN_EMAIL=<MY EMAIL ADDRESS>

# Postfix service configuration
POSTFIX_myhostname=<MY DOMAIN>
# If you need to generate a list of sub/DOMAIN keys
# pass them as a whitespace separated string <DOMAIN>=<selector>
OPENDKIM_DOMAINS=<MY DOMAIN>=peertube
# see https://github.com/wader/postfix-relay/pull/18
OPENDKIM_RequireSafeKeys=no

PEERTUBE_OBJECT_STORAGE_UPLOAD_ACL_PUBLIC="public-read"
PEERTUBE_OBJECT_STORAGE_UPLOAD_ACL_PRIVATE="private"

#PEERTUBE_LOG_LEVEL=info

# /!\ Prefer to use the PeerTube admin interface to set the following configurations /!\
#PEERTUBE_SIGNUP_ENABLED=true
#PEERTUBE_TRANSCODING_ENABLED=true
#PEERTUBE_CONTACT_FORM_ENABLED=true
```

## 3. Modify the Docker Compose file to meet yuour requirements
```
nano compose.yaml 
```

You need to modify these fields
```
<MY POSTGRES USERNAME>
<MY POSTGRES PASSWORD>
<MY DOMAIN> without 'https://'
<MY EMAIL ADDRESS>
<MY PEERTUBE SECRET>
```

## 4. Start your setup and get your automatically-generated admin credentials
```
docker compose up
```

## Bibliography
https://docs.joinpeertube.org/install/docker#tweak-the-docker-compose-yml-file-there-according-to-your-needs
