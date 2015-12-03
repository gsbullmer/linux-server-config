# linux-server-config
This is the fifth project in the Udacity Full Stack Web Developer Nanodegree program. In this project, a Linux web server was created and configured.

## Access the App
The web app can be [viewed here][1.0].

Accessed the web app with the following credentials:
- **IP address**: 52.32.196.18
- **SSH port**: 2200
- **User**: grader

## Software Installed
The web server was configured with the following packages:

#### fail2ban

#### ntp

#### apache2

#### libapache2-mod-wsgi

#### postgresql

#### postgresql-contrib

#### git

#### python-flask

#### python-flask-sqlalchemy

#### python-psycopg2

#### python-pip
Python package manager for app dependencies:
- flask
- sqlalchemy

## Third-Party Resources
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
