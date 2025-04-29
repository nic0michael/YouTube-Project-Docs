---
title: "Create Spring Boot Microservices"
date: 2023-07-09T13:55:01Z
draft: false
---

## Step 1 
Launch the STS IDE

Open the File menu

Click on Switch Workspace

Now navigate to your project folder


## Create a new SpringBoot Microservice

Open the File Menu

click on New

In the Drop-down click on Spring Starter Project

Give the project a suitable name: ManagedToDoList

Select type as Maven \
Select the Java JDK version as 11 \
Make the group : com.springboot.nico \
Make the artefact : todolist \
Make the package : com.springboot.nico.todolist

Click the Next Button

Change the Spring Boot version to : 2.7.13

Open Developer Tools drop-down \
Select Spring Boot DevTools

DONT Open SQL drop-down \
DONT Select Spring Data JPA \
DONT Select MariaDB Driver

Open Security drop-down \
Select Spring Security


Now click on the Finish Button

## Launch the Microservice

In the Package Explorer Pannel right click on your project : ManagedToDoList \
In the Drop-down clcik on : Run as 
then click on Spring Boot App

expect to get this:
```

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
␛[32m :: Spring Boot :: ␛[39m             ␛[2m (v2.7.13)␛[0;39m

␛[2m2023-07-09 16:51:17.053␛[0;39m ␛[32m INFO␛[0;39m ␛[35m8686␛[0;39m ␛[2m---␛[0;39m ␛[2m[  restartedMain]␛[0;39m ␛[36mc.s.n.t.ManagedToDoListApplication      ␛[0;39m ␛[2m:␛[0;39m Starting ManagedToDoListApplication using Java 17.0.6 on dell-laptop with PID 8686 (/opt/projects/workspace-spring-tool-suite-4-4.18.0.RELEASE/ManagedToDoList/target/classes started by nickm in /opt/projects/workspace-spring-tool-suite-4-4.18.0.RELEASE/ManagedToDoList)
␛[2m2023-07-09 16:51:17.054␛[0;39m ␛[32m INFO␛[0;39m ␛[35m8686␛[0;39m ␛[2m---␛[0;39m ␛[2m[  restartedMain]␛[0;39m ␛[36mc.s.n.t.ManagedToDoListApplication      ␛[0;39m ␛[2m:␛[0;39m No active profile set, falling back to 1 default profile: "default"
␛[2m2023-07-09 16:51:17.085␛[0;39m ␛[32m INFO␛[0;39m ␛[35m8686␛[0;39m ␛[2m---␛[0;39m ␛[2m[  restartedMain]␛[0;39m ␛[36m.e.DevToolsPropertyDefaultsPostProcessor␛[0;39m ␛[2m:␛[0;39m Devtools property defaults active! Set 'spring.devtools.add-properties' to 'false' to disable
␛[2m2023-07-09 16:51:17.428␛[0;39m ␛[32m INFO␛[0;39m ␛[35m8686␛[0;39m ␛[2m---␛[0;39m ␛[2m[  restartedMain]␛[0;39m ␛[36mo.s.b.d.a.OptionalLiveReloadServer      ␛[0;39m ␛[2m:␛[0;39m LiveReload server is running on port 35729
␛[2m2023-07-09 16:51:17.446␛[0;39m ␛[32m INFO␛[0;39m ␛[35m8686␛[0;39m ␛[2m---␛[0;39m ␛[2m[  restartedMain]␛[0;39m ␛[36mc.s.n.t.ManagedToDoListApplication      ␛[0;39m ␛[2m:␛[0;39m Started ManagedToDoListApplication in 0.606 seconds (JVM running for 1.12)

```


## Bibliography
https://www.javaguides.net/2020/01/spring-boot-mariadb-crud-example-tutorial.html \
https://springframework.guru/configuring-spring-boot-for-mariadb/ \
https://github.com/springframeworkguru/spring-boot-mariadb-example/blob/master/src/main/resources/application.properties \
https://gist.github.com/DengYiping/25fc8a06813332d9bc24fd48f6022da9 

