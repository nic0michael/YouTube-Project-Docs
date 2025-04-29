---
title: "Install Gradle in Windows 11"
date: 2024-10-04T10:20:50Z
draft: false
---

1 Open this link : \
https://gradle.org/install/

2 open gradle release page : \
https://gradle.org/releases/

3 Click on binary Only : \
https://gradle.org/next-steps/?version=8.10.2&format=bin

4 Now click on verify SHA 256 Checksum (On download popup)

5 Open a command prompt to the downloads folder
```
cd Downloads

dir

# to display the SHA256 checksum
type gradle-8.1.2-bin.zip.sha256

# display the SHA256 checksum of the zip file
certutil -hashfile gradle-8.1.2-bin.zip SHA256

the two checksums should be the same
```
 Open the file in File Explorer

Right click file
 R-> gradle-8.1.2-bin.zip
    -> Extract All

Move the Extracted folder to where you want is our Gradle folder

We are now ging to create an Environment Path

Search for Environment

-> Environment Variables \
On top modify user environment variables \
-> path \
-> Edit \
-> New \
Paste the directory to new gradle folder \
-> OK

Open new command prompt
```
# verify gradle
gradle -v
```

Create a Spring Boot Application \
Open browser to : \
https://start.spring.io/

download and unzip the project zip file

here you will find the build.gradle file

run this command
```
# to get a list of tasks needed to be run
gradle tasks

# to compile and build the project
gradle build

# to Build a Jar file 
gradle bootJar

# to view the jar file
cd build
cd libs
dir

# you can now run this jar file
java -jar the-jar-file.jar
```


## Bibliography
https://www.youtube.com/watch?v=87aI9JL7Lsw

