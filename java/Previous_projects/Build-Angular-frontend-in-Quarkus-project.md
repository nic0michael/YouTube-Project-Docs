---
title: "Build Angular Frontend in Quarkus Project"
date: 2024-03-18T06:31:46Z
draft: false
---
## 1. Install NPM Node Package Manager
Run these commands
```
sudo apt update

sudo apt install npm -y

npm --version

```

## 2. Install NodeJs
Download Nodejs	from : \ 
https://nodejs.org/en/download

For Ubuntu/Mint Linux: \
https://nodejs.org/dist/v20.11.1/node-v20.11.1-linux-x64.tar.xz

Run these commands
```
wget https://nodejs.org/dist/v20.11.1/node-v20.11.1-linux-x64.tar.xz

sudo mkdir -p /usr/local/lib/nodejs

sudo tar -xJvf node-v20.11.1-linux-x64.tar.xz -C /usr/local/lib/nodejs
 
```
Run this command
```
nano ~/.bashrc
```
Add this in the file
```
export PATH=/usr/local/lib/nodejs/node-v20.11.1-linux-x64/bin:$PATH
```
Run these commands
```
source ~/.bashrc

node --version

npm --version
```
### 2.1 install ng Angular CLI:
You can install Angular CLI globally using npm. 

Open a terminal and run the following command:

```
npm install -g @angular/cli
```

Expect this message:

```
npm notice New minor version of npm available! 10.2.4 -> 10.5.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v10.5.0
npm notice Run npm install -g npm@10.5.0 to update!

```

Run this command:

```
npm install -g npm@10.5.0
```

Run this command :

```
ng version
```

## 3. Configure Quarkus to Serve Angular Build:
You need to configure Quarkus to serve the Angular build files. \
Typically, you would build the Angular project and serve its files statically using Quarkus. \
You can achieve this by configuring Quarkus to serve static resources from the Angular dist directory.

Edit the  application.properties to serve static resources:
```
server.host=http://localhost
server.port=8080

# WE ADDED THESE PROPERTIES:
quarkus.http.static-resources.pattern=/frontend/dist/*
quarkus.http.static-resources.path=/frontend/dist
quarkus.http.static-extensions=html,js,css
quarkus.http.static-resources.frontendng.path=META-INF/resources/frontend/frontendng


```
## 4. Create an Angular Project:
Navigate to the frontend directory within your Quarkus project and build the Angular project:
```
cd /opt/projects/LoyaltyPlusProjects/message-frontend/src/main/resources/META-INF/resources/frontend	

ng new frontendng

cd frontendng/src/
```

### 4.1 Create the index.html file
```
nano index.html
```

Put this in the file
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Frontendng</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.8.2/angular.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.8.2/angular-route.min.js"></script>
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

### 4.2 Create the template-component and template-service
```
ng generate component template

ng generate service template
```
Edit the template-component
```
cd /opt/projects/LoyaltyPlusProjects/message-frontend/src/main/resources/META-INF/resources/frontend

cd frontendng/src/app/template

nano template.component.ts
```
Put this in the file
```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-template',
  standalone: true,
  imports: [],
  templateUrl: './template.component.html',
  styleUrl: './template.component.css'
})
export class TemplateComponent {

  constructor() { }

  ngOnInit(): void {
  }

}

```


Edit the template-service
```
cd /opt/projects/LoyaltyPlusProjects/message-frontend/src/main/resources/META-INF/resources/frontend

cd frontendng/src/app/

nano template.service.ts
```
Put this in the file
```
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class TemplateService {

  constructor() { }

  // Example method
  greet(): string {
    return 'Hello from TemplateService!';
  }
}

```

### 4.3 Run the Angular Application:
Start the development server by running the following command:

```bash
cd /opt/projects/LoyaltyPlusProjects/message-frontend/src/main/resources/META-INF/resources/frontend

cd frontendng/

ng serve
```

### 4.4 View the Changes in the browser:
After navigating to 

[http://localhost:4200](http://localhost:4200)

## 5. Run Quarkus Application:
Once the Angular project is built, you can run your Quarkus application. \
Quarkus should now serve the Angular frontend along with the backend APIs.
```
# This prevents test errors: Maven skip tests
./mvnw compile quarkus:dev: -DskipTests

```

## 6. Access the Application:
Open your web browser and navigate to : \
**[http://localhost:8080](http://localhost:8080)**


