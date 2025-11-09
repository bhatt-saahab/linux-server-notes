**Overview**

This comprehensive guide walks you through installing and configuring Apache2, PHP, MySQL, SSL, phpMyAdmin, and WordPress on a Debian-based system. Ideal for setting up a local or production web server.

## Ensure Your System is Up-to-Date

- **Update package list and upgrade packages.**
    
    ```
    apt update
    
    ```
    
    ```
    apt upgrade
    
    ```
    

## Install the Apache Web Server and Networking Tools

- **Install Apache.**
    
    ```
    apt install apache2
    
    ```
    
- **Install networking tools.**
    
    ```
    apt install net-tools
    
    ```
    

## Verify Apache Installation

- **Check listening services and ensure Apache is running.**
    
    ```
    netstat -nltup
    
    ```
    
- **Verify Apache package installation.**
    
    ```
    dpkg -l | grep apache
    
    ```
    

## Configure Apache

- **Edit the Apache configuration file.**
    
    ```
    vim /etc/apache2/apache2.conf
    
    ```
    
- **Ensure these lines are included in the configuration file.**
    
    ```
    # Include generic snippets of statements
    IncludeOptional conf-enabled/*.conf
    
    # Include the virtual host configurations:
    IncludeOptional sites-enabled/*.conf
    
    ```
    

## Restart and Enable Apache Service

- **Restart Apache.**
    
    ```
    systemctl restart apache2.service
    
    ```
    
- **Enable Apache to start on boot.**
    
    ```
    systemctl enable apache2.service
    
    ```
    

## Confirm Apache is Listening

- **Check listening services.**
    
    ```
    netstat -nltup
    
    ```
    

## Disable Directory Listing

- **Improve security by removing the directory listing option.**
    
    ```
    sed -i "s/Options Indexes FollowSymLinks/Options FollowSymLinks/" /etc/apache2/apache2.conf
    
    ```
    
- **Restart Apache to apply changes.**
    
    ```
    systemctl restart apache2.service
    
    ```
    

## Installing PHP

### Search for PHP Versions

- **Search for available PHP versions.**
    
    ```
    apt search php | grep "php/stable"
    
    ```
    

### Install PHP and Extensions

- **Install PHP and common extensions (corrected for syntax; assuming PHP 8.2 for Debian stability in 2025, but adjustable).**
    
    ```
    apt install php php8.2 php8.2-common php8.2-mbstring php8.2-xmlrpc php8.2-soap php8.2-gd php8.2-xml php8.2-intl php8.2-mysql php8.2-cli php8.2-ldap php8.2-zip php8.2-curl
    
    ```
    
- **Install Composer (for PHP dependency management).**
    
    ```
    apt install composer
    
    ```
    

### Configure PHP

- **Edit the configuration file.**
    
    ```
    vim /etc/php/8.2/apache2/php.ini
    
    ```
    
- **Recommended settings (insert or update in php.ini).**
    
    ```
    memory_limit = 512M
    max_execution_time = 500
    max_input_vars = 10000
    upload_max_filesize = 2048M
    post_max_size = 2048M
    allow_url_fopen = On
    
    ```
    
- **Restart Apache after configuration.**
    
    ```
    systemctl restart apache2.service
    
    ```
    

### Create a PHP Info Page

- **Create the PHP info file.**
    
    ```
    vim /var/www/html/phpinfo.php
    
    ```
    
- **Insert the following content into phpinfo.php.**
    
    ```
    <?php
    phpinfo();
    ?>
    
    ```
    
- **Set file ownership.**
    
    ```
    chown -Rv www-data:www-data /var/www/html/phpinfo.php
    
    ```
    

## Installing MySQL Server

### Download MySQL APT Config Package

- **Install prerequisites.**
    
    ```
    apt install wget
    
    ```
    
- **Download the MySQL APT config package (updated URL for 2025 compatibility; version may vary).**
    
    ```
    wget <https://dev.mysql.com/get/mysql-apt-config_0.8.30-1_all.deb>
    
    ```
    
- **Install the downloaded package.**
    
    ```
    apt install ./mysql-apt-config_0.8.30-1_all.deb
    
    ```
    
