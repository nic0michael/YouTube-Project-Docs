---
title: "Installing Postman"
date: 2023-07-25T10:07:12Z
draft: false
---

Postman is needed to send requests using GET POST PUT and DELETE Methods to REST APIs for testing. \
Get Postman here : \
[https://www.postman.com/downloads/](https://www.postman.com/downloads/)

Click on the button for your Operating System Windows or Linux

## Creating a Workspace
This is where you keep all your collections of requests for testing your project APIs 

To create the Workspace:
1. click on new button on to of left Navigator
2. in popup click on Workspace
3. select Blank Workspace
4. click on next button
5. give the collection a name
6. give this collection a description
7. select personal radio button
8. click the Create button

## Creating a Collection 
This is used to keep all your test Requests

To create a Collection
1. click on new	button on to of left Navigator
2. in popup click on Collection
3. Give the collection a name and press enter

## Creating a simple Request
These are the REST Requests we will send to the REST Service

To create a Request
1. click on new button on to of left Navigator
2. in popup click on HTTP
3. Expand the GET dropdown and select the Class of the Request Handler (GET POST DELETE PUT) we will select GET
4. In the URL field enter the URL for the API we will use http://localhost:8090/api/managedtodolist/todos/
5. Press the Blue Send Button

## Creating a Request with Header key/Values and a JSON Body
If you are working with POST or PUT requests with a JASON Body
To create a Request
1. click on new button on to of left Navigator
2. in popup click on HTTP
3. Expand the GET dropdown and select the Class of the Request Handler (GET POST DELETE PUT) we will select POST
4. In the URL field enter the URL for the API we will use http://localhost:8090/api/managedtodolist/todos/
5. Click on Headers
6. Under Key type: Accept and next to that type: application/json
7. Under that type:  Content-Type and next to that type: application/json
8. Click Body
9. Underneath that ckick on RAW Radio Button
10. In the text Box type your Request body

We will use this request body
```
{
    "task": "do this NEW and special task",
    "duedate": "tomorrow",
    "details": "I need this urgently",
    "cellphone": "082 123 4567",
    "email": "carson@gmail.com",
    "status": "TODO"
}
```
11. Press the Blue Send Button
12. save your Request give it a suitable name

## The REST API endpoints (URLs)
```
# 1. Get all Todos
http://localhost:8090/api/managedtodolist/todos/
Microservice mapping : @GetMapping("/todos")

# 2. Get one Todo
http://localhost:8090/api/managedtodolist/todos/{id}
Microservice mapping : @GetMapping("/todos/{id}")

# 3. Get All Active todos
http://localhost:8090/api/managedtodolist/todos/activelist
Microservice mapping : @GetMapping("/todos/activelist")

# 4. Create a Todo
http://localhost:8090/api/managedtodolist/todos/
Microservice mapping : @PostMapping("/todos")

# 5. Delete a Todo
http://localhost:8090/api/managedtodolist/todos/4
Microservice mapping : @DeleteMapping("/todos/{id}")

# 6. Update and toggle the Todo status value
http://localhost:8090/api/managedtodolist/todos/toggle/completed/{id}
Microservice mapping : @PutMapping("/todos/toggle/completed/{id}")

# 7. Update a Todo
http://localhost:8090/api/managedtodolist/todos/{id}
Microservice mapping : @PutMapping("/todos/{id}")


```

## 1. Creating Request to get all Todos
Create a Request with : \
Class : GET
URL : http://localhost:8090/api/managedtodolist/todos/

## 2. Creating Request  to get one Todo
Create a Request with : \
Class : GET
URL : http://localhost:8090/api/managedtodolist/todos/1

## 3. Create Request to get all Active Todos
Create a Request with : \
Class : GET
URL : http://localhost:8090/api/managedtodolist/todos/activelist

## 4. Create Request to create a Todo
Create a Request with : \
Class : POST
URL : http://localhost:8090/api/managedtodolist/todos/
Headers: (key value pairs) :
Accept : application/json
Content-Type : application/json

Body:
```
{
    "task": "do this VERY URGENT NEW task",
    "duedate": "tomorrow",
    "details": "I need this urgently",
    "cellphone": "082 123 4567",
    "email": "carson@gmail.com",
    "status": "TODO" 
}
```
## 5. Create Request to delete a Todo 
Use the todoId of the results of the previots request : let say its 4 \

Create a Request with : \
Class : DELETE
URL : http://localhost:8090/api/managedtodolist/todos/4

## 6. Create Request to Update a Todo and toggle its status value
Lets assume we want to toggle the status of record 1


Create a Request with : \
Class : PUT
URL : http://localhost:8090/api/managedtodolist/todos/toggle/completed/1

## 7. Create Request to Update a Todo
Create a Request with : \
Class : PUT
URL : http://localhost:8090/api/managedtodolist/todos/{id}
Headers: (key value pairs) :
Accept : application/json
Content-Type : application/json

Body:
```
{
    "task": "do this task before tomorrow",
    "duedate": "tomorrow",
    "details": "I need this urgently",
    "cellphone": "082 123 4567",
    "email": "carson@gmail.com",
    "status": "BUSY"
}
