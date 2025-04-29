---
title: "Create a Spring Barch Project"
date: 2024-07-13T10:46:33Z
draft: false
---
## 1. Simplified Architecture

```
Scheduler
└── JobLauncher
    └── JobRepository
        └── Job
            └── Step
                ├── ItemReader
                ├── ItemProcessor
                └── ItemWriter
```

### 1.1 Components Explained:

- **Scheduler**: Triggers the execution of batch jobs at scheduled intervals.
  
- **JobLauncher**: Starts and controls the execution of Spring Batch jobs.
  
- **JobRepository**: Manages metadata and state information about batch jobs, job instances, and step executions.
  
- **Job**: Represents a logical batch job consisting of one or more sequential steps.
  
- **Step**: Defines an individual unit of work within a job.
  
  - **ItemReader**: Retrieves data from a specified source (e.g., database, file).
  
  - **ItemProcessor**: Applies business logic or transformations to the read data.
  
  - **ItemWriter**: Persists or outputs processed data to a designated destination.

### 1.2 Workflow:

1. **Scheduler** triggers the **JobLauncher** to start a specific **Job**.
   
2. **Job** comprises one or more **Steps**.
   
3. Each **Step** contains an **ItemReader**, **ItemProcessor**, and **ItemWriter**.
   
4. **JobRepository** manages job metadata and ensures transactional integrity during job execution.

### 1.3 Example Scenario:

Imagine a scenario where a Spring Batch job processes customer data:
- **ItemReader**: Retrieves customer information from a database (`JdbcCursorItemReader`).
- **ItemProcessor**: Validates and processes customer data.
- **ItemWriter**: Writes processed customer data back to the database (`JdbcBatchItemWriter`).

### 1.4 Benefits:

- **Modularity**: Encourages reusable and maintainable code.
  
- **Scalability**: Handles large datasets efficiently using chunk-oriented processing.
  
- **Reliability**: Manages transaction boundaries to ensure data integrity.

This diagram and explanation encapsulate how Spring Batch architecture organizes and executes batch processing tasks, making it a powerful tool for handling data-intensive operations in enterprise applications.

## 2. Detailed Spring Batch Architecture 
A more detailed diagram of the Spring Batch architecture would typically include additional components and relationships, especially when considering advanced features and configurations. Here's an expanded version:

```
Scheduler
└── JobLauncher
    └── JobRepository
        ├── JobInstance
        │   └── JobExecution
        │       └── StepExecution
        │           └── ExecutionContext
        └── Metadata tables
    └── JobRegistry
        └── Registered Jobs
            ├── Job
            │   └── Step
            │       ├── StepListener
            │       ├── Chunk-oriented Step
            │       │   ├── ItemReader
            │       │   ├── ItemProcessor
            │       │   └── ItemWriter
            │       └── Tasklet Step
            └── JobListener
```

### 2.1 Components Explained:

- **Scheduler**: Triggers the execution of batch jobs based on predefined schedules.
  
- **JobLauncher**: Starts and controls the execution of Spring Batch jobs.
  
- **JobRepository**: Stores metadata about batch jobs, job instances, and executions.
  
  - **JobInstance**: Represents a specific instance of a job.
  
  - **JobExecution**: Represents the execution of a job instance.
  
  - **StepExecution**: Represents the execution of a step within a job.
  
  - **ExecutionContext**: Stores transient data during job or step execution.
  
  - **Metadata tables**: Database tables used by Spring Batch to store job-related metadata.
  
- **JobRegistry**: Stores and manages registered jobs within the application context.
  
- **Job**: Represents a logical batch job, consisting of one or more sequential steps.
  
  - **Step**: Defines an individual unit of work within a job.
    
    - **StepListener**: Listens to events during step execution.
    
    - **Chunk-oriented Step**: Executes items in chunks with an `ItemReader`, `ItemProcessor`, and `ItemWriter`.
      
    - **Tasklet Step**: Executes a single tasklet instead of chunk-oriented processing.
    
  - **JobListener**: Listens to events during job execution.

### 2.2 Workflow:

1. **Scheduler** triggers the **JobLauncher** to start a specific **Job** based on configured schedules or triggers.
   
2. **Job** consists of one or more **Steps**, each configured with appropriate components (`ItemReader`, `ItemProcessor`, `ItemWriter` or `Tasklet`) and optional listeners (`StepListener`, `JobListener`).
   
3. **JobRepository** manages job metadata and state information, ensuring transactional integrity and persistence of job execution details.
   
4. **JobRegistry** provides a centralized registry of jobs within the application context, facilitating job registration and management.

### 2.3 Advanced Features:

