# NextCloud-Installation
https://www.youtube.com/watch?v=2OsNGj2n2zc \
All credits should be given to their respective creator. This is just a compilation guide for my own nextcloud installation.

## Table of content
|S\N|Content                                                                                      |
|---|---------------------------------------------------------------------------------------------|
|01 |[Nextcloud Installation](#nextcloud-installation)                                            | 
|02 |[Access Database](#step-43)                                                                  |
|03 |[php Installation](#step-5-install-apache)                                                   |
|04 |[Initialise Nextcloud](#step-7-intialising-nextcloud)                                        |
|05 |[Mount external hard drive](#additional-steps-1-for-external-drive)                          |
|06 |[Mount external hard drive with UUID](#additional-steps-2-mounting-external-drive-using-uuid)|
|07 |[Migrating Database](#additional-step-3-exporting-and-importing-mariadb)                     |
|08 |[Install Certbot](#additional-step-4-create-certbot-with-lets-encrypt)                       |
|09 |[Remove unwated linux image](#additional-step-5-remove-unwantedold-kernel)                   |
|10 |[Update missing database using occ](#additional-step-6-update-missing-database-using-occ-command)   |
|11 |[Update php version](#additional-step-7-upgrade-php-version)                                 |
|12 |[Automate backup with rsync](#additional-step-8-automatically-backup-nextlcloud)             |
|13 |[crontab job](#step-3-insert-into-crontab)                                                   |
|14 |[Setup redis-server and improve file locking](#additional-step-9-setup-redis-server-for-transactional-file-locking) |
|15 |[Setup HSTS](#additional-step-10-set-hsts-to-expire-in-6-month)                              |
|16 |[Setup Collabora Online](#additional-step-11-setup-collabora-online)                         |

## Nextcloud Installation
### Step 1: Preperation
#### Step 1 - Missing repositories:
- Adding repositories for php  
- Note: install only php7.4 or else a newer version of php will also be installed during apt-get update.  
```
sudo add-apt-repository ppa:ondrej/php -y
```
#### Step 1 - others:
- Purge php if any other version is installed  
```
sudo apt-get purge 'php*'
```
***
### Step 2 Add server details to your computer
#### Step 2.1:
- Add **server name** or **domain name** under **hostname** file  
```
sudo gedit /etc/hostname
```
#### Step 2.2:
- Add  **127.0.1.1**, **server name** or **domain name** and, **nickname**
```
sudo gedit /etc/hosts
```
***
### Step 3 Download Nextcloud
#### Step 3.1:
- Go to https://nextcloud.com/install/  
- Under **ARCHIVE FILE**, right click and copy the link from the download file  
#### Step 3.2:
- Download nextcloud  
- Where [URL] is the link you copied from **Step 3.1**  
```
wget [URL]
```
#### Step 3 - others:
- Check if wget is installed  
```
which wget
```
- If it is not installed  
```
sudo apt install wget
```
***
### Step 4 Setup database
#### Step 4.1:
- Install mariadb-server  
```
sudo apt install mariadb-server
```
- Check status after installation  
```
sudo systemctl status mariadb
```
#### Step 4.2:
- Setup database with secure installation  
```
sudo mysql_secure_installation
```
- Finish up questions prompt
#### Step 4.3:
- Access database 
```
sudo mariadb
```
```
docker exec -it <MariaDB Container> mariadb --user root -p<MariaDB Root Password>
```
- Create database  
```
CREATE DATABASE nextcloud;
```
- Check for database  
```
SHOW DATABASES;
```
- Create and grant privileges for application to access  
```
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost' IDENTIFIED BY '[Random Password]';
```
```
FLUSH PRIVILEGES;
```
- Exit Database  
```
exit
```
#### Step 4 - others:
- If mariadb does not automatically startup  
```
sudo systemctl enable mariadb
```
- To start mariadb  
```
sudo systemctl start mariadb
```
***
### Step 5 Install apache
#### Step 5.1:
- Install all dependencies  
```
sudo apt install php7.4 php7.4-apcu php7.4-bcmath php7.4-cli php7.4-common php7.4-curl php7.4-gd php7.4-gmp php7.4-imagick php7.4-intl php7.4-mbstring php7.4-mysql php7.4-zip php7.4-xml php7.4-memcached
```
#### Step 5.2:
- Check status of apache2  
```
sudo systemctl status apache2
```
#### Step 5.3:
- Enable php modules  
```
sudo phpenmod bcmath gmp imagick intl
```
#### Step 5 - others:
- If apache is missing  
```
sudo apt install apache2
```
***
### Step 6 Install nextcloud
#### Step 6.1:
- Check if unzip is installed  
```
which unzip
```
- Unzip nextcloud download  
```
unzip nextcloud-[nexcloud version].zip
```
- Removev zip file  
```
rm nextcloud-**nexcloud version**.zip
```
- Check if file is unzipped  
```ls``` or ```ls -l```
- Change ownership of the unzipped file, user should already be in.  
```
sudo chown -R www-data:www-data nextcloud/
```
- Move your nextcloud folder to the folder of your choice  
```
sudo mv nextcloud /var/www
```
- Verify the move  
```
ls -l /var/www
```
#### Step 6.2:
- Disable default apache site  
```
sudo a2dissite 000-default.conf
```
- Setup config for nextcloud  
```
sudo gedit /etc/apache2/sites-available/nextcloud.conf
```
- Enable config setup previously for nextcloud  
```
sudo a2ensite nextcloud.conf
```
- Configure php  
```
sudo gedit /etc/php/7.4/apache2/php.ini
```
- Things to change
1) memory_limit
2) max_file_uploads
3) max_execution_time
4) post_max_size
5) date.timezone = Asia/Singapore
6) opcache.enable
7) opcache.interned_strings_buffer
8) opcahce.max_accelerated_files
9) opcache.memory_consumption
10) opcache.save_comments
11) opcache.revalidate_freq=2
- Enable apache modules  
```
sudo a2enmod dir env headers mime rewrite ssl
```
- Reload apache  
```
sudo systemctl restart apache2
```
- Check for reload success  
```
sudo systemctl status apache2
```
#### Step 6 - others:
- If unzip is not installed  
```
sudo apt install unzip
```
***
### Step 7 Intialising nextcloud
#### Step 7.1:
- Go to web browser and enter **localhost**
```
localhost
```
#### Step 7.2:
- Create account
- Select data folder
- Link database to the one created previously on Step 4
***
### Additional Steps 1: For external drive
https://www.simplified.guide/linux/disk-mount
#### Step 1 - Check for drive:
```
lsblk
```
#### Step 2 - Check file system type and partition:
```
blkid /dev/sda
```
- it could be sdb, sdb1, sdc, etc... So, check this.
#### Step 3 - Make a directory to mount the drive to:
```
mkdir \media\[your user]\[A folder name]
```
#### Step 4 - Mount partition or disk:
```
sudo mount -t ext4 /dev/sda [The directory you just created]
```
#### Step 5 - Check if successfully mounted:
```
df -h
```
***
### Additional Steps 2: Mounting external drive using UUID
#### Step 1 - Check for drive UUID:
```
sudo blkid
```
#### Step 2 - create directory or folder:
```
sudo mkdir /media/[Your username]/[Your file name]
```
or anywhere else
#### Step 3 - Access fstab and modify:
```
sudo gedit /etc/fstab
```
#### Step 4 - Insert details of your harddisk:
```
UUID=[Your Harddisk UUID]  /media/[Your username]/[Your file name] ext4 defaults,user,auto  0 1
```
if your drive contains executable files, add in exec to prevent permission denied.  
```
UUID=[Your Harddisk UUID]  /media/[Your username]/[Your file name] ext4 defaults,user,auto,exec  0 1
```
***
### Additional Step 3: Exporting and Importing MariaDB
https://www.digitalocean.com/community/tutorials/how-to-import-and-export-databases-in-mysql-or-mariadb
#### Step 1 - Exporting database:
Type the following the in console where  
1) username is the username of the user that logs into database e.g. "nextcloud" from **Step 4.3**
2) database_name is the name of the database you want to export e.g. "nextcloud" from **Step 4.3**
3) data-dump.sql Can be any name given.
```
mysqldump -u username -p database_name > data-dump.sql
```
##### Step 1.1 - Check if data-dump is created:
```
head -n 5 data-dump.sql
```
#### Step 2 - Importing database:
- Enter SQL as root
```
sudo mariadb
```
- Create the database
```
CREATE DATABASE [new database name]
```
- Follow **Step 4.3** to provide all privileges again.
- Exit the database
```
exit
```
- Import the data-dump.sql where  
1) username is the username of the user that logs into database e.g. "nextcloud" from **Step 4.3**
2) basebase_name is the name of the database you want to export e.g. "nextcloud" from **Step 4.3**
3) data-dump.sql The name given previously together with it's directory. 
```
"/home/[username]/Downloads/data-dump.sql" -u username -p new_database < data-dump.sql
```
***
### Additional Step 4: Create certbot with let's encrypt
#### Step 1 - Install Certbot:
```
sudo apt update
sudo apt install certbot python3-certbot-apache
```
##### Step 1.1 - Check for installataion:
```
certbot --version
```
#### Step 2 - Enable firewall:
```
sudo ufw status
sudo ufw enable
sudo ufw allow 'Apache Full'
```
#### Step 2.1 - Check firewall status:
```
sudo ufw status
```
#### Step 3 - Get SSL certificate:
```
sudo certbot --apache -d [YourDomainName.com] -d [YourDomainName.com]
```
#### Step 3.1 - Verify Certbot:
```
sudo systemctl status certbot.timer
```
#### Step 3.2 - Perform dry run:
```
certbot renew --dry-run
```
### Other MISC
- To update grub
```
sudo update-grub
```
- chomod, u = user, g = group and, o = other
```
sudo chmod u+r
sudo chmod u+w
sudo chmod u+x
sudo chmod g+r
sudo chmod g+w
sudo chmod g+x
sudo chmod o+r
sudo chmod o+w
sudo chmod o+x
```
***
### Additional Step 5: Remove unwanted/old kernel
https://askubuntu.com/questions/345588/what-is-the-safest-way-to-clean-up-boot-partition
#### Step 1 - Check for current version:
```
uname -r
```
#### Step 2 - List all kernals images:
```
dpkg --list 'linux-image*' | grep ^ii
```
#### Step 3 - Remove unwanted linux image:
```
sudo apt-get remove linux-image-VERSION
```
#### Step 4 - Remove unwanted packages:
```
sudo apt-get autoremove
```
#### Step 5 - Update grub:
```
sudo update-grub
```
***
### Additional Step 6: Update missing database using occ command
#### Step 1 - change directory to nextcloud root directory:
```
cd /var/www/nextcloud
```
#### Step 2 - execute command:
```
sudo -u www-data php occ db:add-missing-indices
```
***
### Additional Step 7: Upgrade php version
https://www.reddit.com/r/NextCloud/comments/v5k3eq/php_update_74_81/
#### Step 1 - Install new version of php:
```
sudo apt install libapache2-mod-php8.1 php8.1 php8.1-gmp php8.1-bcmath php8.1-gd php8.1-mysql php8.1-curl php8.1-mbstring php8.1-intl php8.1-imagick php8.1-xml php8.1-zip php8.1-fpm php8.1-redis php8.1-fileinfo php8.1-apcu php8.1-memcached
```
#### Step 2 - Dismount old version of php:
```
sudo a2dismod php7.4
```
#### Step 3 - Mount new version of php:
```
sudo a2enmod php8.1
```
#### Step 4 - Modify php.ini file:
```
sudo gedit /etc/php/8.1/apache2/php.ini
```
#### Step 5 - Restart apache2:
```
sudo systemctl restart apache2
```
#### Step 6 - Remove old php-fpm:
```
sudo apt-get autoremove --purge php8.0-fpm
```
***
### Additional Step 8: Automatically Backup nextlcloud
https://docs.nextcloud.com/server/latest/admin_manual/maintenance/backup.html  
This portion automates the copy of a drive to another drive. **Source drive** refers to the drive you would like to backup and **2nd drive** refers to the drive you would like to backup to.
#### Step 1 - Install rsync:
```
sudo apt-get install rsync
```
#### Step 2 - Create a script:
- This script will automate backup on all the files you would like to backup. 
- This script assumes that your data files are seperated from your your nextcloud root
```
#!/bin/bash

folder_root="your_2nd_drive_root_path"
folder_data_path="your_2nd_drive_data_path"
folder_main_path="your_2nd_drive_nextcloud_main_path"

cd /var/www/nextcloud
sudo -u www-data php occ maintenance:mode --on

if [[ ! -e $folder_data_path ]]; then
    mkdir -p $folder_data_path
    echo "Created folder in $folder_data_path"
fi
rsync -Aavx --delete [your_source_drive_data_path] $folder_data_path
echo "Completed backing up data file to $folder_data_path"

if [[ ! -e $folder_main_path ]]; then
    mkdir -p $folder_main_path
    echo "Created folder in $folder_main_path"
fi
rsync -Aavx --delete /var/www/nextcloud/ $folder_main_path
echo "Completed backing up data file to $folder_main_path"

mysqldump --single-transaction -u [UserID] -p[Password] [Database Name] > $folder_root/nextcloud_sql.bk

now="$(date +'%Y-%m-%d')"
echo "Nextcloud backup on $now" >> $folder_root/logs.txt
sudo -u www-data php occ maintenance:mode --off
```
#### Step 3: Insert into crontab
1. https://phoenixnap.com/kb/set-up-cron-job-linux  
2. [Docker Documentation](https://docs.nextcloud.com/server/19/admin_manual/configuration_server/background_jobs_configuration.html#:~:text=Using%20the%20operating%20system%20cron,the%20Web%20server%20might%20have.&text=You%20have%20to%20replace%20the,to%20your%20current%20Nextcloud%20installation)
- Insert into crontab to automate backup schedule
##### Step 3.1 - open crontab:
```
crontab -e
```
##### Step 3.2 - Enter the following into the file:
```
a b c d e /directory/command output
```
##### Where:
|Field|Possible Values	  |Syntax	|Description                              |
|-----|-------------------|---------|-----------------------------------------|
|[a] – Minute|	0 – 59	|7 * * * * 	|The cron job is initiated every time the system clock shows 7 in the minute’s position.|
|[b] – Hour	 |  0 – 23	|0 7 * * *	|The cron job runs any time the system clock shows 7am (7pm would be coded as 19).|
|[c] – Day	 |  0 – 31	|0 0 7 * * 	|The day of the month is 7 which means that the job runs every 7th day of the month.|
|[d] – Month |	0 = none and 12 = December	|0 0 0 7 *	|The numerical month is 7 which determines that the job runs only in July.|
|[e] – Day of the Week|	0 = Sunday and 7 = Sunday	|0 0 * * 7 	|7 in the current position means that the job would only run on Sundays.|

##### Example:
|Cron Job	|Command                                                    |
|-----------|-----------------------------------------------------------|
|Run Cron Job Every Minute	|* * * * * /root/backup.sh                  |
|Run Cron Job Every 30 Minutes	|30 * * * * /root/backup.sh             |
|Run Cron Job Every Hour	|0 * * * */root/backup.sh                   |
|Run Cron Job Every Day at Midnight	|0 0 * * * /root/backup.sh          |
|Run Cron Job at 2 am Every Day	|0 2 * * * /root/backup.sh              |
|Run Cron Job Every 1st of the Month	|0 0 1 * * /root/backup.sh      |
|Run Cron Job Every 15th of the Month	|0 0 15 * * /root/backup.sh     |
|Run Cron Job on December 1st – Midnight	|0 0 0 12 * /root/backup.sh |
|Run Cron Job on Saturday at Midnight	|0 0 * * 6 /root/backup.sh      |

##### Step 3.3 - Verify crontab job:
```
crontab -l
```
***
### Additional Step 9: Setup redis-server for transactional file locking
1. https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-redis-on-ubuntu-18-04
2. 
#### Step 1 - Install redis-server:
```
sudo apt-get install redis-server
```
#### Step 2 - Modify redis.conf to let systemd manage redis-server
```
sudo gedit /etc/redis/redis.conf
```
##### Step 2.1 - Change ```no``` to ```systemd```
```
...
# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous liveness pings back to your supervisor.
supervised systemd
...
```
##### Step 2.2 - Restart redis-server
```
sudo systemctl restart redis.service
```
##### Step 2.3 - Verify redis-server status

#### Step 3 - Verify redis-server is installed correctly
```
redis-cli
```

##### Step 3.1 - ping redis-server
You should receive a reply ```pong```
```
ping
```

#### Step 4 - Modify config file
##### Step 4.1 - Open config file
```
sudo gedit /var/www/nextcloud/config/config.php
```

##### Step 4.2 - Add the following into the config file
```
...
'filelocking.enabled' => true,
'memcache.locking' => '\\OC\\Memcache\\Redis',
'redis' => array(
     'host' => 'localhost',
     'port' => 6379,
     'timeout' => 0.0,
     'password' => '', // Optional, if not defined no password will be used.
      ),
...
```
***
#### Additional Step 10: Set HSTS to expire in 6 month
##### Step 1 - Open apache config file
```
sudo gedit /etc/apache2/apache2.conf
```
##### Step 2 - Paste the following into the file
```
Header always set Strict-Transport-Security "max-age=15552000"
```
##### Step 3 - Restart apache
```
sudo systemctl restart apache2
```
***
#### Additional Step 11: Setup Collabora Online
https://www.collaboraoffice.com/code/linux-packages/  
https://docs.nextcloud.com/server/latest/admin_manual/office/example-ubuntu.html
##### Step 1 - Import signing keys
```
cd /usr/share/keyrings
```
```
sudo wget https://collaboraoffice.com/downloads/gpg/collaboraonline-release-keyring.gpg
```

##### Step 2 - Add CODE package repositories
```
sudo nano /etc/apt/sources.list.d/collaboraonline.sources
```
Copy and paste the following into the opened file:
```
Types: deb
URIs: https://www.collaboraoffice.com/repos/CollaboraOnline/CODE-deb
Suites: ./
Signed-By: /usr/share/keyrings/collaboraonline-release-keyring.gpg
```
##### Step 3 - Install packages
```
sudo apt update && sudo apt install coolwsd code-brand
```
##### Step 4 - Restart coolwsd
```
sudo systemctl restart coolwsd
```
##### Step 5 - Configuration
```
sudo coolconfig set ssl.enable false
```
```
sudo coolconfig set ssl.termination true
```
```
sudo coolconfig set storage.wopi.host nextcloud.example.com
```
Modify **nextcloud.example.com** to your new subdomain.  
The domain should be different from your nextcloud domain.
```
sudo systemctl restart coolwsd
```
```
systemctl status coolwsd
```
##### Step 6 - Configure reverseProxy
```
sudo nano /etc/apache2/sites-enabled/[Any Name].conf
```
Paste the following into the file opened above. Modify [Your Domain] to your domain.
```
<VirtualHost *:80>
    ServerName [Your Domain]
	ServerAlias www.[Your Domain]
        
	AllowEncodedSlashes NoDecode
	ProxyPreserveHost On


	# static html, js, images, etc. served from coolwsd
	# browser is the client part of Collabora Online
	ProxyPass           /browser http://127.0.0.1:9980/browser retry=0
	ProxyPassReverse    /browser http://127.0.0.1:9980/browser


	# WOPI discovery URL
	ProxyPass           /hosting/discovery http://127.0.0.1:9980/hosting/discovery retry=0
	ProxyPassReverse    /hosting/discovery http://127.0.0.1:9980/hosting/discovery


	# Capabilities
	ProxyPass           /hosting/capabilities http://127.0.0.1:9980/hosting/capabilities retry=0
	ProxyPassReverse    /hosting/capabilities http://127.0.0.1:9980/hosting/capabilities


	# Main websocket
	ProxyPassMatch      "/cool/(.*)/ws$"      ws://127.0.0.1:9980/cool/$1/ws nocanon


	# Admin Console websocket
	ProxyPass           /cool/adminws ws://127.0.0.1:9980/cool/adminws


	# Download as, Fullscreen presentation and Image upload operations
	ProxyPass           /cool http://127.0.0.1:9980/cool
	ProxyPassReverse    /cool http://127.0.0.1:9980/cool
	# Compatibility with integrations that use the /lool/convert-to endpoint
	ProxyPass           /lool http://127.0.0.1:9980/cool
	ProxyPassReverse    /lool http://127.0.0.1:9980/cool
RewriteEngine on
RewriteCond %{SERVER_NAME} =[Your Domain] [OR]
RewriteCond %{SERVER_NAME} =www.[Your Domain]
RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>
```
##### Step 7 - Enable proxy and restart apache2
```
sudo a2enmod proxy
```
```
sudo a2enmod proxy_http
```
```
sudo a2enmod proxy_balancer
```
```
sudo a2enmod lbmethod_byrequests
```
```
sudo systemctl restart apache2
```
##### Step 8 - Add collebora into your nextcloud
1. Ensure that Nextcloud Office is installed  
![Alt text](/images/NCOffice_App.jpg)  
2. Navigate to **Nextcloud** office under the **Administration**
![Alt text](/images/NC_App.jpg)  
3. Select **User your own server**
4. Enter the domain of your collabora and click on **Save**
5. You may or may not **Disable certificate verification (insecure)**
#### Additional Step 12: Enable APCu
#### Step 1 - Add "apc.enable_cli=1"
##### Step 1.1 - open apcu.ini
```
sudo gedit /etc/php/(php version)/mods-available/apcu.ini
```
##### Step 1.2 - paste the following into apcu.ini
```
apc.enable_cli=1
```
