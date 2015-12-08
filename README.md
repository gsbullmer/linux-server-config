# Linux Web Server Configuration

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
- python-oauth2client

### Third-Party Resources
The following resources were used to help set up the server correctly:
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
This step-by-step guide explains everything that was done to setup the server.

### The Virtual Machine
Udacity provided a virtual server through Amazon EC2.

### SSH
Use the udacity_key.rsa provided to log into the Udacity-provided AWS VM. To log into the AWS VM:
```bash
ssh root@52.32.196.18 -i ~/.ssh/udacity_key.rsa
```

### Create New User
Once logged in, create a new user called `grader`. To create this user, type:
```bash
adduser grader
```

Don't worry about giving the user user a strong password; passwords will be disabled in favor of SSH access. Fill out the user details if desired.

### Grant sudo Access
This new user needs `sudo` access. Type:

```bash
touch /etc/sudoers.d/grader
nano /etc/sudoers.d/grader
```

In this file, add the line:
```
grader ALL=(ALL) NOPASSWD:ALL
```

This will add the user `grader` as a sudoer without the need to provide a password.

### Configure SSH for New User
Next, add the ssh authorized key for the new user. Temporarily log in as the new user:
```bash
sudo -su grader
```

Create the `.ssh` directory and the `authorized_keys` file:
```bash
cd /home/grader
mkdir .ssh
touch .ssh/authorized_keys
nano .ssh/authorized_keys
```

Add this public key, which is paired with `udacity_key.rsa`:
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEmB6XaSRvn9MXwPw/FRHA5bVM4ex0hNfZDLq8xGX05HCmr4ErsWJ9/1EhnPEVeAnMhXwKDhAQ4iDxaVt/7tHa05vmRIbfL66uk2vjzt5Hsk5GvjD1bAQ23rTcmqbUQAjjF50+DngyN8NHNJde87K6qo33UyRMyJ3o+gkllaKd578I4qNSEJ92V2m6h3+k+dYVCKJiInZWGBix4zp3GDJmbzlDxgv+kJO5ExPPNeoq4ddKDtOzFYIun/iUUPSTpSnm1UiZreNvBsUT0xDtUX/iew4414VOyBD9ac2IdthSx0ujvLGPVHujSe1BA1PNmigo+KGHUtBwpfsyFQs9zTQr Udacity Student
```

Set the ssh file permissions:
```bash
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
```

In a new terminal window, log in to `grader` with the rsa key:
```bash
ssh grader@52.32.196.18 -i ~/.ssh/udacity_key.rsa
```

**NOTE:** Be sure this can be done before logging out of `root`.

### Update Packages
Update all currently installed packages by typing:
```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```

To allow automatic security updates, install the `unattended-upgrades` package, if not installed:
```bash
sudo apt-get install unattended-upgrades
```

To enable unattended-upgrades, type:
```bash
sudo dpkg-reconfigure --priority-low unattended-upgrades
```

### SSH Config
Change the SSH port from `22` to `2200`, remove root login, and force users to use key-based authentication only by modifying `/etc/ssh/sshd_config`:
```bash
sudo nano /etc/ssh/sshd_config
```

#### Change SSH Port
Change the following line (extra lines removed for readablility):
```
# What ports, IPs and protocols we listen for
Port 2200
```

#### Remove Root Login
Change the following line (extra lines removed for readablility):
```
# Authentication:
PermitRootLogin no
```

#### Force SSH Login
Change the following line (extra lines removed for readablility):
```
# Change to no to disable tunnelled clear text passwords
PasswordAuthentication no
```

#### Restart SSH Service
Restart the `ssh` service for the changes to take effect:
```bash
sudo service ssh restart
```
**NOTE:** Be sure to have the ability to log in to `grader` before logging out of `root`.

### Configure Firewall
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123):
```bash
sudo ufw default deny incoming
sudo ufw allow 2200
sudo ufw allow 80
sudo ufw allow 123
sudo ufw enable
```

### Firewall Monitoring
Configure `fail2ban`, which will automatically block IP addresses that fail to correctly log in multiple times. First, install `fail2ban`:
```bash
sudo apt-get install fail2ban
```

Next, configure a local copy of the `jail.conf`:
```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```

Update the following section:
```
[ssh]

