# NextCloud-Installation
### Step 1:
- Adding repositories for php
- Note: install only php7.4 or else php 8.0 will also be installed during apt-get update.\
```sudo add-apt-repository ppa:ondrej/php -y```
#### Step 1 - others:
- Purge php if any other version is installed\
```sudo apt-get purge 'php*'```
### Step 2:
- Add server details to your computer
#### Step 2.1:
- Add **server name** or **domain name** under **hostname** file
```sudo gedit /etc/hostname```
#### Step 2.2
- Add  **127.0.1.1**, **server name** or **domain name** and, **nickname**
```sudo gedit /etc/hosts```
### Step 3 
- Download Nextcloud
#### Step 3.1
- Go to https://nextcloud.com/install/
- Under **ARCHIVE FILE**, right click and get link
- 
#### Step 3 - others
- Check if wget is installed
```which wget```
- If it is not installed
```sudo apt install wget```
