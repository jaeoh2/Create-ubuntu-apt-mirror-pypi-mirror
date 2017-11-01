# Create-ubuntu-apt-mirror-and-pypi-mirror
How to create ubuntu 16.04 local apt-mirror and pypi-mirror server

# apt-mirror
## Install apt-mirror package
```bash
sudo apt-get update
sudo apt-get install -y apache2 apt-mirror
```
## Configure mirror lists
```bash
sudo mkdir -p /apt-mirror
sudo vi /etc/apt/mirror.list
```
* mirror.list
```
set base_path /apt-mirror
set nthreads 20
set _tilde 0

#Arm64
deb http://ports.ubunut.com/ubuntu-ports xenial main restricted universe multiverse
deb http://ports.ubuntu.com/ubuntu-ports xenial-security main restricted universe multiverse
deb http://ports.ubuntu.com/ubuntu-ports xenial-updates main restricted universe multiverse
deb http://ports.ubuntu.com/ubuntu-ports xenial-proposed main restricted universe multiverse
deb http://ports.ubuntu.com/ubuntu-ports xenial-backports main restricted universe multiverse

clean http://ports.ubuntu.com/ubuntu-ports
```
## Run apt-mirror
```
sudo apt-mirror
```
## Create symlinks for apache
```
sudo ln -s /apt-mirror/mirror/ports.ubuntu.com/ubuntu-ports/ /var/www/ubuntu
```
## Configure Apache2
* /etc/apache2/ports.conf
```
Listen 8080
```
* /etc/apache2/sites-available/000-default.conf
```
<VirtualHost *:8080>
  ServerAdmin admin@localhost
  DocumentRoot  /var/www
    <Directory /var/www/ubuntu>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
    </Directory>
  ErrorLog ${APACHE_LOG_DIR}/error.log
  LogLevel warn
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
```
sudo chown www-data:www-data /var/www/ubuntu
sudo service apache2 restart
```

## Configure Clients
* /etc/apt/sources.list
```
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial main restricted universe multiverse
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial-security main restricted universe multiverse
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial-updates main restricted universe multiverse
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial-backports main restricted universe multiverse
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial-proposed main restricted universe multiverse
  
