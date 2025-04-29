---
title: "Install Stirling-PDF in Proxmox"
date: 2024-05-24T18:34:46Z
draft: false
---
## References 

https://www.stirlingtools.com/ 


https://hub.docker.com/r/frooodle/s-pdf \
https://github.com/Frooodle/Stirling-PDF
## Revised Docker Compose file:
```
version: '3.3'
services:
  stirling-pdf:
    image: frooodle/s-pdf:latest
    ports:
      - '9180:8080'
    volumes:
      - /location/of/trainingData:/usr/share/tesseract-ocr/5/tessdata #Required for extra OCR languages
      - /location/of/extraConfigs:/configs
#      - /location/of/customFiles:/customFiles/
#      - /location/of/logs:/logs/
    environment:
      - DOCKER_ENABLE_SECURITY=false
```


## Open in browser
[http://10.154.2.87:9988](http://10.154.2.87:9988)


