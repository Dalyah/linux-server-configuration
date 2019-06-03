## Description
The main idea behind this project is to lunch your web project on a Linux server to be accessed by the users. In this repository I configured Amazon LightSail Ubuntu Linux server and deployed the item catalog project on it.
## Server Details
Server IP: 3.121.76.187
Web URL: 3.121.76.187.xip.io

To connect to the server using SSH:
 username: grader
 password: grader
 private_key: "UNDER SUBMITTION NOTES"

## Steps to configure the server and deploy Item Catalog Project
1. create and lunch Amazon Lightsail instance
2. change the default port 22 to 2200
3. configure the timezone to UTC
4. create "grader" account with sudo privileges
5. implement RSA authentication (Set up ssh-key on a local machine and copy the public key to the server).
6. Install required packages to deploy a python2.7 project:
  '''
  $ sudo apt-get install apache2
  $ sudo apt-get install libapache2-mod-wsgi python-dev
  $ sudo apt-get install git
  '''
7. Git clone the project in (var/www/catalogApp/catalogApp) from:
https://github.com/Dalyah/catalogApp

*The repository has already included the necessary changes to __init__.py and client_secrets.json files needed to deploy the project on the server*

8. Create catalogapp.wsgi in (/var/www/catalogApp) and add the below code:
'''
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalogApp/")

from catalog import app as application
application.secret_key = 'udacity'
'''

9. Create the virtual environment and install Flask
'''
$ sudo apt-get install python-pip
$ sudo pip install virtualenv
$ sudo virtualenv venv
$ source venv/bin/activate
$ sudo chmod -R 777 venv
$ sudo pip install Flask
$ sudo pip install httplib2 oauth2client sqlalchemy psycopg2 requests
'''

10. Add the below code to (etc/apache2/sites-available/catalogApp.conf):
'''
<VirtualHost * :80>
    ServerName [YOUR PUBLIC IP ADDRESS]
    ServerAlias [YOUR AMAZON LIGHTSAIL HOST NAME]
    ServerAdmin admin@[YOUR PUBLIC IP ADDRESS]
    WSGIDaemonProcess catalogApp python-path=/var/www/catalogApp:/var/www/catalogApp/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalogApp
    WSGIScriptAlias / /var/www/catalog/catalogapp.wsgi
    <Directory /var/www/catalogApp/catalogApp/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalogApp/catalogApp/static
    <Directory /var/www/catalogApp/catalogApp/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
'''
11. Setup the database:
'''
$ sudo apt-get install libpq-dev python-dev
$ sudo apt-get install postgresql postgresql-contrib
$ sudo -u postgres -i
$ psql
$ CREATE USER catalog WITH PASSWORD [your password];
$ ALTER USER catalog CREATEDB;
$ CREATE DATABASE catalog WITH OWNER catalog;
'''

12. Execute database setup python code
'''
$ sudo python fillDB.py
'''
13. $ sudo service apache2 restart
$ sudo a2ensite catalogApp
14. Enter the IP address o the browser and you should see the application running :)

## Resources
1. https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1604
2. https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
3. https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04
4. https://github.com/redateur13/Linux-Server-Configuration/blob/master/README.md
5. https://github.com/juvers/Linux-Configuration
6. https://github.com/juvers/Linux-Configuration
