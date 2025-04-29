---
title: "Install StarRocks"
date: 2024-05-16T07:42:05Z
draft: false
---
## 1. What is StarRocks
StarRocks is a next-gen, high-performance analytical data warehouse that enables real-time, multi-dimensional, and highly concurrent data analysis.
### 1.1 FE (Front End)
FE is responsible for metadata management, client connection management, query planning, and query scheduling. \
Each FE stores and maintains a complete copy of metadata in its memory, which guarantees indiscriminate services among the FEs. \
FEs can work as the leader, followers, and observers. \
Followers can elect a leader according to the Paxos-like BDB JE protocol. BDB JE is short for Berkeley DB Java Edition.
### 1.2 BE (Back End)
BEs are responsible for data storage and SQL execution.

1. Data storage: BEs have equivalent data storage capabilities. \
FEs distribute data to BEs based on predefined rules. \
BEs transform the ingested data, write the data into the required format, and generate indexes for the data.

2. SQL execution: When an SQL query arrives, \
FEs parse it into a logical execution plan according to the semantics of the query, \
and then transform the logical plan into physical execution plans that can be executed on BEs. \
BEs that store the destination data execute the query. \
This eliminates the need for data transmission and copy, achieving high query performance.

### 1.3 Data management
StarRocks is a column-oriented database system. \
It uses the partitioning and bucketing mechanism to manage data. \
Data in a table is first divided into multiple partitions and then into multiple tablets. \
Tablets are the basic logical units of data management in StarRocks. \
Each tablet can have multiple replicas that can be stored across different BEs. \
You can specify the number of tablets and leave StarRocks to take care of the tablets.

Partitions and tablets reduce table scans and increase query concurrency. \
Replicas facilitate data backup and restoration, preventing data loss.

