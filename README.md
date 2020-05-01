# Configuring Linux Web Server
The project explains how to a take a baseline installation of a linux server and prepare it to host a web applications. At the end of the project you will secure your server from a number of attack vectors, install and configure a database server, and delpoy an existing web applications onto it. The steps should progress from acquiring and updating the server dependencies,  user managment(creating, permission, log in via ssh) to configuring the firewall rules followed by application deployment and configuration and finally, database configuration.

## Installation
Initial setup before using the code.

### Get your server
* Amazon account with a _lightsail_
  * Start a new Ubuntu linux server intstance
  * SSH into the Server
  `ssh ubuntu@IP Address -p 22 -i ~/.ssh/privateKeyName`
  We will see later how to generate key pairs and upload the public key to amazon

The IP address: XXXXXXXX
SSH port: XXXX
The complete URL to your hosted web application: www.datafrica.com


### Dependencies on other software or libraries
A list of any third-party resources you need in this project.
To run flask app on the instance (ubuntu OS), we have to install Apache server, WSGI (Web Server Gateway Interface), flask and other libraries used in the app. Basically run the following some inside your project virtualenv:-

`sudo apt-get update`

`sudo apt-get install python-pip`

`sudo apt-get install python-flask`

`sudo apt-get install python3-venv`


## Secure your server
Configure your firewall ()**ufw**} earlier on

```sudo ufw status```
status -inactive

Configure ports:

```sudo ufw default deny incoming```

```sudo ufw default allow outgoing```

```ufw enable status```
status inactive

### Start configuring various ports the application will need
To support SSH:

```sudo ufw allow ssh```

We are going to be using SSH on port 2200, so let us setup ssh to allow port 2200

```sudo ufw allow 2200/tcp```

We plan to support a basic http server,and we can allow this by using:

```sudo ufw allow wwww```

With that we can now enable our firewall with `sudo ufw enable`

Finally, confirm that all our configuration are working with

```sudo ufw status``` command  


### Add custom firewall configuration like so:
  1. Open amazon lightsail panel and, select firewall,
  2. Add _custom_ port 2200, then select tcp protocol and finally save.,
  3. Now go to `/etc/ssh/sshd_config` file and change port option from 22 to 2200.,
  4. Make to configure firewall to allow port 2200 if not already done with `sudo ufw allow 2200/tcp`
  5. Restart SSH service with `sudo systemctl restart ssh`
  6. Exit and connect through ssh with the new ssh port 2200.

_Configure the local timezone to UTC `sudo dpkg-reconfigure tzdata`_

### Dependencies on other software or libraries
A list of any third-party resources you need in this project.
To run flask app on the instance (ubuntu OS), we have to install Apache server, WSGI (Web Server Gateway Interface), flask and other libraries used in the app. Basically run the following:-

`sudo apt-get update`

`sudo apt-get install python-pip`

`sudo apt-get install python-flask`

`sudo apt-get install apache2`

`sudo apt-get install libapache2-mod-wsgi`



## Usage
Configuring a linux web server requires a number of environmental variables for runtime configuration. The following examples demonstrate how to run it manually from the command line in the appriopriate directory
example code

`sudo apt-get install python3-venv`,
`sudo apt-get install python-pip`,
Create a virtual environment with the code `python3 -m venv /path/to/new/virtual/environment`,
Which in my case is  `sudo python3 -m venv /var/www/env`

Now install the following packages without activating virtualenv by using inside the project directory(www) `sudo env/bin/pip install`,

1.`sudo env/bin/pip install flask`,
2. `sudo env/bin/pip install sqlalchemy`


## Create user with access
Create a new user,
Install finger `sudo apt-get install finger,`


`sudo adduser grader`
Give a password to user when prompted to do so,
Log in as grader with given password,


### Giving Sudo Access,
Do this in the main admin account - ubuntu@IP address,
Create a new file inside - sudoers.d with the name of the new user in this case 'grader',
Open this file with visudo,
Create a file name grader with how to [here](https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file-on-ubuntu-and-centos):
`sudo visudo /etc/sudoers.d/grader`,

