## Change Directory and Create an Empty File

To prevent accidental exposure, navigate to a directory and create an empty `index.php` file, which Apache serves by default to suppress directory listings.

- Change to the uploads directory:

```
cd /var/www/html/wordpress/wp-content/uploads

```

- Create an empty index.php file:

```
touch index.php

```

**Note:** This ensures that if directory listing is enabled, the empty `index.php` is shown instead of file contents, hiding the directory structure.

## Disable Directory Listing for All Directories

By default, Apache may enable directory indexing (`Options Indexes`). Disable it globally or per-directory for security.

### Edit the Apache Configuration File

- Open the main Apache config:

```
vim /etc/httpd/conf/httpd.conf

```

- Add or modify the VirtualHost block to disable indexes:

```
<VirtualHost 192.168.1.35:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    DocumentRoot /var/www/html/wordpress/
    DirectoryIndex index.php
    <Directory /var/www/html/wordpress>
        Options -Indexes
    </Directory>
</VirtualHost>

```

**Note:** `Options -Indexes` disables directory listing for the specified directory and subdirectories. This applies to the WordPress root; adjust paths as needed. The VirtualHost is for HTTPS on IP 192.168.1.35.

### Restart Apache

- Reload the configuration:

```
systemctl restart httpd.service

```

**Note:** Always restart after config changes to apply them. Check syntax with `httpd -t` before restarting.

## Edit Site-Specific Configuration (Optional)

For modular configs, use a site-specific file instead of editing `httpd.conf`.

- Edit the site config file:

```
vim /etc/httpd/conf.d/wp-site.conf

```

- Add the VirtualHost block:

```
<VirtualHost 192.168.1.35:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    ServerName armour.local
    DocumentRoot /var/www/html/wordpress/
    DirectoryIndex index.php
    ServerAlias www.armour.local
    <Directory /var/www/html/wordpress>
        Options -Indexes
    </Directory>
</VirtualHost>

```

**Note:** This includes `ServerName` and `ServerAlias` for virtual hosting. Place in `/etc/httpd/conf.d/` for automatic inclusion.

### Restart Apache Again

- Reload the configuration:

```
systemctl restart httpd.service

```

**Note:** Ensures the site-specific changes take effect without altering the main config.

## Enable Directory Listing for a Selected Directory

Sometimes, controlled listing is needed (e.g., for backups). Enable it only for specific paths.

### Create and Set Ownership

- Create the backup directory:

```
mkdir /var/www/html/wordpress/backup

```

- Set proper ownership for Apache:

```
chown -R apache:apache /var/www/html/wordpress/backup

```

**Note:** Corrected path from `/var/ww/html/` to `/var/www/html/`. Ownership ensures Apache can read/serve files.

### Update Apache Configuration

- Open the main config:

```
vim /etc/httpd/conf/httpd.conf

```

- Modify the VirtualHost to enable indexes for the backup directory:

```
<VirtualHost 192.168.1.35:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    DocumentRoot /var/www/html/wordpress/
    DirectoryIndex index.php
    <Directory /var/www/html/wordpress/backup>
        Options Indexes
    </Directory>
    <Directory /var/www/html/wordpress>
        Options -Indexes
    </Directory>
</VirtualHost>

```

**Note:** `Options Indexes` (with + if adding to existing) enables listing only in `/backup`. The root remains disabled.

### Restart Apache

- Reload the configuration:

```
systemctl restart httpd.service

```

**Note:** Test access: Browsing `/backup/` should now list files.

## Allow Selected IP Addresses

Restrict directory access to specific IPs for added security.

- Open the main config:

```
vim /etc/httpd/conf/httpd.conf

```

- Modify the VirtualHost with IP allow rules:

```
<VirtualHost 192.168.1.35:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    DocumentRoot /var/www/html/wordpress/
    DirectoryIndex index.php
    <Directory /var/www/html/wordpress/backup>
        Options Indexes
        Order allow,deny
        Allow from 192.168.1.7 192.168.1.51
    </Directory>
    <Directory /var/www/html/wordpress>
        Options -Indexes
    </Directory>
</VirtualHost>

```

**Note:** `Order allow,deny` processes allows first, then denies. Only listed IPs can access `/backup/`.

### Restart Apache

- Reload the configuration:

```
systemctl restart httpd.service

```

**Note:** For Apache 2.4+, use `Require ip 192.168.1.7 192.168.1.51` instead of `Order/Allow`.

## Deny Selected IP Addresses

Block specific IPs while allowing others.

- Open the main config:

```
vim /etc/httpd/conf/httpd.conf

```

- Modify the VirtualHost with deny rules:

