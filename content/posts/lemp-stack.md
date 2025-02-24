+++
date = '2025-02-24T21:38:48+05:45'
draft = false
title = 'LEMP Stack'
+++

LEMP is an open-source web application stack used to develop web applications.

**LEMP** Stands For:

```
L - Linux Operating System
E - Nginx Server
M - MySQL Database
P - PHP
```

### Set locale (Optional)

Setting the locale can prevent potential issues during installation. Open _/etc/default/locale_ and add the following:

```bash
sudo nano /etc/default/locale
```

```bash
LANG=en_US.UTF-8
LANGUAGE=en_US.UTF-8
LC_ALL=en_US.UTF-8
```

---

### Update the Package list

Update your system's package database

```bash
sudo apt-get update
```

---

### Install Nginx

Install the Nginx web server:

```bash
sudo apt-get install nginx
```

_Verify Installation_

> Access your server's IP in a browser (http://your-server-ip) to confirm Nginx is working.

### Install MySQL Server

```bash
sudo apt-get install mysql-server
```

Purpose: Installs the MySQL database server.
Explanation:

- _mysql-server_: The MySQL server package provides the software required to run and manage databases.
- _What happens_: During installation, you may be prompted to set up a root password for MySQL (depending on the version).
- _Why_: MySQL is used to store and manage data for your applications

---

### Install PHP

**Check which version is being installed. The version is required later.**

```bash
sudo apt-get install php-fpm php-mysql
```

- Purpose: Installs PHP and its necessary modules to work with MySQL.
- Explanation:
  - php-fpm: Stands for PHP FastCGI Process Manager. This is a version of PHP designed to work efficiently with Nginx.
    Nginx doesn’t process PHP directly, so php-fpm acts as a bridge to handle PHP scripts.
  - php-mysql: A PHP module that allows PHP to interact with MySQL databases.
    Check Version: Before running this command, you might check the PHP version with **php -v** if PHP is already installed, to avoid conflicts or ensure compatibility.

---

### Install phpmyadmin

```bash
sudo apt-get install phpmyadmin
```

1. Do not select any in the first selection for server as we have already installed Nginx
2. In the second option, SELECT yes
   ![Configuration of PHPMyAdmin](/lemp-stack-2.png)
3. You will be asked to set password for the database
   ![Setting password for PHPMyAdmin](/lemp-stack-3.png)
4. You will be asked to confirm the password
   ![Confirming password for PHPMyAdmin](/lemp-stack-4.png)

---

### Create _symlink_ of phpmyadmin with

**Symlink = Shortcut**

```bash
sudo ln -s /usr/share/phpmyadmin /var/www/html
```

#### Try http://ip-address/phpmyadmin to see if nginx loads properly

---

**NOTE: Change nginx sites-available for php and phpmyadmin**

- We need to edit the default config for nginx by using the following commands:

```bash
sudo nano /etc/nginx/sites-available/default
```

> Uncomment the following lines
>
> Do not forget to uncomment the last curly bracket
>
> Make sure the PHP version matches the installed version
>
> Usually it is _7.4_
>
> You can check the version using **php -v**

```
 location ~ \.php${
```

```
 include snippets/fastcgi-php.conf
```

```
fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
```

```
}
```

> Add index.php to the following line:

> **Before**

```bash
# Add index.php to the list if you are using PHP
 index index.html index.htm index.nginx-debian.html;
```

> **After**

```bash
# Add index.php to the list if you are using PHP
 index index.html index.htm index.nginx-debian.html index.php;

```

After all the steps are completed, it should look something like this:
![Final version of default config for NGINX](/lemp-stack-1.png)

### Test the file configuration

```bash
sudo nginx -t
```

### Reloading the Nginx with given changes

```bash
sudo service nginx reload
```

---

### Create New User for phpmyadmin

```bash
sudo mysql --user=root mysql
```

> Please **do not copy and paste this line**.
> Otherwise, the line will automatically be executed and password will be set to 'some_pass'

```mysql
CREATE USER 'admin'@'localhost' IDENTIFIED BY '[PASSWORD]';
```

> For connecting to the database from other server, use % when creating the user (This may cause error and is optional)

```mysql
CREATE USER 'admin'@'%' IDENTIFIED BY '[PASSWORD]'
```

```mysql
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
```

```mysql
FLUSH PRIVILEGES;
```

---

### Create swap file

_(Optional)_

> Swap file allows the use of hard disk memory to be used as RAM

Check if swap exists with

```bash
free

```

Create swapfile with

```bash
cd /var
sudo touch swap.img
sudo chmod 600 swap.img
sudo dd if=/dev/zero of=/var/swap.img bs=1024k count=1000
sudo mkswap /var/swap.img
sudo swapon /var/swap.img
```

---

### Installing Node.js

Here, I have listed the installation of Node.js using NVM.

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

The above command will install the nvm script to your user account. To use it, we must first source our .bashrc file:

```bash
source ~/.bashrc
```

To list all the versions in NVM

```bash
nvm list-remote
```

The output will be something like this:

```bash
Output
. . .
iojs -> N/A (default)
unstable -> N/A (default)
node -> stable (-> v20.14.0) (default)
stable -> 20.14 (-> v20.14.0) (default)
lts/* -> lts/iron (-> v20.14.0)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.17.1 (-> N/A)
lts/carbon -> v8.17.0 (-> N/A)
lts/dubnium -> v10.24.1 (-> N/A)
lts/erbium -> v12.22.12 (-> N/A)
lts/fermium -> v14.21.3 (-> N/A)
lts/gallium -> v16.20.2 (-> N/A)
lts/hydrogen -> v18.20.3 (-> N/A)
lts/iron -> v20.14.0
```

We can install any specific version of Node with the use of the specific version of the node.

For example to install v20.14.0

```bash
nvm install v20.14.0
```

We can also install a release based on these aliases as well. For example, to install LTS version with alias **iron**, we run:

```bash
nvm install lts/iron
```

We can easily switch between the installed version of node using the following command (This is why I suggest the use of the NVM installation method):

```bash
nvm use v14.10.0
```

or

```bash
nvm use lts/iron
```

---

### Install PM2

```bash
npm install pm2@latest -g
```

---

### Start API using pm2

> Here, name parameter is optional (I would suggest this if you will setup GitHub actions later)

```bash
pm2 start app.js --name API
```