- **Update packages.**
    
    ```
    apt update
    
    ```
    

### Install MySQL

- **Install MySQL Community Server.**
    
    ```
    apt install mysql-community-server
    
    ```
    
- **Restart and enable MySQL.**
    
    ```
    systemctl restart mysql.service
    
    ```
    
    ```
    systemctl enable mysql.service
    
    ```
    

### Verify MySQL is Running

- **Check listening services.**
    
    ```
    netstat -nltup
    
    ```
    
    ```
    netstat -nltup | grep 3306
    
    ```
    

### Run Secure Setup

- **Run the secure installation script.**
    
    ```
    mysql_secure_installation
    
    ```
    

### Access MySQL

- **Access MySQL as root.**
    
    ```
    mysql -u root -p
    
    ```
    
- **Inside MySQL, show databases.**
    
    ```
    SHOW DATABASES;
    
    ```
    
- **Optional: Create a remote root user (replace 'your_password' with a secure password).**
    
    ```
    CREATE USER 'root'@'%' IDENTIFIED WITH caching_sha2_password BY 'your_password';
    
    ```
    
    ```
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
    
    ```
    

## Enabling SSL on Apache

- **Enable SSL module.**
    
    ```
    a2enmod ssl
    
    ```
    
- **Restart Apache.**
    
    ```
    systemctl restart apache2.service
    
    ```
    
- **Enable default SSL site.**
    
    ```
    a2ensite default-ssl
    
    ```
    
- **Reload Apache.**
    
    ```
    systemctl reload apache2
    
    ```
    
    ```
    service apache2 reload
    
    ```
    
- **Check services.**
    
    ```
    netstat -nltup
    
    ```
    

## Creating a Self-Signed SSL Certificate

- **Create SSL directory.**
    
    ```
    mkdir /etc/apache2/ssl
    
    ```
    
- **Generate self-signed certificate (replace 'armour.local' with your domain).**
    
    ```
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/armour.local.key -out /etc/apache2/ssl/armour.local.crt
    
    ```
    
- **Verify the key file.**
    
    ```
    cat /etc/apache2/ssl/armour.local.key
    
    ```
    
- **Verify the certificate file.**
    
    ```
    cat /etc/apache2/ssl/armour.local.crt
    
    ```
    
- **Update hosts file.**
    
    ```
    vim /etc/hosts
    
    ```
    
- **Example entry in /etc/hosts.**
    
    ```
    192.168.1.28 armour.local www.armour.local
    
    ```
    

## phpMyAdmin Installation

- **Download and unzip phpMyAdmin (corrected version to 5.2.1 for consistency; check latest in 2025).**
    
    ```
    wget <https://files.phpmyadmin.net/phpMyAdmin/5.2.1/phpMyAdmin-5.2.1-all-languages.zip>
    
    ```
    
    ```
    unzip phpMyAdmin-5.2.1-all-languages.zip
    
    ```
    
- **Move files.**
    
    ```
    mv phpMyAdmin-5.2.1-all-languages /var/www/html/phpmyadmin
    
    ```
    
- **Set ownership.**
    
    ```
    chown -Rv www-data:www-data /var/www/html/phpmyadmin
    
    ```
    
- **Create config file.**
    
    ```
    cp /var/www/html/phpmyadmin/config.sample.inc.php /var/www/html/phpmyadmin/config.inc.php
    
    ```
    
- **Set ownership for config.**
    
    ```
    chown -Rv www-data:www-data /var/www/html/phpmyadmin/config.inc.php
    
    ```
    
- **Generate a Blowfish secret.**
    
    ```
    pwgen 32 1
    
    ```
    
- **Edit config.**
    
    ```
    vim /var/www/html/phpmyadmin/config.inc.php
    
    ```
    
- **Insert secret into config.inc.php.**
    
    ```
    $cfg['blowfish_secret'] = 'ophixahbufooshae9veipahK4dae4gah';
    
    ```
    

### Create SSL Config for phpMyAdmin

- **Create SSL virtual host config file.**
    
    ```
    vim /etc/apache2/sites-available/phpmyadmin-ssl.conf
    
    ```
    
