# Configuring Linux Web Server
The project explains how to a take a baseline installation of a linux server and prepare it to host a web applications.   At the end of the project you will secure your server from a number of attack vectors, install and configure a database server,   and delpoy an existing web applications onto it. The steps should progress from acquiring and updating the server dependencies,  user managment(creating, permission, log in via ssh) to configuring the firewall rules followed by application deployment and configuration and finally, database configuration.

### Tasks
1. Launch your Virtual Machine with your Udacity account
2. Follow the instructions provided to SSH into your server
3. Create a new user named grader
4. Give the grader the permission to sudo
5. Update all currently installed packages
6. Change the SSH port from 22 to 2200
7. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
8. Configure the local timezone to UTC
9. Install and configure Apache to serve a Python mod_wsgi application
10. Install and configure PostgreSQL:
        Do not allow remote connections
        Create a new user named catalog that has limited permissions to your catalog application database
11. Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!

### First generate priavte key
In your local machine, generate key pair with
```ssh-keygen```
look view the public key and copy it with cat .ssh/grader.pub. Post the public key inside this file.
copy the key end with ```.pub```
Save the file


### Launch your Virtual Machine
1. Sign up to aws account. 
2. Create a lightsail instance
3. Choose an ubuntu image
4. Choose your instance plan
5. Give your instance a host name- say ubuntu
6. Wait for it to start
7. Then finally add key by uploading the public key created in your local machine

### Development environment
Public IP: 3.11.231.124

### Connect remotely securely

1. In your local machine open the terminal
2. Enter this line oof code
```ssh ubuntu@3.11.231.124 -p 22 -i ~/.ssh/privateKeyName```


### Create a new user named grader
1. ```sudo adduser grader```
2. Give a password to user when prompted to do so. 
3. ```sudo vim /etc/sudoers```
4. ```sudo touch /etc/sudoers.d/grader```
5. **Give Sudo Access.** ```sudo vim /etc/sudoers.d/grader```, type in grader ALL=(ALL:ALL) ALL, save and quit

### Set ssh login using keys
1. Generate keys for the user grader on local machine using ssh-keygen ; then save the private key in ~/.ssh on local machine
2. Deploy public key on developement enviroment

On you virtual machine:
```
su - grader
mkdir .ssh
touch .ssh/authorized_keys
sudo vim .ssh/authorized_keys
```

Copy the public key generated on your local machine to this file and save```
```
chmod 700 .ssh
$ chmod 644 .ssh/authorized_keys
```
3. Reload SSH using ```sudo service ssh restart```

4. You can use ssh to login with the new user you created
```ssh grader@52.24.125.52 -i <privateKeyName>```

### Update all currently installed packages
```
sudo apt-get update
sudo apt-get upgrade
```

### Change the SSH port from 22 to 2200
1. Use ```sudo vim /etc/ssh/sshd_config``` and then change Port 22 to Port 2200 , save & quit.
2. Reload SSH using ```sudo service ssh restart```


## Secure your server
Configure your firewall ()**ufw**} earlier on

```
sudo ufw status
sudo ufw default deny incoming
sudo ufw default allow outgoing
ufw enable status
status inactive
```
### Configure the Uncomplicated Firewall (UFW)
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

```
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable 
```

### Add custom firewall configuration like so:
  1. Open amazon lightsail panel and, select firewall. 
  2. Add _custom_ port 2200, then select tcp protocol and finally save. 
  3. Now go to `/etc/ssh/sshd_config` file and change port option from 22 to 2200.  
  4. Make to configure firewall to allow port 2200 if not already done with `sudo ufw allow 2200/tcp`. 
  5. Restart SSH service with `sudo systemctl restart ssh`. 
  6. Exit and connect through ssh with the new ssh port 2200.  


### Configure the local timezone to UTC 
```sudo dpkg-reconfigure tzdata``` 


### Install and configure Apache to serve a Python mod_wsgi application
1. Install Apache ```sudo apt-get install apache2```
2. Install python-setuptools ```sudo apt-get install python-setuptools```
3 install mod-wsgi ```sudo apt-get install libapache2-mod-wsgi```
3. Restart Apache ```sudo service apache2 restart```

### Install and configure PostgreSQL
1. Install PostgreSQL ```sudo apt-get install postgresql```
2. Check if no remote connections are allowed ```sudo vim /etc/postgresql/9.3/main/pg_hba.conf```
3. Login as user "postgres" sudo su - postgres
4. Get into postgreSQL shell psql
5. Create a new database named catalog and create a new user named catalog in postgreSQL shell
	```
	createuser -P <username>. and set password
	createdb <username> 
	```


7. Quit postgreSQL ```postgres=# \q```

