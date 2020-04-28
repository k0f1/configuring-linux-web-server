# Configuring Linux Web Server
The project explains how to a take a baseline installation of a linux server and prepare it to host a web applications. At the end of the project you will secure your server from a number of attack vectors, install and configure a database server, and delpoy an existing web applications onto it.

## Installation
Initial setup before using the code.
The IP address: 35.176.9.18
SSH port: 22
The complete URL to your hosted web application: https://lightsail.aws.amazon.com/ls/webapp/domains/datafrica-com

Locate the SSH key you created for the grader user.

### Dependencies on other software or libraries
A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.

#### Get your server
* Amazon account with a lightsail
  * Start a new Ubuntu linux server intstance
  * SSH into the Server
    *
    *



## Secure your server
Secure your server earlier on
ufw status -inactive
Configure ports
ufw enable status




1 what steps needs to be taken?
2 what should the user already have installed or configured
3 what might they have hard time understanding right away


## Usage
Configuring a linux web server requires a number of environmental variables for runtime configuration. The following examples demonstrate ho to run it manually from the command line
example code

## Known bugs


## Create user with access
Create a new user
Install finder ```sudo apt-get install finger```
Create user ```grader``` with ```sudo adduser grader```
Give a password to user when prompted to do so

```cat /etc/sudoers```
Create a file name grader with ```sudo touch /etc/sudoers.d/grader```
Then open the file with ```sudo visudo /etc/sudoers.d/grader```
Enter this live of code: ```ALL=(ALL:ALL) ALL```, save and quit.

### Grant persmission
Give ```grader``` the permission to ```sudo```
Create an SSH key pair for ```grader``` using the ssh-keygen tool



## Prepare to deploy your project
* Configure the local timezone to UTC
* Install and configure Apache to server a Python mod_wsgi application
 ```sudo apt-get install libapache2-mod-wsgi-py3
 ```


## Deploy the project
* cd in /var/wwww
* Clone the ptoject from github
* directory persmission
* Install libapache2 ```sudo apt-get install apache2```
*  configure Apache to hand-off certain requests to an application handler - mod_wsgi  Install mod_wsgi with ```sudo apt-get install libapache2-mod-wsgi```

* Configure Apache to handle requests using the WSGI module by editing the
```/etc/apache2/sites-enabled/000-default.conf``` file.
  * Add the following line ```WSGIScriptAlias / /var/www/html/myapp.wsgi``` at the end of the block right before the closing line
  ```/bin/bash
  <VirtualHost *:80>
  </VirtualHost>```
  * Restart Apache with ```sudo apache2ctl restart```

## Contributing
We encourgae contributions to Configuring linux web server. Please checkout  [guidelines] on how to proceed.

## Licensing
Configuring a linux web server is licenced under [GNU AGPLv3]https://choosealicense.com/licenses/agpl-3.0/
