---
title: "Creating Your First Hugo Site"
date: 2023-10-24T15:19:33Z
draft: false
---
## 1. open powershell
Open the Windows Start menu, type powershell press enter \
type hugo version \
type hugo help

## 2. lets find a theme to use on our site 
[https://themes.gohugo.io/](https://themes.gohugo.io/)

lets choose Ananke as our test theme \
[https://themes.gohugo.io/themes/gohugo-theme-ananke/](https://themes.gohugo.io/themes/gohugo-theme-ananke/)

this can be downloded from Github (Click the download button) \
[https://github.com/theNewDynamic/gohugo-theme-ananke](https://github.com/theNewDynamic/gohugo-theme-ananke)
Now click on the Code dropdown button \
click on the two littel squares to copt the code \
we got \
**https://github.com/theNewDynamic/gohugo-theme-ananke.git**

## 3. Lets create a site
```
C:\Users\nico\Documents

mkdir hugo-sites

cd C:\Users\nico\Documents\hugo-sites

# lest create a site called hugo-demo
hugo new site hugo-demo

dir
# lets open our project folder
cd hugo-demo
```
we now want to add our theme
```
cd themes
# we will now download our theme from Github
git clone https://github.com/theNewDynamic/gohugo-theme-ananke.git
dir
# now lets get back to our sites top folder
cd ../
# lets edit the toml file
 notepad .\hugo.toml
# if you have VSCode the command is: code .
# then find the toml file and edit it
```
Put this into the file: (at the bottom we added the theme name)
```
baseURL = 'https://example.org/'
languageCode = 'en-us'
title = 'put a description of your site here:My New Hugo Site'
theme = "gohugo-theme-ananke"
```
Lets open the server
```
hugo server
```
Open the website in your server \
[http://localhost:1313/](http://localhost:1313/)
press CTRL C to stop the server


## 4. Lets use the Ananke exampleSite as config and content
```
# Copy folder themes/gohugo-theme-ananke/exampleSite/content paste over content folder
cp -r themes/gohugo-theme-ananke/exampleSite/content/ ./

# replace config.toml file with themes/gohugo-theme-ananke/exampleSite/config.toml file
cp themes/gohugo-theme-ananke/exampleSite/config.toml ./ 
```
We get an error which we have to fix
```
# Edit config toml file 
notepad .\hugo.toml
```
Change the fourth line where theme = ["github.com/theNewDynamic/gohugo-theme-ananke"] \
Put this there

```
theme = "gohugo-theme-ananke"
```

Lets open the server again
```
hugo server
```
Open the website in your server \
[http://localhost:1313/](http://localhost:1313/)
press CTRL C to stop the server

## 5. Adding content on the Annke Example Site
here we use a different folder for our files \
content/en/post \
Posting a new page to your site (called hello.md)
```
hugo new post/hello.md
# youe new post is here
dir .\content\en\post\

# edit your post
 notepad .\content\posts\hello.md

# Publish to your server
hugo server

```


## 6. Adding content on other sites
Posting a new page to your site (called hello.md)
```
hugo new posts/hello.md
# youe new post is here
dir .\content\posts\

# edit your post
 notepad .\content\posts\hello.md

# Publish to your server
hugo server

```

## 7. Publishing your content to the website
```
hugo -D
```

### 7.1 Pushing your content to GitLab 
When you are happy with the you site running on your huge server its time to put your into the GIT project \
**Copy the folder on your project into the hugo-content folder**
Now open your project inside the hugo-content folder \
For my anake project I did this:
```
cd hugo-content\ananke\
```
### 7.2 Change the top line of the config.tomel file
my project was ananke \
This is what I needed to change the baseURL in my config.toml file look like:
```
notepad config.toml
```
and made these changes:
```
title = "Notre-Dame de Paris" <--This is the title of our site
baseURL = "http://82.165.61.35/hugo-content/ananke/" <-- change this for our server
languageCode = "en-us"
theme = "gohugo-theme-ananke"
resourceDir = "../resources"
```
### 7.3 Use Hugo to publish the content to the public folder
Run this command:
```
hugo -D
```

### 7.4 To get your changes to GIT (Gitlab)
```
# First add your changes
git add .

# Now give a commit comment to describe what changed
git commit -m "updated config and added new content"

# Now we will push the changes to the GIT server
git push
# use your user id and password
```
