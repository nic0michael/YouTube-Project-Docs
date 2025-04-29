---
title: "Install PeerTube in Proxmox"
date: 2024-03-31T14:09:00Z
draft: false
---
## 1. What is PeerTube
https://joinpeertube.org/


## 1. PeerTube Installation Instructions
[https://docs.joinpeertube.org/install/any-os](https://docs.joinpeertube.org/install/any-os)

### Assumptions made
You have created an Ubuntu LXC Container and installed Docker in it. \
If you do not know who to do this please watch this video first: \
[https://youtu.be/ixpQjccC9qI](https://youtu.be/ixpQjccC9qI) \
Then read this document : \
http://rino.kozow.com/devops/posts/install-droppy-in-your-proxmox-server/

### 1.1 Install in Ubuntu LXC Container Docker Container

we will create this machine :
```
buffalo.loseyourip.com 10.154.2.85
```

1. We created a Proxmox Ubuntu Server 22.04 Container
2. Hardware allocated 8GB RAM 2CPU cores and 200GB dickspace

Start Container and open Console and login to the server

### 1.2 Prerequisites
```
sudo su -

apt update

apt install curl wget nano -y
```

### 1.3 after installing docker
The instructions for this step can be found here: \
[https://docs.joinpeertube.org/install/docker](https://docs.joinpeertube.org/install/docker)

The Docker Compose file is available here:
[Docker compose file](https://github.com/Chocobozzz/PeerTube/blob/master/support/docker/production/docker-compose.yml)


run these commands

```
mkdir /opt/peertube

cd /opt/peertube

# Download docker-compose.yml file
wget https://github.com/Chocobozzz/PeerTube/blob/master/support/docker/production/docker-compose.yml
# Get the latest Compose file
curl https://raw.githubusercontent.com/chocobozzz/PeerTube/master/support/docker/production/docker-compose.yml > docker-compose.yml

# edit this file
nano docker-compose.yml
# no changes needed

# download environment file
wget https://github.com/Chocobozzz/PeerTube/blob/master/support/docker/production/.env
# Get the latest env_file
curl https://raw.githubusercontent.com/Chocobozzz/PeerTube/master/support/docker/production/.env > .env


# generate Secret Key
openssl rand -hex 32
# expect to get
c1265fa03e8e1124d2ac2eec8248a6cd0e20a5aa7635028d828651cfea2fddbe

# edit the environment file
nano .env
```
In the downloaded example .env, you must replace:

1. <MY POSTGRES USERNAME>
2. <MY POSTGRES PASSWORD>
3. <MY DOMAIN> without 'https://'
4. <MY EMAIL ADDRESS>
5. <MY PEERTUBE SECRET>

Make these changes
```
POSTGRES_USER=<MY POSTGRES USERNAME> set as admin
POSTGRES_PASSWORD=<MY POSTGRES PASSWORD> set as Pa55w0rd

POSTGRES_USER=admin
POSTGRES_PASSWORD=Pa55w0rd

PEERTUBE_SECRET=<MY PEERTUBE SECRET> set with the Secret key we generated
PEERTUBE_SECRET=c1265fa03e8e1124d2ac2eec8248a6cd0e20a5aa7635028d828651cfea2fddbe


PEERTUBE_SMTP_FROM=noreply@<MY DOMAIN> set as noreply@buffalo.loseyourip.com
PEERTUBE_ADMIN_EMAIL=<MY EMAIL ADDRESS>set as nico@buffalo.loseyourip.com


PEERTUBE_SMTP_FROM=noreply@buffalo.loseyourip.com
PEERTUBE_ADMIN_EMAIL=nico@buffalo.loseyourip.com

# Set <MY DOMAIN> VALUES HERE:
PEERTUBE_WEBSERVER_HOSTNAME=<MY DOMAIN> set as buffalo.loseyourip.com
PEERTUBE_WEBSERVER_HOSTNAME=buffalo.loseyourip.com

POSTFIX_myhostname=<MY DOMAIN> set as buffalo.loseyourip.com
POSTFIX_myhostname=buffalo.loseyourip.com

OPENDKIM_DOMAINS=<MY DOMAIN>=peertube use buffalo.loseyourip.com
OPENDKIM_DOMAINS=buffalo.loseyourip.com=peertube

```

Install the template that the nginx container will use. \
The container will generate the configuration by replacing ${WEBSERVER_HOST} and ${PEERTUBE_HOST} using your docker compose env file.

run these commands:
```
mkdir -p docker-volume/nginx

curl https://raw.githubusercontent.com/Chocobozzz/PeerTube/master/support/nginx/peertube > docker-volume/nginx/peertube
```

You need to manually generate the first SSL/TLS certificate using Let's Encrypt:
```
mkdir -p docker-volume/certbot

docker run -it --rm --name certbot -p 80:80 -v "$(pwd)/docker-volume/certbot/conf:/etc/letsencrypt" certbot/certbot certonly --standalone

# You will be prompted for an email address

# you need to agree to the terms

# then they ask if they can send you emails

# then they ask for the domain to test : buffalo.loseyourip.com

```

## 1.4 Start Peertube Docker Container
Run this command to start the container in detached mode
```
docker compose up -d
```

Obtaining your automatically-generated admin credentials \
```
# You can also grep your peertube container's logs for the default root password. You're going to want to run 
cd /opt/peertube

docker compose logs peertube | grep -A1 root

```

**login as root and use this password**



## 2. Installing Peertube in an Ubuntu Server

### 2.2 Installing the Prerequisites
[https://docs.joinpeertube.org/support/doc/dependencies#debian-ubuntu-and-derivatives](https://docs.joinpeertube.org/support/doc/dependencies#debian-ubuntu-and-derivatives)

Run these commands:
```
sudo su -

apt update 

apt upgrade -y

apt install nano curl wget unzip

sudo apt install python3-dev python-is-python3 # python-is-python2 should also work
# verify Python
python --version 
# Should be >= 2.x or >= 3.x


# Install common dependencies:

sudo apt install certbot nginx ffmpeg postgresql postgresql-contrib openssl g++ make redis-server git cron wget

ffmpeg -version 
# Should be >= 4.1

g++ -v 
# Should be >= 5.x

redis-server --version 
# Should be >= 6.x

#Now that dependencies are installed, before running PeerTube you should start PostgreSQL and Redis:
sudo systemctl start redis postgresq



```

### 2.3 creating the peertube user
Run these commands:
```
sudo su -

# Create a peertube user with /var/www/peertube home:
sudo useradd -m -d /var/www/peertube -s /bin/bash -p peertube peertube

sudo passwd peertube

sudo usermod -aG sudo peertube

# Ensure the peertube root directory is traversable by 
ls -ld /var/www/peertube 
# Should be drwxr-xr-x
```

### 2.4 Creating  Database
Create the production database and a peertube user inside PostgreSQL:
```
cd /var/www/peertube
sudo -u postgres createuser -P peertube

# Here you should enter a password for PostgreSQL peertube user, that should be copied in production.yaml file. Don't just hit enter else it will be empty.
sudo -u postgres createdb -O peertube -E UTF8 -T template0 peertube_prod

# Then enable extensions PeerTube needs:
sudo -u postgres psql -c "CREATE EXTENSION pg_trgm;" peertube_prod
sudo -u postgres psql -c "CREATE EXTENSION unaccent;" peertube_prod
```

### 2.5 Prepare PeerTube directory
```
sudo su -

# Fetch the latest tagged version of Peertube:
VERSION=$(curl -s https://api.github.com/repos/chocobozzz/peertube/releases/latest | grep tag_name | cut -d '"' -f 4) && echo "Latest Peertube version is $VERSION"

# Open the peertube directory, create a few required directories:
cd /var/www/peertube
sudo -u peertube mkdir config storage versions
sudo -u peertube chmod 750 config/

# Download the latest version of the Peertube client, unzip it and remove the zip:
cd /var/www/peertube/versions
# Releases are also available on https://builds.joinpeertube.org/release
sudo -u peertube wget -q "https://github.com/Chocobozzz/PeerTube/releases/download/${VERSION}/peertube-${VERSION}.zip"
sudo -u peertube unzip -q peertube-${VERSION}.zip && sudo -u peertube rm peertube-${VERSION}.zip


# Install Peertube:
cd /var/www/peertube
sudo -u peertube ln -s versions/peertube-${VERSION} ./peertube-latest
cd ./peertube-latest && sudo -H -u peertube yarn install --production --pure-lockfile
```

### 2.6 PeerTube configuration
Copy the default configuration file that contains the default configuration provided by PeerTube. You must not update this file.
```
cd /var/www/peertube
sudo -u peertube cp peertube-latest/config/default.yaml config/default.yaml

# Now copy the production example configuration:
cd /var/www/peertube
sudo -u peertube cp peertube-latest/config/production.yaml.example config/production.yaml

```
Then edit the config/production.yaml file according to your webserver and database configuration. In particular:

1. webserver: Reverse proxy public information
2. secrets: Secret strings you must generate manually (PeerTube version >= 5.0)
3. database: PostgreSQL settings
4. redis: Redis settings
5. smtp: If you want to use emails
6. admin.email: To correctly fill root user email

### 2.7 Webserver
```
sudo su -

# Copy the nginx configuration template:
sudo cp /var/www/peertube/peertube-latest/support/nginx/peertube /etc/nginx/sites-available/peertube

# Set the domain for the webserver configuration file by replacing [peertube-domain] with the domain for the peertube server:
sudo sed -i 's/${WEBSERVER_HOST}/[peertube-domain]/g' /etc/nginx/sites-available/peertube
sudo sed -i 's/${PEERTUBE_HOST}/127.0.0.1:9000/g' /etc/nginx/sites-available/peertube
```
Then modify the webserver configuration file. Please pay attention to:

1. the alias, root and rewrite directives paths, the paths must correspond to your PeerTube filesystem location
2. the proxy_limit_rate and limit_rate directives if you plan to stream high bitrate videos (like 4K at 60FPS)

```
sudo nano /etc/nginx/sites-available/peertube

# Activate the configuration file:
sudo ln -s /etc/nginx/sites-available/peertube /etc/nginx/sites-enabled/peertube

```

### 2.8 systemd
```
# If your OS uses systemd, copy the configuration template:

sudo cp /var/www/peertube/peertube-latest/support/systemd/peertube.service /etc/systemd/system/

# Check the service file (PeerTube paths and security directives):
sudo vim /etc/systemd/system/peertube.service

# Tell systemd to reload its config:
sudo systemctl daemon-reload

# If you want to start PeerTube on boot:
sudo systemctl enable peertube

# Start PeerTube
sudo systemctl start peertube
sudo journalctl -feu peertube
```
### 1.9 Administrator
The administrator username is root and the password is automatically generated. \
It can be found in PeerTube logs (path defined in production.yaml). \
You can also set another password with:
```
cd /var/www/peertube/peertube-latest && NODE_CONFIG_DIR=/var/www/peertube/config NODE_ENV=production npm run reset-password -- -u root
```










## Bibliography
https://docs.joinpeertube.org/install/docker \
https://github.com/Chocobozzz/PeerTube/tree/master/support/docker/production \
https://www.youtube.com/watch?v=qpkHvnkB4IQ
