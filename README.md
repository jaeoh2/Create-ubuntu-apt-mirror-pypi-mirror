# Create-ubuntu-apt-mirror-pypi-mirror
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
