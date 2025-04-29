---
title: "Diagraming as Code and Modeling as Code"
date: 2025-04-28T07:08:04Z
draft: false
---
![](https://c4model.com/images/c4-overview.png) \
Level 1 : Context : 30 000 Foot view \
Level 2 : Containers : Bird Eyeview \
Level 3 : Components : Worm Eyeview \
Level 4 : Code       :Worm Eyeview

C4 Architecture modeling uses Code UML Artecats to define code for that Simon Brown recomends PlantUML as the best Diagramming tool to use

## 1. Diagraming as Code with PlantUML
PlantUML is Opensource and free 

PlantUML is a highly versatile tool that facilitates the rapid and straightforward creation of a wide array of diagrams. \
Utilizing a simple and intuitive language, users can effortlessly draft various types of diagrams. For a detailed exploration of the language's capabilities and syntax, please refer to the PlantUML Language Reference Guide. \
If you are new to PlantUML, we recommend starting with our quick start page to get up and running quickly. Should you have any questions, our F.A.Q. page is a valuable resource. Additionally, PlantUML can be seamlessly integrated with a variety of other tools to enhance your workflow.

![classes](https://miro.medium.com/v2/resize:fit:828/format:webp/1*bBq3lw_9vv-YB-tNSmd6rw.png)

![diagrams](https://marketplace.atlassian.com/product-listing/files/96109c29-2e20-4f3a-aae9-a148983027a5?fileType=image&mode=full-fit)

### 1.1 Website
**[PlantUML](https://plantuml.com/)**

**[Quick start](https://plantuml.com/starting)**

**[Language Reference Guide](https://plantuml.com/guide)**

### 1.2 VS Code Plugin
![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*aPywjB4V4ZIbY96oTSEF_w.gif)
**[VS Code plugin](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml)**

### 1.3 IntelliJ IDEA Plugins
**[1.3.1 PlantUML Studio](https://plugins.jetbrains.com/plugin/14821-plantuml-studio)** \
This plugin makes conveniently utilizes PlantUML on your IntelliJ project.
![](https://plugins.jetbrains.com/files/14821/screenshot_23273.png)

**[1.3.2 PlantUML Integration](https://plugins.jetbrains.com/plugin/7017-plantuml-integration)** \
PlantUML diagramming tool integration. Now better and faster, with code navigation and highlighting.
![](https://plugins.jetbrains.com/files/7017/screenshot_14265.png)

### 1.4 Videos
**[PlantUML - beautiful quick diagrams to explain your models](https://www.youtube.com/watch?v=EM-cvRubP4g)** \
[His Stylesheet](https://github.com/Drakemor/RedDress-PlantUML)

**[How to Make Easy UML Sequence Diagrams and Flow Charts with PlantUML](https://www.youtube.com/watch?v=xObBUVDMbQs&t=398s)**


## 2. Software architecture models as code with Structurizr 
Structurizr built by Simon Brown the creator of C4

He recommends using this tool together with PlantUML 

Structurizr builds upon "diagrams as code", allowing you to create multiple software architecture diagrams from a single model. \
There are a number of tools for creating Structurizr compatible workspaces, with the Structurizr DSL being the recommended option for most teams. 
Designed for the C4 model

![Context](https://static.structurizr.com/workspace/36141/diagrams/SystemContext.png)

### 2.1 Website
**[Structurizr](https://structurizr.com/)**

**[Documentation](https://docs.structurizr.com/)**

**[Quick start](https://docs.structurizr.com/quickstart)**

### 2.2 Videos
This video is by Simon Brown the creator of C4 \
**[C4 models as code - Simon Brown - NDC Oslo Conference 2023](https://www.youtube.com/watch?v=4HEd1EEQLR0)**


## 3. Creating UMl Diagramming Examples
### 3.1 Class Diagram
1. **Open VS Code**
2. **Create a PlantUML File :** \
**ClassDiagram1.wsd or ClassDiagram1.puml**
3. **Write Your Diagram Code**

**Put this is the file:**
```uml
@startuml
class Car {
  -make: string
  -model: string
  -year: int
  +start()
  +stop()
}
@enduml
```
4. **Preview Your Diagram:** \
**Ctrl+Shift+P on Windows or Cmd+Shift+P on Mac**

**You should have this :** \
![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*Hzic63x2OigJl0ZkfYsQdg.png)

5. **Export your diagram: **
- Open the Command Palette again and select “Export Current Diagram.” 
- Choose a file format (PNG, SVG, or PDF), then select a destination folder. 
- Your diagram will be saved as an image file in the selected folder.


### 3.2 Using a Stylesheet 

LIGHTBLUE
```
@startuml
!define LIGHTBLUE
!includeurl https://raw.githubusercontent.com/Drakemor/RedDress-PlantUML/master/style.puml
class Car {
  -make: string
  -model: string
  -year: int
  +start()
  +stop()
}
@enduml
```

![](https://camo.githubusercontent.com/64b5929872d73b93ced54bc061644179080f376a094181a9af87ecb36116c136/687474703a2f2f7777772e706c616e74756d6c2e636f6d2f706c616e74756d6c2f706e672f6853716e336539303434525864624630304d48774170484f594f61587547305a2d6f6c34744f37437034517a6c4a4a556d45667274736c434d4a73644d3547317a7262476a65562d56456f364b72374443694f534b34786b672d733962355f724f5f4f66747839316e714b53756c4d753936674c647159424b655f4741696e7372795a596d774d5a6e455047436c7a34724569732d672d42447756723970554769464b42)

DARKBLUE
```
@startuml
!define DARKBLUE
!includeurl https://raw.githubusercontent.com/Drakemor/RedDress-PlantUML/master/style.puml
class Car {
  -make: string
  -model: string
  -year: int
  +start()
  +stop()
}
@enduml
```

![](https://camo.githubusercontent.com/64b5929872d73b93ced54bc061644179080f376a094181a9af87ecb36116c136/687474703a2f2f7777772e706c616e74756d6c2e636f6d2f706c616e74756d6c2f706e672f6853716e336539303434525864624630304d48774170484f594f61587547305a2d6f6c34744f37437034517a6c4a4a556d45667274736c434d4a73644d3547317a7262476a65562d56456f364b72374443694f534b34786b672d733962355f724f5f4f66747839316e714b53756c4d753936674c647159424b655f4741696e7372795a596d774d5a6e455047436c7a34724569732d672d42447756723970554769464b42)