**Architecture :**
![image](https://docs.starrocks.io/assets/images/architecture_evolution-739e923fb30eddd1d76715af2cef9706.png)

## 2.References
[StarRocks documentation](https://docs.starrocks.io/docs/introduction/StarRocks_intro/) \
[StarRocks Github demo](https://github.com/StarRocks/demo/tree/master/deploy) \
[https://docs.starrocks.io/docs/2.5/quick_start/deploy_in_docker/](https://docs.starrocks.io/docs/2.5/quick_start/deploy_in_docker/)

## 3. Check environment configurations

### 3.1 FE ports
On the instances used for the FE deployment, you need to check the following ports:

1. 8030: FE HTTP server port (http_port)
2. 9020: FE Thrift server port (rpc_port)
3. 9030: FE MySQL server port (query_port)
4. 9010: FE internal communication port (edit_log_port)

### 3.2 BE ports
On the instances used for the BE deployment, you need to check the following ports:

1. 9060: BE Thrift server port (be_port)
2. 8040: BE HTTP server port (be_http_port)
3. 9050: BE heartbeat service port (heartbeat_service_port)
4. 8060: BE bRPC port (brpc_port)


### 3.3 CN ports
On the instances used for the CN deployment, you need to check the following ports:

1. 9060: CN Thrift server port (be_port)
2. 8040: CN HTTP server port (be_http_port)
3. 9050: CN heartbeat service port (heartbeat_service_port)
4. 8060: CN bRPC port (brpc_port)
5. 9070: An extra agent service port for CN (BE in v3.0) in a shared-data cluster (starlet_port)

## 4. Deploy StarRocks with Docker
Run these commands :
```
sudo su -

mkdir -p /opt/starrocks

cd /opt/starrocks
```

### 4.1 Create Dockerfile
Run this command :
```
nano Dockerfile
```

Put this in the file :
```
#==============#
# Ubuntu 22.04 #
#==============#

# Use Ubuntu 22.04 as the base image
FROM ubuntu:22.04


# Set the working directory
WORKDIR /opt/starrocks-tmp

# Create a volume
#VOLUME ["/opt/starrocks"]

# Install wget, tar, nano, tree, and less
RUN apt update \
    && apt install -y curl wget tar nano tree less dpkg

# Install MySQL
RUN apt install mysql-client -y

# Download the StarRocks tar.gz file
RUN wget https://releases.starrocks.io/starrocks/StarRocks-3.2.3.tar.gz \
    && tar -xzvf StarRocks-3.2.3.tar.gz \
    && rm StarRocks-3.2.3.tar.gz

# Set permissions and ownership for StarRocks
RUN chmod -R 755 StarRocks-3.2.3 \
    && chown -R root:root StarRocks-3.2.3

# Rename the extracted directory and move it to /opt
RUN mv /opt/starrocks-tmp/StarRocks-3.2.3 /opt/starrocks-tmp/StarRocks \
    && mv /opt/starrocks-tmp/StarRocks /opt/starrocks


# Create a directory for Java
RUN mkdir -p /opt/java

# Copy the Oracle Java JDK DEB file to the created directory
COPY jdk-17.0.10_linux-x64_bin.deb /opt/java/

# Install Java using the DEB file
RUN dpkg -i /opt/java/jdk-17.0.10_linux-x64_bin.deb && apt install -f -

# Create folder :
RUN mkdir -p /opt/starrocks/be/storage

# Create folder : 
RUN mkdir -p /opt/starrocks/fe/meta

# Create folder:
RUN mkdir -p /opt/starrocks/scripts

# Copy shell script
COPY install.sh /opt/starrocks/scripts

# Copy Instructions
COPY install.txt /opt/starrocks/scripts

# Copy shell script
COPY start.sh	/opt/starrocks/scripts

# Copy shell script
COPY stop.sh   /opt/starrocks/scripts

# Copy shell script
COPY start-at-bootup.sh /opt/starrocks

# Make excecutable
RUN chmod -R 755 /opt/starrocks/scripts/*.sh

# Set JAVA_HOME environment variable
#ENV JAVA_HOME /usr/java/jdk-17
ENV JAVA_HOME /usr/lib/jvm/jdk-17-oracle-x64/

# Set STARROCKS_HOME
ENV STARROCKS_HOME=/opt/starrocks

# Ensure necessary directories exist
RUN mkdir -p $STARROCKS_HOME/conf \
    && mkdir -p $STARROCKS_HOME/log \
    && mkdir -p $STARROCKS_HOME/confmkdir

# Expose necessary ports (adjust as needed)
EXPOSE 8030 8040 8050 8060 8070 8080  9030 9040 

RUN echo "* soft nofile 70000" >> /etc/security/limits.conf
RUN echo "* hard nofile 524288" >> /etc/security/limits.conf
RUN echo "priority_networks = 10.10.10.0/24;192.168.0.0/16" >> /opt/starrocks/fe/conf/fe.conf

# Set the working directory
WORKDIR /opt/starrocks

RUN chmod -R 755 /opt/starrocks/*.sh

RUN echo "* soft nofile 70000" >> /etc/security/limits.conf
RUN echo "* hard nofile 524288" >> /etc/security/limits.conf
RUN echo "priority_networks = 10.10.10.0/24;192.168.0.0/16" >> /opt/starrocks/fe/conf/fe.conf

RUN rm -rf /opt/starrocks-tmp

# Start the Front-end and Back-end services
#CMD ["/bin/bash", "-c", "/opt/starrocks/StarRocks-3.2.6/fe/bin/start_fe.sh & && /opt/starrocks/StarRocks-3.1.11/be/bin/start_be.sh &"]

# Keep the container running (needed for testing)
#CMD tail -f /dev/null

# Set the command to run the start.sh script
CMD ["/opt/starrocks/start-at-bootup.sh"]


```
### 4.2 Download Java Binary
Open browser to : \
[https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html)
we need this file : \
[https://download.oracle.com/java/17/archive/jdk-17.0.10_linux-x64_bin.deb](https://download.oracle.com/java/17/archive/jdk-17.0.10_linux-x64_bin.deb)

We ran this command : 
```
wget https://download.oracle.com/java/17/archive/jdk-17.0.10_linux-x64_bin.deb
```
### 4.3 Create shell scripts needed
Run this command :
```
# create start shell script
nano start.sh
```
Put this in the file :
```
#!/bin/bash
cd /opt/starrocks
echo ">"
echo "set ulimit"
ulimit -u 65535
echo "Starting Frontend"
./fe/bin/start_fe.sh --daemon
echo ">"
echo "Starting Backend"
./be/bin/start_be.sh --daemon
```
Run this command :
```
# create stop shell script
nano stop.sh
```
Put this in the	file :
```
#!/bin/bash
cd /opt/starrocks
echo ">"
echo "Stoping Frontend"
./fe/bin/stop_fe.sh --daemon
echo ">"
echo "Stoping Backend"
./be/bin/stop_be.sh --daemon
```

Run this command :
```
# create start at Ubuntu bootup shell script
nano start-at-bootup.sh
```
Put this in the	file :
```
#!/bin/bash
cd /opt/starrocks
echo ">"
echo "set ulimit"
ulimit -u 65535
echo "Starting Frontend"
./fe/bin/start_fe.sh --daemon
echo ">"
echo "Starting Backend"
./be/bin/start_be.sh --daemon

# Keep the script running
tail -f /dev/null
```


### 4.4 Create Docker compose file
run this command:
```
nano compose.yaml
```
Put this in the file :
```
version: "3.3"
services:
  starrocks-ubu:
    container_name: starrocks-ubu    ports:
      - "8030:8030"
      - "8040:8040"
      - "9030:9030"
      - "9040:9040"
    volumes:
      - /opt/starrocks/starrocks-roc/starrocksdata:/opt/starrocksvol
    image: starrocks-ubu
networks: {}
```

### 4.5 Build Docker Image
Run this command :
```
docker build -t starrocks-ubu .
```
This will use the Dockerfile to build our image

### 4.6 Start Container using Docker run command
we will do this to test the container

Run this command :
```
docker run -d --name starrocks-ubu -v /opt/starrocks/starrocks-roc/starrocksdata:/opt/starrocksvol starrocks-ubu
```

### 4.7 Open a Bash session to the Container
Run this command:
```
docker exec -it starrocks-ubu /bin/bash
```

We type exit to leave the Bash session

### 4.8 Stop and kill container
Run these commands
```
# see what running and is not running
docker ps -a

docker container stop starrocks-ubu

docker container rm starrocks-ubu
```

### 4.9 delete docker image
Run these commands :
```
docker image ls

docker image rm starrocks-ubu

```

### 4.10 We make changes toDocker file and build image again
Run these commands
```
# if we need to make changes to Dockerfile
nano Dockerfile

# build the image again
docker build -t starrocks-ubu .
```
### 4.11 Start the Docker Container with docker compose  file
Run this command :
```
# start the container in detached mode
docker compose up -d
```

### 4.12 Open a Bash session to the Container
Run this command:
```
docker exec -it starrocks-ubu /bin/bash
```
run this command in the bash shell :
```
ps -ef | grep starrocks
```

## 5 Open Starrocks in browser
run this command :
```
curl http://127.0.0.1:8040
```
**Open this URL :** \
[http://10.154.2.87:8040](http://10.154.2.87:8040)


## 6 Create a Database 
### 6.1 connect to MySQL
Run this command :
```
mysql -P9030 -h127.0.0.1 -uroot --prompt="StarRocks > "
```

### 6.2 Check Frontend and Backend status
Run these commands:
```
# FE
SHOW PROC '/frontends'\G

# BE
SHOW PROC '/backends'\G
```

Expect to get :
```
#FE
StarRocks > SHOW PROC '/frontends'\G
*************************** 1. row ***************************
             Name: acc59fb21e19_9010_1715859271980
               IP: acc59fb21e19
      EditLogPort: 9010
         HttpPort: 8030
        QueryPort: 9030
          RpcPort: 9020
             Role: LEADER
        ClusterId: 1543749417
             Join: true
            Alive: true
ReplayedJournalId: 168
    LastHeartbeat: 2024-05-16 11:43:10
         IsHelper: true
           ErrMsg: 
        StartTime: 2024-05-16 11:34:41
          Version: 2.5.4-1021a9299
1 row in set (0.03 sec)
# Also
#BE Expect to get :

StarRocks > SHOW PROC '/backends'\G
*************************** 1. row ***************************
            BackendId: 10002
                   IP: acc59fb21e19
        HeartbeatPort: 9050
               BePort: 9060
             HttpPort: 8040
             BrpcPort: 8060
        LastStartTime: 2024-05-16 11:34:49
        LastHeartbeat: 2024-05-16 11:43:50
                Alive: true
 SystemDecommissioned: false
ClusterDecommissioned: false
            TabletNum: 30
     DataUsedCapacity: 0.000 
        AvailCapacity: 183.569 GB
        TotalCapacity: 237.459 GB
              UsedPct: 22.69 %
       MaxDiskUsedPct: 22.69 %
               ErrMsg: 
              Version: 2.5.4-1021a9299
               Status: {"lastSuccessReportTabletsTime":"2024-05-16 11:43:50"}
    DataTotalCapacity: 183.569 GB
          DataUsedPct: 0.00 %
             CpuCores: 8
    NumRunningQueries: 0
           MemUsedPct: 0.66 %
           CpuUsedPct: 0.1 %
1 row in set (0.00 sec)
```

### 6.3 Now you can create a table and insert some data.
Run these commands in the database :
```
CREATE DATABASE test;

USE test;

CREATE TABLE tbl(c1 int, c2 int) distributed by hash(c1) properties ("replication_num" = "1");

INSERT INTO tbl VALUES (1, 1), (2, 2), (3, 3);
```
Run this command :
```
SELECT * FROM tbl;
```

Expect to get :

```
SELECT * FROM tbl;
+------+------+
| c1   | c2   |
+------+------+
|    1 |    1 |
|    2 |    2 |
|    3 |    3 |
+------+------+
3 rows in set (0.11 sec)
```



## Bibliography
https://hub.docker.com/r/starrocks/allin1-ubuntu/tags \
https://hub.docker.com/u/starrocks \
https://docs.starrocks.io/docs/2.5/quick_start/deploy_with_docker/ \
https://blog.slingdata.io/export-data-from-starrocks-into-duckdb \
https://docs.slingdata.io/sling-cli/run/examples/database-to-file \





