---
title: "Apache Camel File_Copy"
date: 2023-10-22T15:17:12Z
draft: false
---

Create a Java Project

## To create a new Quarkus Project that mvn passes the first time

[Create Quarkus Application Connecting to Kafka Topics](http://rino.kozow.com/devops/posts/create-quarkus-application-connecting-to-kafka-topics/)
```
quarkus create app za.co.quarkus.camel.kafka:quarkus-camel-kafka-new:1.0


```

## What is Apache Camel
- Apache Camel is an open-source integration framework.
- It facilitates the seamless connection of different systems and applications.
- It offers a versatile platform for defining, routing, and transforming data.
- It simplifies the integration of disparate endpoints.
- Aids in building robust and adaptable integration solutions.
- Utilizes a wide range of connectors and components to facilitate integration.
- Supports various data formats, protocols, and technologies.
- Highly extensible and customizable for diverse integration needs.
- Provides a comprehensive and flexible toolkit for developers.


1. **Official Website:** [Apache Camel](http://camel.apache.org/)
2. **Official Documentation**[Apache Camel Documentation](https://camel.apache.org/docs/)
3. **Official Documentation**[https://camel.apache.org/components/4.0.x/kafka-component.html](https://camel.apache.org/components/4.0.x/kafka-component.html)
4. **Component Documentation**[Apache Camel Component Documentation](https://camel.apache.org/components/4.0.x/)
5. **Source Code Repository (GitHub):** [Apache Camel GitHub Repository](https://github.com/apache/camel)


## 1. POM File
Put this into the Pom File
```

    <dependencies>
<!-- https://mvnrepository.com/artifact/org.apache.camel/camel-core -->
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-core</artifactId>
    <version>3.15.0</version>
</dependency>

<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.1</version>
</dependency>

<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-core</artifactId>
    <version>2.3.0.1</version>
</dependency>

<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-impl</artifactId>
    <version>2.3.2</version>
</dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.7.2</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>5.7.2</version>
            <scope>test</scope>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.8</version>
        </dependency>
    </dependencies>
    
    
    <build>
        <plugins>
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.2</version>
            </plugin>
            <plugin>
                <artifactId>maven-failsafe-plugin</artifactId>
                <version>2.22.2</version>
            </plugin>
        </plugins>
    </build>
```

## 2. Create Class CamelCopyFiles with void Method processFiles
Put this code in the Class
```
import org.apache.camel.CamelContext;
import org.apache.camel.builder.RouteBuilder;
import org.apache.camel.impl.DefaultCamelContext;

public class CamelCopyFiles {
  public static void processFiles() {

    // Here we create an instance of CamelContext
    CamelContext context = new DefaultCamelContext();

    try {
      //A route is defined using an anonymous inner class extending RouteBuilder
      context.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {

          //This specifies that the source of the route is a directory named "data/input."
          from("file:data/input?noop=true")

              //This specifies that the processed files should be copied to the "data/output" directory.
              .to("file:data/output");
        }
      });

      context.start();

      //This won't work without this line (we need to wait a few seconds for operations
      //This is done to keep the Camel context running for a while before stopping it.
      Thread.sleep(5000);

      //This gracefully shuts down the Camel context.
      context.stop();

    } catch(Exception e){
      System.out.println("Exception caught: "+e.getMessage());
    }
  }
}

```

In summary, this class sets up a simple Apache Camel route for copying files from one directory to another. It demonstrates the basic steps of creating a Camel context, defining a route using a RouteBuilder, starting and stopping the context, and handling exceptions. The route in this example monitors the "data/input" directory and copies files to the "data/output" directory.

## 3. Call the processFiles method in main Class
Put this code in main Class
```

    public static void main(String[] args) {

        System.out.println("Hello World!");
        CamelCopyFiles.processFiles();
    }
```

## 4. Create a Unit Test Class CamelCopyFilesTest
Put this code in the Unit test class
```

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;

public class CamelCopyFilesTest {
        
  @Test
  public void justTest() {
    
    assertTrue(true);
  }    
}
```

## 5. Create data folder
Right click the Project in the Package Explorer \
Add Directory data

Right click on data folder \
Add directory input

## 6. Create file test1.txt
Right click input directory \
Add file test1.txt \
Put an arbitrary comment there

## 6. Create file test2.txt
Right click input directory \
Add file test2.txt \
Put an arbitrary comment there

## 7. Run project in the IDE
We should fine a new folder : output in there we should find the two files we placed in the input folder

## 8 run this project
```
cd /opt/projects/ApacheCamelFS/
./mvnw compile ApacheCamelFS:dev:
```
