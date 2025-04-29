---
title: "Install Quarkus CLI and Create Projects"
date: 2024-01-17T06:40:57Z
draft: false
---

## 1. Installing Quarkus on Linux using SdkMan

### 1.1 Install SDKMAN
https://sdkman.io/
[Click on Install](https://sdkman.io/install)

Run This command:
```
curl -s "https://get.sdkman.io" | bash

# in the terminal expect to get:
source "/home/nickm/.sdkman/bin/sdkman-init.sh"
```
It is important to open a new terminal or tab in your terminal now

Run these commands:
```
source "/home/nickm/.sdkman/bin/sdkman-init.sh"

# verify it
sdk version
```
expect to get:
```
SDKMAN!
script: 5.18.2
native: 0.4.6
```

### 1.2 Install Quarkus
```
java -version

#You need version 17 or above
sdk install java x.y.z-open

# Installing openjdk 20
sdk install java 20-open

# OpenJDK 21.0.2 is latest stable version at time of publishing
sdk install java 21.0.2-open

# create persisted environment variables here
nano ~/.bashrc

# Install Quarkus
sdk install quarkus

# to verify this
quarkus --version
```
Expect to get
```
3.6.6
```
Check if their is a newer version of Quarkus
```
sdk upgrade quarkus
```
Expect to get an upgrade or if latest you get this:
```
quarkus is up-to-date

```

## 2. Create Quarkus project
To create the default project : code-with-quarkus
```
quarkus create
```
This creates the default project: code-with-quarkus

### 2.1 to create a Quarkus project in package: za.co.nico with name: myquarkus and version: 1.0 
```
# navigate to your project folder
mkdir ~/Documents/workspace-quarkus

cd ~/Documents/workspace-quarkus

# For help on thge quarkus create app command
quarkus create app --help

quarkus create app za.co.nico:myquarkus:1.0
```
Expect to get:
```
Looking for the newly published extensions in registry.quarkus.io
-----------

applying codestarts...
📚 java
🔨 maven
📦 quarkus
📝 config-properties
🔧 tooling-dockerfiles
🔧 tooling-maven-wrapper
🚀 resteasy-reactive-codestart

-----------
[SUCCESS] ✅  quarkus project has been successfully generated in:
--> /home/nickm/Documents/workspace-quarkus/myquarkus
-----------
Navigate into this directory and get started: quarkus dev
```
Open the project running these commands
```
cd myquarkus/

tree
```
Expect to get:
```
.
├── mvnw
├── mvnw.cmd
├── pom.xml
├── README.md
└── src
    ├── main
    │   ├── docker
    │   │   ├── Dockerfile.jvm
    │   │   ├── Dockerfile.legacy-jar
    │   │   ├── Dockerfile.native
    │   │   └── Dockerfile.native-micro
    │   ├── java
    │   │   └── za
    │   │       └── co
    │   │           └── nico
    │   │               └── GreetingResource.java
    │   └── resources
    │       ├── application.properties
    │       └── META-INF
    │           └── resources
    │               └── index.html
    └── test
        └── java
            └── za
                └── co
                    └── nico
                        ├── GreetingResourceIT.java
                        └── GreetingResourceTest.java
```

### 2.2 Adding a Quarkus Extension to your project
Searching for Hibernate Extensions:
```
quarkus ext list --concise -i -s hibernate
```
Expect to get:
```
Looking for the newly published extensions in registry.quarkus.io
Current Quarkus extensions installable: 

✬ ArtifactId                                         Extension Name
✬ blaze-persistence-integration-quarkus-3            Blaze-Persistence
✬ quarkus-agroal                                     Agroal - Database connection pool
  quarkus-groovy-hibernate-orm-panache               Hibernate ORM with Panache in Groovy
  quarkus-groovy-hibernate-reactive-panache          Hibernate Reactive with Panache in Groovy
✬ quarkus-hibernate-envers                           Hibernate Envers
✬ quarkus-hibernate-orm                              Hibernate ORM
✬ quarkus-hibernate-orm-panache                      Hibernate ORM with Panache
✬ quarkus-hibernate-orm-panache-kotlin               Hibernate ORM with Panache and Kotlin
✬ quarkus-hibernate-orm-rest-data-panache            REST resources for Hibernate ORM with Panache
✬ quarkus-hibernate-reactive                         Hibernate Reactive
✬ quarkus-hibernate-reactive-panache                 Hibernate Reactive with Panache
✬ quarkus-hibernate-reactive-panache-kotlin          Hibernate Reactive Panache Kotlin
✬ quarkus-hibernate-reactive-rest-data-panache       REST resources for Hibernate Reactive with Panache
✬ quarkus-hibernate-search-orm-coordination-outb...  Hibernate Search Coordination with Outbox Polling
✬ quarkus-hibernate-search-orm-elasticsearch         Hibernate Search + Elasticsearch
  quarkus-hibernate-search-orm-elasticsearch-aws     Hibernate Search + Elasticsearch - AWS authentication and request signing
  quarkus-hibernate-types                            Hibernate Types
✬ quarkus-hibernate-validator                        Hibernate Validator
  quarkus-jpastreamer                                JPAStreamer
✬ quarkus-narayana-jta                               Narayana JTA - Transaction manager
  quarkus-renarde                                    Renarde
✬ quarkus-security-jpa-reactive                      Security Jakarta Persistence Reactive

To list only extensions from specific category, append `--category "categoryId"` to your command line.

Add an extension to your project by adding the dependency to your pom.xml or use `quarkus extension add "artifactId"`
```
Searching for JPA Extensions:
```
quarkus ext list --concise -i -s jpa
```
Expect to get:
```
Current Quarkus extensions installable: 

✬ ArtifactId                                         Extension Name
✬ quarkus-hibernate-orm                              Hibernate ORM

To list only extensions from specific category, append `--category "categoryId"` to your command line.

Add an extension to your project by adding the dependency to your pom.xml or use `quarkus extension add "artifactId"`

```

Select from the above list the extension you wish to add : quarkus-hibernate-orm
```
quarkus extension add quarkus-hibernate-orm

```
Expect to get:
```
[SUCCESS] ✅  Extension io.quarkus:quarkus-hibernate-orm has been installed

```

Adding MariaDB extenstion
```

quarkus extension add quarkus-jdbc-mariadb
```

To list what Quarkus extensions are added to your project
```
quarkus extension list

```

Expect to get:
```
Current Quarkus extensions installed: 

✬ ArtifactId                                         Extension Name
✬ quarkus-hibernate-orm                              Hibernate ORM
✬ quarkus-resteasy-reactive                          RESTEasy Reactive

To get more information, append `--full` to your command line.
```

Lets look at the POM file:
```
cat pom.xml
```

You are now expecting to filed these dependencies in your POM file:
```
  <dependencies>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-arc</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-resteasy-reactive</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-hibernate-orm</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-junit5</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>rest-assured</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

```

## 3. Running Qurkus
Run this command:
```
./mvnw quarkus:dev
```
## 4. Adding additional Quarkus Extensions

### 4.1 Adding FreeMarker
```
quarkus ext add io.quarkiverse.freemarker:quarkus-freemarker

```
[Quarkus FreeMarker documentation: https://docs.quarkiverse.io/quarkus-freemarker/dev/](https://docs.quarkiverse.io/quarkus-freemarker/dev/)

### 4.2 Adding Camel FreeMarker
```
quarkus ext add org.apache.camel.quarkus:camel-quarkus-freemarker
```

[Quarkus Camel FreeMarker documentation : https://camel.apache.org/camel-quarkus/latest/reference/extensions/freemarker.html](https://camel.apache.org/camel-quarkus/latest/reference/extensions/freemarker.html)






## Bibliography
https://www.youtube.com/watch?v=HacojA4aQaY \
and \
https://www.youtube.com/watch?v=PYs3Cd0ogNk&list=PLg1ikb8zEVCmWtbJHoKZk8O4A8ydYyS40&index=26

[https://www.geekyhacker.com/use-mysql-in-quarkus-with-hibernate-and-panache/](https://www.geekyhacker.com/use-mysql-in-quarkus-with-hibernate-and-panache/) 

https://quarkus.io/guides/cli-tooling

https://quarkus.io/guides/datasource

## Other Links

https://quarkus.io/extensions/io.quarkiverse.freemarker/quarkus-freemarker/ \
https://docs.quarkiverse.io/quarkus-freemarker/dev/index.html \
https://quarkus.io/extensions/org.apache.camel.quarkus/camel-quarkus-freemarker/ \
https://www.knowledgefactory.net/2021/10/quarkus-freemarker-hibernate-crud.html \
**https://camel.apache.org/components/4.0.x/freemarker-component.html** \
**https://access.redhat.com/documentation/en-us/red_hat_fuse/7.1/html/apache_camel_component_reference/freemarker-component** \
**https://access.redhat.com/documentation/en-us/red_hat_fuse/7.1/html/apache_camel_component_reference/kafka-component**


https://www.geekyhacker.com/use-mysql-in-quarkus-with-hibernate-and-panache/ \
https://www.sqliz.com/posts/java-connect-mysql8-micro-quarkus/ \
https://developers.redhat.com/articles/2022/01/06/simplify-java-persistence-using-quarkus-and-hibernate-reactive