8. Exit from user "postgres"
```
exit
```

### Install git, clone and setup your Catalog App project.

1 Install Git using sudo apt-get install git.

2 Use cd /var/www to move to the /var/www directory.

3 Create the application directory ```sudo mkdir Datafrica```.

4 Move inside this directory using ```cd Datafrica```.

5 Clone and renmae the Catalog App to the virtual machine ```git clone https://github.com/kongling893/Item_Catalog_UDACITY.git FlaskApp```.

6. Move to the inner FlaskApp directory using ```cd FlaskApp```.

7. Rename application.py to  ``` __init__.py``` using ```sudo mv  __init__.py```.

8. Edit database_setup.py, application.py and functions_helper.py and change engine = create_engine('sqlite:///catalogwithusers.db') to engine = create_engine('postgresql://catalog:password@localhost/catalog').

9. Install pip ```sudo apt-get install python3-pip```.

10. Use pip to install dependencies ```pip freeze >  requirements.txt /var/www/Datafrica/FlaskApp```.

11. Install psycopg2 ```sudo apt-get -qqy install postgresql python-psycopg2```.

12. Create database schema python3 database_setup.py. It would ask for missing libraries such as sqlalchemy.


### Configure and Enable a New Virtual Host
1. Create FlaskApp.conf to edit: sudo nano /etc/apache2/sites-available/FlaskApp.conf

2. Add the following lines of code to the file to configure the virtual host.


```
<VirtualHost *:80>
        ServerName www.datafrica.com
        ServerAdmin kofuafor@dgmail.com

        <Directory /var/www/Datafrica/FlaskApp/>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/Datafrica/FlaskApp/static
        <Directory /var/www/Datafrica/FlaskApp/static/>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        WSGIScriptAlias / /var/www/Datafrica/flaskapp.wsgi
</VirtualHost>
```

3. Enable the virtual host with the following command: ```sudo a2ensite FlaskApp```

### Create the .wsgi File
1. Create the .wsgi File under /var/www/Datafrica:

```
cd /var/www/Datafrica
sudo nano flaskapp.wsgi
```

2. Add the following lines of code to the flaskapp.wsgi file:

```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'
```
Existing code.
```
def application(environ, start_response):
    status = '200 OK'
    output = 'Hello World!'

    response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

    return [output]
  ```

