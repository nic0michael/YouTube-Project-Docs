---
title: "Install Kafka on Linux"
date: 2024-01-18T12:52:07Z
draft: false
---
We need to start using Kafka with KRaft it now uses that by default
 
## 1. You need Java Open JDK installed
https://youtu.be/Abo7d9SpWXg


## 2. you need to install Zookeeper
```
sudo apt-get install zookeeperd

sudo service zookeeper status


# Check Zookeeper
telnet localhost 2181

# at the Telnet prompt send:
ruok
```
Create zookeeper service
```
sudo nano /etc/systemd/system/zookeeper.service
```
Put in file:
```
[Unit]
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
User=kafka
ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```

expect to get:
```
imok
```

## 2. Create User kafka

```
# create the user kafka
sudo adduser kafka
```

## 3. Make the user a mamber of sudoers group
```
usermod -aG sudo kafka
```

## 4. log in as a user kafka:
```
su -l kafka
```

## 5. Dowload Kafka
[https://kafka.apache.org/downloads](https://kafka.apache.org/downloads)

Binary downloads:
Scala 2.12  - [kafka_2.12-3.6.1.tgz](https://downloads.apache.org/kafka/3.6.1/kafka_2.12-3.6.1.tgz)

```
# Download the kafka binary
curl "https://downloads.apache.org/kafka/3.6.1/kafka_2.12-3.6.1.tgz" --output kafka.tgz

# Extract the Tarball
tar -xvzf kafka.tgz

ls -la

# Rename the kafka_2.12-3.6.1/ folder to  kafka/
mv kafka_2.12-3.6.1/ kafka

ls -la

cd kafka
```

## 6. Configure Kafka to remove topics
Edit the server.properties 
```
nano config/server.properties
```
Add the following line to the bottom of this file
```
delete.topic.enable = true
```

Search for : log.dirs Ctrl W \
replace : /tmp/kafka-logs \
with : /home/kafka/logs
```
#log.dirs=/tmp/kafka-logs
log.dirs=/home/kafka/logs
```
Save server.properties file
## 7. Create a Kafka Service
```
sudo nano /etc/systemd/system/kafka.service
```

Put this in the file:
```
[Unit]
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
User=kafka
ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```

Start the Kafka service using the following command:
```
sudo systemctl start kafka

sudo systemctl status kafka

#If you want this service to start automatically after bootup
sudo systemctl enable kafka
sudo systemctl enable zookeeper



```

add rest of instructions from https://learnubuntu.com/install-kafka/





## Bibliography

https://www.conduktor.io/kafka/how-to-install-apache-kafka-on-linux-without-zookeeper-kraft-mode/


https://learnubuntu.com/install-kafka/ \
https://medium.com/javarevisited/running-kafka-without-zookeeper-66b685db4991#:~:text=Kafka%20uses%20the%20Raft%20consensus%20algorithm%20for%20leader%20election%20in,machines%20in%20a%20distributed%20system.





https://kafka.apache.org/quickstart

https://hevodata.com/blog/how-to-install-kafka-on-ubuntu/

https://www.digitalocean.com/community/tutorials/how-to-install-apache-kafka-on-ubuntu-20-04

https://www.linuxtechi.com/how-to-install-apache-kafka-on-ubuntu/

https://learnubuntu.com/install-kafka/

https://tecadmin.net/how-to-install-apache-kafka-on-ubuntu-22-04/