```
<VirtualHost 192.168.1.35:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    DocumentRoot /var/www/html/wordpress/
    DirectoryIndex index.php
    <Directory /var/www/html/wordpress/backup>
        Options Indexes
        Order allow,deny
        Allow from all
        Deny from 192.168.1.51
    </Directory>
    <Directory /var/www/html/wordpress>
        Options -Indexes
    </Directory>
</VirtualHost>

```

**Note:** `Allow from all` permits everyone except the denied IP. Corrected path from `/var/ww/html/` to `/var/www/html/`.

### Restart Apache

- Reload the configuration:

```
systemctl restart httpd.service

```

**Note:** For Apache 2.4+, use `Require all granted` and `Require not ip 192.168.1.51`.

## Secure Directory Hosting with User Authentication

Protect directories with HTTP Basic Authentication.

### Using .htaccess

### Edit .htaccess

- Create/edit the .htaccess file:

```
vim /var/www/html/wordpress/backup/.htaccess

```

- Add authentication directives:

```
AuthName "Armour Infosec"
AuthType Basic
AuthUserFile /etc/httpd/htpasswd
Require valid-user

```

**Note:** Corrected path from `vim/var/www/...` to `vim /var/www/...`. This prompts for credentials on access.

### Create Password File and Users

- Create the password file and add user1:

```
htpasswd -c /etc/httpd/htpasswd user1

```

- Add user2:

```
htpasswd /etc/httpd/htpasswd user2

```

- View the file:

```
cat /etc/httpd/htpasswd

```

**Note:** Example output:

```
user1:$apr1$KTRQrDKN$GMStvuFhhOmRMDY14D5V8/1
user2:$apr1$3BaCfSJa$r2Ljfq9Q3dK/qt51316P50

```

- `c` creates the file; omit for additions. Hashes are APR1 MD5.

### Update Apache Configuration

- Open the main config:

```
vim /etc/httpd/conf/httpd.conf

```

- Modify the VirtualHost to allow .htaccess overrides:

```
<VirtualHost 192.168.1.35:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    DocumentRoot /var/www/html/wordpress/
    DirectoryIndex index.php
    <Directory /var/www/html/wordpress/backup>
        Options Indexes
        AllowOverride AuthConfig
    </Directory>
    <Directory /var/www/html/wordpress>
        Options -Indexes
    </Directory>
</VirtualHost>

```

**Note:** `AllowOverride AuthConfig` permits .htaccess auth directives. Corrected path from `/var/ww/html/...` to `/var/www/html/...`.

### Restart Apache

- Reload the configuration:

```
systemctl restart httpd.service

```

**Note:** Enables .htaccess-based auth for `/backup/`.

## User Authentication Without .htaccess

Direct config-based auth is more performant.

### Edit Apache Configuration

- Open the main config:

```
vim /etc/httpd/conf/httpd.conf

```

- Modify the VirtualHost with inline auth:

```
<VirtualHost 192.168.1.35:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    DocumentRoot /var/www/html/wordpress/
    DirectoryIndex index.php
    <Directory /var/www/html/wordpress/backup>
        AuthName "Private"
        AuthType Basic
        AuthBasicProvider file
        AuthUserFile /etc/httpd/htpasswd
        Require valid-user
        Options +Indexes
    </Directory>
    <Directory /var/www/html/wordpress>
        Options -Indexes
    </Directory>
</VirtualHost>

```

**Note:** `Options +Indexes` enables listing. `AuthBasicProvider file` uses the htpasswd file. Corrected path from `/var/ww/html/...` to `/var/www/html/...`.

### Remove .htaccess

- Delete the .htaccess file:

```
rm -f /var/www/html/wordpress/backup/.htaccess

```

**Note:** Switches to config-based auth, avoiding .htaccess overhead.

### Restart Apache

- Reload the configuration:

```
systemctl restart httpd.service

```

**Note:** Applies the direct auth rules.

## Verification Commands

Test configurations with curl for headers and access.

- Test uploads directory (should 403 or serve index):

```
curl -I <http://armour.local/wordpress/uploads/>

```

- Test backup directory (should list or prompt auth):

```
curl -I <http://armour.local/wordpress/backup/>

```

- Test authenticated access:

```
curl -u user1:password <https://armour.local/wordpress/backup/>

```

**Note:** `-I` fetches headers only. Replace `password` with actual. Use HTTPS for auth to avoid plaintext creds.

## Notes & Best Practices

- Always use HTTPS when Basic Auth is enabled to encrypt credentials.
• For Apache 2.4+, prefer modern directives:
• `Require ip 192.168.1.7`
• `Require not ip 192.168.1.51`
• Secure your password file:
• Set permissions:

```
chmod 640 /etc/httpd/htpasswd

```

- Set ownership:

```
chown root:apache /etc/httpd/htpasswd

```

**Note:** Restricts access to root and Apache group. Disable .htaccess where not required—it's slower than direct config-based authentication. Test all changes in a staging environment. Use `apachectl configtest` for validation.
