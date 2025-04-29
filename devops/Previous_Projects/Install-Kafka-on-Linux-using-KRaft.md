---
title: "Install Kafka on Linux Using KRaft"
date: 2024-01-22T13:21:03Z
draft: false
---

We need to start using Kafka with KRaft it now uses that by default
 
## 1. You need Java Open JDK 17 installed
https://youtu.be/Abo7d9SpWXg

https://computingforgeeks.com/install-java-on-rocky-almalinux-9/#google_vignette
```
sudo yum install java-11-openjdk  -y
```

## 2. Download Kafka as root user
```
sudo su -
```

[https://kafka.apache.org/downloads](https://kafka.apache.org/downloads)
```
cd /opt
wget https://downloads.apache.org/kafka/3.6.1/kafka_2.12-3.6.1.tgz

ls -la
```
Expect to find:
```
kafka_2.12-3.6.1.tgz
```
## 3. Extract the tarball
```
sudo tar xzf kafka_2.12-3.6.1.tgz

ls -la
```
Expect to find directory : kafka_2.12-3.6.1

## 4. Create User kafka
If you installing Kafka on a dedicated server you need to create a Kafka user \
However if in your laptop or PC computer yo skip this step \
In my laptop the user is nickm

```
# create the user kafka
sudo adduser kafka
```

## 5. Make user a member of sudoers group
```
usermod -aG sudo kafka
```
## 6. Make kafka user own /opt/kafka_2.12-3.6.1
```
# For a dedicated Server
sudo chown -R kafka: /opt/kafka_2.12-3.6.1

# For my laptop
sudo chown -R nickm: /opt/kafka_2.12-3.6.1
```

## 7. log in as a user kafka:
```
# For a dedicated Server
su -l kafka

# For my laptop
su -l nickm

```
Run these commands
```
cd /opt/kafka_2.12-3.6.1

ls -la
```
## 8. Generate a new ID for your cluster
```
KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"
echo $KAFKA_CLUSTER_ID
```
This returns a UUID /
Expect to get:
```
WNc4TGllTbWVELltBv1OVw
```


## 9. Setup the $PATH environment variable
In order to easily access the Kafka binaries, you can edit your PATH variable by adding the following line to your system run commands (for example ~/.zshrc if you use zshrc):

```
nano ~/.bashrc
```
Put this in the file
```
PATH="$PATH:/opt/kafka_2.12-3.6.1/bin"
export PATH
```
### 9.1 make the kafka shellscripts commands
Run these commands
```
cd /opt/kafka_2.12-3.6.1/bin
# We need to add Sym Links to .sh files
sudo nano add_symbolic_links.sh
```
Put this in the file:
```
#!/bin/bash

for file in /opt/kafka_2.12-3.6.1/bin/*.sh; do
    ln -s "$file" "${file%.sh}"
done
```
Run these commands
```
sudo chmod +x add_symbolic_links.sh

sudo ./add_symbolic_links.sh

ls -la
```
## 10. Configure Kafka to run in KRaft mode
Edit the server.properties 
```
mkdir /opt/kafka_2.12-3.6.1/logs
nano config/server.properties
```
Add the following line to the bottom of this file
```
controller.mode=kraft
```
## 11. Add additional Server Properties
```
nano config/server.properties
# Search for : log.dirs Ctrl W \
# replace : /tmp/kafka-logs \
# with : /opt/kafka_2.12-3.6.1/logs

#log.dirs=/tmp/kafka-logs
log.dirs=/opt/kafka_2.12-3.6.1/logs
```
Save server.properties file


## 12. Setup your Storage Directory
This will format the directory that is in the log.dirs in the config/kraft/server.properties file (by default /tmp/kraft-combined-logs) \
Run this command:
```
bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/kraft/server.properties
```
Expect to get:
```
Formatting /tmp/kraft-combined-logs with metadata.version 3.6-IV2.
```
Now you can launch the broker itself in daemon mode by running this command.
```
#sudo bin/kafka-server-start.sh config/kraft/server.properties
sudo bin/kafka-server-start config/kraft/server.properties
ctrl+c
```

## 13. Test the Kafka installation
Open a new tab in the terminal
```
sudo su - kafka

/opt/kafka_2.12-3.6.1/bin/kafka-topics.sh --create --topic kraft-test --partitions 1 --replication-factor 1 --bootstrap-server localhost:9092
```
Expect to get
```
Created topic kraft-test.
```
Run this command
```
/opt/kafka_2.12-3.6.1/bin/kafka-topics.sh --describe --bootstrap-server localhost:9092 --topic kraft-test
```
Expect to get
```
Topic: kraft-test       TopicId: LXKm3IZjRWCX9xGK_yIGJQ PartitionCount: 1       ReplicationFactor: 1    Configs: segment.bytes=1073741824
        Topic: kraft-test       Partition: 0    Leader: 1       Replicas: 1     Isr: 1

```
## 14. Using Kafka
### 14.1 Create a Topic to receive User Messages
```
/opt/kafka_2.12-3.6.1/bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
```
### 14.2 Describe the Topic
```
/opt/kafka_2.12-3.6.1/bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092
```

### 14.3 Setup a Producer to send messages
```
/opt/kafka_2.12-3.6.1/bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
```
in the > prompt type
```
>This is my first message
>This is my second message
```

### 14.4 Setup a consumer to receive messages
```
/opt/kafka_2.12-3.6.1/bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092
```
Expect to get
```
This is my first message
This is my second message
```
Ctrl C to exit
## 15. Create a Kafka Service
```
sudo nano /etc/systemd/system/kafka.service
#sudo nano /lib/systemd/system/kafka.service

```

Put this in the file:
```
[Unit]
Description=Kafka Kraft Daemon
Documentation=https://kafka.apache.org/quickstart
Requires=network.target
After=network.target

[Service]
Type=forking
WorkingDirectory=/opt/kafka_2.12-3.6.1
User=kraft
Group=kraft
Environment=KAFKA_HEAP_OPTS="-Xmx1G -Xms1G"
Environment=KAFKA_JVM_PERFORMANCE_OPTS="-XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+ExplicitGCInvokesConcurrent"
Environment="JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64"
#ExecStartPre=/bin/bash -c 'KAFKA_CLUSTER_ID="$(/opt/kafka_2.12-3.6.1/bin/kafka-storage.sh random-uuid)"; /opt/kafka_2.12-3.6.1/bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c /opt/kafka_2.12-3.6.1/config/kraft/server.properties'
#ExecStartPre=/opt/kafka_2.12-3.6.1/bin/kafka-setup.sh
ExecStart=/opt/kafka_2.12-3.6.1/bin/kafka-server-start.sh -daemon /opt/kafka_2.12-3.6.1/config/kraft/server.properties --override controller.mode=kraft
ExecStop=/opt/kafka_2.12-3.6.1/bin/kafka-server-stop.sh /opt/kafka_2.12-3.6.1/config/kraft/server.properties --override controller.mode=kraft
TimeoutSec=30
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=default.target
```
run these commands:
```
#sudo chown -R kraft:kraft kafka_2.12-3.6.1
sudo systemctl daemon-reload
```

## 16. Start the Kafka service using the following command:
```
sudo systemctl start kafka.service


#If you want this service to start automatically after bootup
sudo systemctl enable kafka

sudo systemctl status kafka.service
```

## 17. Look at the log
```
sudo  tail -100f /opt/kafka_2.12-3.6.1/logs/server.log
sudo ss -plnt

```

## 18. Administrating Kafka
Launch the broker itself in daemon mode by running these commands in a seperate terminal tab:
```
# Open new Terminal Tab
cd /opt/kafka_2.12-3.6.1/

sudo bin/kafka-server-start config/kraft/server.properties
ctrl+c
```


### 18.1 Creating a Topic
```
kafka-topics --bootstrap-server localhost:9092 --topic zs6bvr --create --partitions 3 replication-factor 1
```
### 18.2 List topics of the Bootstrap server
```
kafka-topics --bootstrap-server localhost:9092 --list
```

### 18.3 Describe a Topic
```
kafka-topics --bootstrap-server localhost:9092 --topic zs6bvr --describe
```

### 18.4 Create a Producer
```
kafka-console-producer --bootstrap-server localhost:9092 --topic zs6bvr --producer-property acks=all
```

### 18.5 Create a consumer that gets the new messages
```
kafka-console-consumer --bootstrap-server localhost:9092 --topic zs6bvr
```

### 18.6 Create a consumer that gets all the messages
```
kafka-console-consumer --bootstrap-server localhost:9092 --topic zs6bvr --from-beginning
```
#### 18.6.1 Producer
```
kafka-console-producer --bootstrap-server localhost:9092 --topic zs6bvr --producer-property acks=all
# send messages
>hello1
>hello2 
```
#### 18.6.2 Consumer
```
kafka-console-producer --bootstrap-server localhost:9092 --topic zs6bvr --producer-property acks=all
# Expect:
hello1
hello2
```
### 18.7 Creating a Consumer Group (Behave like a RabbitMQ Queue)
```
kafka-console-consumer --bootstrap-server localhost:9092 --topic sv1ahw --group sv1ahw
```
#### 18.7.1 Producer
```
kafka-console-producer --bootstrap-server localhost:9092 --topic sv1ahw --producer-property acks=all
# send messages
>hello1
>hello1
>hello2
>hello2
>hello3
```
#### 18.7.2 Consumers
```
# First tab
kafka-console-consumer --bootstrap-server localhost:9092 --topic sv1ahw --group sv1ahw
# Expect:
hello 1
hello 1
hello3
# Second tab
kafka-console-consumer --bootstrap-server localhost:9092 --topic sv1ahw --group sv1ahw
# Expect:
hello2
hello2
# Third tab
kafka-console-consumer --bootstrap-server localhost:9092 --topic sv1ahw --group sv1ahw
```

### 18.8 List Consumer Groups
```
kafka-consumer-groups --bootstrap-server localhost:9092 --list
```

### 18.9 Produce Messages with Keys
```
kafka-console-producer.sh --broker-list localhost:9092 --topic sv1ahw --property "parse.key=true" --property "key.separator=:"
# send messages
ID001:One
ID002:Two
ID003:Three
ID004:Four
ID005:Five

```




## Bibliography
https://kafka.apache.org/quickstart \
https://www.fatlan.com/06-07-2023-kafka-kraft-installation/ \
https://www.youtube.com/watch?v=7yrwTLnPewQ


https://www.conduktor.io/kafka/how-to-install-apache-kafka-on-linux-without-zookeeper-kraft-mode/ \
https://www.fatlan.com/06-07-2023-kafka-kraft-installation/ \
https://stackoverflow.com/questions/77375531/start-apache-kafka-in-kraft-mode-as-a-servcice-on-ubuntu \
https://www.youtube.com/watch?v=aH3-UjIrnRs \
https://www.conduktor.io/kafka/how-to-install-apache-kafka-on-linux/ \
https://medium.com/@fixitblog/solved-start-apache-kafka-in-kraft-mode-as-a-servcice-on-ubuntu-c355662731df \
https://access.redhat.com/documentation/en-us/red_hat_amq_streams/2.2/html/using_amq_streams_on_rhel/assembly-kraft-mode-str \

