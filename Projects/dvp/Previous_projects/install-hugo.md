---
title: "Install Hugo"
date: 2022-12-27T10:51:54Z
draft: false
---
### Installing Hugo on a FREE FOR LIFE Centos 8 Server
After viewing this Youtube video from Chris Titus : https://www.youtube.com/watch?v=6JaBian3vgI&t=122s
and reading his article  : https://christitus.com/how-i-setup-my-website/

I decided I needed a Hugo website of my own.

One of the deferences between Chris Titus' approach I did not want any software running on my workstation and I wanted every thing to run from the Cloud
I would only use a passwordless SSH session to connect to my server with SSL certificates

To make it easy to use I would create shell scripts that would wrap all the Linux and Hugo commands for me

```
* help-manage-web-site my-site          # This command would display these commands 
* list-md-pages my-site                 # This command would list all the articles (md files) in my site : my-site
* edit-md-page  create-hugo-website.md  # This command would aloy me to edit my article : create-hugo-website.md
* web-publish my-site                   # This command would publish all my non-draft articles to my site : my-site on the Apache server in my Centos VM
```


### When I learned That I could have my own Centos Cloud VM server FREE FOR LIFE 

Then I researched this and came up with my procedure : https://www.youtube.com/watch?v=NhDJI26Mt48&t=599s

**You need to watch this video first and the download and read my article.**

**As you only get one chance to do this** and if you miss a step or make a mistake it is going to be extreemly difficult to fix the problem as once you generate the VM you cant change it . It happened to me so I know that.

However after you have watched my video are read my article you will be able to get a bit server with 500GB and 16 or 32GB RAM depending on you settings (Speed or Size ) 

**Please note we will be running Hugo on a Centos Server running Apache Webserver**

**Instructions to install Apache is provided on this blog page**

http://rino.kozow.com/dvp/posts/installing-apache-webserver/

### 1. Login as root
```
sudo su -
```
### 2. In the folder: /etc/yum.repos.d create file : hugo.repo
```
cd /etc/yum.repos.d
```
### 3. Open browser to and copy text
https://copr.fedorainfracloud.org/coprs/daftaupe/hugo/repo/centos-stream-8/daftaupe-hugo-centos-stream-8.repo
### 4. Create file hugo.repo
```
nano hugo.repo
```
### 5. Paste the above text below to this file (currently its this content:)
```
[copr:copr.fedorainfracloud.org:daftaupe:hugo]
name=Copr repo for hugo owned by daftaupe
baseurl=https://download.copr.fedorainfracloud.org/results/daftaupe/hugo/centos-stream-8-$basearch/
type=rpm-md
skip_if_unavailable=True
gpgcheck=1
gpgkey=https://download.copr.fedorainfracloud.org/results/daftaupe/hugo/pubkey.gpg
repo_gpgcheck=0
enabled=1
enabled_metadata=1
```
### 6. Update the packages
```
yum update
```
### 7. Run this command to install Hugo
```
yum install hugo -y
```
### 8. Test Hugo
```
hugo version
```

## Creating a Hugo Static web site
Read this blog post :
Here I created my wapper shell scripts

http://rino.kozow.com/dvp/posts/create-hugo-website/


### Reference:
Here is the instructions I used to do this here:

https://tecofers.com/how-to-install-hugo-on-centos-8/