### Restart Apache
1. Restart Apache sudo service apache2 restart
2. Next you want to create a new host record in your hosts file (/etc/hosts).
```127.0.0.1 my.flaskapp































### User management
#### Create user with access
Create a new user,
Install finger. 
`sudo apt-get install finger`   


Create user. 
`sudo adduser grader`. 
Give a password to user when prompted to do so. 
Log in as grader with given password. 

Make these changes to `sudo nano /etc/ssh/sshd_config`:
`PermitRootLogin prohibit-password` to `PermitRootLogin yes` 
`PasswordAuthentication no` to `PasswordAuthentication yes`

### Give Sudo Access. 
Do this in the main admin account - ubuntu@IP address. 
Create a new file inside - sudoers.d with the name of the new user in this case 'grader'. 
Open this file with visudo,
Create a file name grader with how to.  [here](https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file-on-ubuntu-and-centos):
`sudo visudo -f /etc/sudoers.d/grader`. 

Add this line of code inside this file. 
`grader ALL = (ALL : ALL) ALL`. 
Save and quit  with `control + o`, then press enter, and then `control + x`.

Create public key pairs in your local amchine and upload the public key to amazon
Now login as the new user ie grader. 
with `ssh <username>@Public IP Address -p 2200 ~/.ssh/user-private-key-name`.


### Finally force key based authentication
With `sudo vim /etc/ssh/sshd_config`This is the server listening for all of your ssh connections. 


### Generate key pairs with the commmand
Use this command to generate key pairs. 
`ssh-keygen`. 
* Once done, use the `cat` command to copy the public key ending in `.pub`. 
* Upload this key to your amazon account as one of the keys to be used for access.  
* Alternatively, first make sure to lohin as the grader,
  * Then create a directory .ssh with `mkdir .ssh` within the home directory
  * Then create a new file within this directory called authorized_keys `sudo touch ~/.ssh/authorized_keys`. 
  * Back in your local machine, read out the content of the key pair generated with the extension `.pub`. 
  * Copy it, and back in your server, edit the authorized keys file by adding this key. 
  * go into `sudo /etc/.ssh/sshd_config` and change `PasswordAuthentication no`. 
  
Then restart the service with sudo ssh service restart`. 

Then login with `ssh <username>@ip-address -p 2200 -i ~/.ssh/your_private_key_name`.  
login as grader. 
Create a directory `mkdir .ssh`. 
In your loacl machine, look view the public key and copy it with `cat .ssh/grader.pub`. 
Post the public key inside this file. 
	
Set up some specific file permissions on .ssh and authorized_keys with

`chmod 700 .ssh`
`chmod 644 .ssh/authorized_keys`

set up a change of passwd when next the users login
`sudo passwd -e grader`
When when next the user log in, they will be forced to change thier password.

## Install apache2:
## Install and configure the Apache Web Server on an Ubuntu
```
sudo apt-get update. 
sudo apt install apache2
sudo service apache2 start
sudo service apache2 reload
sudo systemctl status apache2
sudo tail /var/log/apache2/error.log


Then visit your client to check if you have a working server by typing. 
your_domain_name_or_ip_address:80
This is the default web page for this server. 
```


### Prelimnary configuration of apache to display "Hello World!"
* The first step in this process is to install. 
`sudo apt install libapache2-mod-wsgi-py3`,  



### Configure the virtaul host
You then need to configure Apache to handle requests using the WSGI module. You will do this by editing:   
 Now **edit** the `/etc/apache2/sites-enabled/000-default.conf` file., This file tells Apache how to respond to requests.  where to find the files for a particular site and much more. 

 Adding the following line. 
 `WSGIScriptAlias / /var/www/html/myapp.wsgi`  at the end of the. 
 
 ```
 <VirtualHost *:80>
	# The ServerName directive sets the request scheme, hostname and port that
	# the server uses to identify itself. This is used when creating
	# redirection URLs. In the context of virtual hosts, the ServerName
	# specifies what hostname must appear in the request's Host: header to
	# match this virtual host. For the default virtual host (this file) this
	# value is not decisive as it is used as a last resort host regardless.
	# However, you must set it for any further virtual host explicitly.
	#ServerName www.example.com

	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/html

	# Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
	# error, crit, alert, emerg.
	# It is also possible to configure the loglevel for particular
	# modules, e.g.
	#LogLevel info ssl:warn

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined

	# For most configuration files from conf-available/, which are
	# enabled or disabled at a global level, it is possible to
	# include a line for only one particular virtual host. For example the
	# following line enables the CGI configuration for this host only
	# after it has been globally disabled with "a2disconf".
	#Include conf-available/serve-cgi-bin.conf
	WSGIScriptAlias / /var/www/html/myapp.wsgi
</VirtualHost>
```

