WebDAV (Web Distributed Authoring and Versioning) extends the HTTP protocol to enable remote file management on a web server. Apache supports WebDAV using the mod_dav and mod_dav_fs modules.

## Features of WebDAV

- Access and manage files over HTTP/S
- Supports creation, editing, moving, and deletion of files
- Integrates with version control tools like Git and SVN
- Clients can mount WebDAV directories as local drives
- Suitable for collaborative environments and remote file hosting

## Install and Verify Apache

### Check if Apache is Installed (Verifies Whether Apache is Already Installed)

```bash
rpm -qa | grep httpd

```

### Install Apache and Related Packages on RHEL-Based Systems

```bash
yum install httpd*

```

### Lists All Currently Active Modules

```bash
httpd -M

```

## Check for WebDAV Module Support

```bash
httpd -M | grep fs

```

- Expected output:

```
  dav_fs_module (shared)

```

- Confirms that WebDAV support is enabled via mod_dav_fs

## Create and Set Up WebDAV Directory

### Create Directory (Sets Up the Directory for WebDAV Content)

```bash
mkdir /var/www/html/webdav

```

### Set Ownership (Ensures Apache Owns the Directory)

```bash
chown apache:apache /var/www/html/webdav/

```

### Set Permissions

```bash
chmod 777 /var/www/html/webdav/

```

- For production: prefer 755 or 775 for better security

## Configure Apache for WebDAV

### Edit Apache Configuration

```bash
vim /etc/httpd/conf/httpd.conf

```

- Add or update the Virtual Host block:

```
<VirtualHost *:80>
    DocumentRoot /var/www/html/
    DirectoryIndex index.html
    <Directory /var/www/html/webdav>
        DAV On
    </Directory>
</VirtualHost>

```

- This enables WebDAV under the /webdav path

## Test WebDAV Directory Access

### Use curl to Send OPTIONS Request (Verifies Supported HTTP Methods by the Server)

```bash
curl -v -X OPTIONS <http://192.168.1.32/webdav/>

```

## Configure SELinux and Firewall

### Restart Apache

```bash
systemctl restart httpd.service

```

### Check SELinux Status

```bash
sestatus

```

### Disable SELinux Temporarily (Not Recommended for Production)

```bash
vim /etc/sysconfig/selinux

```

- Change the value:

```
SELINUX=disabled

```

- Instead, prefer using proper context labeling:

```bash
chcon -R -t httpd_sys_content_t /var/www/html/webdav

```

## Secure WebDAV with Basic Authentication

### Create User Password File

```bash
htpasswd -c /etc/httpd/htpasswd dev

```

### Add More Users

```bash
htpasswd /etc/httpd/htpasswd admin

```

```bash
htpasswd /etc/httpd/htpasswd root

```

```bash
htpasswd /etc/httpd/htpasswd user

```

### View .htpasswd File

```bash
cat /etc/httpd/htpasswd

```

- Sample output:

```
dev:$apr1$GXmFNd4f$HBa.Fu2SB1SMUncBcATuG/
admin:$apr1$NmNcn0p5$Lhk2I/HVjXV7mKKpXis4C/
root:$apr1$Qey..Fqn$y2GIMsuP1JDDMrnWHgDnU.
user:$apr1$zp/J6/mB$v.h.deWHBq/34CABimwk3/

```

## Configure Apache for WebDAV Authentication

### Edit Apache Config Again

```bash
vim /etc/httpd/conf/httpd.conf

```

- Update VirtualHost block:

```
<VirtualHost *:80>
    DocumentRoot /var/www/html/
    DirectoryIndex index.html
    <Directory /var/www/html/webdav>
        DAV On
        AuthType Basic
        AuthName "webdav"
        AuthUserFile /etc/httpd/htpasswd
        Require valid-user
    </Directory>
</VirtualHost>

```

### Restart Apache After Config Change

```bash
systemctl restart httpd

```

## WebDAV Client Testing

### Test HTTP Methods

```bash
curl -v -X OPTIONS <http://192.168.1.36/webdav/>

```

```bash
curl -i -k -X OPTIONS <https://192.168.1.36/webdav/>

```

### Scan WebDAV with Nmap

```bash
nmap -v -sT -sV -A -p 80 --script http-methods.nse --script-args http-methods.url-path='/webdav/' 192.168.1.36

```

## Authenticated File Operations

### OPTIONS with Auth

```bash
curl -v -u dev:123 -X OPTIONS <http://192.168.1.32/webdav/>

```

```bash
curl -i -k -u dev:123 -X OPTIONS <https://192.168.1.32/webdav/>

```

### Authenticated File Upload

```bash
curl -u dev:123 -X PUT -d "hi" <http://192.168.1.32/webdav/1.txt>

```

```bash
curl -X PUT -u dev:123 -d "<?php phpinfo(); ?>" <http://192.168.1.32/webdav/phpinfo.php>

```

### Authenticated File Deletion

```bash
curl -u dev:123 -X DELETE <http://192.168.1.32/webdav/phpinfo.php>

```

```bash
curl -v -u root:root -X PUT -d "hi" <http://192.168.1.31/webdav/1.txt>

```

```bash
curl -v -u root:root -X DELETE <http://192.168.1.32/webdav/1.txt>

```

## Using Cadaver - WebDAV Command-Line Client

### Install Cadaver

- On Debian/Ubuntu:

```bash
apt install cadaver

```

- On RHEL-based:

```bash
yum install cadaver

```

### Connect to WebDAV Server

```bash
cadaver <http://192.168.1.32/webdav/>

```

### Sample Cadaver Session

```
dav:/webdav/> ls
dav:/webdav/> put network.png
dav:/webdav/> mkdir newdir
dav:/webdav/> mput *.php
dav:/webdav/> delete *

```

### Connect to a Different Path

```bash
cadaver <http://192.168.1.32/test/>

```
