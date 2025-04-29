---
title: "Create Postman Test Suite"
date: 2024-04-15T06:29:39Z
draft: false
---
## Create Collection
Click New button (on top left side)
Click on Collection Button
fill in form
give the collection a name

## Create a Request
Click New button (on top left side)
Click on HTTP Button
In top left dropdown Select  GET , POST ..tec
Enter URL
If needed click on Body 
click on raw
enter JSON body text
Press Save button
Press Send button

## Creating a test for your Request
Open your Request
In the menu under the URL click on Tests (menu option)
In the Snippets dropdown select : Status code : Code in 200 (or other desired option)
Press Save button
Press Send button
The test will run

## Creating Environments
Click New button (on top left side)
Click on Environment Button
Give your environment a name : Dev-Environment
Press Save button

## Creating Environment Variables
In the left Navigator pane click on Environments
Click Dev-Environment
Click on Add New Variable Textbox
type variable name and initialvalue :  path : http://localhost:8080
Press Save button

## Using path variables in Requests
Open your request
In the top left dropdown select Environment as : Dev-Environment
replace (Server part of URL) http://localhost:8080 with {{path}} your URL should look like this
Press Save button
Press Send button
The request will run with ULL asspecified by path variable
If you added a test it should anso run

## Creating a Test Runner
On the bottom of Postman click on Runner
On the left Navigator top click on Collections
Drag and drop your requests to the Runner
Click Start Run button



