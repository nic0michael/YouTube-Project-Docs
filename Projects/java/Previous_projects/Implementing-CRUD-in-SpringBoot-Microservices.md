---
title: "Implementing Full CRUD in SpringBoot Microservices"
date: 2023-07-23T10:54:43Z
draft: false
---


In the previous two lessons, first we created our Spring Boot Microservice, Then we installed mariaDB and implemented a rudimentary CRUD with just GET methods.

Now we are going to implement the full CRUD (Create, Read, Update, and Delete) using the following HTTP Requests (GET, POST, DELETE, and PUT) \
after that we are going to install Postman and use this to create Requests to test all our CRUD functionality.

## Read the JavaDocs
We have	provided JavaDoc Comments for all the Classes and Methods that we are now going	to change to explain how they work. \
It is recomended that you read these JavaDoc comments to understand how	the code works

## 1. Changing the Todo Entity Class
This Class is a Java Represeentation of the "todo" table in our Database

**Edit the class com.springboot.nico.todolist.model.Todo class replace all its code with this:**
```
package com.springboot.nico.todolist.model;

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
import javax.validation.Valid;
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


	public Long getTodoId() {
		return todoId;
	}

	public Todo(@NotBlank String task, String details, String duedate, String cellphone, String email, String status) {
		super();
		this.task = task;
		this.details = details;
		this.duedate = duedate;
		this.cellphone = cellphone;
		this.email = email;
		this.status = status;
	}

	public void setTodo(@Valid Todo todo) {
		this.task = todo.getTask();
		this.details = todo.getDetails();
		this.duedate = todo.getDuedate();
		this.cellphone = todo.getCellphone();
		this.email = todo.getEmail();
		this.status = todo.getStatus();
	}	
}

```

## 2. Changing the TodoService to perform needed Database Operations
This class will be used to perform all the needed Database operations calling the Repository Interface  \
We have added JavaDoc comments for every method to describe what they do


**Edit the class com.springboot.nico.todolist.services.TodoService class replace all its code with this:**
```
package com.springboot.nico.todolist.services;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import javax.validation.Valid;
import java.util.HashMap;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Sort;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import com.springboot.nico.todolist.exceptions.ResourceNotFoundException;
import com.springboot.nico.todolist.model.Todo;
import com.springboot.nico.todolist.repositories.TodoRepository;

/**
 * This class Calls the TodoRepository interface to all operations on the Database
 * 
 * @author nickm
 *
 */
@Service
public class TodoService {
	private final String COMPLETED = "COMPLETED";
	private final String BUSY = "BUSY";

	@Autowired
	TodoRepository todoRepo;

	/**
	 * This method is used to return a list of all the Todos in the Database
	 * 
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public List<Todo> findAlTodos() throws ResourceNotFoundException{
		List<Todo> foundTodos = new ArrayList<>();
		List<Todo> todos;
		try {
			todos = todoRepo.findAll(Sort.by("task"));
			if(todos != null) {
				foundTodos = todos;
			}
			return foundTodos;
		} catch (Exception e) {
			throw new ResourceNotFoundException("Todos not retrieved ");
		}
	}

	/**
	 * This method is used to find and return a Todo
	 *  In this method we receive the Record number of our Todo to retrieve
	 *  
	 * @param todoId
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public Todo findTodoById(Long todoId) throws ResourceNotFoundException{
		try {
			return todoRepo.findByTodoId(todoId);
			
		} catch (Exception e) {
			throw new ResourceNotFoundException("Todo not found for this id :: " + todoId);
		}
	}
	
	/**
	 * This method is used to Delete a Todo record from the database
	 * In this method we receive the Record number of our Todo to delete
	 * 
	 * @param todoId
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public Map < String, Boolean >  deleteTodoById(Long todoId) throws ResourceNotFoundException{
		Todo todo = todoRepo.findByTodoId(todoId);
		try {
			todoRepo.delete(todo);
			Map < String, Boolean > response = new HashMap < > ();
	        response.put("deleted", Boolean.TRUE);
	        return response;
	        
		} catch (Exception e) {
			throw new ResourceNotFoundException("Todo not deleted for this id :: " + todoId);
		}
	}

	/**
	 * This method is used to Create a New Todo record
	 * In this method we receive a Todo object from the Request and save it to the Database
	 *  
	 * @param todo
	 * @return
	 */
	public Todo createTodo(@Valid Todo todo) {		
		return todoRepo.save(todo);
	}



	/**
	 * This method is used to toggle the status between COMPLETED and BUSY
	 * In this method we receive the Record number of our Todo 
	 * 
	 * @param todoId
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public Todo toggleCompletedByTodoById(Long todoId) throws ResourceNotFoundException{		
		try {
			Todo todo = todoRepo.findByTodoId(todoId);
			String status = todo.getStatus().toUpperCase();
			if(COMPLETED.equalsIgnoreCase(status)) {
				todo.setStatus(BUSY);				
			} else {
				todo.setStatus(COMPLETED);
			}
			todoRepo.save(todo);
	        return todo;
	        
		} catch (Exception e) {
			throw new ResourceNotFoundException("Todo not updated for this id :: " + todoId);
		}
	}
	

	/**
	 * This method is used to Update our Todo
	 * In this method we receive the Record number of our Todo and a Todo object to replace it  
	 * 
	 * @param todoId
	 * @param todo
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public Todo updateTodo(Long todoId, @Valid Todo todo) throws ResourceNotFoundException{		
		try {
			Todo foundTodo = todoRepo.findByTodoId(todoId);
			String status = todo.getStatus().toUpperCase();
			foundTodo.setTodo(todo);
			todoRepo.save(foundTodo);
	        return foundTodo;
	        
		} catch (Exception e) {
			throw new ResourceNotFoundException("Todo not updated for this id :: " + todoId);
		}
	}
}
```

