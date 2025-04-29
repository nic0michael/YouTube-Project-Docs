---
title: "Create Website"
date: 2022-12-27T10:50:17Z
draft: false
---

## 1 Create web-publish Shell script
Run this command:
```
nano /usr/bin/web-publish

```

add This content to this file:
```
#!/bin/bash

hugo -D
cp -rf public/. /var/www/html/$1/
echo "http://yourwebsite.com/$1/
```
run this command
```
chmod 775 /usr/bin/web-publish
```
## 2 Create shell script create-md-page
nano /usr/bin/create-md-page
```
#!/bin/bash

hugo new posts/$1
echo "to edit"
echo "editMdPage content/posts/"$1
```

chmod 775 /usr/bin/create-md-page

## 3 Create shell script edit-md-page
run this command
```
nano /usr/bin/edit-md-page
```
Put this into the file
```
#!/bin/bash
```
nano content/posts/"$1
Run this command
```
chmod 775 /usr/bin/edit-md-page
```
## 4 Create a New Site  my-site
Rin this command to create the new site
```
hugo new site my-site
```
## 5 Adding a theme
run these commands
```
cd my-site
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

Add the theme to the site configuration:
```
echo theme = \"ananke\" >> config.toml
```

Step 4: Add Some Content 

## 6 add content
hugo new posts/about-us.md

Edit the newly created content file if you want, it will start with something like this: make draft: false
```
nano content/posts/about-us.md
Adding content here
```

## 7 Customize the Theme 
Your new site already looks great, but you will want to tweak it a little before you release it to the public.

### Site Configuration 
Open up config.toml in a text editor:
```
baseURL = "http://your-web-server.com/your-site/"
languageCode = "en-us"
title = "Change this title"
theme = "ananke"
```
## 8 Build static pages
Run this command
```
hugo -D
```
Publish this page
run this command
```
web-publish my-site
```

## 9 Creating more content
run this command
```
create-md-page your-next-page.md
```
To Edit this file
run this command
```
edit-md-page your-next-page.md
```

Publish this page
run this command
```
web-publish my-site
```

