# Project-Linux-Server-Configuration>
This is a Linux-Server-Configuration last Project for udacity Full Stack Web Developer Nano-degree Program.
______________________________________________________________#

IP address :18.184.51.13
URl: http://18.184.51.13
SSH port : 2200
SSH key created for the grader user : 58335833 to Access use : ssh -i ~/Lcourses -p 2200 grader@18.184.51.13


______________________________________________________________#

Prerequisites:

1- you will have to install python3 , vm virtualbox with the appropriate vagrant version , gitbash terminal to work this project.
______________________________________________________________#

Vagrant/VirtualBox, how to set it up:

Install VirtualBox:
VirtualBox is the software that actually runs the virtual machine. You can download it from (virtualbox.org) , here. Install the platform package for your operating system. You do not need the extension pack or the SDK. You do not need to launch VirtualBox after installing it; Vagrant will do that.

Install Vagrant:
Vagrant is the software that configures the VM and lets you share files between your host computer and the VM's filesystem. Download it from (vagrantup.com). Install the version for your operating system.
Windows users: The Installer may ask you to grant network permissions to Vagrant or make a firewall exception. Be sure to allow this.
If Vagrant is successfully installed, you will be able to run vagrant --version
in your terminal to see the version number.
The shell prompt in your terminal may differ. Here, the $ sign is the shell prompt

_____________________________________________________________#

Software to install during the configuration :

Apache2

mod_wsgi

PostgreSQL

git

pip

virtualenv

httplib2

Python Requests

oauth2client

SQLAlchemy

Flask

libpq-dev

Psycopg2
______________________________________________________________#

Configuration steps :
Create an instance with Amazon Lightsail
Sign in to Amazon Lightsail using an Amazon Web Services account

Follow the 'Create an instance' link

Choose the 'OS Only' and 'Ubuntu 16.04 LTS' options

Choose a payment plan

Give the instance a unique name and click 'Create'

Wait for the instance to start up

Connect to the instance on a local machine
Note: While Amazon Lightsail provides a broswer-based connection method, this will no longer work once the SSH port is changed (see below). The following steps outline how to connect to the instance via the Terminal program on Mac OS machines (this can also be done on a Windows machine with a program such as PuTTY).

Download the instance's private key by navigating to the Amazon Lightsail 'Account page'

Click on 'Download default key'

A file called LightsailDefaultPrivateKey.pem or LightsailDefaultPrivateKey-YOUR-AWS-REGION.pem will be downloaded; open this in a text editor

Copy the text and put it in a file called lightrail_key.rsa in the local ~/.ssh/ directory

Run chmod 600 ~/.ssh/lightrail_key.rsa

Log in with the following command: ssh -i ~/.ssh/lightrail_key.rsa ubuntu@XX.XX.XX.XX, where XX.XX.XX.XX is the public IP address of the instance (note that Lightsail will not allow someone to log in as root; ubuntu is the default user for Lightsail instances)

Upgrade currently installed packages
Notify the system of what package updates are available by running sudo apt-get update

Download available package updates by running sudo apt-get upgrade

Configure the firewall
Start by changing the SSH port from 22 to 2200 (open up the /etc/ssh/sshd_config file, change the port number on line 5 to 2200, then restart SSH by running sudo service ssh restart; restarting SSH is a very important step!)

Check to see if the ufw (the preinstalled ubuntu firewall) is active by running sudo ufw status

Run sudo ufw default deny incoming to set the ufw firewall to block everything coming in

Run sudo ufw default allow outgoing to set the ufw firewall to allow everything outgoing

Run sudo ufw allow ssh to set the ufw firewall to allow SSH

Run sudo ufw allow 2200/tcp to allow all tcp connections for port 2200 so that SSH will work

Run sudo ufw allow www to set the ufw firewall to allow a basic HTTP server

Run sudo ufw allow 123/udp to set the ufw firewall to allow NTP

Run sudo ufw deny 22 to deny port 22 (deny this port since it is not being used for anything; it is the default port for SSH, but this virtual machine has now been configured so that SSH uses port 2200)

Run sudo ufw enable to enable the ufw firewall

Run sudo ufw status to check which ports are open and to see if the ufw is active

Update the external (Amazon Lightsail) firewall on the browser by clicking on the 'Manage' option, then the 'Networking' tab, and then changing the firewall configuration to match the internal firewall settings above (only ports 80(TCP), 123(UDP), and 2200(TCP) should be allowed; make sure to deny the default port 22)

Now, to login (on a Mac), open up the Terminal and run:

