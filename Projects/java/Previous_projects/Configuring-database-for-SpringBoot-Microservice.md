---
title: "Configuring Database for SpringBoot Microservice"
date: 2023-07-16T10:25:54Z
draft: false
---

## Install Maria DB
Follow these instructions: \
http://rino.kozow.com/java/posts/install-mariadb/

## Adding the todo table
login as root
```
sudo mariadb -u root -p
# OR
sudo mariadb -u root -p'YourRootPassword'

create database todolist;

use todolist;

exit
```

## To add all the needed dependencies to POM file
Edit pom file and replace from the dependencies and the bottom of the POM file with this:
```

	<dependencies>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>
		
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
<!-- 	
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jdbc</artifactId>
		</dependency>
-->
<!-- https://mvnrepository.com/artifact/javax.persistence/javax.persistence-api -->
<dependency>
    <groupId>javax.persistence</groupId>
    <artifactId>javax.persistence-api</artifactId>
    <version>2.2</version>
</dependency>

		<!--
		https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client -->
		<dependency>
			<groupId>org.mariadb.jdbc</groupId>
			<artifactId>mariadb-java-client</artifactId>
			<version>2.5.2</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>
		<dependency>
			<groupId>org.mariadb.jdbc</groupId>
			<artifactId>mariadb-java-client</artifactId>
			<version>2.5.2</version>
		</dependency>

		<!--
		https://mvnrepository.com/artifact/jakarta.persistence/jakarta.persistence-api -->
		<dependency>
			<groupId>jakarta.persistence</groupId>
			<artifactId>jakarta.persistence-api</artifactId>
			<version>3.1.0</version>
		</dependency>


		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-lang3</artifactId>
			<!-- <version>3.3.2</version> -->
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-csv -->
		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-csv</artifactId>
			<version>1.1</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/commons-codec/commons-codec -->
		<dependency>
			<groupId>commons-codec</groupId>
			<artifactId>commons-codec</artifactId>
			<version>1.11</version>
		</dependency>
		
		
		<dependency>
			<groupId>org.junit.jupiter</groupId>
			<artifactId>junit-jupiter-api</artifactId>
			<version>5.5.0-M1</version>
		</dependency>

		<dependency>
			<groupId>org.junit.platform</groupId>
			<artifactId>junit-platform-runner</artifactId>
			<version>1.3.0</version>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.junit.jupiter</groupId>
			<artifactId>junit-jupiter-engine</artifactId>
			<version>5.3.2</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>	
		
	</dependencies>


	<build>
		<finalName>todolist-service</finalName>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>	

```


## Edit the aplication.properties file in the src/main/resources folder
This is want we want there:
```



#spring.main.banner-mode=off
logging.level.org.springframework=ERROR

# https://www.baeldung.com/spring-boot-custom-error-page
server.error.whitelabel.enabled=false
#the next property is annotated to main class
#spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration
server.error.path=/error

#create this database with this command:
# create database project_management;
spring.datasource.url=jdbc:mysql://localhost:3306/todolist?useSSL=false
spring.datasource.username=root
spring.datasource.password=P@55w0rd
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver


# Hibernate ddl auto (create, create-drop, validate, update)
spring.jpa.hibernate.ddl-auto = update
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.datasource.initialization=never
spring.jpa.show-sql=true

spring.thymeleaf.cache=true
```
## create the following Source Folder src/test/resources
Right click the Project 
select New 
select Source Folder 

enter this path\
src/test/resources 

Now copy and paste the file aplication.properties there

## create the following packages

```
com.springboot.nico.todolist.controllers;
com.springboot.nico.todolist.dtos;
com.springboot.nico.todolist.enums;
com.springboot.nico.todolist.exceptions;
com.springboot.nico.todolist.helpers;
com.springboot.nico.todolist.model;
com.springboot.nico.todolist.repositories;
com.springboot.nico.todolist.security;
com.springboot.nico.todolist.services;
com.springboot.nico.todolist.utils;
```