## 3. Changing the TodoHelper 
This class actsas a Facade infront of our TodoService class providing a "Friendlier" interface for the TodoRestController to call. \
We have added JavaDoc comments for every method to describe what they do.

**Edit the class com.springboot.nico.todolist.helpers.TodoHelper class replace all its code with this:**
```
package com.springboot.nico.todolist.helpers;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import javax.validation.Valid;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Sort;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Component;
import com.springboot.nico.todolist.exceptions.ResourceNotFoundException;
import com.springboot.nico.todolist.model.Todo;
import com.springboot.nico.todolist.repositories.TodoRepository;
import com.springboot.nico.todolist.services.TodoService;
/**
 * This class Calls the TodoService to send and receive data from the Database
 * 
 * @author nickm
 *
 */
@Component
public class TodoHelper {

	@Autowired
	TodoService todoService;

	/**
	 * This method returns a List of Todos
	 * 
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public List<Todo> findAlTodos() throws ResourceNotFoundException {
		return todoService.findAlTodos();
	}
	
	/**
	 * This method is used to retrieve all the Todos from the Database
	 * 
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public List<Todo> findAllActiveTodos() throws ResourceNotFoundException {
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

	/**
	 * This method is used to get one todo from the Database
	 * In this method we receive the Record number of our Todo that we need to retrieve from the Database
	 * 
	 * @param todoId
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public ResponseEntity<Todo> findTodoById(Long todoId) throws ResourceNotFoundException {
		Todo todo = todoService.findTodoById(todoId);
		return ResponseEntity.ok().body(todo);

	}

	/**
	 * This method will Delete a Todo record in the database
	 * In this method we receive the Record number of our Todo that we need to delete the record
	 * 
	 * @param todoId
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public Map<String, Boolean> deleteTodoById(Long todoId) throws ResourceNotFoundException {
		Map<String, Boolean> response = todoService.deleteTodoById(todoId);
		return response;
	}

	/**
	 * This method is used to toggle the status between COMPLETED and BUSY
	 * In this method we receive the Record number of our Todo 
	 * 
	 * @param todoId
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public ResponseEntity<Todo> toggleCompletedByTodoById(Long todoId) throws ResourceNotFoundException {
		Todo todo = todoService.toggleCompletedByTodoById(todoId);
		return ResponseEntity.ok().body(todo);
	}

	/**
	 * This method is used to Create a New Todo record
	 * In this method we receive a Todo object from the Request and save it to the Database
	 * 
	 * @param todo
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public ResponseEntity<Todo> createTodo(@Valid Todo todo) throws ResourceNotFoundException {
		Todo createTodo =  todoService.createTodo(todo);
		return ResponseEntity.ok().body(createTodo);
	}

	/**
	 * This method is used to Update our Todo
	 * In this method we receive the Record number of our Todo and a Todo object from the Request to replace it  
	 * 
	 * @param todoId
	 * @param todo
	 * @return
	 * @throws ResourceNotFoundException
	 */
	public ResponseEntity<Todo> updateTodo(Long todoId, @Valid Todo todo) throws ResourceNotFoundException {
		Todo updateTodo =  todoService.updateTodo(todoId, todo);
		return ResponseEntity.ok().body(updateTodo);
	}
}

```
## 4. Changing the TodoRestController to perform needed Database Operations
This class provides the REST API for our Microservice \
will be used to perform all the needed Database operations calling the TodoHelper which acts as a Facade to the back-end\
We have added JavaDoc comments for every method to describe what they do

