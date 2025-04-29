---
title: "Install Kafdrop"
date: 2024-01-30T15:08:20Z
draft: false
---

https://towardsdatascience.com/overview-of-ui-monitoring-tools-for-apache-kafka-clusters-9ca516c165bd



## Github
https://github.com/obsidiandynamics/kafdrop


## Community
[Kafdrop - An Open-Source Kafka Web UI](https://dev.to/ekoutanov/kafdrop-an-open-source-kafka-web-ui-mbn)


## Downloading the JAR file
To get to the latest release look at the right hand side of the GitHub page click on the link "Latest" \
[Release : 4.0.1](https://github.com/obsidiandynamics/kafdrop/releases/tag/4.0.1) \
Download the Jarfile \
[kafdrop-4.0.1.jar](https://github.com/obsidiandynamics/kafdrop/releases/download/4.0.1/kafdrop-4.0.1.jar)

On your server: 
```
sudo su -

cd /opt

wget https://github.com/obsidiandynamics/kafdrop/releases/download/4.0.1/kafdrop-4.0.1.jar
```

## Running from JAR
```
java --add-opens=java.base/sun.nio.ch=ALL-UNNAMED \
    -jar target/kafdrop-<version>.jar \
    --kafka.brokerConnect=<host:port,host:port>,...
```

Run this command from your Kafka server
```
java --add-opens=java.base/sun.nio.ch=ALL-UNNAMED \
    -jar kafdrop-4.0.1.jar \
    --kafka.brokerConnect=127.0.0.1:9092

```

## If Port 9000 is already running on your PC or  laptop
```
sudo lsof -i :9000
```
I got
```
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
docker-pr 1980 root    4u  IPv4  26337      0t0  TCP *:9000 (LISTEN)
docker-pr 1986 root    4u  IPv6  32877      0t0  TCP *:9000 (LISTEN)

```
To Kill docker-pr 
```
sudo kill -9 1980 1986
```

Run this command to confirm the port is free
```
sudo lsof -i :9000
```
## Open Kafdrop in your browser
[http://localhost:9000/](http://localhost:9000/) \
OR \
[http://127.0.0.1:9000](http://127.0.0.1:9000)



## Bibliograpgy
https://www.youtube.com/watch?v=bedkXpJigAY