## create the Todo Entity class in the com.springboot.nico.todolist.model package
put this into the file
```

import java.util.Objects;
import java.util.Date;
import java.util.List;
import jakarta.persistence.*;
import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.JoinTable;
import javax.persistence.ManyToMany;
import javax.persistence.Table;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;
import javax.validation.constraints.NotBlank;
import org.apache.commons.lang3.StringUtils;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.lang.NonNull;

@Entity
@Table(name = "todo")
public class Todo {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long todoId;
	
	@NotBlank
    @Column(name="task", unique=true)
	private String task;
	
	@Column(name="due_date", nullable = true, updatable = true)
	private String duedate;
	
	private String details;
	private String cellphone;	
	private String email;
    private String status;

	@Column(name="date_created", nullable = true, updatable = true)
	@Temporal(TemporalType.TIMESTAMP)
	@CreatedDate
	private Date dateCreated;
	
	public Todo() {}	

	public String getTask() {
		return task;
	}

	public void setTask(String task) {
		this.task = task;
	}

	public String getDetails() {
		return details;
	}

	public void setDetails(String details) {
		this.details = details;
	}

	public String getDuedate() {
		return duedate;
	}

	public void setDuedate(String duedate) {
		this.duedate = duedate;
	}

	public String getCellphone() {
		return cellphone;
	}

	public void setCellphone(String cellphone) {
		this.cellphone = cellphone;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public String getStatus() {
		return status;
	}

	public void setStatus(String status) {
		this.status = status;
	}

	public Date getDateCreated() {
		return dateCreated;
	}

	public void setDateCreated(Date dateCreated) {
		this.dateCreated = dateCreated;
	}

	public Long getTodoId() {
		return todoId;
	}

	public Todo(@NotBlank String task, String details, String duedate, String cellphone, String email, String status,
			Date dateCreated) {
		super();
		this.task = task;
		this.details = details;
		this.duedate = duedate;
		this.cellphone = cellphone;
		this.email = email;
		this.status = status;
		this.dateCreated = dateCreated;
	}	
}
```

## Create the TodoRepository Interface in the com.springboot.nico.todolist.repositories package
Put this into the Interface
```

import java.util.List;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import com.springboot.nico.todolist.model.Todo;

public interface TodoRepository  extends JpaRepository<Todo, Long> {

	public Todo findByTodoId(Long todoId);
}

```
## Create the TodoService class in the com.springboot.nico.todolist.services package
put this code in the file
```

import java.util.ArrayList;
import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;
import com.springboot.nico.todolist.model.Todo;
import com.springboot.nico.todolist.repositories.TodoRepository;

@Service
public class TodoService {

	@Autowired
	TodoRepository todoRepo;

	public List<Todo> findAlTodos() {
		List<Todo> foundTodos = new ArrayList<>();
		List<Todo> todos;
		try {
			todos = todoRepo.findAll(Sort.by("task"));
			if(todos != null) {
				foundTodos = todos;
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
		
		return foundTodos;
	}
}
```

## Create the TodoHelper class in the com.springboot.nico.todolist.helpers package
put this code in the file
```

import java.util.ArrayList;
import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Component;
import com.springboot.nico.todolist.model.Todo;
import com.springboot.nico.todolist.repositories.TodoRepository;
import com.springboot.nico.todolist.services.TodoService;

@Component
public class TodoHelper {

	@Autowired
	TodoService todoService;

	public List<Todo> findAllActiveTodos() {
		List<Todo> foundTodos = new ArrayList<>();
		List<Todo> todos;
		todos = todoService.findAlTodos();
		for (Todo todo : todos) {
			if (todo != null && !"COMPLETED".equalsIgnoreCase(todo.getStatus())) {
				foundTodos.add(todo);
			}
		}

		return foundTodos;
	}

	public List<Todo> findAlTodos() {
		return todoService.findAlTodos();
	}
}
```

## Create class TodoRestController in package com.springboot.nico.todolist.controllers