### Create your first wsgi application
First install wsgi module with:  

 Then **_create_** the `/var/www/html/myapp.wsgi` file using the command `sudo vim /var/www/html/myapp.wsgi`. 
 This is a python application even though it ends with wsgi.  
 
 Add this code to file and change the output to. 
 'Hello World!'    
 
 ```
 def application(environ, start_response):
    status = '200 OK'
    output = 'Hello World!'

    response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

    return [output]
    
 ```
 Finally, restart Apache with the `sudo apache2ctl restart`. 

Refresh your browser and you should see your app runing Hello World!   


## Install
```
First create a new directory:
mkdir datafrica /var/www/
cd /var/www/datafrica
Create setup file:
sudo touch setup.py
Confugure setup.py file:

from setuptools import setup

setup(
        name='catalog',
        version='0.1.0',
        packages=['catalog'],
        include_package_data=True,
        install_requires=[
            'flask', sqlalchemy,
        ],

 )


## Permissions
Assign ownership of the directory:
The permissions of your web roots should be correct and made as shown. 

Then check permissions with ls -al with inside the directory or add path to the directory.
Your may change ownership to root if you want to with change owner - chown:
sudo chown <newname> [name_of_entry]
In this case using path to entry:
sudo chown root /var/www/datafrica

You may also change the group name with change group - chgrp:
sudo chgrp root /var/www/datafrica
or
sudo chgrp -R $USER /var/www/datafrica
This will change it to whoever the user is that is logged in.

You may add permissions with:
sudo chmod -R 755 /var/www/datafrica

$ git clone https://github.com/k0f1/catalog.git
$ cd catalog
Remove all files ending with .pyc. 
The application.py file has been modified and no longer a astart up file but a views file.
```

Install pip;
`apt install python3-pip'

## Create a virtualenv and activate it:
```
$ python3 -m venv venv
$ . venv/bin/activate
Install catalog:
pip3 install -e .
```

Then install Flask inside the activated environment of the catalog
```
pip3 install flask
pip3 install sqlalchemy
pip install psycopg2-binary
If there is persmission error:
This line of code, will give ubuntu as the user the permission neccessary to install it.
sudo chownn -R $USER . (inside venv folder)

```

```
Run

$ export FLASK_APP=catalog
$ export FLASK_ENV=development
$ flask run 
App run successful. No __init__.py file yet
```

Then deactivate venv with just. 
`Deactivate`    

Then create  __init__.py. (Make sure to delete all .pyc files first, otherwise things would most likely break)
Use `mv application.py __init__.py`

 Python does not want modules in packages to be the startup file. 
 So create a new file called `setup.py` inside the root directory datafrica. 
 

```
# Filename:  __init__.py
# encoding: utf-8

import os
import sys


from flask import Flask
from flask import render_template

app = Flask('catalog')
app.config.from_mapping(
        # a default secret that should be overridden by instance config
        SECRET_KEY="dev",
        UPLOAD_FOLDER='/datafrica/catalog/uploads/'

)

# Lets define the project root
PROJECT_ROOT = os.path.dirname(os.path.abspath(__file__))

# Explictly import modules containing routes
from catalog import application

# make url_for('index') == url_for('application.index')
# in another app, you might define a separate main index here with
# app.route, while giving the application blueprint a url_prefix, but for
# the tutorial the application will be the main index
app.add_url_rule("/", endpoint="index")
```
**_Make sure to delete all .pyc files first, otherwise things would most likely break_**



If you cannot pip install these packages, then you you have a permission problem which you can solve as shown below

* Still inside the cloned `cd /var/www/catalog`. 
* Next assign ownership of the project directory with user environment variable. 

   `sudo chown -R $USER /var/www/catalog`. 
   `sudo chgrp -R $USER /var/www/catalog`

* Next give permissions to the user. 

  `sudo chmod -R 775 /var/www/catalog`. 
  
  
### Setting up posgresql  
First install as ubuntu global user:
```
sudo apt-get update && sudo apt-get upgrade
sudo apt install postgresql. 
Inside the venv, pip install psycopg2 like so:
pip install psycopg2-binary.
```

### Switch over to postgresql object with:
```
sudo -i -u postgres. 
Use this command to start the Postgres interactive shell and to switch user to Postgres:  
You must be already logged in as a sudo user at the.  