Add this line of code inside this file,
grader `ALL = (ALL : ALL) ALL`,
Save and quit.
Now login as the new user ie grader

### Finally force key based authentication
With `sudo vim /etc/ssh/sshd_config`This is the server listening for all of your ssh connections



### Generate key pairs with the commmand
`ssh-keygen
* Once done, use the `cat` command to copy the public key ending in `.pub`,
* Upload this key to your amazon account as one of the keys to be used for access.,
* Alternatively, first make sure to lohin as the grader,
  * Then create a directory .ssh with `mkdir .ssh` within the home directory
  * Then create a new file within this directory called authorized_keys `sudo touch ~/.ssh/authorized_keys`,
  * Back in your local machine, read out the content of the key pair generated with the extension `.pub`,
  * Copy it, and back in your server, edit the authorized keys file by adding this key.
  
Then restart the service with sudo ssh service restart`

Then login with `ssh <username>@ip-address -p 2200 -i ~/.ssh/your_private_key_name`.,
Then restart the service with sudo ssh service restart`


## Prepare to deploy your project
## Install and configure the Apache Web Server on an Ubuntu
`sudo apt-get update`
`sudo apt-get install apache2`

Then visit your client to check if you have a working server by typing,
`your_domain_name_or_ip_address`
This is the default web page for this server.

### The Apache File Hierarchy in Ubuntu
Apache, by default, serves its files from the `/var/www/html` directory., If you explore this directory you will find a file called `index.html`

Update the index.html to simply display “Hello, World!” and refresh your browser to see your new page.


### Prelimnary configuration of apache to display "Hello World!"
* The first step in this process is to install `sudo apt-get install libapache2-mod-wsgi`


* Install and configure Apache to server a Python mod_wsgi application outside of the virtual environment

 ```sudo apt-get install libapache2-mod-wsgi-py3```
 
 You then need to configure Apache to handle requests using the WSGI module. You will do this by editing:

 Now **edit** the `/etc/apache2/sites-enabled/000-default.conf` file. This file tells Apache how to respond to requests, where to find the files for a particular site and much more.

 Adding the following line  `WSGIScriptAlias / /var/www/html/myapp.wsgi`  at the end of the

 ```
 <VirtualHost *:80> block, right before the closing
 
         Here
 </VirtualHost> line:
 ```
Finally, restart Apache with the `sudo apache2ctl restart`


#### Create your first wsgi application
 Then **_create_** the `/var/www/html/myapp.wsgi` file using the command `sudo vim /var/www/html/myapp.wsgi`
 This is a python application even though it ends with wsgi.
 
 Add this code to file and change the output to 'Hello World!'
 
 ```
 def application(environ, start_response):
    status = '200 OK'
    output = 'Hello Udacity!'

    response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

    return [output]
    
 ```
 
Finally, refresh your browser and you should see your app runing Hello World!



On Ubuntu, Apache keeps its main configuration files within the "/etc/apache2" folder:

`cd /etc/apache2`
`ls`

`apache2`
`conf`
`envvars`
`magic`
`mods-enabled/`
`sites-available/`
`conf.d/`
`httpd.conf`
`mods-available/`
`\ports.conf`
`sites-enabled/`




## Deploy the project
### Setting Up Virtual Hosts
Create a directory structure within `/var/www/` for your domain.
cd into your project directory

#### Prepare the virtualenv.
cd into the project root directory in this case `/var/project_drectory/` 
and install venv `sudo apt-get install python3-venv`,
Then install pip with `sudo apt-get install python-pip`,
Create a virtual environment with the code `python3 -m venv /path/to/new/virtual/environment`,
Which in my case is  `sudo python3 -m venv /var/www/env`

Now install the following packages without activating `env` by env/bin/pip install`, without activating venv 
Inside the project directory(w),

1.`sudo env/bin/pip install flask`,
2. `sudo env/bin/pip install sqlalchemy`