put this code in the file
```

import java.util.List;
import org.apache.commons.lang3.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Sort;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import com.springboot.nico.todolist.helpers.TodoHelper;
import com.springboot.nico.todolist.model.Todo;

@RestController
@RequestMapping("/api/todo")
public class TodoRestController {
	private static final Logger log = LoggerFactory.getLogger(TodoRestController.class);

	@Autowired 
	TodoHelper todoHelper;	

	// http://localhost:8090/api/todo/activelist
	@GetMapping("/activelist")
	public List<Todo>  getActiveTodos(Model model) {
		log.info("getTodos called");
		return todoHelper.findAllActiveTodos();
	}
	
	// http://localhost:8090/api/todo/
	@GetMapping
	public List<Todo>  getAllTodos(Model model) {
		log.info("getTodos called");
		return todoHelper.findAlTodos();
	}
}
```

## Run the TodoList Micoservice
Right click on the project 
select: Run As
select: Spring Boot App

## Connect to the database and see that the todo table was created
```
sudo mariadb -u root -p

create database todolist;

use todolist;

show tables;
```
Expect to get
```
show tables;
+--------------------+
| Tables_in_todolist |
+--------------------+
| todo               |
+--------------------+
1 row in set (0.001 sec)

```


Run these queries
```
insert into todo ( task, due_date,details, email,cellphone,status) values ( 'do this task','tomorrow','I need this urgently', 'carson@gmail.com','082 123 4567','TODO');	

insert into todo ( task, due_date,details, email,cellphone,status) values ( 'previous task','done','was needed urgently', 'carson@gmail.com','082 123 4567','COMPLETED');	

insert into todo ( task, due_date,details, email,cellphone,status) values ( 'a task','I am busy with this','was needed urgently', 'carson@gmail.com','082 123 4567','BUSY');

select * from todo;
```
Expected
```
select * from todo;
+---------+--------------+--------------+----------------------+---------------------+------------------+-----------+---------------+
| todo_id | cellphone    | date_created | details              | due_date            | email            | status    | task          |
+---------+--------------+--------------+----------------------+---------------------+------------------+-----------+---------------+
|       1 | 082 123 4567 | NULL         | I need this urgently | tomorrow            | carson@gmail.com | TODO      | do this task  |
|       2 | 082 123 4567 | NULL         | was needed urgently  | done                | carson@gmail.com | COMPLETED | previous task |
|       3 | 082 123 4567 | NULL         | was needed urgently  | I am busy with this | carson@gmail.com | BUSY      | a task        |
+---------+--------------+--------------+----------------------+---------------------+------------------+-----------+---------------+
3 rows in set (0.001 sec)
```

## Testing API with Browser
**open browser to this url: to view list sorted by task name** \
[http://localhost:8090/api/todo/](http://localhost:8090/api/todo/)

**open browser to	this url: to view list of uncompleted tasks sorted by task name** \
[http://localhost:8090/api/todo/activelist](http://localhost:8090/api/todo/activelist)


## Now we will explain what we did

### Step 1 we added database and othe dependencies to the POM file
In order to communicate with the maria DB database we needed to ad dependencies to the POM File

### Step 2 We need Database Credentials and other properties in the application.properties file
we added the Root password and database URL to the properties file
Additionally we added the port number 8090 there

### Step 3 We needed to create and Entity Class
The Class ToDo is the Java representation of the database table todo

### Step 4 We added the Repository Interface
TodoRepository was added to allow us to use JPA technology to talk to the database \
We also added a method there findByTodoId() 

### Step 5 We added the Service class TodoService
This class uses the TodoRepository interface to communicate with the database 
 
### Step 6 We added the Helper class
The todoHelper class simplifies the way the Controller classes will call the services \
Its like a façade infront of a building in this case a  façade infromt of the TodoService class providing simple methods you can use

### Step 7 we added the Controller class
The TodoController class has the Webmethods that represent the RESTful Web interface or API to the Backend data \
Our TodoController has methods with request mappings representing these URLs : 

http://localhost:8090/api/todo/ \
and \
http://localhost:8090/api/todo/activelist

Which are wired to call these methods: \
getAllTodos() \
and \
getActiveTodos

both of these methods call different methods of our TodoHelper class to get data from the Database and return it on the web interface (REST API).
