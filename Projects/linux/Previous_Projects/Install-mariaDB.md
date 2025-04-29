---
title: "Install MariaDB"
date: 2023-07-13T08:04:11Z
draft: false
---

## Windows Installation
Download the latest version of MariaDB here: \
https://mariadb.org/download/?t=mariadb&p=mariadb&r=11.2.0&os=windows&cpu=x86_64&pkg=msi&m=xtom_fre

1. Do not select the Alpha versions
2. Select the Operating System as : Windows
3. Select the Architecture as : X86_64
4. Select package type as : MSI package

Now download and install the MSI file \
It is prefered to run this as a service this will allow you to enable or disable this whan restarting windows


## Ubuntu Linux Installation
### Step 1 — Installing MariaDB
First Update your Linux
```
sudo apt update
```

Now Install MariaDB
```
#sudo apt install mariadb-server
sudo apt install mariadb-server mariadb-client 
```

Ensure that MariaDB is running with the systemctl start command:
```
sudo systemctl start mariadb.service
```
### Step 2 — Configuring MariaDB
Run the security script:
```
sudo mysql_secure_installation
```
It is recomended you enter a root password and use these responses:
```
# set root password? [Y/n]
Y

# Disalow root login remotely? [Y/n]
Y

# Remove test database? [Y/n]        (you my want to keep it)
Y

# Reload priveledges tablke now? [Y/n]
Y

```

Run the MariaDB Terminal
```
sudo mariadb 
```

Then create a new user with root privileges 
```
GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;

```

To save privlidges you need to run this command
```
FLUSH PRIVILEGES;
```

Now exit from MariaDb
```
EXIT
```
### Step 4 — Testing MariaDB
To test this, check its status.
```
sudo systemctl status mariadb
```

MariaDB as root using the Unix socket and return the version:
```
sudo mysqladmin version
```

If you configured a separate administrative user with password authentication, you could perform the same operation by typing:
```
mysqladmin -u admin -p version
```

to login as root
```
sudo mariadb -u root -p <rootpassword>
```