- **Listeners**: Allow customization and handling of events during job or step execution (`JobListener`, `StepListener`).
  
- **Chunk-oriented Processing**: Efficiently processes large datasets in chunks, handling transactions and failures gracefully.
  
- **Tasklet Steps**: Executes custom task logic as a single unit of work within a job.
  
- **Metadata tables**: Persist job-related metadata to track job and step executions over time.

This expanded diagram illustrates the comprehensive structure and components involved in Spring Batch architecture, accommodating various batch processing requirements in enterprise applications.

## 3. Our Spec for ths project

We need a Spring batch project built into a springboot microservice 
in the POM file we need to use this package path
com.nico.spring.batch

we will use these packages added to this package
.repo for JPA REpositories
.entity for Entities
.config for configuration 
.dbwriter for DBWriter class
.process for Processor class
and so on

I with use the STS Spring Tool Suite IDE

Java --version
java 17.0.10 2024-01-16 LTS
Java(TM) SE Runtime Environment (build 17.0.10+11-LTS-240)
Java HotSpot(TM) 64-Bit Server VM (build 17.0.10+11-LTS-240, mixed mode, sharing) 


It should read a csv file : \
Employee.csv

fith fields : \
id name department salary

values like \
1 Nico 001 $1000 \
2 Joe 002 $500 \
3 Pauls 003 $1000

The path to the csv file should be in the aplications.properties


and wite to a JPA entity 

with fields \
id name department salary

The processor should use a hashmap with \
001 IT \
002 Management \
003 Sales \
to convert department from 001 to IT

there should be a configuration class 

@Configuration \
MySpringBatchConfig

with 2 Beans

```
@Bean \
Job job(JobbuilderFactory jobFactory, \
    SteBuilderFactory stepFactory, \
    ItemReader itemReader,
    ItemWriter itemWriter 
){

and

@Bean
FlatFileItemReader

```

We want a JPA repository
```
@Repository
EmployeeRepository
```
and an Entity class
```
@Entity
Employee
```

The processor should be
```
@Component Processor extends FlatItemProcessor
// and apply the department name business rule
```

We need a RestController
```
@RestController
BatchRunController
```
with a request mapping /runbatch
and a method 

```
@Getmapping
public BatchsStatus runBatch()
```

the batch process shoulkd be triggered when you call this method
it should autowire all the needed instances of needed classes

this method should return BatchsStatus 
jobexecution.getBatchStatus

For getting Instances of the following :
1. **ItemReader**: Use `FlatFileItemReader` from Spring Batch.
2. **ItemWriter**: Use `JpaItemWriter` or `RepositoryItemWriter` from Spring Batch.
3. **JobLauncher**: Provided by Spring Batch.
4. **JobBuilderFactory**: Provided by Spring Batch.
5. **StepBuilderFactory**: Provided by Spring Batch.
6. **JobParametersBuilder**: Provided by Spring Batch.

They can be obtain these through configuration and dependency injection.


## 4. The code

### 1. `MySpringBatchConfig.java`

```java
package com.nico.spring.batch.config;

import com.nico.spring.batch.entity.Employee;
import com.nico.spring.batch.process.Processor;
import org.springframework.batch.core.Job;
import org.springframework.batch.core.Step;
import org.springframework.batch.core.configuration.annotation.EnableBatchProcessing;
import org.springframework.batch.core.configuration.annotation.JobBuilderFactory;
import org.springframework.batch.core.configuration.annotation.StepBuilderFactory;
import org.springframework.batch.core.launch.support.RunIdIncrementer;
import org.springframework.batch.item.database.JpaItemWriter;
import org.springframework.batch.item.file.FlatFileItemReader;
import org.springframework.batch.item.file.builder.FlatFileItemReaderBuilder;
import org.springframework.batch.item.file.mapping.BeanWrapperFieldSetMapper;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.FileSystemResource;

import javax.persistence.EntityManagerFactory;

@Configuration
@EnableBatchProcessing
public class MySpringBatchConfig {

    @Value("${input.file.path}")
    private String inputFilePath;

    @Bean
    public Job job(JobBuilderFactory jobBuilderFactory, StepBuilderFactory stepBuilderFactory,
                   FlatFileItemReader<Employee> itemReader, JpaItemWriter<Employee> itemWriter, Processor processor) {
        Step step = stepBuilderFactory.get("step")
                .<Employee, Employee>chunk(10)
                .reader(itemReader)
                .processor(processor)
                .writer(itemWriter)
                .build();

        return jobBuilderFactory.get("job")
                .incrementer(new RunIdIncrementer())
                .start(step)
                .build();
    }

    @Bean
    public FlatFileItemReader<Employee> itemReader() {
        return new FlatFileItemReaderBuilder<Employee>()
                .name("employeeItemReader")
                .resource(new FileSystemResource(inputFilePath))
                .delimited()
                .names("id", "name", "department", "salary")
                .fieldSetMapper(new BeanWrapperFieldSetMapper<>() {{
                    setTargetType(Employee.class);
                }})
                .build();
    }

    @Bean
    public JpaItemWriter<Employee> itemWriter(EntityManagerFactory entityManagerFactory) {
        JpaItemWriter<Employee> writer = new JpaItemWriter<>();
        writer.setEntityManagerFactory(entityManagerFactory);
        return writer;
    }
}
```

