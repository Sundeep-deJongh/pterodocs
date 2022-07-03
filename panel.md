# pterodocs

::: warning
Pterodactyl does not support most OpenVZ systems due to incompatibilities with Docker. If you are planning on running
this software on an OpenVZ based system you will &mdash; most likely &mdash; not be successful.
:::

| Operating System | Version |     Supported      | Notes                                                       |
| ---------------- | ------- | :----------------: | ----------------------------------------------------------- |
| **Ubuntu**       | 18.04   | :white_check_mark: | Documentation written assuming Ubuntu 18.04 as the base OS. |
|                  | 20.04   | :white_check_mark: |                                                             |
|                  | 22.04   | :white_check_mark: | MariaDB can be installed without the repo setup script.     |
| **CentOS**       | 7       | :white_check_mark: | Extra repos are required.                                   |
|                  | 8       | :white_check_mark: | Note that CentOS 8 is EOL. Use Rocky or Alma Linux.         |
| **Debian**       | 9       | :white_check_mark: | Extra repos are required.                                   |
|                  | 10      | :white_check_mark: |                                                             |
|                  | 11      | :white_check_mark: |                                                             |

The commands below are simply an example of how you might install these dependencies. Please consult with your
operating system's package manager to determine the correct packages to install.

``` bash
# Add "add-apt-repository" command
apt -y install software-properties-common curl apt-transport-https ca-certificates gnupg

# Add additional repositories for PHP, Redis, and MariaDB
LC_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php
add-apt-repository ppa:redislabs/redis -y

# MariaDB repo setup script can be skipped on Ubuntu 22.04
curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash

# Update repositories list
apt update

# Add universe repository if you are on Ubuntu 18.04
apt-add-repository universe

# Install Dependencies
apt -y install php8.1 php8.1-{common,cli,gd,mysql,mbstring,bcmath,xml,fpm,curl,zip} mariadb-server nginx tar unzip git redis-server
```

### Installing Composer

Composer is a dependency manager for PHP that allows us to ship everything you'll need code wise to operate the Panel. You'll
need composer installed before continuing in this process.

``` bash
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```
## Download Files

The first step in this process is to create the folder where the panel will live and then move ourselves into that
newly created folder. Below is an example of how to perform this operation.

``` bash
mkdir -p /var/www/pterodactyl
cd /var/www/pterodactyl
```
## MySQL Installation
See [Setting up MySQL](https://github.com/Sundeep-deJongh/pterodocs/blob/main/MYSQL.md) to create a user and database for your Pterodactyl panel quickly.

### Environment Setup
First we will copy over our default environment settings file, install core dependencies, and then generate a
new application encryption key.

``` bash
cp .env.example .env
composer install --no-dev --optimize-autoloader

# Only run the command below if you are installing this Panel for
# the first time and do not have any Pterodactyl Panel data in the database.
php artisan key:generate --force
```

### Environment Configuration

Pterodactyl's core environment is easily configured using a few different CLI commands built into the app. This step
will cover setting up things such as sessions, caching, database credentials, and email sending.

``` bash
php artisan p:environment:setup
php artisan p:environment:database

# To use PHP's internal mail sending (not recommended), select "mail". To use a
# custom SMTP server, select "smtp".
php artisan p:environment:mail
```

| **IMPORTANT:** When you setting up the database environment make sure you choose the option "Database" otherwise Redis with automaticly be chosen as default. |
| --- |

### Database Setup

Now we need to setup all of the base data for the Panel in the database you created earlier. **The command below
may take some time to run depending on your machine. Please _DO NOT_ exit the process until it is completed!** This
command will setup the database tables and then add all of the Nests & Eggs that power Pterodactyl.

``` bash
php artisan migrate --seed --force
```

### Add The First User

You'll then need to create an administrative user so that you can log into the panel. To do so, run the command below.
At this time passwords **must** meet the following requirements: 8 characters, mixed case, at least one number.

``` bash
php artisan p:user:make
```

### Set Permissions

The last step in the installation process is to set the correct permissions on the Panel files so that the webserver can
use them correctly.

``` bash
# If using NGINX or Apache (UBUNTU ONLY):
chown -R www-data:www-data /var/www/pterodactyl/*
```

### Crontab Configuration

You'll want to open your crontab using `sudo crontab -e` and choose for the first option **NANO** then paste this line below in the crontab configuration.
```bash
* * * * * php /var/www/pterodactyl/artisan schedule:run >> /dev/null 2>&1
```

### Create Queue Worker
Create a file called ``pteroq.service`` with ``nano /etc/systemd/system/pteroq.service``,
And paste this in the file.
``` text
# Pterodactyl Queue Worker File
# ----------------------------------

[Unit]
Description=Pterodactyl Queue Worker
After=redis-server.service

[Service]
# On some systems the user and group might be different.
# Some systems use `apache` or `nginx` as the user and group.
User=www-data
Group=www-data
Restart=always
ExecStart=/usr/bin/php /var/www/pterodactyl/artisan queue:work --queue=high,standard,low --sleep=3 --tries=3
StartLimitInterval=180
StartLimitBurst=30
RestartSec=5s

[Install]
WantedBy=multi-user.target
```


Finally, enable the service and set it to boot on machine start.

``` bash
sudo systemctl enable --now pteroq.service
```
#### Next Step: [Webserver Configuration](https://github.com/Sundeep-deJongh/pterodocs/blob/main/webserver_configuration.md)
