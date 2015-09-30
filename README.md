# RTPA Development Environment Setup

## Prerequisites
Install Virtual Box and Vagrant 
- https://www.virtualbox.org/wiki/Downloads
- https://www.vagrantup.com/downloads.html

## Getting started 

Clone git repository 
```sh
git clone git@gitlab.insight-centre.org:egov/rtpa-devenv.git
cd rtpa-devenv
vagrant up
```

SSH into the VM
```sh
vagrant ssh
```

Setup the database 
```sh
sudo -u postgres createuser -S -D -R -P ckan_default
```
Enter **"test"**" as password when prompted 

```sh
sudo -u postgres createdb -O ckan_default ckan_default -E LATIN1
cd /usr/lib/ckan/default/src/ckan
. /usr/lib/ckan/default/bin/activate
paster db init -c /etc/ckan/default/development.ini
ln -s /usr/lib/ckan/default/src/ckan/who.ini /etc/ckan/default/who.ini
```

All done, you can now start CKAN

```sh
cd /usr/lib/ckan/default/src/ckan
paster serve /etc/ckan/default/development.ini
```

Browser to http://127.0.0.1:5000/

## Installing Wordpress

Wordpress is already installed, install mysql and initialize the db

```sh
sudo apt-get install mysql-server
```

Enter **"test"** as password when prompted, once installed login to mysql with the same password 

```sh
mysql -u root -p
```

excute following commands

```sh
CREATE DATABASE wp;
CREATE USER wp_user@localhost IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wp.* TO wp_user@localhost;
FLUSH PRIVILEGES;
exit

```
restart apache service 

```sh
sudo service apache2 restart
```
Browser to http://127.0.0.1:8080/