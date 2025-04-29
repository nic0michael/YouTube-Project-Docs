---
title: "Create Quarkus Application Connecting to Kafka Topics"
date: 2024-01-25T11:13:30Z
draft: false
---
## 1. Create Quarkus Project
```
cd ~/Documents/workspace-quarkus/

quarkus create app za.co.nico:kafka-quarkus:1.0

ls -la

cd kafka-quarkus/

tree
```
## 2. Add dependencies
### 2.1 Add Kafka
Search for Kafka
```
quarkus ext list --concise -i -s kafka
```
Expect to find
```
✬ ArtifactId                                         Extension Name
✬ quarkus-smallrye-reactive-messaging-kafka          SmallRye Reactive Messaging - Kafka Connector

```
Add kafka dependency
```
quarkus extension add quarkus-smallrye-reactive-messaging-kafka
```
### 2.2 add json
Search for json
```
quarkus ext list --concise -i -s json
```
Expect to find:
```
✬ ArtifactId                                         Extension Name
✬ camel-quarkus-gson                                 Camel Gson
✬ camel-quarkus-jackson                              Camel Jackson
✬ camel-quarkus-jolt                                 Camel JOLT
✬ camel-quarkus-jq                                   Camel JQ
✬ camel-quarkus-jslt                                 Camel JSLT
✬ camel-quarkus-json-validator                       Camel JSON Schema Validator
✬ camel-quarkus-jsonata                              Camel JSONATA
✬ camel-quarkus-jsonb                                Camel JSON-B
✬ camel-quarkus-jsonpath                             Camel JSON Path
✬ quarkus-jackson                                    Jackson
✬ quarkus-jsonb                                      JSON-B
✬ quarkus-jsonp                                      JSON-P
✬ quarkus-logging-json                               Logging JSON
  quarkus-logging-json                               Logging JSON Jackson
✬ quarkus-rest-client-jackson                        RESTEasy Classic's REST Client Jackson
✬ quarkus-rest-client-jsonb                          RESTEasy Classic's REST Client JSON-B
✬ quarkus-rest-client-reactive-jackson               RESTEasy Reactive's REST Client Jackson
✬ quarkus-rest-client-reactive-jsonb                 RESTEasy Reactive's REST Client JSON-B
✬ quarkus-rest-client-reactive-kotlin-serialization  RESTEasy Reactive's REST Client Kotlin Serialization
✬ quarkus-resteasy-jackson                           RESTEasy Classic Jackson
✬ quarkus-resteasy-jsonb                             RESTEasy Classic JSON-B
✬ quarkus-resteasy-reactive-jackson                  RESTEasy Reactive Jackson
✬ quarkus-resteasy-reactive-jsonb                    RESTEasy Reactive JSON-B
✬ quarkus-resteasy-reactive-kotlin-serialization     RESTEasy Reactive Kotlin Serialization
✬ quarkus-smallrye-jwt                               SmallRye JWT
✬ quarkus-smallrye-jwt-build                         SmallRye JWT Build
  timefold-solver-quarkus-jackson                    Timefold Jackson
  timefold-solver-quarkus-jsonb                      Timefold JSON-B
```
Removing quarkus-jackson Extension
```
quarkus extension remove quarkus-jackson
```

Add quarkus-jsonb Extension
```
quarkus extension add quarkus-jsonb
```

### 2.3 List extenssions
To list what Quarkus extensions are added to your project
```
quarkus extension list

# for more detail
quarkus extension list --full
```
## 3. Open project with IntelliJ
```
-> File
   -> New
      -> Project from Existing Sources
	 -> /home/nickm/Documents/workspace-quarkus/kafka-quarkus/pom.xml
```
## 4. Edit application.properties file
Put this in the file:
```
# The Kafka broker location (defaults to localhost:9092)
kafka.bootstrap.servers=localhost:9092

# Configuring the incoming channel (reading from Kafka)
mp.messaging.incoming.movies-in.connector=smallrye-kafka
mp.messaging.incoming.movies-in.topic=messages
mp.messaging.incoming.movies-in.key.deserializer=org.apache.kafka.common.serialization.IntegerDeserializer
mp.messaging.incoming.movies-in.value.deserializer=org.apache.kafka.common.serialization.StringDeserializer

# Configuring the outgoing channel (writing to Kafka)
mp.messaging.outgoing.movies-out.connector=smallrye-kafka
mp.messaging.outgoing.movies-out.topic=messages
mp.messaging.outgoing.movies-out.key.serializer=org.apache.kafka.common.serialization.IntegerSerializer
mp.messaging.outgoing.movies-out.value.serializer=org.apache.kafka.common.serialization.StringSerializer
```
## 5. Parsing Objects to Json Strings
  	 

## Bibliography
https://quarkus.io/blog/getting-started-kafka/

https://www.mastertheboss.com/java-ee/json/how-to-use-json-b-for-converting-java-objects-to-from-json/#google_vignette

