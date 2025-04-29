---
title: "Starrocks Quickstart Tutorial Running on Dockage in Portainer"
date: 2024-06-08T09:32:07Z
draft: false
---

## What is Starrocks

1. StarRocks is a parallel processing database.
2. It uses the same MySQL client for basic SQL queries.
3. StarRocks is designed to work with much larger databases than MySQL.
3. StarRocks provides better performance for analytics workloads on large datasets compared to MySQL.
4. It is optimized for handling large-scale distributed analytics workloads.
5. StarRocks achieves high performance and scalability by distributing data and query processing across multiple nodes in a cluster.

## The docker compose file
We will deploy this using Dockage :
```
version: "3"

services:
  quickstart:
    image: starrocks/allin1-ubuntu
    ports:
      - "9030:9030"
      - "8030:8030"
      - "8040:8040"
    tty: true
    stdin_open: true
    container_name: quickstart
    restart: unless-stopped

```

## Using the MySQL client by running the Starrocks Front-end FE
The easiest way to use the mysql CLI is to run it from the StarRocks container starrocks-fe: \
Run this command in the Proxmox Container:
```
docker exec -it quickstart \
mysql -P 9030 -h 127.0.0.1 -u root --prompt="StarRocks > "
```
## We will now create a database and add afew customers
```
create database bigdata;

use bigdata;
```

```
CREATE TABLE customers (
    id INT,
    name VARCHAR(255),
    age INT,
    city VARCHAR(255),
    state VARCHAR(255),
    customer_type VARCHAR(15),
    social_security_number VARCHAR(11)
);
```

```
INSERT INTO customers (id, name, age, city, state, customer_type, social_security_number) VALUES
(1, 'John Doe', 30, 'New York', 'NY', 'CASH', '123-45-6789'),
(2, 'Jane Smith', 25, 'Los Angeles', 'CA', 'CREDIT_CARD', '987-65-4321'),
(3, 'Alice Johnson', 40, 'Chicago', 'IL', 'CHEQUE', '555-12-3456');

```

```
select * from customers;
```
expect to get
```
+------+---------------+------+-------------+-------+---------------+------------------------+
| id   | name          | age  | city        | state | customer_type | social_security_number |
+------+---------------+------+-------------+-------+---------------+------------------------+
|    1 | John Doe      |   30 | New York    | NY    | CASH          | 123-45-6789            |
|    2 | Jane Smith    |   25 | Los Angeles | CA    | CREDIT_CARD   | 987-65-4321            |
|    3 | Alice Johnson |   40 | Chicago     | IL    | CHEQUE        | 555-12-3456            |
+------+---------------+------+-------------+-------+---------------+------------------------+
```

## We would like you to do this excercize
Now as an excercize got to the Starrocks Quick Start page :
[https://docs.starrocks.io/docs/quick_start/](https://docs.starrocks.io/docs/quick_start/)

Click on the "Starrocks Basics" link and do that excercize

and complete the excercize


## Bibliography
https://docs.starrocks.io/docs/quick_start/shared-nothing/
