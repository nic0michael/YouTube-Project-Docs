---
title: "To Create a Quarkus Project"
date: 2023-11-10T09:42:49Z
draft: false
---


https://www.baeldung.com/quarkus-io

## Method 1 create a new Quarkus Project using mvn command

### 1. Create Quarkus Project using Maven

```
mvn io.quarkus:quarkus-maven-plugin:0.13.1:create \
    -DprojectGroupId=com.dessertmenu.quarkus \
    -DprojectArtifactId=quarkus-project \
    -DclassName="com.dessertmenu.quarkus.DessertMenuResource" \
    -Dpath="/dessertmenu"
```
    
You should get this:  
```
.
├── quarkus-project
│   ├── mvnw
│   ├── mvnw.cmd
│   ├── pom.xml
│   └── src
│       ├── main
│       │   ├── docker
│       │   │   ├── Dockerfile.jvm
│       │   │   └── Dockerfile.native
│       │   ├── java
│       │   │   └── com
│       │   │       └── dessertmenu
│       │   │           └── quarkus
│       │   │               └── DessertMenuResource.java
│       │   └── resources
│       │       ├── application.properties
│       │       └── META-INF
│       │           └── resources
│       │               └── index.html
│       └── test
│           └── java
│               └── com
│                   └── dessertmenu
│                       └── quarkus
│                           ├── DessertMenuResourceTest.java
│                           └── NativeDessertMenuResourceIT.java


```
### 2. Open the Project in an IDE
(Intellij ->New ->Project from Existing Sources)
  The content of the DessertMenuResource class:
```

package com.dessertmenu.quarkus;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Path("/dessertmenu")
public class DessertMenuResource {

    @GET
    @Produces(MediaType.TEXT_PLAIN)
    public String hello() {
        return "hello";
    }
}
```
Change The hello method to this:
```

    @GET
    @Produces(MediaType.TEXT_PLAIN)
    public String listFruit() {
        return "You can order : Banana, Apple, or Strawberry as desert.";
    }
```

### 3. Launch the Quarkus Project 

Run this command
```
cd quarkus-project/
./mvnw compile quarkus:dev:
```
## 4. We have Hot Reload
When running in development mode (./mvn compile quarkus:dev), Quarkus provides a hot-reload capability. In other words, changes made to Java files or to configuration files will a>


### 5. Call the REST Endpoint  
The REST endpoint should be exposed at localhost:8080/hello. \
In a new terminal (tab)

Let’s test it with the curl command:
```
curl localhost:8080/dessertmenu
```
Expect
```
You can order : Banana, Apple, or Strawberry as desert.
```

## 6. adding more API functionality to the project

Create a DessertMenuService class in the same package:
```
package com.dessertmenu.quarkus;

import javax.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class DessertMenuService {

  public String orderDesert(String fruit){
    return "You have ordered: " + selectDesert(fruit);
  }

  private String selectDesert(String fruit){
    String dessert;
    switch(fruit.toUpperCase()){
      case "BANANA" :
        dessert ="Banana Split";
        break;
      case "APPLE" :
        dessert ="Apple Pie";
        break;
      case "STRAWBERRY" :
        dessert ="Strawberries and Creame";
        break;
      default:
        dessert=fruit;
    }
    return dessert;
  }
}

```

Edit the DessertMenuResource class, injecting the DessertMenuService and add a new method:
```
    @Inject
    DessertMenuService dessertMenuService;

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    @Path("/order/{fruit}")
    public String orderDesert(@PathParam("fruit") String fruit) {
        return dessertMenuService.orderDesert(fruit);
    }
    
```

To test our new endpoint:
```
curl localhost:8080/dessertmenu/order/Apple
```
Expect
```
You have ordered: Apple Pie
```

Edit the application.properties file and add one more key:
```
dailyspecial=with icecream or fresh cream
```
Edit the DessertMenuService to use our new property, add this code:
```

  @ConfigProperty(name = "dailyspecial")
  private String dailyspecial;


  public String orderSpecialOfTheDay(String fruit){
    return "You have ordered: " + selectDesert(fruit) + " "+dailyspecial;
  }
  
```

