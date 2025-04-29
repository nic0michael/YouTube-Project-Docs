---
title: "Build Quarkus REST CRUD Applications"
date: 2024-03-22T07:38:43Z
draft: false
---
We are going to assume you have created a database sakila \
We have created and empty database: sakila as a MariaDB Database
## 1. Create Qusrkus project
Open the Quarkus Project Configurator : [https://quarkus.io](https://quarkus.io) \
Click on the Start Coding button.

1.  Select a quarkus version : 3.8 : (Recomended) LTS Release
2.  Please note that Quarkus v3 compatable to Hibernate >= v6
3.  Set Group ID : za.co.nico
4.  Select Artifact: quarkus-crud-app (lowercase with dashes one word)
5.  Select Java Version: 17
6.  Starter Code : Keep Selected
7.  Search Add : jdbc          : add JDBC Driver : Ortacle / MySQL /MariaDB
8.  Search Add : hibernate pan : add Hibernate ORM with Panache (recomended for Quarkus)
9.  Search Add : jackson       : add RESTEasy Reactive Jackson (recomended for creating REST Endpoints)
10. Search Add : small         : add SmallRye OpenAPI (recomended for using Swagger for testing the Endpoints)
11. Click Generate your application : Select from Dropdown : Download as a Zip

We will have these dependencies in the POM file (we decided to work with a dependency for : MariaDB)
```

    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-hibernate-orm-rest-data-panache</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-resteasy-reactive-jackson</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-smallrye-openapi</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-jdbc-mysql</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-jdbc-oracle</artifactId>
    </dependency>

    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-jdbc-mariadb</artifactId>
    </dependency>

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
      <artifactId>quarkus-junit5</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>rest-assured</artifactId>
      <scope>test</scope>
    </dependency>
```

Please note we selected both MySQL and Oracle in practive you  would select one only

## 2. Open Project in your IDE
Open the project in your IDE (Using Eclipse ->File ->Import Existing Maven Project -> Browse)

## 3. Set Applicatyion Properties
Open the application.config file put this code there :

```
# General database config
quarkus.datasource.jdbc.driver=org.mariadb.jdbc.Driver
quarkus.datasource.jdbc.url=jdbc:mariadb://localhost:3306/sakila
quarkus.datasource.username=root
quarkus.datasource.password=sakila

# Hibernate config
quarkus.hibernate-orm.database.generation=update
quarkus.hibernate-orm.log.sql=true
quarkus.hibernate-orm.dialect=org.hibernate.dialect.MariaDBDialect

```
## 4. Add Start instructions
Right Click on your Project and select Properties \
Location : /opt/projects/quarkus-crud-app

Edit the README.md file Right Click it select Open with Text Editor \
Put this on the topf the file:

```
## To start this app for testing

cd /opt/projects/quarkus-crud-app

# This prevents test errors: Maven skip tests
./mvnw compile quarkus:dev: -DskipTests
```
## 5. Get the source code
[https://github.com/nic0michael/quarkus-crud-app](https://github.com/nic0michael/quarkus-crud-app)
