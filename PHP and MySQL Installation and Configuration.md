Covers the installation and configuration of PHP, MySQL, phpMyAdmin, WordPress, and Apache Virtual Hosts. Each step includes an explanation before executing the corresponding command.

## PHP Installation and Configuration

### Install Required Repositories

Before installing PHP, we must enable the Extra Packages for Enterprise Linux (EPEL) repository and the Remi repository, which provides updated PHP versions.

Run the following command to install EPEL and YUM utilities:

```bash
yum install epel-release yum-utils mod_ssl

```

After installation, verify the available repositories by running:

```bash
yum repolist all

```

Visit the official Remi repository to get the appropriate repository package: [Remi Repository](http://rpms.remirepo.net/)

Now, install the Remi repository for Enterprise Linux 9:

```bash
yum install <http://rpms.remirepo.net/enterprise/remi-release-9.rpm>

```

Verify that the repository has been added by listing all available repositories again:

```bash
yum repolist all

```

To check configuration files related to the remi-release package, run:

```bash
rpm -qc remi-release

```

```bash
rpm -ql remi-release

```

If necessary, edit the repository configuration file manually:

```bash
vim /etc/yum.repos.d/remi.repo

```

### Search for Available PHP Versions

Before installing PHP, check available PHP versions in the repository:

```bash
yum search php

```

For more detailed information about the default PHP package, run:

```bash
yum info php.x86_64

```

To check the details of PHP 8.1 and PHP 8.4, use:

```bash
yum info php81.x86_64

```

```bash
yum info php84.x86_64

```

### Install PHP and Required Extensions

Once we have identified the PHP version to install, proceed with the installation of PHP and essential extensions. This command installs PHP and commonly used modules such as MySQL support, XML processing, multibyte string handling, and more:

```bash
yum install php php-common php-cli php-opcache php-gd php-curl php-mysqlnd php-xml php-mbstring php-pear php-pecl-http php-session

```

### Verify PHP Installation

After installation, confirm that PHP has been successfully installed by checking the version:

```bash
php -v

```

### Restart Apache

To apply PHP changes, restart the Apache web server:

```bash
systemctl restart httpd.service

```

### Create a PHP Info Page

To verify that PHP is working correctly with Apache, create a phpinfo.php file inside the web root directory:

```bash
vim /var/www/html/phpinfo.php

```

Inside the file, add the following PHP script:

```php
<?php
phpinfo();
?>

```

Save the file and access it in your browser at: http://192.168.1.33/phpinfo.php

## MySQL Installation and Configuration

### Download and Install MySQL Repository

To install MySQL, first, download the official MySQL repository package:

```bash
wget <https://dev.mysql.com/get/mysql84-community-release-el9-2.noarch.rpm>

```

Now install the downloaded repository package:

```bash
yum install ./mysql84-community-release-el9-2.noarch.rpm

```

Verify that the MySQL repository has been added:

```bash
yum repolist all

```

### Enable and Disable MySQL Versions

Enable MySQL 8.0 repository:

```bash
yum-config-manager --enable mysql80-community

```

(Optional) Enable MySQL 5.7 repository if required:

```bash
yum-config-manager --enable mysql57-community

```

If you mistakenly enabled MySQL 5.7, disable it:

```bash
yum-config-manager --disable mysql57-community

```

### Search and Install MySQL

Search for available MySQL packages:

```bash
yum search mysql

```

Install MySQL server and development libraries:

```bash
yum install mysql-community-server mysql-community-devel

```

### Start and Enable MySQL Service

Start the MySQL service:

```bash
systemctl start mysqld.service

```

Enable MySQL to start automatically on boot:

```bash
systemctl enable mysqld.service

```

Check if MySQL is running:

```bash
systemctl status mysqld.service

```

### Retrieve Temporary Root Password

After MySQL installation, a temporary root password is generated. Retrieve it using:

```bash
grep 'temporary password' /var/log/mysqld.log

```

### Secure MySQL Installation

Run the MySQL secure installation script to set a new root password and remove insecure default settings:

```bash
mysql_secure_installation

```

Example new password: password@1234

Now, log into MySQL using the new password:

```bash
mysql -u root -p'p@ssW0rd@123'

```

### Change Root Password (If Needed)

If you want to manually update the MySQL root password, log in and run:

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Armour@123';
SHOW DATABASES;
EXIT;

```

## phpMyAdmin Installation and Configuration

phpMyAdmin is a web-based tool used for managing MySQL databases. It provides an intuitive graphical interface for performing database operations without needing to use the command line.

### Download and Extract phpMyAdmin

First, navigate to the official phpMyAdmin website to get the latest version: [phpMyAdmin](https://www.phpmyadmin.net/)

Now, download the phpMyAdmin package:

```bash
wget <https://files.phpmyadmin.net/phpMyAdmin/5.1.0/phpMyAdmin-5.1.0-all-languages.zip>

```

Extract the downloaded package:

```bash
unzip phpMyAdmin-5.1.0-all-languages.zip

```

Move the extracted files to the Apache web directory:

```bash
mkdir /var/www/html/phpmyadmin

```

```bash
cp -r phpMyAdmin-5.1.0-all-languages/* /var/www/html/phpmyadmin/

```

Navigate to the web root directory:

```bash
cd /var/www/html/

#change the owernership 
chown -R apache:apache php*
```

### Configure phpMyAdmin(when you login in web pgpmyadmin it show error or warring yellow box we can fixed from her )

Copy the sample configuration file to make it active:

```bash
cp /var/www/html/phpmyadmin/phpMyAdmin-5.2.3-all-languages/config.sample.inc.php /var/www/html/phpmyadmin/phpMyAdmin-5.2.3-all-languages/config.inc.php

```

Edit the configuration file:

```bash
vim /var/www/html/phpmyadmin/config.inc.php

```

Generate a random secret passphrase to enhance security:

```bash
pwgen 32 1

```

Set proper ownership to ensure Apache can access the phpMyAdmin files:

```bash
chown -R apache:apache /var/www/html/phpmyadmin

```

Restart Apache to apply changes:

```bash
systemctl restart httpd.service

```

## WordPress Installation and Configuration

WordPress is a popular content management system (CMS) used to create and manage websites.

### Download and Extract WordPress

Download the latest version of WordPress from the official website:

```bash
wget <https://wordpress.org/latest.zip>

```

Extract the downloaded package:

```bash
unzip latest.zip

```

Move WordPress files to the Apache web directory:

```bash
cp -r wordpress/* /var/www/html/

```

Navigate to the web root directory:

```bash
cd /var/www/html/

```

Set the correct ownership to allow Apache to manage WordPress files:

```bash
chown -R apache:apache /var/www/html/wordpress/

```

Navigate to the WordPress directory:

```bash
cd wordpress/

```

Set proper permissions for essential WordPress directories:

```bash
chmod -R 0755 wp-includes/ wp-admin/ wp-content/themes/ wp-content/plugins/

```

### Configure Apache for WordPress

Open the Apache configuration file for editing:

```bash
vim /etc/httpd/conf/httpd.conf

```

Add the following VirtualHost configuration (note: this includes SSL setup for HTTPS):

```
<VirtualHost 172.27.65.205:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    DocumentRoot /var/www/html/wordpress/
    DirectoryIndex index.php
</VirtualHost>

```

Restart Apache to apply changes:

```bash
systemctl restart httpd.service
#if error 
#then run 
yum install mod_ssl   #https ki problum he 

```

Restart DNS service if applicable:

```bash
systemctl restart named.service

```

## Apache VirtualHost Configuration

A VirtualHost configuration allows Apache to serve multiple websites from the same server.

### Configure VirtualHost for SSL

To enable HTTPS support for your website, add the following configuration in the Apache Virtual Host file:

```
<VirtualHost 192.168.1.37:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    ServerName armour.local
    DocumentRoot /var/www/html/armour-wp/
    DirectoryIndex index.php
    ServerAlias www.armour.local
</VirtualHost>

```

Test domain resolution to ensure WordPress is accessible:

```bash
dig armour.local

```

### Configure VirtualHost for HTTP

If you want your site to be accessible over HTTP (non-secure), use this configuration:

```
<VirtualHost 192.168.1.31:80>
    ServerName armour.local
    DocumentRoot /var/www/html/armour-wp/
    DirectoryIndex index.php
    ServerAlias www.armour.local
</VirtualHost>

```