* Create database user with:
postgresql@IP Adress: createruser -P <username>. 
Give password as password on prompt. 

* Create database with the same name as username. 
postgresql@IP Adress: createrdb <username> 

Delete a user
dropuser <username>
```
* Edit
```
application.py
database_setup.py
lotsofitems.py
functions_helper.py 
Change engine = create_engine('sqlite:///database.db') to.  engine = create_engine('postgresql://catalog:password@localhost/catalog')

Then run:
python3 database_setup.py 
To populate the database run: 
python3 lotsofitems.py
```

 Connect to db. 
```
psql postgresql://catalog:password@localhost/catalog
```


### Customise the Apache to hand-off certain requests to myapp
#### Setting Up Virtual Hosts
* Configure Apache to handle requests using the WSGI module.  But instead of by editing the file `/etc/apache2/sites-enabled/000-default.conf`.  
Lets create a new file with:

sudo vim  /etc/apache2/sites-enabled/catalog.conf`. 

The `/etc/apache2/sites-enabled/catalog.conf` should now look like this:

 ```
<VirtualHost *:80>
        ServerName www.datafrica.com
        ServerAdmin kofuafor@datafrica.com
        DocumentRoot /var/www/datafrica/
       
        # python-home set to the location of an empty python virtual environment. 
        WSGIDaemonProcess catalog python-home=/usr/local/venv/empty user=ubuntu group=ubuntu processes=2 threads=5 python-path=/var/www/datafrica/catalog/
     
        WSGIProcessGroup catalog

        <Directory /var/www/datafrica/catalog/>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/datafrica/catalog/static
        <Directory /var/www/datafrica/catalog/static/>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        WSGIScriptAlias / /var/www/datafrica/catalog.wsgi
</VirtualHost>

 ```
 
Enable the virtual host with the following command:
```
Reload Apache with
`sudo service apache2 reload`. 
```

#### Setup my catalog.wsgi file:
Add this code to it `catalog.wsgi`. 

The final wsgi app look something like this `catalog.wsgi`:
We first must import the os module    

```
#!/usr/bin/python3

import os
import sys
import logging


logging.basicConfig(stream=sys.stderr)

# Path to project
sys.path.insert(0,"/var/www/datafrica")

from catalog import app as application
application.root_path = '/var/www/datafrica/catalog/'

application.secret_key = 'os.urandom(24)'

def application(environ, start_response):
    status = '200 OK'
    output = 'Hello World!'

    response_headers = [('Content-type', 'text/plain'),
                        ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

    return [output]


```

Let’s enable the file with the a2ensite tool:
```
sudo a2ensite catalog.conf
```

Disable the default site defined in 000-default.conf:
```
sudo a2dissite 000-default.conf
```
    
Next, let’s test for configuration errors:
```
sudo apache2ctl configtest
```

You should see the following output:
`syntax ok`

Error:
```
sudo apache2ctl configtest

AH00526: Syntax error on line 7 of /etc/apache2/sites-enabled/catalog.conf:

Invalid command 'WSGIDaemonProcess', perhaps misspelled or defined by a module not included in the server configuration

Action 'configtest' failed.

The Apache error log may have more information.

```

