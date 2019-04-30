

# Deploying_Linux_Servers

## Description

This project is about learning a baseline installation of a Linux server and prepare it to host my catalog project web applications. Then I will secure my server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it

#The IP address and SSH port so your server can be accessed by the reviewer

IP address: 13.127.125.191 SSH Port:2200

#The complete URL to your hosted web application

[http://13.127.125.191](http://13.127.125.191/)

##A summary of software you installed and configuration changes made



### 1- Secure the Server


1.1  Update and upgrade packages

-   sudo apt-get update
-   sudo apt-get upgrade

Remove unwanted packages

-   sudo apt-get autoremove

1.2  Change SSH port from 22 to 2200

-   Run sudo nano /etc/ssh/sshd_config
-   Port 2200
-   Restart SSH - /etc/init.d/ssh restart

1.3  Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

-   sudo ufw default deny incoming
-   sudo ufw default allow outgoing
-   sudo ufw allow ssh
-   sudo ufw allow 2200/tcp
-   sudo ufw allow www
-   sudo ufw allow ntp
-   sudo ufw deny 22

### 2.  New User Grader

-   Create new user grader1
-   sudo adduser grader 
-   Grant sudo access to grader1 - Create sudoers file for grader1

-   sudo touch /etc/sudoers.d/grader1

- Add below text to the file

-  ```grader1 ALL=(ALL:ALL) ALL```

### 3. Create key pair for grader

   Run ssh-keygen on local machine
-   Install Public Key
-   Login as grader and run following commands
-   mkdir .ssh
-   touch .ssh/authorized_keys
-   Add the contents of the .pub file which was generated locally with ssh-keygen command to authorized_keys
-   chmod 700 .ssh
-   chown 644 .ssh/authorized_keys
-   Disable password based login - Edit configuration file with below command
-   sudo nano /etc/ssh/sshd_config
-   Change PasswordAuthentication from yes to no
-   PasswordAuthentication no
-   Restart ssh service
-   sudo service ssh restart

### 4. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and UDP(port 123)

-   check the firewall status using sudo ufw status.
-   block all incoming connections on all ports using sudo ufw default deny incoming.
-   allow outgoing connections on all ports using sudo ufw default allow outgoing.
-   allow incoming connection for SSH(port 2200) using sudo ufw allow 2200/tcp.
-   allow incoming connection for HTTP(port 80) using sudo ufw allow 80/tcp.
-   allow incoming connection for NTP(port 123) using sudo ufw allow 123/udp.
-   check the added rules using sudo ufw show added.
-   enable the firewall using sudo ufw enable.
-   check whether firewall is enable or not using sudo ufw status.
### 5: Install Postgresql

-   Install the Python PostgreSQL adapter psycopg: sudo apt-get install python-psycopg2
-   Install PostgreSQL:  `sudo apt-get install postgresql postgresql-contrib`
-   open database_setup.py using :  `sudo nano database_setup.py`
-   update the create_engine line:  `python engine = create_engine('postgresql://catalogdb:sillypassword@localhost/catalogdb')`
-   Update the create_engine line in project.py and lotsofmenus.py too.
-   move the project.py file to  **init**.py file : mv project.py  **init**.py
-   Change to default user postgres:  `sudo su  postgre`
-   Connect to the system:  `psql`
-   Create user catalog:  `CREATE USER catalogdbWITH PASSWORD 'sillypassword';`
-   Allow the user to create database :  `ALTER USER catalogdb CREATEDB;`  and check the roles and attributes using \du.
-   Create database using :  `CREATE DATABASE catalogdb WITH OWNER catalogdb;`
-   Connect to database using :  `\c catalogdb`
-   Revoke all the rights :  `REVOKE ALL ON SCHEMA public FROM public;`
-   Grant the access to catalog:  `GRANT ALL ON SCHEMA public TO catalogdb;`
-   exit from Postgresql :  `\q` then  `exit` from postgresql user.
-   restart postgresql:  `sudo service postgresql restart`
### 6: Install other Packages

-   `sudo apt-get install python-pip`
-   `source venv/bin/activate`
-   `pip install httplib2`
-   `pip install requests`
-   `sudo pip install --upgrade oauth2client`
-   `sudo pip install sqlalchemy`
-   `pip install Flask-SQLAlchemy`
-   `sudo pip install flask-seasurf`

### 7. Install and configure Apache to serve a Python mod_wsgi application:

-   install apache using sudo apt-get install apache2 .
-   type 13.127.125.191 (public IP address) on URL . You will see the apache ubuntu default page .
-   Install mod_wsgi using sudo apt-get install libapache2-mod-wsgi .
-   You then need to configure Apache to handle requests using the WSGI module. You’ll do this by editing the /etc/apache2/sites-enabled/000-default.conf file. This file tells Apache how to respond to requests, where to find the files for a particular site and much more.
-   add the following line at the end of the <VirtualHost *:80> block, right before the closing line: WSGIScriptAlias / /var/www/html/myapp.wsgi

### 6. Install Git

-   Install git using `sudo apt-get install git`
-   set up git using :

```
        git config --global user.name "username"
```

```
        git config --global user.email "email@domain.com"
```

-   check the configurations items using `git config --list`

### 7. Deploy Flask Application:

#### 7-1

-   WSGI (Web Server Gateway Interface) is an interface between web servers and web apps for python. Mod_wsgi is an Apache HTTP server mod that enables Apache to serve Flask applications. So the first step to install python-dev (mod-wsgi is already installed ) `sudo apt-get install python-dev`
-   To enable mod_wsgi, run `sudo a2enmod wsgi`.

#### 7-2

-   move to the `/var/www` directory:
-   Create the application directory structure using mkdir `sudo mkdir CatalogApp`
-   Move inside this directory : `cd CatalogApp`
-   Create another directory : `sudo mkdir CatalogApp`
-   move inside this directory and create two subdirectories named static and templates: `cd CatalogApp`  `sudo mkdir static templates`
-   create the **init**.py file that will contain the flask application logic. `sudo nano __init__.py`
- -   Add following logic to the file:
```
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "Hello, everyone!"
if __name__ == "__main__":
    app.run()
```
close and save the file.
#### 7-3

-   Now , we will create a virtual environment for our flask application. use pip to install virtualenv and Flask. Install pip :`sudo apt-get install python-pip`
-   Install virtualenv: `sudo pip install virtualenv`
-   Set enviornment name using : `sudo virtualenv venv`
-   Install Flask in that environment by activating the virtual environment using : `source venv/bin/activate`
-   Install Flask using : `sudo pip install Flask`
-   Run the following command to test if the installation is successful and the app is running: `sudo python __init__.py`
-   It should display "Running on `http://127.0.0.1:5000/"`. If you see this message, you have successfully configured the app.

#### 7-4

-   Run - `sudo nano /etc/apache2/sites-available/CatalogApp.conf`
-   configure the virtual host adding your Servername:
```
<VirtualHost *:80>
    ServerName 13.127.125.191
    ServerAdmin admin@13.127.125.191
    WSGIScriptAlias / /var/www/CatalogApp/CatalogApp.wsgi
    <Directory /var/www/CatalogApp/CatalogApp/>
        Order allow,deny
        Allow from all
	Options -Indexes
    </Directory>
    Alias /static /var/www/CatalogApp/CatalogApp/static
    <Directory /var/www/CatalogApp/CatalogApp/static/>
        Order allow,deny
        Allow from all
	Options -Indexes
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
## 7.5
-   Create the wsgi file using:  `cd /var/www/CatalogApp`
-   `sudo nano catalogapp.wsgi`  and add the code :

```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/CatalogApp/")

from CatalogApp import app as application
application.secret_key = 'super_secret_key'
```
### 8 : Clone the Github Repository
-  git clone https://github.com/latifa727/catalog.git catalog
-   `sudo mv catalog /var/www/CatalogApp/CatalogApp/`
-   move the catalog directory to  `/var/www/CatalogApp/CatalogApp`
### 9: Run the application:

-   Create the datbase schema:  `python database_setup.py`  `python lotsofmenus.py`
    
-   Restart Apache :  `sudo service apache2 restart`
    
-   in /var/www/CatalogApp/CatalogApp directory : execute -  `python __init__.py`

### 10

related to client_secrets.json file. You need to give absolute path to these files . change the CLIENT_ID = json.loads( open('client_secret_last.json', 'r').read())['web']['client_id'] to

open(r'/var/www/CatalogApp/CatalogApp/client_secret_ last.json', 'r').read())['web']['client_id']

## Google Authorization steps:

-   Go to [console.developer](https://console.developers.google.com/)
-   click on Credentails --> edit
-   add you hostname ([http://ec2-13-127-125-191.ap-south-1.compute.amazonaws.com](http://ec2-13-127-125-191.ap-south-1.compute.amazonaws.com) ) and public IP address ([http://13.127.125.191](http://13.127.125.191)) to Authorised JavaScript origins.
-   add hostname ([http://ec2-13-127-125-191.ap-south-1.compute.amazonaws.com/oauth2callback](http://ec2-13-127-125-191.ap-south-1.compute.amazonaws.com/oauth2callback)) to Authorised redirect URIs.
-   update the client_secret.json file too(adding hostname and public IP address).
## Resourses
1-   Github repos
2-   StackOverflow posts
3- Digitalocean Tutorials
