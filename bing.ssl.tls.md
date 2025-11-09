## Install and Configure SSL/TLS in Apache

### Install mod_ssl

mod_ssl is an Apache module that provides SSL and TLS support.

```bash
yum install mod_ssl

```

- **Note**: This installs the mod_ssl module for HTTPS. On RHEL 8+, use `dnf` instead of `yum`. Verify post-install with `httpd -M | grep ssl`.

### Verify mod_ssl Installation

### Check if the Module is Installed

Display detailed information about the mod_ssl package.

```bash
rpm -qi mod_ssl

```

### List Installed Files

List all files installed by mod_ssl.

```bash
rpm -ql mod_ssl

```

### List Configuration Files

List configuration files provided by mod_ssl.

```bash
rpm -qc mod_ssl

```

### List Documentation Files

List documentation files for mod_ssl.

```bash
rpm -qd mod_ssl

```

- **Note**: These `rpm` queries confirm installation details. Expect output showing version, files like `/etc/httpd/conf.d/ssl.conf`, and docs. If not installed, the commands will error.

### Configure SSL in Apache

### Edit the SSL Configuration File

Open the SSL module configuration file.

```bash
vim /etc/httpd/conf.d/ssl.conf

```

**Make Sure That SSL is Enabled by Setting Appropriate Certificate Files:**

```
SSLCertificateFile /etc/pki/tls/certs/localhost.crt
SSLCertificateKeyFile /etc/pki/tls/private/localhost.key

```

- **Note**: Uncomment or add these lines to point to certificate files. Default localhost certs are self-signed; replace with real ones for production. Backup the file before edits.

### Restart Apache

Restart Apache to apply SSL configuration.

```bash
systemctl restart httpd.service

```

### Check Generated Files (for Default Certs)

List details of the default certificate.

```bash
ls -lh /etc/pki/tls/certs/localhost.crt

```

List details of the default key.

```bash
ls -lh /etc/pki/tls/private/localhost.key

```

- **Note**: These verify file existence and sizes. Default files are created during mod_ssl install; permissions should be 644 for cert, 600 for key.

### Verify Apache Listening on SSL Port

Check if Apache is listening on port 443 (SSL).

```bash
netstat -nltup | grep httpd

```

- **Note**: Expect output showing *:443 or specific IP:443. Use `ss -nltup | grep httpd` alternative.

### Update Firewall for SSL

Edit the firewall rules to allow SSL traffic (port 443).

```bash
firewall-cmd --permanent --add-port=443/tcp

```

```bash
firewall-cmd --permanent --add-port=8080/tcp

```

```bash
firewall-cmd --reload

```

- **Note**: Port 443 for HTTPS; 8080 optional for testing. `-permanent` saves rules; `-reload` applies immediately. Verify: `firewall-cmd --list-ports`.

## Create Self-Signed SSL Certificates

### Create SSL Directory

Create a directory to store SSL certificates.

```bash
mkdir /opt/ssl

```

```bash
cd /opt/ssl

```

- **Note**: `/opt/ssl` is a custom dir for organization; use any secure path.

### Generate SSL Certificate and Key

Generate a self-signed SSL certificate and key.

```bash
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365

```

- **Note**: `x509` for self-signed; `newkey rsa:2048` generates 2048-bit RSA key; `days 365` sets 1-year validity. Prompted for details (e.g., Country, Common Name=server FQDN). Use `nodes` to avoid passphrase if desired.

### Step 3: Check the Generated Files

### Verify the Files

List file details.

```bash
ls -lh

```

Check file types.

```bash
file key.pem

```

```bash
file cert.pem

```

- **Note**: `ls -lh` shows sizes (key ~1.7K, cert ~1.3K). `file` confirms PEM formats (e.g., "PEM RSA private key", "PEM certificate").

### Step 4: Copy SSL Files to Appropriate Directories

Copy the certificate and key to the proper directories.

