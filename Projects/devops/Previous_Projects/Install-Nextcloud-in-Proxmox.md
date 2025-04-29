---
title: "Install Nextcloud in Proxmox"
date: 2024-05-25T06:55:04Z
draft: false
---

## References
[https://nextcloud.com/](https://nextcloud.com/)

## Featured Apps
[https://apps.nextcloud.com/featured](https://apps.nextcloud.com/featured)

## Revised Docker Compose file
```
version: "2"
volumes:
  nextcloud: null
  db: null
services:
  db:
    image: mariadb:10.6
    restart: always
    command: --transaction-isolation=READ-COMMITTED --log-bin=binlog --binlog-format=ROW
    volumes:
      - db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=Password1234
      - MYSQL_PASSWORD=Password1234
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
  app:
    image: nextcloud
    restart: always
    ports:
      - 9280:80
    links:
      - db
    volumes:
      - nextcloud:/var/www/html
    environment:
      - MYSQL_PASSWORD=Password1234
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db
networks: {}
```


