---
title: "Self-host This and say Goodbye to Ads,Tracking,and spying from Google"
date: 2025-03-21T15:57:45Z
draft: false
---
## 1. Why Use Whoogle?

1. **What is Whoogle?**  
   Whoogle is a self-hosted, privacy-friendly proxy for Google Search that removes ads, tracking, and JavaScript.  

2. **Privacy Protection**  
   No tracking, no logs, and no connection to your Google account—ensuring fully anonymous searches.  

3. **Ad-Free Experience**  
   Unlike Google Search, Whoogle strips out all ads, giving you clean, distraction-free results.  

4. **Self-Hosted Control**  
   You can deploy Whoogle on your own server or Docker container, ensuring full control over your data and search experience.  

## 2. Website
**[https://github.com/benbusby/whoogle-search/blob/main/docker-compose.yml](https://github.com/benbusby/whoogle-search/blob/main/docker-compose.yml)**

**[https://github.com/benbusby/whoogle-search/tree/main?tab=readme-ov-file#manual](https://github.com/benbusby/whoogle-search/tree/main?tab=readme-ov-file#manual)**


## 3. Installation
**Run these commands:**
```bash

sudo su -

mkdir /opt/whoogle -p

cd /opt/whoogle

nano compose.yaml
```

**Put this in the file:**
```yaml
# can't use mem_limit in a 3.x docker-compose file in non swarm mode
# see https://github.com/docker/compose/issues/4513
version: "2.4"

services:
  whoogle-search:
    image: ${WHOOGLE_IMAGE:-benbusby/whoogle-search}
    container_name: whoogle-search
    restart: unless-stopped
    pids_limit: 50
    mem_limit: 256mb
    memswap_limit: 256mb
    # user debian-tor from tor package
    user: whoogle
    security_opt:
      - no-new-privileges
    cap_drop:
      - ALL
    tmpfs:
      - /config/:size=10M,uid=927,gid=927,mode=1700
      - /var/lib/tor/:size=15M,uid=927,gid=927,mode=1700
      - /run/tor/:size=1M,uid=927,gid=927,mode=1700
    #environment: # Uncomment to configure environment variables
      # Basic auth configuration, uncomment to enable
      #- WHOOGLE_USER=<auth username>
      #- WHOOGLE_PASS=<auth password>
      # Proxy configuration, uncomment to enable
      #- WHOOGLE_PROXY_USER=<proxy username>
      #- WHOOGLE_PROXY_PASS=<proxy password>
      #- WHOOGLE_PROXY_TYPE=<proxy type (http|https|socks4|socks5)
      #- WHOOGLE_PROXY_LOC=<proxy host/ip>
      # Site alternative configurations, uncomment to enable
      # Note: If not set, the feature will still be available
      # with default values.
      #- WHOOGLE_ALT_TW=farside.link/nitter
      #- WHOOGLE_ALT_YT=farside.link/invidious
      #- WHOOGLE_ALT_IG=farside.link/bibliogram/u
      #- WHOOGLE_ALT_RD=farside.link/libreddit
      #- WHOOGLE_ALT_MD=farside.link/scribe
      #- WHOOGLE_ALT_TL=farside.link/lingva
      #- WHOOGLE_ALT_IMG=farside.link/rimgo
      #- WHOOGLE_ALT_WIKI=farside.link/wikiless
      #- WHOOGLE_ALT_IMDB=farside.link/libremdb
      #- WHOOGLE_ALT_QUORA=farside.link/quetre
      #- WHOOGLE_ALT_SO=farside.link/anonymousoverflow
    #env_file: # Alternatively, load variables from whoogle.env
      #- whoogle.env
    ports:
      - 5000:5000
```

## 4. Open browser here:
**[http://your-servers-ip:5000/](http://tiger.loseyourip.com:5000/)**

**[http://tiger.loseyourip.com:5000/](http://tiger.loseyourip.com:5000/)**
