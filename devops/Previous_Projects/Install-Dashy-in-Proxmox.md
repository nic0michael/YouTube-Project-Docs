---
title: "Install Dashy in Proxmox"
date: 2024-03-08T16:42:30Z
draft: false
---
**Dashy is a self-hosted dashboard, to help you keep your Homelab organized** \
Dashy is a self-hosted fully customizable dashboard that you can save bookmarks to. You can customize your bookmarks to display in various different ways. Dashy can also integrate with other self-hosted applications so their data can be displayed on the dashboard. You can use Dashy on all your devices by using the IP address as your homepage.
## 1. Live demo:
**[https://demo.dashy.to/](https://demo.dashy.to/)**

## 2. Assumptions made
1. You have docker installed in one of your Proxmox VMs or Containers (micro VMs)
2. That you will use the docker compose file
3. We will install this file using the Dashy procedures
4. **If you have not installed docker and Portainer please watch this video:** \
**[Full Proxmox installation of LXC Container, Docker , Portainer, and Smokeping](https://youtu.be/Jd8OkutJGr0)**

## 3. Procedures
[Dashy Quick start docs](https://dashy.to/docs/quick-start/) \
[Official Docker compose file](https://github.com/Lissy93/dashy/blob/master/docker-compose.yml) \
[Official dashy config files](https://gist.github.com/Lissy93/000f712a5ce98f212817d20bc16bab10) 

## 4. Dashy resources
**[Official dashy config files](https://gist.github.com/Lissy93/000f712a5ce98f212817d20bc16bab10)** \
**[Further Customization](https://github.com/Lissy93/dashy/blob/master/docs/management.md)** \
**[Example Configuration Files For Dashy](https://gist.github.com/Lissy93/000f712a5ce98f212817d20bc16bab10)** \
**[Free Dashboard Icons](https://github.com/walkxhub/dashboard-icons)** \
**[Dashy Showcase](https://dashy.to/docs/showcase)** \
**[Application Management](https://github.com/Lissy93/dashy/blob/master/docs/management.md)**

## 4. Install it as a docker command
This is a quick way to get Dashy running \
However we dont recomend this way of doing the installation \
Run this command:
```
docker run -d \
  -p 9080:80 \
  -v ~/my-conf.yml:/app/public/conf.yml \
  --name my-dashboard \
  --restart=always \
  lissy93/dashy:latest

```
Open your browser to your server and port 9080 \
**http://10.1054.2.87:9080**
## 5. Simple installation with docker compose file

Run these commands :
```
mkdir /opt/dashy/
cd /opt/dashy/


nano docker-compose.yml
```

put this in the file:
```
---
version: "3.8"
services:
  dashy:
    # To build from source, replace 'image: lissy93/dashy' with 'build: .'
    # build: .
    image: lissy93/dashy
    container_name: Dashy
    # Pass in your config file below, by specifying the path on your host machine
    # volumes:
      # - /root/my-config.yml:/app/public/conf.yml
    ports:
      - 4000:80
    # Set any environmental variables
    environment:
      - NODE_ENV=production
    # Specify your user ID and group ID. You can find this by running `id -u` and `id -g`
    #  - UID=1000
    #  - GID=1000
    # Specify restart policy
    restart: unless-stopped
    # Configure healthchecks
    healthcheck:
      test: ['CMD', 'node', '/app/services/healthcheck']
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s
```
run this command: (Detached mode)
```
docker compose up -d
```
Detached mode, often denoted by the -d flag in Docker commands, allows you to run a Docker container in the background, meaning it runs independently of the current shell session.>


open your browser to your server and port 4000 \
[http://10.1054.2.87:4000](http://10.1054.2.87:4000)
## 6. Install Hasy using a docker compose file and a conf.yml file
### 6.1 Adding the docker-compose.yml file
Run these commands :
```
mkdir /opt/dashy/
cd /opt/dashy/


nano docker-compose.yml
```

put this in the file:
```
---
version: "3.8"

services:
  dashy:
    image: lissy93/dashy
    container_name: Dashy
    ports:
      - 4000:80
    environment:
      - NODE_ENV=production
    restart: unless-stopped
    healthcheck:
      test: ['CMD', 'node', '/app/services/healthcheck']
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s
    volumes:
      - /opt/dashy/conf.yml:/app/public/conf.yml

```
### 6.2 Adding the first config file conf1.yml file (Example1)
Please read this first : \
**[Official dashy config files](https://gist.github.com/Lissy93/000f712a5ce98f212817d20bc16bab10)**

Run this command :
```
nano conf1.yml
```
put this in the file:
```
---
# Page meta info, like heading, footer text and nav links
pageInfo:
  title: Dashy
  description: Welcome to your new dashboard!
  navLinks:
  - title: GitHub
    path: https://github.com/Lissy93/dashy
  - title: Documentation
    path: https://dashy.to/docs

# Optional app settings and configuration
appConfig:
  theme: colorful

# Main content - An array of sections, each containing an array of items
sections:
- name: Getting Started
  icon: fas fa-rocket
  items:
  - title: Dashy Live
    description: Development a project management links for Dashy
    icon: https://i.ibb.co/qWWpD0v/astro-dab-128.png
    url: https://live.dashy.to/
    target: newtab
  - title: GitHub
    description: Source Code, Issues and Pull Requests
    url: https://github.com/lissy93/dashy
    icon: favicon
  - title: Docs
    description: Configuring & Usage Documentation
    provider: Dashy.to
    icon: far fa-book
    url: https://dashy.to/docs
  - title: Showcase
    description: See how others are using Dashy
    url: https://github.com/Lissy93/dashy/blob/master/docs/showcase.md
    icon: far fa-grin-hearts
  - title: Config Guide
    description: See full list of configuration options
    url: https://github.com/Lissy93/dashy/blob/master/docs/configuring.md
    icon: fas fa-wrench
  - title: Support
    description: Get help with Dashy, raise a bug, or get in contact
    url: https://github.com/Lissy93/dashy/blob/master/.github/SUPPORT.md
    icon: far fa-hands-helping
```
### 6.3 Create a second config file conf2.yml (Example2)
Run this command :
```
nano conf2.yml
```
put this in the file:
```
---
pageInfo:
  title: Bookmarks
  navLinks:
  - title: Home
    path: /
  - title: About
    path: /about
  - title: Source Code
    path: https://github.com/Lissy93/dashy
appConfig:
  theme: dracula
  fontAwesomeKey: 13014ae648
sections:
- name: Productivity
  items:
  - title: ProtonMail
    description: Secure Encrypted Email
    icon: favicon
    url: https://mail.protonmail.com/
  - title: AnonAddy
    description: Mail aliasing and forwarding service
    icon: favicon
    url: https://app.anonaddy.com/
  - title: LessPass
    description: Deterministic password generator
    icon: favicon
    url: https://lesspass.com/
  - title: EteSync
    description: Calendar, contacts and tasks
    icon: favicon
    url: https://client.etesync.com/
  - title: Live Coin Watch
    description: Real-time crypto prices and read-only portfolio
    icon: favicon
    url: https://www.livecoinwatch.com/
  - title: Standard Notes
    description: Encrypted productivity suit
    icon: favicon
    url: https://app.standardnotes.org/
- name: Social
  items:
  - title: Discord
    icon: fab fa-discord
    url: https://discord.com/channels/
  - title: Mastodon
    icon: fab fa-mastodon
    url: https://mastodon.social/
  - title: Reddit
    icon: fab fa-reddit
    url: https://www.reddit.com/
  - title: Twitter
    icon: fab fa-twitter
    url: https://twitter.com/
  - title: YouTube
    icon: fab fa-youtube
    url: https://youtube.com/
  - title: Instagram
    icon: fab fa-instagram
    url: https://www.instagram.com/
- name: Coding
  displayData:
    rows: 2
  items:
  - title: StackOverflow
    icon: fab fa-stack-overflow
    url: https://stackoverflow.com/
  - title: GitHub
    icon: fab fa-github
    url: https://github.com/
  - title: Grep App
    icon: fas fa-slash
    description: Allows you to search the contents of files within GitHub repos, with a RegEx option too
    url: https://grep.app/
  - title: Hoppscotch
    icon: fas fa-dice-d6
    description: API development tool
    url: https://hoppscotch.io/
  - title: Regexr
    icon: fas fa-code
    description: App for creating, testing and understanding regular expressions
    url: https://regexr.com/
  - title: Tmate
    icon: fas fa-terminal
    description: Instant TMUX-based terminal sharing
    url: https://tmate.io/
  - title: Debian Handbook
    icon: fab fa-linux
    url: https://debian-handbook.info/browse/stable/
  - title: Crontab Guru
    description: Editor for cron schedule expressions
    icon: fas fa-pen-square
    url: https://crontab.guru/
  - title: CyberChef
    description: All in one encryption, encoding, hashes etc
    icon: fas fa-user-astronaut
    url: https://gchq.github.io/CyberChef/
  - title: SSL Labs Tester
    description: Check SSL Certificates
    icon: fab fa-expeditedssl
    url: https://www.ssllabs.com/ssltest/
  - title: JSON Formatter
    description: JSON validator, beautifier and convertor
    url: https://jsonformatter.org/
    icon: fas fa-brackets-curly
  - title: JSFiddle
    url: https://jsfiddle.net/
    icon: fab fa-jsfiddle
  - title: Digital Ocean
    description: Affordable, preformant cloud services
    url: https://cloud.digitalocean.com/
    icon: fab fa-digital-ocean
  - title: HealthChecks.io
    description: Off-site cron job and service monitoring
    icon: fal fa-monitor-heart-rate
    url: https://healthchecks.io/
- name: Utilities
  displayData:
    itemSize: small
    cols: 2
  items:
  - title: Chroniker Timer
    description: A productivity timer
    url: https://chroniker.co/
  - title: CopyChar
    description: easily search and copy any symbol
    url: https://copychar.cc/
  - title: DeepL
    description: Fast, accurate, secure online multi-language translator
    url: https://www.deepl.com/translator
  - title: Font Generator
    description: Use ASCII characters for different plaintext fonts
    url: https://www.fontgeneratoronline.com/
  - title: JSON Formatter
    description: CSV, JSON, YAML, XML and more formatting tools
    url: https://jsonformatter.org/
  - title: Time.Is
    description: Time.Is
    url: https://time.is/
  - title: Torrent Parts
    description: Inspect and edit what's in your Torrent file or Magnet link
    url: https://torrent.parts/
  - title: Photo College
    url: https://www.befunky.com/create/collage/
  - title: MD5 Hash
    url: http://www.miraclesalad.com/webtools/md5.php
  - title: GitHub Stars Over Time
    url: https://seladb.github.io/StarTrack-js/
  - title: Text & List Tools
    url: https://pinetools.com/c-text-lists/
  - title: Online Text Tools
    description: More text tools, allowing for RegEx actions
    url: https://onlinetexttools.com/
  - title: Conversao
    description: Instantly convert a unit to all others
    url: https://conversao.net/eng/
  - title: Omni Calculators
    description: A collection of thousands of calculators
    url: https://www.omnicalculator.com/
  - title: Dr Meme
    description: Meme Generator
    url: https://www.drmemes.com/
  - title: ASCII Tree Generator
    description: For displaying directory structure
    url: https://ascii-tree-generator.com/
  - title: ASCII Diagram Drawer
    description: For using box characters to draw boxes
    url: For using box characters to draw boxes
  - title: Bullet Converter
    description: Small script I use
    url: https://listed.to/p/LxO8yHsB9E#bc
  - title: Lenny Face Maker
    description: Easily generate ASCII Lenny emojis
    url: https://www.fontspace.com/lenny-face
```

Run thiis command :
```
cp -f conf1.yml conf.yml
```


run this command: (Detached mode)
```
docker compose up -d
```
Detached mode, often denoted by the -d flag in Docker commands, allows you to run a Docker container in the background, meaning it runs independently of the current shell session. When you run a container in detached mode, Docker returns the control of the terminal to you immediately after starting the container, enabling you to continue using the terminal for other tasks.


open your browser to your server and port 4000 \
**[http://10.154.2.87:4000/](http://10.154.2.87:4000/)**

**Click on the Theme Dropdown Box**

Run this command:
```
cp -f conf2.yml conf.yml
```
**Refresh browser**

## 6. Open Portainer
**[http://10.154.2.87:9000/](http://10.154.2.87:9000/)** \
from here we can manage our docker containers

## 7. We made our own Proxmox Dashboard for Dashy
**Read these documents to configure your Dashboard with widgets:** \
https://dashy.to/docs/widgets \
https://dashy.to/docs/widgets/#clock \
https://dashy.to/docs/widgets/#weather \
https://dashy.to/docs/widgets/#exchange-rates \
https://dashy.to/docs/widgets/#proxmox-lists \
https://dashy.to/docs/widgets/#prometheus-data

**Setup Authentication** \
https://dashy.to/docs/authentication/

### We have added extra buttons that you can change for your own use
Run this command
```
nano proxmox-dashy-conf.yml
```
put this in the file
```
---
# Page meta info, like heading, footer text and nav links
pageInfo:
  title: Dashy Dashboard V2.0
  description: Proxmox Companion Dashboard
  logo: https://i.ibb.co/71WyyzM/little-bot-3.png

# Optional app settings and configuration
appConfig:
  statusCheck: false
  theme: charry-blossom
  fontAwesomeKey: c94dc2b452
  customCss: '.clock p.time { font-size: 3rem !important; }'
  layout: vertical
  iconSize: small

# Main content - An array of sections, each containing an array of items
sections:
- name: Today
  icon: far fa-smile-beam
  displayData:
    collapsed: false
    hideForGuests: false
  widgets:
  - type: clock
  - type: weather
    options:
      apiKey: efdbade728b37086877a5e83442004db
      city: London
  - type: crypto-watch-list
    options:
      currency: GBP
      sortBy: marketCap
      assets:
      - bitcoin
      - ethereum
      - monero
      - solana
      - polkadot

- name: Support
  icon: far fa-briefcase
  items:
  - title: Proxmox
    icon: favicon
    url: https://upupa.loseyourip.com:8006/
  - title: Portainer
    icon: favicon
    url: http://10.154.2.87:9000/#!/home
  - title: Ansible Semaphore
    icon: favicon
    url: http://leopard.loseyourip.com:3000/auth/login
  - title: Jenkins
    icon: favicon
    url: http://lion.loseyourip.com:8080/login?from=%2F
  - title: Netlify
    icon: favicon
    url: https://app.netlify.com/
  - title: CodeSandbox
    icon: favicon
    url: https://codesandbox.io/dashboard
  - title: Hack the Box
    icon: favicon
    url: https://www.hackthebox.com/home
  - title: DynuDNS
    icon: favicon
    url: https://www.dynu.com/en-US/



- name: Development
  icon: far fa-thumbs-up
  items:
  - title: GitHub
    icon: favicon
    url: https://github.com/
  - title: StackOverflow
    icon: favicon
    url: http://stackoverflow.com/
  - title: CloudFlare
    icon: favicon
    url: https://dash.cloudflare.com/
  - title: DigitalOcean
    icon: favicon
    url: https://cloud.digitalocean.com/
  - title: Netlify
    icon: favicon
    url: https://app.netlify.com/
  - title: CodeSandbox
    icon: favicon
    url: https://codesandbox.io/dashboard
  - title: Hack the Box
    icon: favicon
    url: https://www.hackthebox.com/home

- name: Personal
  icon: far fa-code
  items:
  - title: YahooMail
    icon: favicon
    url: https://mail.yahoo.com/?.intl=us&.partner=ftr&.lang=en-us
    description: Primary email account
    tags: [hosted, personal, email, mail]
    hotkey: 1
  - title: Bitwarden
    icon: favicon
    url: https://vault.bitwarden.com/#/login
    tags: [hosted, personal, passwords ]
    hotkey: 2
  - title: Banggood
    icon: favicon
    url: https://www.banggood.com/index.php
    hotkey: 3
  - title: eBay
    icon: favicon
    url: https://www.ebay.com/
    hotkey: 4
  - title: Wish
    icon: favicon
    url: https://www.wish.com/
    hotkey: 5
  - title: Temu
    icon: favicon
    url: https://www.temu.com/za-en
    tags: [hosted, personal, notes ]
    hotkey: 6
  - title: Bitwarden
    icon: favicon
    url: https://vault.bitwarden.com/#/login
    tags: [hosted, personal, passwords ]
    hotkey: 7
  - title: Takealot
    icon: favicon
    url: https://www.takealot.com/
    hotkey: 8
  - title: BotShop
    icon: favicon
    url: https://botshop.co.za/
    hotkey: 9
  - title: MicroRobotics
    icon: favicon
    url: https://www.robotics.org.za/
    hotkey: 10


- name: Social
  icon: far fa-thumbs-up
  items:
  - title: Discord
    icon: si-discord
    url: https://discord.com/channels/
  - title: Mastodon
    icon: si-mastodon
    url: https://mastodon.social/
  - title: Reddit
    icon: si-reddit
    url: https://www.reddit.com/
  - title: HackerNews
    icon: si-ycombinator
    url: https://news.ycombinator.com/
  - title: Twitter
    icon: si-twitter
    url: https://twitter.com/
  - title: YouTube
    icon: si-youtube
    url: https://youtube.com/
  - title: Instagram
    icon: si-instagram
    url: https://www.instagram.com/
  - title: News
    icon: si-bbc
    url: https://bbc.co.uk/news
  - title: Crypto Prices
    icon: fab fa-bitcoin
    url: https://www.livecoinwatch.com/
    description: Real-time crypto prices and read-only portfolio
    provider: Live Coin Watch

```
run this command:
```
cp -f proxmox-dashy-conf.yml conf.yml
```

## Bibliography
https://gist.github.com/Lissy93 \
https://dashy.to/ \
https://dashy.to/docs/quick-start/ \
https://github.com/Lissy93/dashy/blob/master/docker-compose.yml \
dashy config files : https://gist.github.com/Lissy93/000f712a5ce98f212817d20bc16bab10 \
https://addictedtotech.net/install-dashy-dashboard-using-portainer-and-docker-on-a-raspberry-pi-4-episode-30/

