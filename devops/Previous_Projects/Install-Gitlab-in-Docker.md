---
title: "Install Gitlab in Docker"
date: 2024-09-21T11:29:09Z
draft: false
---
## Create gitlab folder
```
sudo su -

mkdir /opt/stacks/gitlab -p

cd /opt/stacks/gitlab

```

## Create Compose file
```
nano compose.yaml
```

Put this in the file:
```
# docker-compose.yml
version: '3.7'
services:
  web:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'localhost'
    container_name: gitlab-ce
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://localhost'
    ports:
      - '8080:80'
      - '8443:443'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    networks:
      - gitlab
  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    container_name: gitlab-runner    
    restart: always
    depends_on:
      - web
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - '$GITLAB_HOME/gitlab-runner:/etc/gitlab-runner'
    networks:
      - gitlab

networks:
  gitlab:
    name: gitlab-network
```

## Create .env file
```
nano .env
```

Put this in the file
```
GITLAB_HOME=/opt/stacks/gitlab
```

## Start Gitlab
```
docker compose up -d
```

```
docker-compose exec gitlab-runner gitlab-runner register
```

## Bibliography
https://docs.gitlab.com/ee/install/docker/installation.html