## Trouble shooting the WSGIDeamonProcess:
```
sudo apt install python3-dev python3-pip libapache2-mod-wsgi
Then enable mod_wsgi:
sudo a2enmod wsgi
This lead to a new error:
ERROR: Module wsgi does not exist!

$ pip3 install mod_wsgi
Lead to a new error message thus:'missing Apache httpd server packages.' % APXS)
    RuntimeError: The 'apxs' command appears not to be installed or is not executable. Please check the list of prerequisites in the documentation for this package and install any missing Apache httpd server packages.
    
To correct the above, run:
pip install -v mod_wsgi-httpd
After completion of the above installation 

Error- mod_wsgi could not be installed, permission denied:
I needed to install this inside the venv but the permission was not correct.
venv ownership is root currently.
I changed this `sudo chown -R $USER:$USER venv`
Now when I activate venv and use pip install mod_wsgi`
it worked.
```

```
Enable the virtual host by using the command:
sudo a2ensite catalog
Type the following command for restarting the apache:
service apache2 reload
service apache2 restart

And verify that wsgi module is loaded: 
sudo apache2ctl -M|grep -i wsgi
```
```
Error message:
$ sudo apt install libapache2-mod-wsgi-py3
Reading package lists... Done
Building dependency tree
Reading state information... Done
E: Unable to locate package libapache2-mod-wsgi-py3

Solution:
sudo add-apt-repository universe
sudo add-apt-repository multiverse
sudo apt update
Thereafter:
$ sudo apt install libapache2-mod-wsgi-py3 
Successful

Now when I run `sudo apache2ctl configtest`, I get syntax OK finally.

Key takeaway:
Install `libapcahe2-mod-wsgi-py3` as ubuntu user and make sure the ownership of your project directory is matched by this user.
Make sure you are in venv and it is activated, when you `pip install mod_wsgi`
```
## Trouble shooting missing flask module
Missing flask module
```
Traceback (most recent call last):
  File "/var/www/datafrica/catalog.wsgi", line 13, in <module>
    from catalog import app as application
  File "/var/www/datafrica/catalog/__init__.py", line 8, in <module>
    from flask import Flask
ModuleNotFoundError: No module named 'flask'

Debug (missing global packages)
Start python in REPL mode (just type in python3 in the command line to enter python terminal)

Try importing the module there. import flask for example.
python3
Python 3.6.9 (default, Apr 18 2020, 01:56:04) 
[GCC 8.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import flask
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'flask'

If you get the same ImportError the module is not installed, therefore your application cannot use it

Solution 
Install the missing modules with sudo and pip. 
sudo pip3 install flask.
This successfully resolved the issue
```
## Test if my flask app import in catalog.wsgi
```
 python3
Python 3.6.9 (default, Apr 18 2020, 01:56:04) 
[GCC 8.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from catalog import app as application
>>> print (application)
<Flask 'catalog'>
>>> 
```

## Using the "unattended-upgrades" package
The purpose of unattended-upgrades is to keep the computer. 
current with the latest security (and others) updates automatically. 
Install the unattended-upgrades package:  
```
sudo apt-get install unattended-upgrades
To enable it, do:
sudo dpkg-reconfigure --priority=low unattended-upgrades
```



## Usage
Configuring a linux web server requires a number of environmental variables for runtime configuration as shown above.  The following examples demonstrate how to run it manually from the command line in the appriopriate directory
example code. 

## Known Bugs

## Networking
Attach your instance to a static IP address. 
Create A records with example.com inside your amazon lighsail instance.  Also create subdomains starting with `www` subdomain, record. 

Go to your DNS host provider and add the Nameservers provided in the lightsail instance.  

These mappings to show you are the owner of the domain name. 



## Contributing
We encourgae contributions to Configuring linux web server. Please checkout  [guidelines]() on how to proceed. I wish to acknowledge the use of knowledge materials from Udacity videos, digitalocean, ubuntu ask questions and stackoverflow in this README.

## Licensing
Configuring a linux web server is licenced under [GNU AGPLv3](https://choosealicense.com/licenses/agpl-3.0/). 
