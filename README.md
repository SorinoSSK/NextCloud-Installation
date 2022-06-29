# NextCloud-Installation
https://www.youtube.com/watch?v=2OsNGj2n2zc\
All credits should be given to their respective creator. This is just a compilation guide for my own nextcloud installation.
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
***
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
***
### Step 5 Install apache
#### Step 5.1
- Install all dependencies\
```sudo apt install php7.4 php7.4-apcu php7.4-bcmath php7.4-cli php7.4-common php7.4-curl php7.4-gd php7.4-gmp php7.4-imagick php7.4-intl php7.4-mbstring php7.4-mysql php7.4-zip php7.4-xml```
#### Step 5.2
- Check status of apache2\
```sudo systemctl status apache2```
#### Step 5.3
- Enable php modules\
```sudo phpenmod bcmath gmp imagick intl```
#### Step 5 - others
- If apache is missing\
```sudo apt install apache2```
***
### Step 6 Install nextcloud
#### Step 6.1
- Check if unzip is installed\
```which unzip```
- Unzip nextcloud download\
```unzip nextcloud-**nexcloud version**.zip```
- Removev zip file\
```rm nextcloud-**nexcloud version**.zip```
- Check if file is unzipped\
```ls``` or ```ls -l```
- Change ownership of the unzipped file, user should already be in.\
```sudo chown -R www-data:www-data nextcloud/```
- Move your nextcloud folder to the folder of your choice\
```sudo mv nextcloud /var/www```
- Verify the move\
```ls -l /var/www```
#### 6.2
- Disable default apache site\
```sudo a2dissite 000-default.conf```
- Setup config for nextcloud\
```sudo gedit /etc/apache2/sites-available/nextcloud.conf```
- Enable config setup previously for nextcloud\
```sudo a2ensite nextcloud.conf```
- Configure php\
```sudo gedit /etc/php/7.4/apache2/php.ini```
- Things to change
1) memory_limit
2) max_file_uploads
3) max_execution_time
4) post_max_size
5) date.timezone = Asia/Singapore
6) opcache.enable
7) opcache.interned_srings_buffer
8) opcahce.max_accelerated_files
9) opcache.memory_consumption
10) opcache.save_comments
11) opcache.revalidate_freq=2
- Enable apache modules\
```sudo a2enmod dir env headers mime rewrite ssl```
- Reload apache\
```sudo systemctl restart apache2```
- Check for reload success\
```sudo systemctl status apache2```
#### Step 6 - others
- If unzip is not installed\
```sudo apt install unzip```
***
### Step 7 Intialising nextcloud
#### Step 7.1
- Go to web browser and enter **localhost**
```localhost```
#### Step 7.2
- Create account
- Select data folder
- Link database to the one created previously on Step 4
***
### Additional Steps 1: For external drive
https://www.simplified.guide/linux/disk-mount
#### Step 1 Check for drive
```lsblk```
#### Step 2 Check file system type and partition
```blkid /dev/sda```
- it could be sdb, sdb1, sdc, etc... So, check this.
#### Step 3 Make a directory to mount the drive to
```mkdir \media\**your user**\**A folder name**```
#### Step 4 Mount partition or disk
```sudo mount -t ext4 /dev/sda **The directory you just created**```
#### Step 5 Check if successfully mounted
```df -h```
### Additional Steps 2: Mounting external drive using UUID
#### Step 1 Check for drive UUID
```sudo blkid```
#### Step 2 create directory or folder
```sudo mkdir /media/**Your username**/**Your file name**```\
or anywhere else
#### Step 3 Access fstab and modify
```sudo gedit /etc/fstab```
#### Step 4 Insert details of your harddisk
```UUID=**Your Harddisk UUID**  /media/**Your username**/**Your file name** defaults,user,auto  0 1```
