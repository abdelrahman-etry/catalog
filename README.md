### Project
A baseline installation of a Linux distribution on a virtual machine and prepare it to host web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers
- Project Link : http://18.197.69.192
- Public IP address: 18.197.69.192
- SSH port: 2200.

# Start a new Ubuntu Linux Server instance on Amazon Lightsail.
1 -Create an AWS account
2- Click Create instance button on the home page
3- Select Linux/Unix platform
4- Select OS Only and Ubuntu as blueprint
5- Select an instance plan
6- Name your instance
7- Click Create button
# Update all packages.
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```
# Change timezone to UTC and Fix language issues.
```
sudo timedatectl set-timezone UTC
sudo update-locale LANG=en_US.utf8 LANGUAGE=en_US.utf8 LC_ALL=en_US.utf8
```
# Create a new user grader with sudo access.
```
sudo adduser grader
sudo nano /etc/sudoers.d/grader 
```
Then add the following ```sh grader ALL=(ALL) ALL```
# Setup SSH keys for user grader.
- On local machine ssh-keygen Then choose the path for storing public and private keys.
- On remote machine home as user grader.
    ```
    sudo su - grader
    mkdir .ssh
    touch .ssh/authorized_keys 
    sudo chmod 700 .ssh
    sudo chmod 600 .ssh/authorized_keys 
    nano .ssh/authorized_keys  
    ```
    Then paste the contents of the public key created on the local machine.
    you can get the public key by running the command ``` cat grader.pub ```
    you can get the private key by running the command ``` cat grader ```
# Change the SSH port from 22 to 2200.
- Run ``` sudo nano /etc/ssh/sshd_config ``` to open up the configuration file.
- Change the port number from 22 to 2200.
- Save and exit by clicking on Ctrl+X then y.
- Restart the SSH using this command ``` sudo service ssh restart ``` .
# Configure the firewall.
| Command | Description |
|----|----|
| ``` sudo ufw status ``` | Check firewall status |
| ``` sudo ufw default deny incoming ``` | Set default firewall to deny all incomings |
| ``` sudo ufw default allow outgoing ``` | Set default firewall to allow all outgoings |
| ``` sudo ufw allow 2200/tcp ``` | Allow incoming TCP packets on port 2200 to allow SSH |
| ``` sudo ufw allow www ``` | Allow incoming TCP packets on port 80 to allow www |
| ``` sudo ufw allow 123/udp ``` | Allow incoming UDP packets on port 123 to allow NTP |
| ``` sudo ufw deny 22 ``` | Close port 22 |
| ``` sudo ufw enable ``` | Enable firewall |
| ``` sudo ufw status ``` | Check out current firewall status |
- Update the firewall configuration on Amazon Lightsail website under Networking. Delete default SSH port 22 and add port 80, 123, 2200

# Install Apache2 and mod-wsgi for python3 and Git.
``` sudo apt-get install apache2 libapache2-mod-wsgi-py3 git ```
# Install and configure PostgreSQL.
``` sudo apt-get install libpq-dev python3-dev ```
``` sudo apt-get install postgresql postgresql-contrib ```
``` sudo su - postgres ```
``` psql ```

Then create the user and the owner :
```sh
CREATE USER catalog WITH PASSWORD 'password';
CREATE DATABASE catalog WITH OWNER catalog;
\c catalog
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;
\q
exit
```
# change database engine in catalog project.
- ``` engine = create_engine('postgresql://catalog:password@localhost/catalog') ```

# Clone the Catalog app from GitHub and Configure it.
```sh
cd /var/www/
sudo mkdir catalog
sudo chown grader:grader catalog
git clone <your_repo_url> catalog
cd catalog
nano catalog.wsgi
```
- Then rename the project.py file to app.py with ``` mv application.py app.py ```
- Then add the following in catalog.wsgi file
```sh
import sys
sys.stdout = sys.stderr

# Add this if you'll create a virtual environment, So you need to activate it
# -------
activate_this = '/var/www/catalog/env/bin/activate_this.py'
with open(activate_this) as file_:
    exec(file_.read(), dict(__file__=activate_this))
# -------

sys.path.insert(0,"/var/www/catalog")

from app import app as application
application.secret_key = 'super_secret_key'
```
Then
```sh
sudo apt-get install python3-pip
sudo -H pip3 install virtualenv
virtualenv env
source env/bin/activate
pip3 install flask packaging oauth2client redis passlib flask-httpauth
pip3 install sqlalchemy flask-sqlalchemy psycopg2 bleach requests
```
# Edit the database path.
- Replace lines in app.py, database_setup.py, and lotsofitems.py with engine = create_engine('postgresql://catalog:password@localhost/catalog')
# Edit app.py file.
```sh
sudo nano app.py
- look for 'client_secret.json' using Ctrl+W 
- there will be 2 results
- add /var/www/catalog/ before client_secret.json
```
# Configure apache server.
``` sudo nano /etc/apache2/sites-enabled/000-default.conf ```
```sh
<VirtualHost *:80>
  ServerName <IP_Address or Domain>
  ServerAlias <DNS>
  ServerAdmin <Email>
  DocumentRoot /var/www/catalog
  WSGIDaemonProcess catalog user=grader group=grader
  WSGIScriptAlias / /var/www/catalog/catalog.wsgi

  <Directory /var/www/catalog>
    WSGIProcessGroup catalog
    WSGIApplicationGroup %{GLOBAL}
    Require all granted
  </Directory>

  ErrorLog ${APACHE_LOG_DIR}/error.log
  LogLevel warn
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
# Disable defualt Apache page.
``` sudo a2dissite 000-defualt.conf ```
``` sudo service apache2 reload ```
``` sudo service apache2 restart ```
# Add MenuItems Data.
``` python3 lotsofmenus.py ```
# Reload & Restart Apache Server.
``` sudo service apache2 reload ```
``` sudo service apache2 restart ```
# End
