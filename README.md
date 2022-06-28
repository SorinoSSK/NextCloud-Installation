# NextCloud-Installation
### Step 1 Missing repositories
- Adding repositories for php
- Note: install only php7.4 or else php 8.0 will also be installed during apt-get update.\
```sudo add-apt-repository ppa:ondrej/php -y```
#### Step 1 - others:
- Purge php if any other version is installed\
```sudo apt-get purge 'php*'```
***
### Step 2 Add server details to your computer
#### Step 2.1:
- Add **server name** or **domain name** under **hostname** file\
```sudo gedit /etc/hostname```
#### Step 2.2
- Add  **127.0.1.1**, **server name** or **domain name** and, **nickname**\
```sudo gedit /etc/hosts```
***
### Step 3 Download Nextcloud
#### Step 3.1
- Go to https://nextcloud.com/install/
- Under **ARCHIVE FILE**, right click and get link from the download file
### Step 3.2
- Download nextcloud\
```wget **URL**```
#### Step 3 - others
- Check if wget is installed\
```which wget```
- If it is not installed\
```sudo apt install wget```
### Step 4 Setup database
#### Step 4.1
- Install mariadb-server\
``` sudo apt install mariadb-server```
- Check status after installation\
```sudo systemctl status mariadb```
#### Step 4.2
- setup database secure installation\
```sudo mysql_secure_installtion```
- Finish up questions prompt
#### Step 4.3
- Access database\
``` sudo mariadb```
- Create database\
```CREATE DATABASE nextcloud;```
- Check for database\
```SHOW DATABASES;```
- Create and grant privileges for application to access\
```GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost' IDENTIFIED BY '**Random Password**'`;```\
```FLUSH PRIVILEGES;```
- Exit Database\
```exit```
#### Step 4 - others
- If mariadb does not automatically startup\
```sudo systemctl enable maria```
- To start mariadb\
```sudo systemctl start maria```
### Step 5 Install apache
#### Step 5.1
- Install all dependencies\
```sudo apt install php7.4 php7.4-apcu php7.4-bcmath php7.4-cli php7.4-common php7.4-curl php7.4-gd php7.4-gmp php7.4-imagick php7.4-intl php7.4-mbstring php7.4-mysql php7.4-zip php7.4-xml```