ssh -i ~/.ssh/lightrail_key.rsa -p 2200 ubuntu@XX.XX.XX.XX, where XX.XX.XX.XX is the public IP address of the instance

Note: As mentioned above, connecting to the instance through a browser now no longer works; this is because Lightsail's browser-based SSH access only works through port 22, which is now denied.

Create a new user named grader
Run sudo adduser grader

Enter in a new UNIX password (twice) when prompted

Fill out information for the new grader user

To switch to the grader user, run su - grader, and enter the password

Give grader user sudo permissions
Run sudo visudo

Search for a line that looks like this:

root ALL=(ALL:ALL) ALL

Add the following line below this one:

grader	ALL=(ALL:ALL) ALL

Save and close the visudo file

To verify that grader has sudo permissions, su as grader (run su - grader), enter the password, and run sudo -l; after entering in the password (again), a line like the following should appear, meaning grader has sudo permissions:

Matching Defaults entries for grader on
    ip-XX-XX-XX-XX.ec2.internal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User grader may run the following commands on
	ip-XX-XX-XX-XX.ec2.internal:
    (ALL : ALL) ALL
Allow grader to log in to the virtual machine
Run ssh-keygen on the local machine

Choose a file name for the key pair (such as grader_key)

Enter in a passphrase twice (two files will be generated; the second one will end in .pub)

Log in to the virtual machine

Switch to grader's home directory, and create a new directory called .ssh (run mkdir .ssh)

Run touch .ssh/authorized_keys

On the local machine, run cat ~/.ssh/insert-name-of-file.pub

Copy the contents of the file, and paste them in the .ssh/authorized_keys file on the virtual machine

Run chmod 700 .ssh on the virtual machine

Run chmod 644 .ssh/authorized_keys on the virtual machine

Make sure key-based authentication is forced (log in as grader, open the /etc/ssh/sshd_config file, and find the line that says, '# Change to no to disable tunnelled clear text passwords'; if the next line says, 'PasswordAuthentication yes', change the 'yes' to 'no'; save and exit the file; run sudo service ssh restart)

Log in as the grader using the following command:

ssh -i ~/.ssh/grader_key -p 2200 grader@XX.XX.XX.XX

Note that a pop-up window will ask for grader's password.

Configure the local timezone to UTC
Run sudo dpkg-reconfigure tzdata, and follow the instructions (UTC is under the 'None of the above' category)

Test to make sure the timezone is configured correctly by runningdate

Install and configure Apache
Run sudo apt-get install apache2 to install Apache

Check to make sure it worked by using the public IP of the Amazon Lightsail instance as as a URL in a browser; if Apache is working correctly, a page with the title 'Apache2 Ubuntu Default Page' should load

Install mod_wsgi
Install the mod_wsgi package (which is a tool that allows Apache to serve Flask applications) along with python-dev (a package with header files required when building Python extensions); use the following command:

sudo apt-get install libapache2-mod-wsgi python-dev

Make sure mod_wsgi is enabled by running sudo a2enmod wsgi

Install PostgreSQL and make sure PostgreSQL is not allowing remote connections
Install PostgreSQL by running sudo apt-get install postgresql

Open the /etc/postgresql/9.5/main/pg_hba.conf file

Make sure it looks like this (comments have been removed here for easier reading):

local   all             postgres                                peer
local   all             all                                     peer
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
Make sure Python is installed
Python should already be installed on a machine running Ubuntu 16.04. To verify, simply run python. Something like the following should appear:

Python 2.7.12 (default, Nov 19 2016, 06:48:10) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> 
Create a new PostgreSQL user named catalog with limited permissions
PostgreSQL creates a Linux user with the name postgres during installation; switch to this user by running sudo su - postgres (for security reasons, it is important to only use the postgres user for accessing the PostgreSQL software)

Connect to psql (the terminal for interacting with PostgreSQL) by running psql

Create the catalog user by running CREATE ROLE catalog WITH LOGIN;

Next, give the catalog user the ability to create databases: ALTER ROLE catalog CREATEDB;

Finally, give the catalog user a password by running \password catalog

Check to make sure the catalog user was created by running \du; a table of sorts will be returned
 
Exit psql by running \q

Switch back to the ubuntu user by running exit

Create a Linux user called catalog and a new PostgreSQL database
Create a new Linux user called catalog:

run sudo adduser catalog
enter in a new UNIX password (twice) when prompted
fill out information for catalog
Give the catalog user sudo permissions:

run sudo visudo

search for a line that looks like this: root ALL=(ALL:ALL) ALL

