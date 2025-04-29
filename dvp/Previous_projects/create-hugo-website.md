---
title: "Create Hugo Website"
date: 2022-12-27T10:48:24Z
draft: false
---
**Please note this instruction is for running Hugo on the Centos Webserver with the Apache server installed**

**Installation Instructions for that found here:**

http://rino.kozow.com/dvp/posts/install-hugo/

## 1. Hugo Documentation
https://gohugo.io/documentation/

## 2. Hugo Quick Start
https://gohugo.io/getting-started/quick-start/

## 3.Themes
https://themes.gohugo.io/

## 4. Creating your Hugo Static site

### 4.1 Always Login as root
run this command
```
sudo su -
```
### 4.2 we will need nano to be able to create the User friendly wrapper shell scripts
You need nano installed if you dont have it run this command
```
yum install nano
```

### 4.3 Working with Hugo to Create a New Site (call it my-site)
You will need to do this once to create your site and skin it with the Ananke Theme

After you have done this then our shell scripts will greatly speed up and simplify the process of adding new content and publishing it to the server

run these commands
```
cd /home/myuser/website/

hugo new site my-site

mkdir my-site/content/posts
```

### 4.4 Create shell script to create new md pages and edit them
run this command:
```
nano /usr/bin/edit-md-page
```
Paste this into editor:
```
#!/bin/bash
hugo new posts/$1
nano content/posts/$1
```

### 4.5 Create this shellscript to publish web content
run this command:
```
nano /usr/bin/web-publish
```
add this content to this file
```
#!/bin/bash

hugo -D
cp -rf public/. /var/www/html/$1/
echo "http://yourwebsite.com/"$1/

```
you need to create a folder my-site  with the name of your website 

I sugest you give it a better name
```
mkdir /var/www/html/my-site
```

run this command
```
nano /usr/bin/help-manage-web-site
```
put the folowing into this script
```
echo "cd /home/myuser/website/my-site/"
echo "list-md-pages my-site"
echo "edit-md-page  my-first-blog-post.md"
echo "web-publish my-site"
cd /home/opc/website/my-site/
```
Now every time you want to work with Hugo online just run the help command for quick instructions
```
help-manage-web-site
```


## 5. Setting up your New Site (called my-site)
You will need to do this once to create your site and skin it with the Ananke Theme

After you have done this then our shell scripts will greatly speed up and simplify the process of adding new content and publishing it to the server


### 5.1 Create quickstart directory on the Apache server
run these commands
```
sudo su -
mkdir /var/www/html/quickstart
```

### 5.2 Add a Theme 
See themes.gohugo.io for a list of themes to consider. This quickstart uses the beautiful Ananke theme.

This site is built on this theme (Ananke)

First, download the theme from GitHub and add it to your site’s themes directory:

run these commands
```
cd my-site
cd themes
git init
git clone https://github.com/theNewDynamic/gohugo-theme-ananke.git
#git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

### 5.3 add the theme to the site configuration:
run this command
```
echo theme = \"gohugo-theme-ananke\" >> config.toml
```

### 5.4 edit config.toml file
run this command
```
nano config.toml

```
You should have this text in this file:
```
baseURL = 'http://rino.kozow.com/dvp/'
languageCode = 'en-us'
title = 'DVP Development Best Practices'
theme = "gohugo-theme-ananke"
```
### 5.5 make default setting for new content set to 
```
nano archetypes/default.md
```
Set draft: true  to false
```
draft: false
```
## 5.6  Add Some Content 
run this command
```
sudo su -
cd /home/myuser/website/my-site
```

You can manually create content files (for example as content/<CATEGORY>/<FILE>.<FORMAT>) and provide metadata in them, however you can use the new command to do a few things for you (like add title and date):

Although you can run this command
```
hugo new posts/my-first-post.md
```

It is better to run this command (using our shell script)

It will create the page and edit it 

```
edit-md-page my-first-post.md
```
You can run this command to make changes to your md page


## 5.7 publishing you content to your Centos server
run this command
```
web-publish quickstart
```

For help you can always run thies help script
```
help-manage-web-site
```

going forward you can  repeat steps 5.2 and 5.3 every time you want to add a new page or change an existing MD page

## 6 Going forward
Now every time you want to work with Hugo online just run the help command for quick instructions
```
help-manage-web-site
```


## 7 Now read this page to learm the markdown used on this site
**http://rino.kozow.com/dvp/posts/markdown-summary/**

