---
title: "Install Filestash in Proxmox"
date: 2024-05-29T11:18:09Z
draft: false
---
## Overview of Filestash
Filestash is an open-source web-based file manager that allows you to connect to and manage files stored across various backend services and protocols. It provides a clean, modern interface for managing files, supporting services like FTP, SFTP, WebDAV, Dropbox, Google Drive, Amazon S3, and many others.

## Key Features
1. Multi-Backend Support: Connect to multiple types of storage backends including cloud services and traditional protocols.
2. Web-Based Interface: Access your files through a web browser with a user-friendly interface.
3. File Operations: Perform basic file operations such as upload, download, copy, move, rename, and delete.
4. Search and Preview: Search files and preview different file types directly in the browser.
5. Secure Access: Supports HTTPS and can integrate with authentication mechanisms for secure access.

## Installing Filestash
### The official Docker compose file
https://github.com/mickael-kerjean/filestash/blob/master/docker/docker-compose.yml

Please note this file did not work without modification

### Create a Docker Compose and .env Files  :
Run this command:
```
nano compose.yaml
```
Put this in the file :
```
version: "2"
services:
  app:
    container_name: filestash
    image: machines/filestash
    restart: always
    environment:
      - APPLICATION_URL=${APPLICATION_URL}
      - GDRIVE_CLIENT_ID=${GDRIVE_CLIENT_ID}
      - GDRIVE_CLIENT_SECRET=${GDRIVE_CLIENT_SECRET}
      - DROPBOX_CLIENT_ID=${DROPBOX_CLIENT_ID}
      - ONLYOFFICE_URL=${ONLYOFFICE_URL}
    ports:
      - 8334:8334
    volumes:
      - filestash:/app/data/state/
  onlyoffice:
    container_name: filestash_oods
    image: onlyoffice/documentserver
    restart: always
    security_opt:
      - seccomp:unconfined
volumes:
  filestash: {}
networks: {}
```
You may want a smaller/faster server without OnlyOffice then use this content
```
version: "2"
services:
  app:
    container_name: filestash
    image: machines/filestash
    restart: always
    environment:
      - APPLICATION_URL=${APPLICATION_URL}
      - GDRIVE_CLIENT_ID=${GDRIVE_CLIENT_ID}
      - GDRIVE_CLIENT_SECRET=${GDRIVE_CLIENT_SECRET}
      - DROPBOX_CLIENT_ID=${DROPBOX_CLIENT_ID}
      - ONLYOFFICE_URL=${ONLYOFFICE_URL}
    ports:
      - 8334:8334
    volumes:
      - filestash:/app/data/state/
volumes:
  filestash: {}
networks: {}

```


Run this command :
```
nano .env
```
Put this in the file:
```
APPLICATION_URL=
GDRIVE_CLIENT_ID=your_gdrive_client_id
GDRIVE_CLIENT_SECRET=your_gdrive_client_secret
DROPBOX_CLIENT_ID=your_dropbox_client_id
ONLYOFFICE_URL=http://onlyoffice
```

## Run Docker Compose:
Run this command :
```
docker compose up -d
```
## Access Filestash:
Open your web browser and navigate to \
[http://10.154.2.87:8334/](http://10.154.2.87:8334/) \
[http://10.154.2.87:8334/admin/](http://10.154.2.87:8334/admin/)

You should see the Filestash login page. \
You will now set the admin Password

## Connecting to Storage Backends
Once you have Filestash running, you can configure connections to various storage backends through the web interface. Here are some examples:

### Adding a Google Drive Connection
Click on the settings icon (usually in the top-right corner).
Go to the Storage Backends section.
Add a new backend and select Google Drive.
Enter the Client ID and Client Secret from your .env file.
Follow the on-screen instructions to authenticate and connect.

### Adding an FTP Connection
Go to the Storage Backends section in the settings.
Add a new backend and select FTP.
Enter the FTP server address, username, and password.
Save the configuration and connect.



## User Login
[http://10.154.2.87:8334/login](http://10.154.2.87:8334/login)


## Summary
Filestash is a powerful and versatile web-based file manager that supports a wide range of storage backends. By using Docker, you can easily deploy and start using Filestash to manage your files through a modern web interface. Be sure to secure your deployment and explore the official documentation for advanced configurations and features.

## Bibliography

https://www.filestash.app/docs/ \
https://elest.io/open-source/filestash/resources/installation-guide \
https://github.com/mickael-kerjean/filestash/blob/master/docker/docker-compose.yml

