# Create-ubuntu-apt-mirror-and-pypi-mirror
How to create ubuntu 16.04 local apt-mirror and pypi-mirror server

# apt-mirror
### Install apt-mirror package
```bash
sudo apt-get update
sudo apt-get install -y apache2 apt-mirror
```
### Configure mirror lists
```bash
sudo mkdir -p /apt-mirror
sudo vi /etc/apt/mirror.list
```
* mirror.list
```
set base_path /apt-mirror
set nthreads 20
set _tilde 0

#amd64
deb http://archive.ubuntu.com/ubuntu xenial main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu xenial-security main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu xenial-updates main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu xenial-proposed main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu xenial-backports main restricted universe multiverse

#Arm64
deb-arm64 http://ports.ubuntu.com/ubuntu-ports xenial main restricted universe multiverse
deb-arm64 http://ports.ubuntu.com/ubuntu-ports xenial-security main restricted universe multiverse
deb-arm64 http://ports.ubuntu.com/ubuntu-ports xenial-updates main restricted universe multiverse
deb-arm64 http://ports.ubuntu.com/ubuntu-ports xenial-proposed main restricted universe multiverse
deb-arm64 http://ports.ubuntu.com/ubuntu-ports xenial-backports main restricted universe multiverse

clean http://ports.ubuntu.com/ubuntu-ports
```
### Run apt-mirror
```
sudo apt-mirror
```
### Configure Clients
* /etc/apt/sources.list
```
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial main restricted universe multiverse
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial-security main restricted universe multiverse
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial-updates main restricted universe multiverse
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial-backports main restricted universe multiverse
deb http://IP_ADDRESS_OF_MIRROR_SERVER:8080/ubuntu/ xenial-proposed main restricted universe multiverse
```

# Pypi mirror
### Install pypi virtualenv
```
sudo apt-get update
sudo apt-get install -y apache2 python-pip python3-pip
sudo -H pip install -U virtualenv virtualenvwrapper
sudo -H pip3 install -U virtualenv virtualenvwrapper
```

### Run Pypi mirror
```
virtualenv --python=python3.5 pypi-mirror
cd pypi-mirror
bin/pip install -r https://bitbucket.org/pypa/bandersnatch/raw/stable/requirements.txt
```
* Run bandersnatch mirror - it will create an empty configuration file for you in /etc/bandersnatch.conf.
* Review /etc/bandersnatch.conf and adapt to your needs.
* Run bandersnatch mirror again. It will populate your mirror with the current status of all PyPI packages - roughly 500GiB (2017-02-12). Expect this to grow substantially over time.
* Run bandersnatch mirror regularly to update your mirror with any intermediate changes.


### Configure Clients
```
pip install --index-url=http://IP_ADDRESS_OF_MIRROR_SERVER:8080/pypi sompackage
```

# Apache2
### Create symlinks for apache
```
sudo ln -s /apt-mirror/mirror/ports.ubuntu.com/ubuntu-ports/ /var/www/ubuntu
sudo ln -s /pypi-mirror/web/ /var/www/pypi
```
### Configure Apache2
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
    
    <Directory /var/www/pypi>
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
sudo chown www-data:www-data /var/www/pypi

sudo service apache2 restart
```