Edit the DessertMenuResource class to call the new service method add this code:
```
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    @Path("/orderspecial/{fruit}")
    public String orderSpecialOfTheDay(@PathParam("fruit") String fruit) {
        return dessertMenuService.orderSpecialOfTheDay(fruit);
    }
```

To test our new endpoint:
```
curl localhost:8080/dessertmenu/orderspecial/Apple
```
Expect
```
You have ordered: Apple Pie with icecream or fresh cream
```

### 7. Edit Unit Test  
Before we can build this project we need to incorporate our changes in our unit tests \
Open unit test class : DessertMenuResourceTest change body value
```
    @Test
    public void testHelloEndpoint() {
        given()
          .when().get("/dessertmenu")
          .then()
             .statusCode(200)
             .body(is("You can order : Banana, Apple, or Strawberry as desert."));
    }
``` 

### 8. Package the application
First stop Quarkus Application running in the first terminal tab
To package the application run:
```  
./mvnw package
```


### 9.Running the packaged Application
To run the packaged application:
```
java -jar target/quarkus-project-1.0-SNAPSHOT-runner.jar
```

```
curl localhost:8080/dessertmenu
curl localhost:8080/dessertmenu/order/Apple
curl localhost:8080/dessertmenu/orderspecial/Apple
```

### 10. Using a Native Image
**You need to install GraalVM to do this**
The instalation of GraalVM is beyond the scope of this document but we do provide instructions to work with native Images after you have installed GraalVM \
Next, we’ll produce a native image of our application. \
A native image will improve start-up time and time to first response. In other words, it contains everything it needs to run, including the minimal JVM necessary to run the application.

To start with, we need to have GraalVM installed and the GRAALVM_HOME environment variable configured.

We’ll now stop the application (Ctrl + C), if not stopped already, and run the command:
```
./mvnw package -Pnative
```

To o verify that our native artifact was properly constructed:
Run:
```
./mvnw verify -Pnative
```

To create a Docker Container image using our native executable.
```
./mvnw package -Pnative -Dnative-image.docker-build=true
```

The project generation created a Dockerfile.native:
```
FROM registry.fedoraproject.org/fedora-minimal
WORKDIR /work/
COPY target/*-runner /work/application
RUN chmod 775 /work
EXPOSE 8080
CMD ["./application", "-Dquarkus.http.host=0.0.0.0"]
```

To create a docker image:
```
docker build -f src/main/docker/Dockerfile.native -t quarkus/quarkus-project .
```

Run the container using:
```
docker run -i --rm -p 8080:8080 quarkus/quarkus-project
```

To test our modified REST to validate our application:
```
$ curl localhost:8080/dessertmenu/orderspecial/Apple
```

Expect
```
You have ordered: Apple Pie with icecream or fresh cream
```

## Method 2 create a Quarkus project using the Code With Quarkus website
Open this URL : [https://code.quarkus.io/](https://code.quarkus.io/) \
Here you get to choose all your dependencies. \
These dependencies are Quarkus Complient. \
And then you download a zip file of a generated Project.

## Configuring Datasources
Quarkus now expects to launch a Docker container with your database of choice you need to configure this so as to not get errors.\
Please refer to our Bibliography


# Bibliography
[configuring_data_sources_in_your_quarkus_applications](https://access.redhat.com/documentation/en-us/red_hat_build_of_quarkus/1.7/html-single/configuring_data_sources_in_your_quarkus_applications/index) \
[configuring_data_sources_in_your_quarkus_applications/assembly_intro-datasources_quarkus-configuring-datasources](https://access.redhat.com/documentation/en-us/red_hat_build_of_quarkus/1.7/html/configuring_data_sources_in_your_quarkus_applications/assembly_intro-datasources_quarkus-configuring-datasources \
[REACTIVE SQL CLIENTS](https://quarkus.io/guides/reactive-sql-clients) \
[USING HIBERNATE ORM AND JAKARTA PERSISTENCE](https://quarkus.io/guides/hibernate-orm) \


