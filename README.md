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
4. Check with the systemd init system to make sure the service is running by typing: ```sudo systemctl status apache2```
4. Access the default Apache landing page to confirm that the software is running properly through your public IP address: http://your_server_ip

### Install and configure PostgreSQL
1. Install PostgreSQL ```sudo apt-get install postgresql```
2. Check if no remote connections are allowed ```sudo vim /etc/postgresql/9.3/main/pg_hba.conf``` see version with ```psql --version```
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

3 Create the application directory ```sudo mkdir datafrica```.

4 Move inside this directory using ```cd datafrica```.

5 Clone and renmae the Catalog App to the virtual machine ```git clone https://github.com/k0f1/catalog.git datafrica```.

6. Move to the inner flaskApp directory using ```cd datafrica```.
	* Assign ownership of the directory: ```sudo chown -R grader:grader /var/www/datafrica/datafrica```
	* Assign permissions to the web root: ```sudo chmod -R 775 /var/www/datafrica/datafrica```

7. Then rename application.py to __init__.py. (Make sure to delete all .pyc files first, otherwise 	things would most likely break) to define the datafrica as a package.


	 Python does not want modules in packages to be the startup file. 
	 So create a new file called `setup.py` inside the root directory datafrica. 
	 
	 ## Create setup.py file

	Inside  /var/www/datafrica
	Create setup file:
	sudo touch setup.py
	Confugure setup.py file:
	
	```
	from setuptools import setup

	setup(
		name='datafrica',
		version='0.1.0',
		packages=['datafrica'],
		include_package_data=True,
		install_requires=['Flask']

	 )
	 ```

	Inside datafrica directory: var/www/datafrica/
	Run source venv/bin/activate:
	then Run
	```

	1. $ export FLASK_ENV=datafrica
	2. pip3 install -e .
	```
	
	**Installing setup.py** 
	
	To install your application (ideally into a virtualenv) just run the setup.py script with the 	           install parameter.  It will install your application into the virtualenv’s site-packages folder and 	       also download and install all dependencies:
	
	```(venv) $ python setup.py install```

	**Setting Up Gunicorn and Supervisor**
	When you run the server with flask run, you are using a web server that comes with Flask. This 		server isn't a good choice to use for a production server because it wasn't built with performance         and robustness in mind. Instead of the Flask development server, for this deployment I will use    	      gunicorn, which is also a pure Python web server, but unlike Flask's, it is a robust production    	 server.

	To start datafrica under gunicorn you can use the following command:
	```
	cd into /var/www/datafrica
	source venv/bin/activate
	
	Run
	flask run -b localhost:8000 -w 4 datafrica:app

	
	**Create views.py file**.
	```
	from datafrica import app

	@app.route('/')
	@app.route('/index')
	def showIndex():
	    return render_template("index.html")

	```
	
	

8. Edit database_setup.py, application.py and functions_helper.py and change engine = create_engine('sqlite:///catalogwithusers.db') to engine = create_engine('postgresql://flaspApp:password@localhost/flaskApp').

9. Install pip ```sudo apt-get install python3-pip```.

10. Use pip to install dependencies ```pip freeze >  requirements.txt /var/www/datafrica/datafrica```.

11. Install psycopg2 ```sudo apt-get -qqy install postgresql python-psycopg2```.

12. Create database schema python3 database_setup.py. It would ask for missing libraries such as sqlalchemy.




### Configure and Enable a New Virtual Host
1. Create FlaskApp.conf to edit: sudo nano /etc/apache2/sites-available/datafrica.conf

2. Add the following lines of code to the file to configure the virtual host.


```
<VirtualHost *:80>
        ServerName www.datafrica.com
        ServerAdmin kofuafor@dgmail.com

        <Directory /var/www/datafrica/datafrica/>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/datafrica/datafrica/static
        <Directory /var/www/datafrica/datafrica/static/>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        WSGIScriptAlias / /var/www/datafrica/datafrica.wsgi
</VirtualHost>
```

3. Enable the virtual host with the following command: ```sudo a2ensite datafrica```

### Create the .wsgi File
1. Create the .wsgi File under /var/www/datafrica:

```
cd /var/www/datafrica
sudo nano datafrica.wsgi
```

2. Add the following lines of code to the flaskapp.wsgi file:

```
#!/usr/bin/python

import os
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/datafrica/datafrica/")

from datafrica import app as application
application.secret_key = 'Add your secret key'
_environ = os.environ.get("FLASK_ENV", default="true")


 def application(environ, start_response):
    status = '200 OK'
    output = 'Hello World!'

    response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

    return [output]

if '__name__' == '__main__':
	application(_environ, start_response(status, response_headers))


### Restart Apache
1. Restart Apache sudo service apache2 restart
2. Next you want to create a new host record in your hosts file (/etc/hosts).
27.0.0.1 my.datafrica
```


### Defualt virtual host file

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


### First/test wsgi application
First install wsgi module with:  

 Then **_create_** the `/var/www/html/myapp.wsgi` file using the command `sudo vim /var/www/html/myapp.wsgi`. 
 This is a python application even though it ends with wsgi.  
 
 Add this code to file and change the output to. 
 'Hello World!'    
 
 ```
 def application(environ, start_response):
    status = '200 OK'
    output = 'Hello World!'
    _environ = 

    response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

    return [output]
    
 ```
 Finally, restart Apache with the `sudo apache2ctl restart`. 

Refresh your browser and you should see your app runing Hello World!   



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
## Test if my flask app import in datafrica.wsgi
```
 python3
Python 3.6.9 (default, Apr 18 2020, 01:56:04) 
[GCC 8.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from datafrica import app as application
>>> print (application)
<Flask 'datafrica'>
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
### Important trouble shooting code
```
sudo apache2ctl restart
sudo tail /var/log/apache2/error.log
```

### Flask config file
```
app = Flask(__name__)
app.config['FLASK_ENV'] = True # defaults to the production
app.config['DEBUG'] = True 
app.config['TESTING'] = True 

```

## Environ variables
export FLASK_ENV=datafrica
import os

environ = os.environ.get("FLASK_ENV", default="true")

## Usage
Configuring a linux web server requires a number of environmental variables for runtime configuration as shown above.  The following examples demonstrate how to run it manually from the command line in the appriopriate directory
example code. 



## Contributing
We encourgae contributions to Configuring linux web server. Please checkout  [guidelines]() on how to proceed. I wish to acknowledge the use of knowledge materials from Udacity videos, digitalocean, ubuntu ask questions and stackoverflow in this README.

## Licensing
Configuring a linux web server is licenced under [GNU AGPLv3](https://choosealicense.com/licenses/agpl-3.0/). 