add the following line below this one: catalog ALL=(ALL:ALL) ALL

save and close the visudo file

to verify that catalog has sudo permissions, su as catalog (run sudo su - catalog), and run sudo -l

after entering in the UNIX password, a line like the following should appear (meaning catalog has sudo permissions):

 User catalog may run the following commands on
 	ip-XX-XX-XX-XX.ec2.internal:
     (ALL : ALL) ALL
While logged in as catalog, create a database called catalog by running createdb catalog

Run psql and then run \l to see that the new database has been created

Switch back to the ubuntu user by running exit

Install git :
Run sudo apt-get install git

Application Deployment

Start by installing the required software

$ sudo apt-get install apache2

$ sudo apt-get install libapache2-mod-wsgi python-dev

$ sudo apt-get install git

Enable mod_wsgi with the command $ sudo a2enmod wsgi and restart Apache using $ sudo service apache2 restart.

If you input the servers IP address into a web browser you'll see the Apache2 Ubuntu Default Page

We now have to create a directory for our catalog application and make the user grader the owner.

$ cd /var/www
$ sudo mkdir catalog
$ sudo chown -R grader:grader catalog
$ cd catalog

In this directory we will have our catalog.wsgi file var/www/catalog/catalog.wsgi, our virtual environment directory which we will create soon and call venv /var/www/catalog/venv, and also our application which will sit inside of another directory called catalog /var/www/catalog/catalog.

First lets start by cloning our Catalog Application repository by $ git clone [repository url] catalog
Create the .wsgi file by $ sudo nano catalog.wsgi and make sure your secret key matches with your project secret key

import sys

import logging

logging.basicConfig(stream=sys.stderr)

sys.path.insert(0, "/var/www/catalog/")

from catalog import app as application

application.secret_key = 'super_secret_key'


Rename your application.py, project.py, or whatever you called it in your catalog application folder to __init__.py by $ mv project.py __init__.py
Now lets create our virtual environment, make sure you are in /var/www/catalog.

$ sudo pip install virtualenv
$ sudo virtualenv venv
$ source venv/bin/activate
$ sudo chmod -R 777 venv

This is what your command line should look like enter image description here

While our virtual environment is activated we need to install all packages required for our Flask application. Here are some defaults but you may have more to install.

$ sudo apt-get install python-pip
$ sudo pip install flask
$ sudo pip install httplib2 oauth2client sqlalchemy psycopg2 #etc...

Now for our application to properly run we must do some tweaking to the __init__.py file.

Anywhere in the file where Python tries to open client_secrets.json or fb_client_secrets.json must be changed to its complete path ex: /var/www/catalog/catalog/client_secrets.json enter image description here

Time to configure and enable our virtual host to run the site

$ sudo nano /etc/apache2/sites-available/catalog.conf
Paste in the following:

<VirtualHost *:80>

    ServerName [Public IP]
    
    ServerAlias [Hostname]
    
    ServerAdmin admin@35.167.27.204
    
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    
    WSGIProcessGroup catalog
    
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    
    <Directory /var/www/catalog/catalog/>
    
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


If you need help finding your servers hostname go here and paste the IP address. Save and quit nano

Enable to virtual host: $ sudo a2ensite catalog.conf and DISABLE the default host $ a2dissite 000-default.conf otherwise your site will not load with the hostname.

The final step is setting up the database

$ sudo apt-get install libpq-dev python-dev
$ sudo apt-get install postgresql postgresql-contrib
$ sudo su - postgres -i
$ psql
Create a database user and password
postgres=# CREATE USER catalog WITH PASSWORD [password];
postgres=# ALTER USER catalog CREATEDB;
postgres=# CREATE DATABASE catalog with OWNER catalog;
postgres=# \c catalog
catalog=# REVOKE ALL ON SCHEMA public FROM public;
catalog=# GRANT ALL ON SCHEMA public TO catalog;
catalog=# \q
$ exit
Your command line should now be back to grader.

Now use nano again to edit your __init__.py, database_setup.py, and createitems.py files to change the database engine from sqlite://catalog.db to postgresql://username:password@localhost/catalog enter image description here

Restart your apache server $ sudo service apache2 restart and now your IP address and hostname should both load your application.

References:
https://github.com/bencam/linux-server-configuration

https://github.com/mulligan121/Udacity-Linux-Configuration/blob/master/README.md
______________________________________________________________#

Authors:
Mariam Alghamdi

Many instructions have been reached through :udacity.com - Full Stack Web Developer Nanodegree Program

