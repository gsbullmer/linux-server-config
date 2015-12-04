# linux-server-config
This is the fifth project in the Udacity Full Stack Web Developer Nanodegree program. In this project, a Linux web server was created and configured.

There are two main parts:
- [Access the App](#access-the-app)
- [Setup the Server](#setup-the-server)

## Access the App
The web app can be viewed at: [http://ec2-52-32-196-18.us-west-2.compute.amazonaws.com/][1.0]

Accessed the web app with the following credentials:
- **IP address**: 52.32.196.18
- **SSH port**: 2200
- **User**: grader

### Software Installed
The web server was configured with the following packages:

- fail2ban
- ntp
- apache2
- libapache2-mod-wsgi
- postgresql
- postgresql-contrib
- git
- python-flask
- python-flask-sqlalchemy
- python-psycopg2

### Third-Party Resources
I used the following resources to help set up the server correctly:
- [Server Config Walkthrough][3.0]
- [Automatic Security Updates][3.1]
- [Disable root login][3.2]
- [fail2ban][3.3]
- [fail2ban ufw Config][3.4]
- [NTP][3.5]
- [Deploy WSGI on Apache][3.6]
- [Deploy Flask app on aws ec2][3.7]
- [PostgreSQL][3.8]
- [Secure PostgreSQL on Ubuntu][3.9]

## Setup the Server
This step-by-step guide will walk you through the server setup
### Step 1
Launch your Virtual Machine with your Udacity account. Please note that upon graduation from the program your free Amazon AWS instance will no longer be available.

### Step 2
Follow the instructions provided to SSH into your server

### Step 3
Create a new user named grader

### Step 4
Give the grader the permission to sudo

### Step 5
Update all currently installed packages

### Step 6
Change the SSH port from 22 to 2200

### Step 7
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

### Step 8
Configure the local timezone to UTC

### Step 9
Install and configure Apache to serve a Python mod_wsgi application

### Step 10
Install and configure PostgreSQL:
- Do not allow remote connections
- Create a new user named catalog that has limited permissions to your catalog application database

### Step 11
Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!

<!-- Resource Links -->
[1.0]:http://ec2-52-32-196-18.us-west-2.compute.amazonaws.com/

[3.0]:https://github.com/skh/fsnd-linux-server-config
[3.1]:https://help.ubuntu.com/community/AutomaticSecurityUpdates
[3.2]:http://serverfault.com/questions/178080/how-do-i-disable-root-login-in-ubuntu
[3.3]:https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-14-04
[3.4]:http://askubuntu.com/questions/54771/potential-ufw-and-fail2ban-conflicts
[3.5]:https://help.ubuntu.com/lts/serverguide/NTP.html
[3.6]:http://flask.pocoo.org/docs/0.10/deploying/mod_wsgi/
[3.7]:http://www.datasciencebytes.com/bytes/2015/02/24/running-a-flask-app-on-aws-ec2/
[3.8]:https://help.ubuntu.com/community/PostgreSQL
[3.9]:https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps
