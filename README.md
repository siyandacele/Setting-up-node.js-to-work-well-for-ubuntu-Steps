# Let begin with installing node on ubuntu (Documenting my struggle setting up ubuntu)
==========================================
## (1) Generally speaking, loading arbitrary data from a URL into a root shell session is not a good idea and I wish people would stop peddling it as a solution for everything - "Please just run this script".

As an alternative, here's the "Ubuntu Way" of doing the same, where you can see how the system is being updated and know what repositories and what keys are added to your system configuration:

```
curl https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo apt-key add -
sudo apt-add-repository "deb https://deb.nodesource.com/node_13.x $(lsb_release -sc) main"
sudo apt-get update
sudo apt-get install nodejs
```
This is for the latest (at time of writing) Nodejs version 13. Other versions can also be gotten with a simple change to the repo URL - consult nodesource.com documentation for details.

# (2) Resolving EACCES permissions errors when installing packages globally
#### Personally I always see an EACCES error when I try to install a package globally for the first tiem [install a package globally](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) but let's manually change npm’s default directory

To minimize the chance of permissions errors, you can configure npm to use a different directory. In this example, you will create and use hidden directory in your home directory.

1. Back up your computer.
2. On the command line, in your home directory, create a directory for global installations

```
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
source ~/.profile
```
3. Now to test your new configuration, install a package globally without using sudo:
```
npm install -g jshint
```

# (3) Node setup in Ubuntu to allow you to use node cli's packages command
Follow this steps if you getting the below message on the Ubuntu terminal when trying to run node cli's commands
```bash
$ ng --help
The program 'ng' is currently not installed. You can install it by typing:
sudo apt install ng-common
```

Path known
Assuming you have a global modules set up for npm (by default ~/.node_modules_global)
$ npm prefix -g

Now set the path of modules/bin to our bash path environment to access over terminal
```bash
$ echo -e "export PATH=$(npm prefix -g)/bin:$PATH" >> ~/.bashrc 
$ source ~/.bashrc
```
###  How about setting a new one!
```bash
$ mkdir ~/path/module
$ npm config set prefix '~/path/module'
$ sudo chown -R $USER <directory>
```
Now the final touch, you can install angular/cli package
```bash
npm install -g @angular/cli
```

NestJs package
```bash
npm i -g @nestjs/cli
```

#### If already installed your cli's use their commands
```bash
ng new new- project
```
```bash
ng new new- project
```
```bash
nest new project-name
```

# Installing MySQL
## Introduction
The short version of the installation is simple: update your package index, install the mysql-server package, and then run the included security script.

```
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
```

This tutorial will explain how to install MySQL version 5.7 on an Ubuntu 18.04 server.

## Prerequisites
To follow this tutorial, you will need:

One Ubuntu 18.04 server set up by following this initial server setup guide, including a non-root user with sudo privileges and a firewall.
### Step 1 — Installing MySQL
On Ubuntu 18.04, only the latest version of MySQL is included in the APT package repository by default. At the time of writing, that’s MySQL 5.7

To install it, update the package index on your server with ``apt``:
```
sudo apt update
```
Then install the default package:
```
sudo apt install mysql-server
```
This will install MySQL, but will not prompt you to set a password or make any other configuration changes. Because this leaves your installation of MySQL insecure, we will address this next.

### Step 2 — Configuring MySQL
For fresh installations, you’ll want to run the included security script. This changes some of the less secure default options for things like remote root logins and sample users. On older versions of MySQL, you needed to initialize the data directory manually as well, but this is done automatically now.

Run the security script:
```
sudo mysql_secure_installation
```
This will take you through a series of prompts where you can make some changes to your MySQL installation’s security options. The first prompt will ask whether you’d like to set up the Validate Password Plugin, which can be used to test the strength of your MySQL password. Regardless of your choice, the next prompt will be to set a password for the MySQL root user. Enter and then confirm a secure password of your choice.

From there, you can press ``Y`` and then ``ENTER`` to accept the defaults for all the subsequent questions. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.

To initialize the MySQL data directory, you would use ``mysql_install_db`` for versions before 5.7.6, and mysqld --initialize for 5.7.6 and later. However, if you installed MySQL from the Debian distribution, as described in Step 1, the data directory was initialized automatically; you don’t have to do anything. If you try running the command anyway, you’ll see the following error:

> ***Output***
``
mysqld: Can't create directory '/var/lib/mysql/' (Errcode: 17 - File exists)
. . .
2018-04-23T13:48:00.572066Z 0 [ERROR] Aborting
``
Note that even though you’ve set a password for the root MySQL user, this user is not configured to authenticate with a password when connecting to the MySQL shell. If you’d like, you can adjust this setting by following Step 3.

### Step 3 — (Optional) Adjusting User Authentication and Privileges
In Ubuntu systems running MySQL 5.7 (and later versions), the root MySQL user is set to authenticate using the auth_socket plugin by default rather than with a password. This allows for some greater security and usability in many cases, but it can also complicate things when you need to allow an external program (e.g., phpMyAdmin) to access the user.

In order to use a password to connect to MySQL as root, you will need to switch its authentication method from auth_socket to mysql_native_password. To do this, open up the MySQL prompt from your terminal:
`
sudo mysql
`
Next, check which authentication method each of your MySQL user accounts use with the following command:

```
SELECT user,authentication_string,plugin,host FROM mysql.user;
Output
```
```
+------------------+-------------------------------------------+-----------------------+-----------+
| user             | authentication_string                     | plugin                | host      |
+------------------+-------------------------------------------+-----------------------+-----------+
| root             |                                           | auth_socket           | localhost |
| mysql.session    | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| mysql.sys        | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| debian-sys-maint | *CC744277A401A7D25BE1CA89AFF17BF607F876FF | mysql_native_password | localhost |
+------------------+-------------------------------------------+-----------------------+-----------+
4 rows in set (0.00 sec)
```
In this example, you can see that the root user does in fact authenticate using the auth_socket plugin. To configure the root account to authenticate with a password, run the following ALTER USER command. Be sure to change password to a strong password of your choosing, and note that this command will change the root password you set in Step 2:

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
Then, run `FLUSH PRIVILEGES` which tells the server to reload the grant tables and put your new changes into effect:

```
FLUSH PRIVILEGES;
```
Check the authentication methods employed by each of your users again to confirm that root no longer authenticates using the auth_socket plugin:

```
SELECT user,authentication_string,plugin,host FROM mysql.user;
```
> Output
```
+------------------+-------------------------------------------+-----------------------+-----------+
| user             | authentication_string                     | plugin                | host      |
+------------------+-------------------------------------------+-----------------------+-----------+
| root             | *3636DACC8616D997782ADD0839F92C1571D6D78F | mysql_native_password | localhost |
| mysql.session    | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| mysql.sys        | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| debian-sys-maint | *CC744277A401A7D25BE1CA89AFF17BF607F876FF | mysql_native_password | localhost |
+------------------+-------------------------------------------+-----------------------+-----------+
4 rows in set (0.00 sec)
```
You can see in this example output that the root MySQL user now authenticates using a password. Once you confirm this on your own server, you can exit the MySQL shell:

``` exit```

Alternatively, some may find that it better suits their workflow to connect to MySQL with a dedicated user. To create such a user, open up the MySQL shell once again:

sudo mysql
Note: If you have password authentication enabled for root, as described in the preceding paragraphs, you will need to use a different command to access the MySQL shell. The following will run your MySQL client with regular user privileges, and you will only gain administrator privileges within the database by authenticating:

mysql -u root -p
From there, create a new user and give it a strong password:

CREATE USER 'sammy'@'localhost' IDENTIFIED BY 'password';
Then, grant your new user the appropriate privileges. For example, you could grant the user privileges to all tables within the database, as well as the power to add, change, and remove user privileges, with this command:

GRANT ALL PRIVILEGES ON *.* TO 'sammy'@'localhost' WITH GRANT OPTION;
Note that, at this point, you do not need to run the FLUSH PRIVILEGES command again. This command is only needed when you modify the grant tables using statements like INSERT, UPDATE, or DELETE. Because you created a new user, instead of modifying an existing one, FLUSH PRIVILEGES is unnecessary here.

Following this, exit the MySQL shell:

exit
Finally, let’s test the MySQL installation.

Step 4 — Testing MySQL
Regardless of how you installed it, MySQL should have started running automatically. To test this, check its status.

systemctl status mysql.service
You’ll see output similar to the following:

Output
● mysql.service - MySQL Community Server
   Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: en
   Active: active (running) since Wed 2018-04-23 21:21:25 UTC; 30min ago
 Main PID: 3754 (mysqld)
    Tasks: 28
   Memory: 142.3M
      CPU: 1.994s
   CGroup: /system.slice/mysql.service
           └─3754 /usr/sbin/mysqld
If MySQL isn’t running, you can start it with sudo systemctl start mysql.

For an additional check, you can try connecting to the database using the mysqladmin tool, which is a client that lets you run administrative commands. For example, this command says to connect to MySQL as root (-u root), prompt for a password (-p), and return the version.

sudo mysqladmin -p -u root version
You should see output similar to this:

Output
mysqladmin  Ver 8.42 Distrib 5.7.21, for Linux on x86_64
Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version      5.7.21-1ubuntu1
Protocol version    10
Connection      Localhost via UNIX socket
UNIX socket     /var/run/mysqld/mysqld.sock
Uptime:         30 min 54 sec

Threads: 1  Questions: 12  Slow queries: 0  Opens: 115  Flush tables: 1  Open tables: 34  Queries per second avg: 0.006
This means MySQL is up and running.
