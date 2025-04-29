---
title: "Create Business Hugo Site"
date: 2022-12-27T16:57:00Z
draft: false
---
## Create Hugo Site
```
cd /home/myuser/website/
hugo new site my-site
cd my-site
```

## Clone Theme from Github
You need git
```
yum install git
OR
sudo apt install git
```


open this URL \
https://github.com/carsonip/hugo-theme-minos

run this commands:
```
cd themes\
git clone https://github.com/carsonip/hugo-theme-minos.git
```

Edit configuration
```
nano config.toml
```

put this in file
```
heme = "hugo-theme-minos"

paginate = 10

[params]
    smartToc = true
```

## Upload Banner Image : banner.png

```
mkdir /var/www/html/my-site

mkdir /var/www/html/my-site/images

cp ~/Uploads/my-site/*.png /var/www/html/my-site/images/

```

## Modify HTML fragment
```
nano themes/hugo-theme-minos/layouts/index.html
```

change the top of the file to look like this:
```
<body>
<style type="text/css">
.bgimg {
    background-image: url('http://rino.kozow.com/ZS6BVR/images/banner.png');
    height: 300px;
    width: 100%;
}
</style>


<div id="container">
    {{ partial "header.html" . }}
<div class="bgimg">
    div with background
</div>

```

## Create a md file with tags
```
edit-md-page Page-One.md
```

Put content like this in page:
```
---
title: "Page One"
date: 2022-12-27T15:01:01Z
draft: false
tags: ['Pages']
---

This is Page 1

```

## Publish web site
```
web-publish my-site 
```
