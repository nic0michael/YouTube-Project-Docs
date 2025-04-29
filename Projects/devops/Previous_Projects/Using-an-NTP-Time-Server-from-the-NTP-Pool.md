---
title: "Using an NTP Time Server From the NTP Pool"
date: 2024-01-11T08:57:00Z
draft: false
---

## 1. What is NTP (Network Time Protocol)?

1. **Standard protocol for time synchronization in computer networks.***
Ensures accurate and consistent timekeeping across connected devices.
Clock Synchronization:

2. **NTP synchronizes clocks on servers and devices.**
Mitigates issues related to time discrepancies in distributed systems.
Precision and Accuracy:

3. **Facilitates precise timekeeping by adjusting clocks to a common reference.**
Crucial for various applications, including security, log management, and global coordination.


## 2. Why should we install NTP on our servers?

1. **NTP (Network Time Protocol) ensures accurate and synchronized time across servers.** \
Critical for consistent log entries, debugging, and coordination between systems.
Security:

2. **Timestamps are crucial for security protocols.** \
Synchronized time helps in detecting and investigating security incidents.
Transaction Ordering:

3. **Essential for applications with distributed databases or transactions.** \
Maintains chronological order and consistency in data transactions.
Legal and Compliance:

4. **Compliance requirements often mandate accurate and synchronized timekeeping.** \
Timestamps are crucial for legal and regulatory purposes.
Prevents Data Inconsistencies:

5. **Helps prevent data discrepancies that can occur when systems have divergent time settings.** \
Consistent timestamps aid in data integrity and reliability.
Log Analysis and Troubleshooting:

6. **Facilitates log correlation and analysis across multiple servers.** \
Simplifies troubleshooting by providing accurate time references.
Authentication and Authorization:

7. **Timestamps play a role in authentication protocols.** \
Ensures accurate token validity periods, enhancing security.
Global Operations:

8. **Especially crucial in environments with geographically distributed servers.** \
Ensures coordination across different time zones, preventing operational issues.
Prevents Skewed Metrics:

9. **Important for performance monitoring and metric analysis.** \
Accurate time synchronization ensures reliable performance metrics.
Network Efficiency:

10. **Helps in scheduling tasks and managing resources efficiently.** \
Reduces potential issues arising from time-related inconsistencies in networked systems.


## 3. International and National NTP Pools 
[NTP Pool UK](https://www.ntppool.org/en/zone/uk) \
[NTP Pool USA](https://www.ntppool.org/en/zone/us) \
[NTP Pool Australia](https://www.ntppool.org/en/zone/au) \
[NTP Pool ZA](https://www.ntppool.org/en/zone/za) \

### 3.1 Pools by Continent
See all zones in [North America](https://www.ntppool.org/zone/north-america) \
See all	zones in [South America](https://www.ntppool.org/zone/south-america) \
See all zones in [Europe](https://www.ntppool.org/zone/europe) \
See all zones in [Africa](https://www.ntppool.org/zone/africa) \
See all zones in [Asia](https://www.ntppool.org/zone/asia) \
See All zones in [Oceania](https://www.ntppool.org/zone/oceania) \
See all zones in [All Pool Servers](https://www.ntppool.org/zone) 

For other countries: https://www.ntppool.org/en/zone/{internet country codes} \
[International internet country code list](https://en.wikipedia.org/wiki/Country_code_top-level_domain) 

**USA settings:**
```
server 0.us.pool.ntp.org
server 1.us.pool.ntp.org
server 2.us.pool.ntp.org
server 3.us.pool.ntp.org
```
USE :
```
pool 0.us.pool.ntp.org iburst
pool 1.us.pool.ntp.org iburst
pool 2.us.pool.ntp.org iburst
pool 3.us.pool.ntp.org iburst
```
## 4. Setting NTP on Linux Servers
### 4.1 Setting NTP on Ubuntu Servers
**Install NTP on Ubuntu server**
```
sudo su -

sudo apt update

sudo apt install ntp
```


**Edit your NTP Pool: **
```
sudo nano /etc/ntp.conf
```
Look for :
```
# Specify one or more NTP servers.
```

**Put this into the file**
```
server 0.us.pool.ntp.org
server 1.us.pool.ntp.org
server 2.us.pool.ntp.org
server 3.us.pool.ntp.org
```

**Check status of ntp service:**
```
sudo service --status-all | grep ntp

sudo systemctl status ntp
```
**Restart the Ntp service**
```
sudo systemctl restart ntp
```
### 4.2 Setting NTP on Rocky Linux
```
sudo dnf update

timedatectl

sudo timedatectl set-ntp on

sudo dnf install chrony -y

sudo systemctl start chronyd
sudo systemctl enable chronyd
sudo systemctl status chronyd
```
**Edit NTP configuration**
```
sudo nano /etc/chrony.conf

#  add # before the “pool 2.rocky.pool
```

**Add the folowing entries for a server in the USA**
```	   
server 0.us.pool.ntp.org
server 1.us.pool.ntp.org
server 2.us.pool.ntp.org
server 3.us.pool.ntp.org
```
**Enable the NTP sync by running the below command:**
```
sudo timedatectl set-ntp true
```
**Restart the Chrony service to make changes successful:**
```
sudo systemctl restart chronyd
```
**To display the Chronyd tracking that shows the complete statistics about the system:**
```
chronyc tracking
```

**Use this command to verify that NTP is using Chrony for the time sync:**
```
chronyc sources
```

## 5. Setting NTP on Proxmox Servers
**Edit NTP configuration**
```
sudo nano /etc/chrony/chrony.conf
```
**You should locate this entry:**
```
# Use Debian vendor zone.
pool 2.debian.pool.ntp.org iburst

```

**The relevant dynamic NTP servers are dished out from:**
```
server 0.debian.pool.ntp.org iburst dynamic
server 1.debian.pool.ntp.org iburst dynamic
server 2.debian.pool.ntp.org iburst dynamic
server 3.debian.pool.ntp.org iburst dynamic
```
**Use server for your country:** \
**USA**
```
pool 0.us.pool.ntp.org iburst
pool 1.us.pool.ntp.org iburst
pool 2.us.pool.ntp.org iburst
pool 3.us.pool.ntp.org iburst
```
**UK**
```
pool 0.uk.pool.ntp.org iburst
pool 1.uk.pool.ntp.org iburst
pool 2.uk.pool.ntp.org iburst
pool 3.uk.pool.ntp.org iburst

```
**ZA**
```
pool 0.za.pool.ntp.org iburst
pool 1.za.pool.ntp.org iburst
pool 2.za.pool.ntp.org iburst
pool 3.za.pool.ntp.org iburst
```



**Apply changes**
```
sudo systemctl restart chronyd
```
**Check time on your proxmox server**
```
date
```
## Bibliography
https://www.ntppool.org/zone \
https://www.digitalocean.com/community/tutorials/how-to-configure-ntp-for-use-in-the-ntp-pool-project-on-ubuntu-16-04 \
https://linuxhint.com/configure-ntp-linux-mint/ \
https://www.unixmen.com/syncronise-time-ntp-server-ubuntu-mintdebian/ \
https://forums.linuxmint.com/viewtopic.php?t=378554 \
https://linuxhint.com/configure-ntp-time-rocky-linux-9/ \
https://linuxconfig.org/ubuntu-22-04-ntp-server
