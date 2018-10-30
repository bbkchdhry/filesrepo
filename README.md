# Files Repo
# Bigdata Administer App

- [Introduction](#introduction)
- [Features](#features)
    - [Bigdata Services](#bigdata-services)
    - [Linux System](#linux-system)
- [Getting Started](#getting-started)
- [Prerequisites](#prerequisites)
- [Deployment](#deployment)
- [Built with](#built-with)
- [License](#license)

## Introduction

Bigdata Administer App is a python based application used to view and manage bigdata services such as 'Hadoop', 'HBase', 'Elasticsearch' and many more. It is also used to monitor cpu-usage, memory-usage, bandwidth of a particular service as well as your linux system.


## Features
We have many features for both Bigdata services and linux server.

- #### Bigdata Services
```
* Restart all services
* Stop all services
* Restart/Stop specific service such as Namenode, Datanode, Regionserver etc.
* Monitor their usages (cpu, memory, i/o...)
* Changing configuration of services
* Copy from/Copy to HDFS
```

- #### Linux system
```
* Make new directories
* Archive/Extract-Archive
* Copy multiple files
* Move multiple files
* Rename files
* Delete files/folders
* Head/Tail files
```

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

What things you need to install and how to install them

```
* Flask
* requests
* json2xml
* python-dotenv

You can install the above libraries using pip3

pip3 install -r requirements.txt
```

## Deployment

This section discuss about how to deploy this application on live system with apache2 and mod_wsgi in ubuntu server.

- Step 1: Installing apache2 and mod_wsgi in server
```
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install apache2 libapache2-mod-wsgi python3-pip
```

- Step 2: Clone bigdata service project 
```
cd /var/www/
git clone git@******:*******/******.git
```

- Step 3: Setting up Apache2 Virtual Host

```
Go to apache2 directory(cd /etc/apache2/sites-available)
Create a file named as mysite.conf inside this directory
sudo nano mysite.conf
```

Copy and paste these things below and configure as your want.

    Listen 11605
    <VirtualHost 10.10.10.110:11605>
	    ErrorLog ${APACHE_LOG_DIR}/bigdata_services_error.log
        CustomLog ${APACHE_LOG_DIR}/bigdata_services_access.log combined    
        WSGIDaemonProcess bigdata_services python-path=/var/www/bigdata_services python-home=/usr/bin/python3.5
        WSGIScriptAlias / /var/www/bigdata_services/Main/route_apps.wsgi

        <Directory /var/www/bigdata_services>
            WSGIProcessGroup bigdata_services
            WSGIApplicationGroup %{GLOBAL}
            Require all granted
        </Directory>
    </VirtualHost>

- Step 4: Configure .env file (you can take help from .env-example)
- Step 5: Enable mysite.conf in apache2

```
sudo a2ensite mysite.conf
sudo service apache2 restart
```

- Step 6: Create a db and table to store master and slave status of zookeeper and spark in postgres.
[example: db_name=bigdata, table_name=administer_master_status]

- Step 7: Run the following python file in crontab, make it run at every 2 minutes so that it can update the table with new values if changed.

```
/var/www/bigdata_services/Master_State/InsertServiceMasterStatus.py 
[Note: Copy the file in different location and run it on any one hadoop nodes]
/var/www/bigdata_services/Master_State/InsertZkAndSparkMasterStatus.py
[Note: Copy the file in different location and run it on every hadoop nodes]
```

- Step 8: Finally

Goto browser and check if 10.10.10.110:11605 site is working.

## Built With

* [Flask](http://flask.pocoo.org/docs/1.0/) - The web framework used

## License

This project is licensed under *******

