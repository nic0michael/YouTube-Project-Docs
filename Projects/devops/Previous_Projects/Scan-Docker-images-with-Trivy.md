---
title: "Scan Docker Images With Trivy"
date: 2024-10-12T08:55:48Z
draft: false
---

**1. What is Trivy?** \
Trivy is an open-source vulnerability scanner developed by Aqua Security. \
It identifies security vulnerabilities in container images(Docker), file systems(Linux), Infrastructure as code (Terraform) and Git repositories.

**2. What Do We Use Trivy For?** \
We use Trivy to detect vulnerabilities in OS packages and application dependencies. \
This helps ensure that containerized applications are secure and compliant with best practices.

## 1. Trivy is found here :
https://trivy.dev/

## 2. Documentation
https://aquasecurity.github.io/trivy/v0.56/

## 3. Source Code in Github here:
https://github.com/aquasecurity/trivy

## 4. Running Trivt from Docker CLI commands
```
docker pull aquasec/trivy

# get container pulled locally to scan it
docker pull hackersploit/bwapp-docker

# Scan container locally
docker run --rm -v trivy:/root/.cache aquasec/trivy hackersploit/bwapp-docker

# Pull and scan Ubuntu:18:04 image
docker run --rm aquasec/trivy image ubuntu:18:04

# Pull  docker image : vulnerables/cve-2014-6271
docker pull vulnerables/cve-2014-6271

# Scan image : vulnerables/cve-2014-6271
docker run --rm aquasec/trivy image vulnerables/cve-2014-6271

# Run usung a cache
docker run -v /var/run/docker.sock:/var/run/docker.sock -v $HOME/Library/Caches:/root/.cache/ aquasec/trivy:0.56.2 image python:3.4-alpine
```

## 5. Creating a bash shell script to run the trivy scan command
Create this file
```
nano /usr/bin/trivy_scan

```
Put this in the file :
```
#!/bin/bash

# Check if an image name was provided
if [ -z "$1" ]; then
  echo "Usage: $0 <docker-image-to-scan>"
  exit 1
fi

# Run the Trivy scan on the provided Docker image
docker run --rm aquasec/trivy image "$1"

```
Run this command to make script execurable
```
sudo chmod 775 /usr/bin/trivy-scan
```

Run the script with a Docker image as a parameter:
```
trivy_scan vulnerables/cve-2014-6271
```


## 6. Docker Compose file:
Run this command:
```
nano compose.yaml
```

Put this in the file:
```
version: '3'
services:
  trivy:
    image: aquasec/trivy:latest
    volumes:
      - ./docker.sock:/var/run/docker.sock
      - ./trivy-cache:/root/.cache
    command: image python:3.4-alpine

```

Start in detached mode
```
docker compose up -d
```

## Bibliography
https://www.youtube.com/watch?v=lVGAWdKDS9A