enabled  = true
banaction = ufw-ssh
port     = 2200
filter   = sshd
logpath  = /var/log/auth.log
maxretry = 3
```
This will enable the ssh jail, set the ban action to the new action `ufw-ssh` that will be defined below, listen on port `2200` instead of the default ssh port, and allows a max of `3` attempts to log in before the ban action triggering.

Next, create the `ufw-ssh` action referenced above:
```bash
sudo touch /etc/fail2ban/action.d/ufw-ssh.conf
sudo nano /etc/fail2ban/action.d/ufw-ssh.conf
```

Define the action as follows:
```
[Definition]
actionstart =
actionstop =
actioncheck =
actionban = ufw insert 1 deny from <ip> to any app OpenSSH
actionunban = ufw delete deny from <ip> to any app OpenSSH
```

Finally, restart `fail2ban`:
```bash
sudo service fail2ban restart
```

### Set Local Timezone
To set the system to use UTC, install `ntp`:
```bash
sudo apt-get install ntp
```

### Apache2 and mod_wsgi
Install the Apache2 web server and wsgi module:
```bash
sudo apt-get install apache2 libapache2-mod-wsgi
```

### PostgreSQL
Install and configure PostgreSQL:
```bash
sudo apt-get install postgresql postgresql-contrib
```

Login to postgreSQL as `postgres`:
```bash
sudo -u postgres psql
```

#### Create Users and Database
It's common practice to assign a distinct role for each application. It's also good practice to separate users from functions, further securing permissions. Create a new postgres user (role with login privileges) named `ibgdb`, and a postgres group (role without login privileges) named `ibgdb_mod` with user membership `ibgdb`.  To do this, type:
```pgsql
CREATE ROLE ibgdb LOGIN;
CREATE ROLE ibgdb_mod WITH ROLE ibgdb;
```

Now, create the `ibgdb` database, owned by `ibgdb_mod`:
```pgsql
CREATE DATABASE ibgdb OWNER ibgdb_mod;
```

Lock down permissions to this database to only let `ibgdb_mod` create tables:
```pgsql
\c ibgdb
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO ibgdb_mod;
```

#### Disable Remote Login
Open `/etc/postgresql/9.3/main/pg_hba.conf`:
```bash
sudo nano /etc/postgresql/9.3/main/pg_hba.conf
```

Add the following rule:
```
# "local" is for Unix domain socket connections only
local   sameuser        ibgdb                                   trust
```
This will not ask for a password when connecting to the `ibgdb` database with user `ibgdb` since this is only allowed on a local connection.

Finally, restart the postgresql service:
```bash
sudo service postgresql restart
```

### Deploy Web App
Install `git` if not already installed:
```bash
sudo apt-get install git
```

Install the web app framework packages:
```bash
sudo apt-get install python-flask python-flask-sqlalchemy python-psycopg2 python-oauth2client
```

Clone the web app into the home directory and link it to the apache doc root:
```bash
git clone -b production --single-branch https://github.com/gsbullmer/catalog.git ibgdb
sudo ln -sT ~/ibgdb /var/www/html/ibgdb
```

Configure apache and wsgi to run the web app:
```bash
sudo nano /etc/apache2/sites-enabled/000-default.conf
```

Make the following additions (some lines removed for readability):
```
<VirtualHost *:80>
  ...
  DocumentRoot /var/www/html

  WSGIDaemonProcess ibgdb threads=5
  WSGIScriptAlias / /var/www/html/ibgdb/ibgdb.wsgi

  <Directory ibgdb>
    WSGIProcessGroup ibgdb
    WSGIApplicationGroup %{GLOBAL}
    Order deny,allow
    Allow from all
  </Directory>
  ...
</VirtualHost>
```

Restart the apache service:
```bash
sudo service apache2 restart
```

Finally, check that the web app is running by visiting [http://ec2-52-32-196-18.us-west-2.compute.amazonaws.com/][1.0].

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