```bash
cp -v /opt/ssl/cert.pem /etc/pki/tls/certs/cert.pem

```

```bash
cp -v /opt/ssl/key.pem /etc/pki/tls/private/key.pem

```

- **Note**: `v` verbose. Set permissions: `chmod 644 /etc/pki/tls/certs/cert.pem; chmod 600 /etc/pki/tls/private/key.pem; chown root:root` both.

### Update Apache Configuration with SSL Certificates

### Edit the Apache Configuration

Edit to point to the new certificates.

```bash
vim /etc/httpd/conf.d/ssl.conf

```

**Add These Lines to Enable SSL:**

```
# SSL Cipher Suite:
#   List the ciphers that the client is permitted to negotiate.
#   See the mod_ssl documentation for a complete list.
#   The OpenSSL system profile is configured by default. See
#   update-crypto-policies(8) for more details.
SSLCipherSuite PROFILE=SYSTEM
SSLProxyCipherSuite PROFILE=SYSTEM

#   Point SSLCertificateFile at a PEM encoded certificate.
#   If the certificate is encrypted, then you will be prompted for a pass phrase. Note that restarting httpd will prompt again. Keep in mind that if you have both an RSA and a DSA certificate you can configure both in parallel (to also allow the use of DSA
#   ciphers, etc.)
#   Some ECC cipher suites (<http://www.ietf.org/rfc/rfc4492.txt>)
#   require an ECC certificate which can also be configured in
#   parallel.
#SSLCertificateFile /etc/pki/tls/certs/localhost.crt
SSLCertificateFile /etc/pki/tls/certs/cert.pem

#   Server Private Key:
#   If the key is not combined with the certificate, use this
#   directive to point at the key file. Keep in mind that if
#   you've both a RSA and a DSA private key you can configure
#   both in parallel (to also allow the use of DSA ciphers, etc.)
#   ECC keys, when in use, can also be configured in parallel
#SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
SSLCertificateKeyFile /etc/pki/tls/private/key.pem

```

### Restart Apache Again

Apply the certificate updates.

```bash
systemctl restart httpd.service

```

- **Note**: Replaces default localhost certs. `PROFILE=SYSTEM` uses OS-defined secure ciphers. Test syntax: `httpd -t`.

### Verify SSL Binding in Apache

Check if Apache is listening on both HTTP (port 80) and HTTPS (port 443).

```bash
netstat -nltup | grep httpd

```

- **Note**: Look for :80 and :443 lines.

### cheak certificate info there is your info

### Update Firewall for SSL/TLS Ports

Ensure that port 443 is open in your firewall configuration.

```bash
firewall-cmd --permanent --add-port=443/tcp

```

```bash
firewall-cmd --permanent --add-port=8080/tcp

```

```bash
firewall-cmd --reload

```

- **Note**: Duplicate from earlier; ensures consistency. Port 8080 optional.

## Configure Apache Virtual Hosts with SSL/TLS

You can now create Virtual Hosts with SSL/TLS enabled. Below are example configurations for both HTTP and HTTPS.

### Edit Apache Configuration for Virtual Hosts

Open the main config.

```bash
vim /etc/httpd/conf/httpd.conf

```

**Example of Apache Virtual Hosts for HTTP and HTTPS:**

```
<VirtualHost 192.168.1.41:80>
    DocumentRoot /var/www/html/site1/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.41:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/cert.pem
    SSLCertificateKeyFile /etc/pki/tls/private/key.pem
    DocumentRoot /var/www/html/site1/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.42:80>
    ServerName armour.local
    DocumentRoot /var/www/html/site2/
    DirectoryIndex index.html
    ServerAlias www.armour.local
</VirtualHost>

<VirtualHost 192.168.1.42:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/cert.pem
    SSLCertificateKeyFile /etc/pki/tls/private/key.pem
    ServerName armour.local
    DocumentRoot /var/www/html/site2/
    DirectoryIndex index.html
    ServerAlias www.armour.local
</VirtualHost>

```

