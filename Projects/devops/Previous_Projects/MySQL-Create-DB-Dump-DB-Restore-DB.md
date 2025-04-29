---
title: "MySQL Create DB Dump DB Restore DB"
date: 2023-11-06T09:04:53Z
draft: false
---

## 1. Create DB
Run these commands
```
# Login to DB
mysql -u root -p<ROOT_PASSWORD>

# Create database
create database [DATABASE_NAME]

```

## 2. Dump DB
Run these commands
```
sudo mysqldump -u [user] -p [database_name] > [filename].sql

```

## 3. Restore DB from Dump
Run this command
```
# To restore your dump using the command below
mysql -u [user] -p [database_name] < [filename].sql
```
