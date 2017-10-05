## Linux Server

Installed Linux Ubuntu on a virtual machine and prepared it to host a web application. Updated, secured, installed/configured web and database servers.

## IP address

* 13.58.54.67

## SSH Port

* 2200

## Software Installed

1. Apache
2. PostgreSQL
3. Virtualenv

## Amazon Lightsail Setup and Firewall Configuration

1. Setup Amazon Lightsail server instance. [Lightsail](https://amazonlightsail.com/)
2. SSH into server. Update then upgrade packages with command `sudo apt-get update` and `sudo apt-get upgrade`.
3. Configured Uncomplicated Firewall(UFW). Click here for more information - [Setup Firewall](https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server).

## Server Configuration

1. Created new user accound named grader using `sudo adduser grader`.
2. Gave grader sudo using `usermod -aG sudo grader`.
3. Generate key pair on local machine using `ssh-keygen`.
4. Installed the public key on server.
	* Logged into server as grader.
	* Created ssh directory using `mkdir .ssh`.
	* Created new file in .ssh to store key using `touch .ssh/authorized_keys`.
	* Set permissions for .ssh and .ssh/authorized_keys using `chmod 700 .ssh` and `chmod 644 .ssh/authorized_keys`.
	* Ran `service ssh restart` to update.
5. SSH into server using `ssh grader@13.58.54.67 -p 2200 -i ~/.ssh/keyfile
6. Disabled root remote login by changing the `/etc/ssh/sshd_config` file.

## Apache and Install mod_wsgi

1. Install Apache using `sudo apt-get install apache2`.
2. Install mod_wsgi using `sudo apt-get install libapache2-mod-wsgi python-dev`.
3. Ran `sudo a2enmod wsgi` to enable mod_wsgi.

## PostgreSQL Configuration

1. Install postgreSQL using `sudo apt-get install postgresql`.
2. Connect as postgres using `sudo su - postgres`.
3. Ran `psql` to to bring up postgreSQL prompt. 
4. Created new user:
	*`CREATE USER catalog WITH PASSWORD 'password';`
5. Created new database with catalog as the owner:
	*`CREATE DATABASE catalog OWNER catalog;`

## Application Setup

1. Install git using `sudo apt-get install git`.
2. Changed directory using `cd /var/www`.
3. Created directory using `mkdir catalog`.
4. Created application directory inside previous directory using `mkdir catalog`.
5. Cloned project from github account.
6. Changed catalog.py file to __init__.py.
7. Updated database_setup.py and __init__.py engine from sqlite to postgreSQL.
8. Installed all required libraries.
9. Ran `sudo python database_setup.py` to create schema.
10. Ran `sudo python lotsofcategories.py` to populate database.

## Configure and Enable Virtual host

1. Create catalog.conf file in sites-available directory using `sudo nano /etc/apache2/sites-available/catalog.conf` command.
2. Added the following:
`
<VirtualHost *:80>
                ServerName 52.14.253.36
                ServerAdmin admin@52.14.253.36
                ServerAlias ec2-52-14-253-36.us-east-2.compute.amazonaws.com
                WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                <Directory /var/www/catalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/catalog/catalog/static
                <Directory /var/www/catalog/catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
`
3. Enabled host using command `sudo a2ensite catalog.conf`

## Create WSGI File

1. Create catalog.wsgi file insite www directory using `sudo nano /var/www/catalog/catalog.wsgi`.
2. Added the following:
`
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'super_secret_key'
`
3. Restart apache using `sudo service apache2 restart`.
4. Run __init__.py using `sudo python __init__.py`.

## Third-party Resources

* Secure PostgreSQL - [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)
* Deploy Flask App on Ubuntu - [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
* PostgreSQL - [PostgreSQL](https://www.postgresql.org/)

## Author

* **Nicolas Bolduc** _-Initial Work-_ [Lazarus](https://github.com/lazarus432)