**Edit the class com.springboot.nico.todolist.controllers.TodoRestController class replace all its code with this:**
```
package com.springboot.nico.todolist.controllers;

import java.util.List;
import java.util.Map;
import javax.validation.Valid;
import org.apache.commons.lang3.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Sort;
import org.springframework.http.ResponseEntity;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import com.springboot.nico.todolist.exceptions.ResourceNotFoundException;
import com.springboot.nico.todolist.helpers.TodoHelper;
import com.springboot.nico.todolist.model.Todo;

/**
 * This Class calls the TodoHelper which is used as a Facade to the Back-End operations
 * @author nickm
 *
 */
@RestController
@RequestMapping("/api/managedtodolist")
public class TodoRestController {
	private static final Logger log = LoggerFactory.getLogger(TodoRestController.class);

	@Autowired 
	TodoHelper todoHelper;	

	// http://localhost:8090/api/managedtodolist/todos/
	/**
	 * This method returns a list of all the Todos from the database
	 * 
	 * @param model
	 * @return
	 * @throws ResourceNotFoundException
	 */
	@GetMapping("/todos")
	public List<Todo>  getAllTodos(Model model) throws ResourceNotFoundException {
		log.info("getTodos called");
		return todoHelper.findAlTodos();
	}

	// http://localhost:8090/api/managedtodolist/todos/{id}
	/**
	 * This returns one Todo whose record Id is passed from the Request
	 * 
	 * @param todoId
	 * @return
	 * @throws ResourceNotFoundException
	 */
	@GetMapping("/todos/{id}")
	 public ResponseEntity < Todo > getTodoId(@PathVariable(value = "id") Long todoId) throws ResourceNotFoundException {
		log.info("getTodoId called");
		return todoHelper.findTodoById(todoId);
	}

	// http://localhost:8090/api/managedtodolist/todos/activelist
	/**
	 * This method gets a modified List of only the records that do nt have the status of COMPLETED
	 * 
	 * @param model
	 * @return
	 * @throws ResourceNotFoundException
	 */
	@GetMapping("/todos/activelist")
	public List<Todo>  getActiveTodos(Model model) throws ResourceNotFoundException {
		log.info("getActiveTodos called");
		return todoHelper.findAllActiveTodos();
	}
	
//	"http://localhost:8090/api/managedtodolist/todos/
	/**
	 * This method is used to Create a New Todo record
	 * In this method we receive a Todo object from the Request and save it to the Database
	 * 
	 * 
	 * @param todo
	 * @return
	 * @throws ResourceNotFoundException
	 */
	@PostMapping("/todos")
	public ResponseEntity<Todo> createTodo(@Valid @RequestBody Todo todo) throws ResourceNotFoundException {
		log.info("createTodo called");
        return todoHelper.createTodo(todo);
    }

//	"http://localhost:8090/api/managedtodolist/todos/4"
	/**
	 * This method will Delete a Todo record in the database
	 * In this method we receive the Record number of our Todo that we need to delete the record from the Request
	 * 
	 * @param todoId
	 * @return
	 * @throws ResourceNotFoundException
	 */
	@DeleteMapping("/todos/{id}")
	 public Map < String, Boolean >  deleteTodoById(@PathVariable(value = "id") Long todoId) throws ResourceNotFoundException {
		log.info("deleteTodoById called");
		return todoHelper.deleteTodoById(todoId);
	}
	
	// http://localhost:8090/api/managedtodolist/todos/toggle/completed/{id}
	/**
	 * This method is used to toggle the status between COMPLETED and BUSY
	 * In this method we receive the Record number of our Todo from the Request
	 * 
	 * @param todoId
	 * @return
	 * @throws ResourceNotFoundException
	 */
	@PutMapping("/todos/toggle/completed/{id}")
	 public ResponseEntity<Todo> setCompletedByTodoById(@PathVariable(value = "id") Long todoId) throws ResourceNotFoundException {
		log.info("setCompletedByTodoById called");
		return todoHelper.toggleCompletedByTodoById(todoId);
	}
	
//	"http://localhost:8090/api/managedtodolist/todos/{id}"
	/**
	 * This method is used to Update our Todo
	 * In this method we receive from the Request the Record number of our Todo and a Todo object from the Request to replace it  

	 * 
	 * @param todoId
	 * @param todo
	 * @return
	 * @throws ResourceNotFoundException
	 */
	@PutMapping("/todos/{id}")
	public ResponseEntity<Todo> updateTodo(@PathVariable(value = "id") Long todoId,
    		@Valid @RequestBody Todo todo) throws ResourceNotFoundException {
		log.info("updateTodo called");
        return todoHelper.updateTodo(todoId,todo);
    }
}

```
## Run The Microservice from the IDE

1. Right click : The project in the Navigator 
2. select : Run As
3. Select : Spring Boot App


## Installing Postman and testing our Spring Boot CRUD API 
**The instructione for this is provided in our next video.(As Soon as it is published)**

Please watch this video: \
[http://rino.kozow.com/java/posts/installing-postman/](http://rino.kozow.com/java/posts/installing-postman/)
