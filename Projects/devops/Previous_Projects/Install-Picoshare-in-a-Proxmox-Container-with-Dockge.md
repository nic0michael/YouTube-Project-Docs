---
title: "Install Picoshare in a Proxmox Container With Dockge"
date: 2024-05-29T18:06:43Z
draft: false
---
## Use Case
1. Its all about collaboration, Teams, Clubs, Organizations, Home Use
2. You want to leave a file on a server and send a link to it for somone to download 
3. The file can have an expiry date for self delete

## Create the Modified Docker compose file
Run this command :
```
nano compose.yaml
```

Put this in the file :
```
version: "3.3"
services:
  picoshare:
    environment:
      - PORT=4001
      - PS_SHARED_SECRET=${PASSWORD}
    ports:
      - 4001:4001/tcp
    volumes:
      - .data:/data
    container_name: Picoshare
    image: mtlynch/picoshare:latest
networks: {}
```

Run this command :
```
nano .env
```

Put this in the	file :
```
PASSWORD=Password123
```
## Open the app in the Browser
[http://10.154.2.87:4001/](http://10.154.2.87:4001/)