**In the Above Configuration:**
• The first block listens on HTTP (port 80) for site1.
• The second block enables SSL (port 443) for site1.
• Similarly, the third and fourth blocks handle HTTP and HTTPS for armour.local.

### Restart Apache Again

Apply VirtualHost changes.

```bash
systemctl restart httpd.service

```

- **Note**: Corrected "ww.armour.local" to "www.armour.local", "<<VirtualHost>" to "</VirtualHost>", "Directory Index" to "DirectoryIndex". Add `<Directory /var/www/html/site*>` blocks for security if needed (e.g., `AllowOverride None`).

## Advanced Configuration with Custom CA (Certificate Authority)(no password requred bas itna he )

If you want to use a self-generated Certificate Authority (CA):

### Generate the Private Key for the CA

Navigate and generate the CA key.

```bash
cd /opt/ssl

```

```bash
openssl genrsa -out ca.key 2048

```

- **Note**: 2048-bit RSA for CA private key. Secure storage essential.

### Generate the Certificate Signing Request (CSR)

Create CSR for the CA.

```bash
openssl req -new -key ca.key -out ca.csr

```

- **Note**: Prompted for details; use CA-specific info (e.g., Common Name="My CA").

### Create the Self-Signed Certificate for the CA

Sign the CSR to create the CA cert.

```bash
openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt

```

- **Note**: `days 365` for 1-year CA validity. This makes a self-signed root CA.

### Copy the CA Certificate and Key to the Appropriate Directories

Copy files to standard locations.

```bash
cp -v /opt/ssl/ca.crt /etc/pki/tls/certs/ca.crt

```

```bash
cp -v /opt/ssl/ca.key /etc/pki/tls/private/ca.key

```

- **Note**: Set permissions as before. For client certs, use this CA to sign server certs (e.g., `openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 365`).

### Configure Apache Virtual Hosts with SSL/TLS (CA Version)

Edit for CA-signed certs.

```bash
vim /etc/httpd/conf/httpd.conf

```

**Example of Apache Virtual Hosts for HTTP and HTTPS:**

```
<VirtualHost 192.168.1.41:80>
    DocumentRoot /var/www/html/site1/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.41:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/ca.crt
    SSLCertificateKeyFile /etc/pki/tls/private/ca.key
    DocumentRoot /var/www/html/site1/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.42:80>
    ServerName ai.local
    DocumentRoot /var/www/html/site2/
    DirectoryIndex index.html
    ServerAlias www.ai.local
</VirtualHost>

<VirtualHost 192.168.1.42:443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/ca.crt
    SSLCertificateKeyFile /etc/pki/tls/private/ca.key
    ServerName ai.local
    DocumentRoot /var/www/html/site2/
    DirectoryIndex index.html
    ServerAlias www.ai.local
</VirtualHost>

```

- **Note**: Uses CA cert/key directly (for simplicity; typically sign intermediate certs). Corrected "[ca.ky](http://ca.ky/)" to "ca.key", "ww.ai.local" to "www.ai.local". For production, use proper chain: `SSLCACertificateFile` for intermediates.

## Final Steps

After configuring your Virtual Hosts and SSL certificates, restart Apache and check the ports once again.

### Restart Apache

Apply all changes.

```bash
systemctl restart httpd.service

```

### Check Ports

Verify listening.

```bash
netstat -nltup | grep httpd

```

### Test Your Websites Using Both HTTP and HTTPS URLs

| Domain | Port | Expected Output |
| --- | --- | --- |
| [http://armour.local](http://armour.local/) | 80 | site2 homepage |
| <[https://armour.local](https://armour.local/) | > 443 | site2 (SSL-enabled) |
- **Note**: Test with `curl -k <http://armour.local`> (HTTP) and `curl -k <https://armour.local`> (-k ignores self-signed warnings). Browser: Accept cert exception. Logs: `tail -f /var/log/httpd/ssl_error_log`. For multi-site, adjust IPs/domains.