### 2. `Employee.java`

```java
package com.nico.spring.batch.entity;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Employee {
    @Id
    private Long id;
    private String name;
    private String department;
    private String salary;

    // Getters and Setters

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDepartment() {
        return department;
    }

    public void setDepartment(String department) {
        this.department = department;
    }

    public String getSalary() {
        return salary;
    }

    public void setSalary(String salary) {
        this.salary = salary;
    }
}
```

### 3. `EmployeeRepository.java`

```java
package com.nico.spring.batch.repo;

import com.nico.spring.batch.entity.Employee;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long> {}
```

### 4. `application.properties`

```properties
spring.batch.job.enabled=false
input.file.path=src/main/resources/Employee.csv
```

Here's the remaining part of the code, including the processor and the controller:

### 5. `Processor.java`

```java
package com.nico.spring.batch.process;

import com.nico.spring.batch.entity.Employee;
import org.springframework.batch.item.ItemProcessor;
import org.springframework.stereotype.Component;

import java.util.HashMap;
import java.util.Map;

@Component
public class Processor implements ItemProcessor<Employee, Employee> {
    private static final Map<String, String> DEPARTMENT_NAMES = new HashMap<>();

    static {
        DEPARTMENT_NAMES.put("001", "IT");
        DEPARTMENT_NAMES.put("002", "Management");
        DEPARTMENT_NAMES.put("003", "Sales");
    }

    @Override
    public Employee process(Employee employee) throws Exception {
        employee.setDepartment(DEPARTMENT_NAMES.get(employee.getDepartment()));
        return employee;
    }
}
```

### 6. `BatchRunController.java`

```java
package com.nico.spring.batch.controller;

import org.springframework.batch.core.Job;
import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.JobParametersBuilder;
import org.springframework.batch.core.launch.JobLauncher;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/runbatch")
public class BatchRunController {

    @Autowired
    private JobLauncher jobLauncher;

    @Autowired
    private Job job;

    @GetMapping
    public String runBatch() {
        try {
            JobExecution jobExecution = jobLauncher.run(job, new JobParametersBuilder().toJobParameters());
            return "Batch job has been invoked. Batch Status: " + jobExecution.getStatus();
        } catch (Exception e) {
            e.printStackTrace();
            return "Batch job failed to run";
        }
    }
}
```

### 7. `Employee.csv`

```
id,name,department,salary
1,Nico,001,$1000
2,Joe,002,$500
3,Pauls,003,$1000
```

### File Structure

```
src
└── main
    ├── java
    │   └── com
    │       └── nico
    │           └── spring
    │               └── batch
    │                   ├── config
    │                   │   └── MySpringBatchConfig.java
    │                   ├── controller
    │                   │   └── BatchRunController.java
    │                   ├── entity
    │                   │   └── Employee.java
    │                   ├── process
    │                   │   └── Processor.java
    │                   └── repo
    │                       └── EmployeeRepository.java
    ├── resources
    │   └── application.properties
    └── Employee.csv
```

### Explanation

- **Processor (`Processor.java`)**: Implements `ItemProcessor<Employee, Employee>` to apply business logic to each `Employee` read from the input file before writing it to the database.
  
- **BatchRunController (`BatchRunController.java`)**: Provides a REST endpoint (`/runbatch`) to trigger the Spring Batch job. It autowires `JobLauncher` and `Job` to launch and execute the job respectively.
  
- **`Employee.csv`**: Contains sample data in CSV format that will be read by the batch job.

### Summary

This setup completes the Spring Batch project configuration and implementation as per the provided specifications. \
The project reads data from `Employee.csv`, processes it using the `Processor` class to convert department codes, \
and writes the processed data to the database using Spring Batch's `JpaItemWriter` configured with `EmployeeRepository`. \
The `BatchRunController` provides a convenient way to trigger the batch job via a RESTful endpoint. \
Adjust the `application.properties` file as needed for your specific environment configurations.


## Bibliography
https://www.youtube.com/watch?v=2sdY-fKaR2o