- **Insert the following configuration.**
    
    ```
    <VirtualHost 192.168.1.35:8443>
        DocumentRoot /var/www/html/phpmyadmin
        SSLEngine on
        SSLCertificateFile /etc/apache2/ssl/armour.local.crt
        SSLCertificateKeyFile /etc/apache2/ssl/armour.local.key
        <Directory /var/www/html/phpmyadmin/>
            Options FollowSymLinks
            AllowOverride All
            Order allow,deny
            allow from all
        </Directory>
        ErrorLog /var/log/apache2/phpmyadmin-error_log
        CustomLog /var/log/apache2/phpmyadmin-access_log common
    </VirtualHost>
    
    ```
    

## Configure Apache Virtual Host for HTTPS

- **Create web root.**
    
    ```
    mkdir -p /var/www/html/armour.local
    
    ```
    
- **Create virtual host file.**
    
    ```
    vim /etc/apache2/sites-available/armour.local-ssl.conf
    
    ```
    
- **Insert configuration (HTTP and HTTPS blocks).**
    
    ```
    <VirtualHost 192.168.1.28:80>
        ServerAdmin webmaster@armour.local
        ServerName armour.local
        ServerAlias www.armour.local
        DocumentRoot /var/www/html/armour.local
        <Directory /var/www/html/armour.local>
            Options Indexes FollowSymLinks
            AllowOverride All
            Require all granted
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/armour.local-error.log
        CustomLog ${APACHE_LOG_DIR}/armour.local-access.log combined
    </VirtualHost>
    
    <VirtualHost 192.168.1.28:443>
        ServerAdmin webmaster@armour.local
        ServerName armour.local
        ServerAlias www.armour.local
        DocumentRoot /var/www/html/armour.local
        SSLEngine on
        SSLCertificateFile /etc/apache2/ssl/armour.local.crt
        SSLCertificateKeyFile /etc/apache2/ssl/armour.local.key
        <Directory /var/www/html/armour.local>
            Options Indexes FollowSymLinks
            AllowOverride All
            Require all granted
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/armour.local-error.log
        CustomLog ${APACHE_LOG_DIR}/armour.local-access.log combined
    </VirtualHost>
    
    ```
    
- **Enable site.**
    
    ```
    a2ensite armour.local-ssl.conf
    
    ```
    
- **Disable default sites.**
    
    ```
    a2dissite 000-default.conf
    
    ```
    
    ```
    a2dissite default-ssl.conf
    
    ```
    
- **Reload and restart Apache.**
    
    ```
    systemctl reload apache2
    
    ```
    
    ```
    systemctl restart apache2
    
    ```
    

### Optional: Provide Certificate for Download

- **Copy certificate to web root.**
    
    ```
    cp -v /etc/apache2/ssl/armour.local.crt /var/www/html/
    
    ```
    
- **Access via browser.**
    
    ```
    <http://192.168.1.45/armour.local.crt>
    
    ```
    

## Install WordPress

- **Download and unzip WordPress.**
    
    ```
    wget <https://wordpress.org/latest.zip>
    
    ```
    
    ```
    unzip latest.zip
    
    ```
    
- **Move WordPress files.**
    
    ```
    mv -v wordpress/* /var/www/html/armour.local/
    
    ```
    
- **Set permissions.**
    
    ```
    chown -Rv www-data:www-data /var/www/html/armour.local
    
    ```
    

## Notes

- Always use secure passwords and consider Let's Encrypt for production SSL certificates instead of self-signed.
- Adjust IP addresses (e.g., 192.168.1.28) to match your environment.
- For PHP/MySQL versions, verify compatibility with your Debian release (e.g., Debian 12/Bookworm uses PHP 8.2; update as needed in 2025).
- Test configurations with `apache2ctl configtest` before restarting.
- Firewall: Ensure ports 80, 443, and 3306 are allowed (e.g., via ufw).
- phpMyAdmin: Restrict access in production to prevent unauthorized database exposure.
- WordPress: After installation, run the setup wizard via browser and configure wp-config.php with database credentials.