* Now clone the project
* Now cd into the cloned `cd /var/www/your_cloned_project_ directory`
* Next assign ownership of the directory with $USER environment variable.

   `sudo chown -R $USER:$USER /var/www/your_cloned_project_ directory`

* Next give permissions to your web root

  `sudo chmod -R 775 /var/www/your_cloned_project_ directory`
  
Install postgresql with `sudo apt-get install postgresql`, inside the cloned directory
Install psycopg2 with `sudo apt-get install python3-psycopg2`
  
cd into cloned directory and edit the files within the directory as follows:
* Edit application.py to `__init__.py` with sudo mv,
* Edit database_setup.py, application.py and functions_helper.py to change engine = create_engine('sqlite:///database.db') to engine = create_engine('postgresql://catalog:password@localhost/catalog')

Switch to postgres object with: `sudo -i -u postgres`


### Setting Up Postgresql Database

Use this command to start the Postgres interactive shell and to switch user to Postgres: You must be already logged in as a sudo user

`$ sudo -i -u postgres`

* Create database user with:
`postgresql@IP Adress: createruser -P <username>`
Give password as password on prompt

* Create database with the same name as username
`postgresql@IP Adress: createrdb <username>`
* Check to see permissions to the user with:
    


### Customise the Apache to hand-off certain requests to an app

* Configure Apache to handle requests using the WSGI module. But instead of by editing the file`/etc/apache2/sites-enabled/000-default.conf`, lets create a new file with:

  `sudo vim  /etc/apache2/sites-enabled/your_domain.conf` Take note that this is you app configuration file and is inate. It doesn not require touching.

  * Add the following default values to `/etc/apache2/sites-enabled/your_domain.conf`:

    * ServerAdmin webmaster@localhost to the email address of the domain manager

    * ServerName www.example.com to your DNS or IP address.

    * DocumentRoot `/var/www/html` to `/var/www/your_domain`

    * Add the following line
     `WSGIScriptAlias / /var/www/your_domain/your_domain.wsgi` at the end of the block right before the closing line

The `/etc/apache2/sites-enabled/your_domain.conf` should now look like this:

 ```bin/bash
       <VirtualHost *:80>
          ServerAdmin domain_admin@gmail.com
          ServerName your_domain
          DocumentRoot /var/www/your_domain

          DocumentRoot /var/www/your_doamin

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

          WSGIScriptAlias / /var/www/your_domain/myapp.wsgi
       </VirtualHost>
 ```

  Restart Apache with `sudo apache2ctl restart`


#### Add the following lines of code to the myapp.wsgi file:
Then add this block of code to the `your_domain.wsgi` application.

```#!/usr/bin/python3
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/your_doamin/")

from your_domain import app as application
application.secret_key = 'Add your secret key'
```
## Setting up a secret key and sessions in [Python Apps](https://developer.ibm.com/qradar/2018/10/03/secret-key-session-python-apps/)
For a secure session information is secure, a strong, cryptographically secure secret key is needed,
Also because diferent versions of the app would have different secret_keys at different times. This avoids hard coding the secret key.

We first must import the os module.

`Import os`

```
app.config.update(

    #Set the secret key to a sufficiently random value
    SECRET_KEY=os.urandom(24),

    #Set the session cookie to be secure
    SESSION_COOKIE_SECURE=True,

    #Set the session cookie for our app to a unique name
    SESSION_COOKIE_NAME='YourAppName-WebSession',

    #Set CSRF tokens to be valid for the duration of the session. This assumes you’re using WTF-CSRF protection
    WTF_CSRF_TIME_LIMIT=None

)

```


## Known Bugs
Internal Server Error
The server encountered an internal error or misconfiguration and was unable to complete your request.

Please contact the server administrator at kofuafor@gmail.com to inform them of the time this error occurred, and the actions you performed just before this error.

More information about this error may be available in the server error log.


## Contributing
We encourgae contributions to Configuring linux web server. Please checkout  [guidelines]() on how to proceed.

## Licensing
Configuring a linux web server is licenced under [GNU AGPLv3](https://choosealicense.com/licenses/agpl-3.0/)
