# Configuring Linux Web Server
The project explains how to a take a baseline installation of a linux server and prepare it to host a web applications. At the end of the project you will secure your server from a number of attack vectors, install and configure a database server, and delpoy an existing web applications onto it.

## Installation
Initial setup before using the code.

### Get your server
* Amazon account with a _lightsail_
  * Start a new Ubuntu linux server intstance
  * SSH into the Server
  `ssh ubuntu@IP Address -p 22 -i ~/.ssh/privateKeyName`

The IP address: XXXXXXXX
SSH port: XXXX
The complete URL to your hosted web application: www.datafrica.com


### Dependencies on other software or libraries
A list of any third-party resources you need in this project.
To run flask app on the instance (ubuntu OS), we have to install Apache server, WSGI (Web Server Gateway Interface), flask and other libraries used in the app. Basically run the following:-

`sudo apt-get update`

`sudo apt-get install python-pip`

`sudo apt-get install python-flask`

`sudo apt-get install apache2`

`sudo apt-get install libapache2-mod-wsgi`


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


### Dependencies on other software or libraries
A list of any third-party resources you need in this project.
To run flask app on the instance (ubuntu OS), we have to install Apache server, WSGI (Web Server Gateway Interface), flask and other libraries used in the app. Basically run the following:-

`sudo apt-get update`

`sudo apt-get install python-pip`

`sudo apt-get install python-flask`

`sudo apt-get install apache2`

`sudo apt-get install libapache2-mod-wsgi`



## Usage
Configuring a linux web server requires a number of environmental variables for runtime configuration. The following examples demonstrate how to run it manually from the command line
example code
`sudo pip3 install virtualenv` inside your application domain/directory
To activate : `source venv/bin/activate`


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
Create a file name grader with:
`sudo visudo /etc/sudoers.d/grader`,

Add this line of code inside this file,
grader `ALL = (ALL : ALL) ALL`,
Save and quit.

### Finally grant permission


### Generate key pairs with the commmand
`ssh-keygen
* One done use the cat command to copy the public key ending in `.pub`,
* Upload this key to your amazon account as one of the keys to be used for access.,
* Alternatively, first make sure to lohin as the grader,
* The create a directory .ssh with `mkdir .ssh` within the home directory
* Then create a new file within this directory called authorized_keys `sudo touch ~/.ssh/authorized_keys`,
* Back in your local machine, read out the content of the key pair generated with the extension `.pub`,
* Copy it, and back in your server, edit the authorized keys file by adding this key.

Then login with `ssh <username>@ip-address -p 2200 -i ~/.ssh/your_private_key_name`.,


## Prepare to deploy your project
## Install and configure the Apache Web Server on an Ubuntu
`sudo apt-get update`
`sudo apt-get install apache2`

The visit your client to check if you have a working server by typing,
`your_domain_name_or_ip_address`
This is the default web page for this server.

### The Apache File Hierarchy in Ubuntu
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


* Configure the local timezone to UTC


### Prelimnary configuration of apache to display "Hello World!"
* Install and configure Apache to server a Python mod_wsgi application

 ```sudo apt-get install libapache2-mod-wsgi-py3```

 Define the name of the file you need to write within Apache configuration by using `WSGIScriptAlias` directive

 Now **edit** the `/etc/apache2/sites-enabled/000-default.conf` file. This file tells Apache how to respond to requests, where to find the files for a particular site and much more.

 Edit by adding the following line  `WSGIScriptAlias / /var/www/html/myapp.wsgi`  at the end of the

 ```
 <VirtualHost *:80> block, right before the closing

 </VirtualHost> line:

 ```


 Then **_create_** the `/var/www/html/myapp.wsgi` file using the command `sudo vim /var/www/html/myapp.wsgi`
 This is a python application even though it ends with wsgi.


 Finally, restart Apache with the sudo apache2ctl restart

The name of the file you need to write within Apache configuration by using `WSGIScriptAlias` directive.


## Deploy the project
### Setting Up Virtual Hosts
Create a directory structure within `/var/www/` for your domain.
* cd in /var/wwww
* Clone the project from github with this directory `www`

* Now cd into the project directory cloned `cd /var/www/your_cloned_project_ directory`
* Next assign ownership of the directory with $USER environment variable.

  * `sudo chown -R $USER:$USER /var/www/your_cloned_project_ directory`

* Next give permissions to your web root

  * `sudo chmod -R 775 /var/www/your_cloned_project_ directory`


### Customise the Apache to hand-off certain requests to an app

* Configure Apache to handle requests using the WSGI module. But instead of by editing the file`/etc/apache2/sites-enabled/000-default.conf`, lets create a new file with:

  * `sudo vim  /etc/apache2/sites-enabled/your_domain.conf` Take note that this is you app configuration file and is inate. It doesn not require touching.

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

  * Restart Apache with `sudo apache2ctl restart`


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

### Setting Up Postgresql Database
* Install posgressql with
`sudo apt-get install postgresql`

Use this command to start the Postgres interactive shell and to switch user to Postgres: You must be already logged in as a sudo user

`$ sudo -i -u postgres`

* Create database user with:
`postgresql@IP Adress: createruser -P <username>`
Give password as password on prompt

* Create database with the same name as username
`postgresql@IP Adress: createrdb <username>`
* Check to see permissions to the user with:


Rename `application.py` to `__init__.py` using

`sudo mv application.py __init__.py`

* Edit these files, ( `database_setup.py`, `application.py` and `functions_helper.py` ),  by changing

```engine = create_engine('sqlite:///your_database_name.db.db'``` to

 `engine = create_engine('postgresql://<username>:password@localhost/<username>'`

 Exit postgresql user:
  `Exit`

Run the database setup file  `sudo python database_setup.py`


## Known Bugs
Internal Server Error
The server encountered an internal error or misconfiguration and was unable to complete your request.

Please contact the server administrator at kofuafor@gmail.com to inform them of the time this error occurred, and the actions you performed just before this error.

More information about this error may be available in the server error log.


## Contributing
We encourgae contributions to Configuring linux web server. Please checkout  [guidelines]() on how to proceed.

## Licensing
Configuring a linux web server is licenced under [GNU AGPLv3](https://choosealicense.com/licenses/agpl-3.0/)
