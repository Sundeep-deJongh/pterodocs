### Logging In
The first step in this process is to login to the MySQL command line where we will be executing some statements to get
things setup. To do so, simply run the command below and provide the Root MySQL account's password that you setup when
installing MySQL. If you do not remember doing this, chances are you can just hit enter as no password is set.

``` bash
mysql -u root -p
```
### Creating a user
For security sake, and due to changes in MySQL 5.7, you'll need to create a new user for the panel. To do so, we want
to first tell MySQL to use the mysql database, which stores such information.

``` sql
# Remember to change 'somePassword' below to be a unique password specific to this account.
CREATE USER 'pterodactyl'@'127.0.0.1' IDENTIFIED BY 'somePassword';
```
### DO NOT SHARE THIS PASSWORD WITH ANYONE!

### Create a database
Next, we need to create a database for the panel. In this tutorial we will be naming the database `panel`, but you can
substitute that for whatever name you wish.

``` sql
CREATE DATABASE panel;
```

``` sql
GRANT ALL PRIVILEGES ON panel.* TO 'pterodactyl'@'127.0.0.1' WITH GRANT OPTION;
```

## Creating a Database Host for Nodes
:::tip
This section covers creating a MySQL user that has permission to create and modify users. This allows the Panel to create per-server databases on the given host.
:::

### Creating a user
Replace ``pterodactyluser`` with something else, and replace ``12.7.0.0.1`` with your VPS ipadress.

```sql
# You should change the username and password below to something unique.
CREATE USER 'pterodactyluser'@'127.0.0.1' IDENTIFIED BY 'somepassword';
```
### DO NOT SHARE THIS PASSWORD WITH ANYONE!

### Assigning permissions

```sql
GRANT ALL PRIVILEGES ON *.* TO 'pterodactyluser'@'127.0.0.1' WITH GRANT OPTION;
```

### Allowing external database access
Open `my.cnf`, add text below to the bottom of the file and save it:
```
[mysqld]
bind-address=0.0.0.0
```

### Restarting MySQL
You need to restart the mysql service with ``service mysql restart``.
