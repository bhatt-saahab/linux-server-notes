Apache can bind to specific ports, allowing you to run multiple websites on different ports using Virtual Hosts. This is useful when you want to test different sites on the same IP but using unique ports. The provided data has been reviewed, corrected for syntax errors (e.g., fixed typos like "/var/ww/html" to "/var/www/html", "<<VirtualHost>" to "</VirtualHost>", "Directory Index" to "DirectoryIndex", "firewall-cmd-reload" to "firewall-cmd --reload"), removed duplicates (e.g., repeated firewall configs), and standardized for consistency (e.g., Listen directives are global, not per-vhost; IPs aligned to 192.168.1.33/34 where specified). All data preserved; virtual hosts expanded for clarity with sites 1-6.

## 1. Modify Apache Configuration

### Open the Main Apache Configuration File

Edit the primary Apache config to add global Listen directives.

```bash
vim /etc/httpd/conf/httpd.conf

```

### Add Listen Directives for New Ports

The Listen directive tells Apache which ports to bind to. Add the following lines to allow Apache to listen on ports 80, 81, 82, 8080, 8081, and 8082 (global, before VirtualHost blocks).

**Example Additions:**

```
Listen 80
Listen 81
Listen 82
Listen 8080
Listen 8081
Listen 8082

```

### Example of a Complete Apache Virtual Host Configuration Using Different Ports

Add VirtualHost blocks for port-based routing (corrected syntax and paths).

**Example Content (in /etc/httpd/conf/httpd.conf or included files):**

```
<VirtualHost 192.168.1.34:80>
    DocumentRoot /var/www/html/site1/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.33:80>
    DocumentRoot /var/www/html/site1/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.33:8080>
    DocumentRoot /var/www/html/site2/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.33:8081>
    DocumentRoot /var/www/html/site3/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.34:8082>
    DocumentRoot /var/www/html/site4/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.34:81>
    DocumentRoot /var/www/html/site5/
    DirectoryIndex index.html
</VirtualHost>

<VirtualHost 192.168.1.34:82>
    DocumentRoot /var/www/html/site6/
    DirectoryIndex index.html
</VirtualHost>

```

### Save and Exit

Save the file and exit the editor.

- **Note**: Global `Listen` directives enable ports server-wide. VirtualHosts route based on IP:port. Ensure `IncludeOptional conf.d/*.conf` is present in `httpd.conf` to load per-site files. Create site directories/files first (e.g., `mkdir -p /var/www/html/site{1..6}`; add index.html). Backup config before edits.

## 2. Configure Firewall

### Add Ports for Custom Bindings

Open TCP ports 8080, 8081, and 8082 (port 80/81/82 assumed already open; add if needed).

```bash
firewall-cmd --permanent --add-port=8080/tcp

```

```bash
firewall-cmd --permanent --add-port=8081/tcp

```

```bash
firewall-cmd --permanent --add-port=8082/tcp

```

### Reload Firewall

Apply the permanent changes.

```bash
firewall-cmd --reload

```

- **Note**: `-permanent` persists rules across reboots. TCP for HTTP; add UDP if needed (rare). Verify: `firewall-cmd --list-ports`. For port 80/81/82: `firewall-cmd --permanent --add-port=80/tcp --add-port=81/tcp --add-port=82/tcp`.

## 3. Apply the Changes by Restarting Apache

Restart to load new ports and VirtualHosts.

```bash
systemctl restart httpd.service

```

- **Note**: Ensures bindings take effect. Check status: `systemctl status httpd.service`. If errors, review `/var/log/httpd/error_log`.

## 4. Create Virtual Host Files

Create separate virtual host files for each port under `/etc/httpd/conf.d/`. (Listen directives are global, so omit them from .conf files.)

### Default HTTP Port (80)

Create a virtual host for port 80.

```bash
vim /etc/httpd/conf.d/site1.conf

```

**Example Content:**

```
<VirtualHost 192.168.1.34:80>
    DocumentRoot /var/www/html/site1/
    DirectoryIndex index.html
</VirtualHost>

```

### Virtual Host for Port 8080

Create a virtual host for port 8080.

```bash
vim /etc/httpd/conf.d/site2.conf

```

**Example Content:**

```
<VirtualHost 192.168.1.34:8080>
    DocumentRoot /var/www/html/site2/
    DirectoryIndex index.html
</VirtualHost>

```

### Virtual Host for Port 8081

Create a virtual host for port 8081.

```bash
vim /etc/httpd/conf.d/site3.conf

```

**Example Content:**

```
<VirtualHost 192.168.1.34:8081>
    DocumentRoot /var/www/html/site3/
    DirectoryIndex index.html
</VirtualHost>

```

- **Note**: Each .conf file defines a port-specific VirtualHost. Omit `Listen` here (global in httpd.conf). Add ServerName for domain routing if combining with name-based. Set permissions: `chown -R apache:apache /var/www/html/site*`. Test: `curl http://192.168.1.34:8080`.

## 5. Validate Apache Configuration

### Check if Apache Configuration is Valid

Test syntax.

```bash
httpd -t

```

**Expected Output:**

```
Syntax OK

```

- **Note**: Runs without starting server; catches errors like mismatched tags or invalid directives.

## 6. Restart Apache

### Apply the Changes

Reload after validation.

```bash
systemctl restart httpd.service

```

- **Note**: Integrates .conf files. Monitor: `journalctl -u httpd -f`.

## 7. Verify Listening Ports

### Use Netstat to Confirm Bindings

Check Apache listening on configured ports.

```bash
netstat -nltup | grep httpd

```

**Example Output:**

```
tcp        0      0 192.168.1.34:80         0.0.0.0:*               LISTEN      1234/httpd
tcp        0      0 192.168.1.34:8080      0.0.0.0:*               LISTEN      1234/httpd
tcp        0      0 192.168.1.34:8081      0.0.0.0:*               LISTEN      1234/httpd
tcp        0      0 192.168.1.34:8082      0.0.0.0:*               LISTEN      1234/httpd
tcp        0      0 192.168.1.50:81        0.0.0.0:*               LISTEN      1234/httpd
tcp        0      0 192.168.1.50:82        0.0.0.0:*               LISTEN      1234/httpd

```

- **Note**: Confirms IP:port bindings. Use `ss -nltup | grep httpd` alternative. Test access: `curl <http://IP>:PORT`. If not listening, check SELinux (`setsebool -P httpd_can_network_connect 1`).

# Binding with Domain Names in Apache

Apache can bind to specific domain names using the `ServerName` and `ServerAlias` directives in Virtual Hosts. This allows you to host multiple websites on the same IP address, each accessible by a unique domain name. Data corrected: fixed /etc/hosts entries (removed garbled text like "localhost si ns1.armour.local", "localhosto asi nst.armour.local", "ครi osi.armour.local"; standardized to proper mappings for 192.168.1.32), paths (/var/ww/html to /var/www/html), syntax (DirectoryIndex), and firewall (consistent --permanent and --reload). Duplicates streamlined.

## 1. Configure Hostnames (DNS or /etc/hosts)

### Edit /etc/hosts

To simulate DNS resolution, define the domain names in the /etc/hosts file.

```bash
vim /etc/hosts

```

**Add the Following Entries:**

```
127.0.0.1   localhost ns1.armour.local
::1         localhost ns1.armour.local
192.168.1.32 armour.local www.armour.local ai.local www.ai.local infosec.local www.infosec.local

```

- **Note**: Maps domains to server IP (192.168.1.32). Edit on client machines too for testing. For real DNS, use A records in BIND zones.

### Test DNS Resolution

Use dig to confirm that the domain names resolve to the correct IP.

```bash
dig www.armour.local +short

```

**Example Output:**

```
192.168.1.32

```

### Repeat for Other Domains

Test additional domains.

```bash
dig www.infosec.local +short

```

```bash
dig www.ai.local +short

```

- **Note**: `+short` shows only the IP. Expect 192.168.1.32 for all. If using real DNS, query `@server-ip`.

## 2. Create Apache Virtual Host Files

Define virtual hosts for each domain. Apache will route incoming requests based on the `ServerName` and `ServerAlias`.

### Main Virtual Host

Create a default virtual host file (fallback).

```bash
vim /etc/httpd/conf/httpd.conf

```

**Example Content:**

```
<VirtualHost 192.168.1.32:80>
    DocumentRoot /var/www/html/site1/
    DirectoryIndex index.html
</VirtualHost>

```

### Virtual Host for armour.local

Create a virtual host file for armour.local.

```bash
vim /etc/httpd/conf.d/armour.conf

```

**Example Content:**

```
<VirtualHost 192.168.1.32:80>
    ServerName armour.local
    ServerAlias www.armour.local
    DocumentRoot /var/www/html/site1/
    DirectoryIndex index.html
</VirtualHost>

```

### Virtual Host for infosec.local

Create a virtual host file for infosec.local.

```bash
vim /etc/httpd/conf.d/infosec.conf

```

**Example Content:**

```
<VirtualHost 192.168.1.32:80>
    ServerName infosec.local
    ServerAlias www.infosec.local
    DocumentRoot /var/www/html/site2/
    DirectoryIndex index.html
</VirtualHost>

```

### Virtual Host for ai.local

Create a virtual host file for ai.local.

```bash
vim /etc/httpd/conf.d/ai.conf

```

**Example Content:**

```
<VirtualHost 192.168.1.32:80>
    ServerName ai.local
    ServerAlias www.ai.local
    DocumentRoot /var/www/html/site3/
    DirectoryIndex index.html
</VirtualHost>

```

- **Note**: `ServerName` matches the requested host header; `ServerAlias` for variants (e.g., www). All on same IP:80 for name-based virtual hosting. Create site dirs/files: `mkdir -p /var/www/html/site{1..3}`. First VirtualHost is default.

## 3. Validate the Apache Configuration

### Check Syntax

Test the config.

```bash
httpd -t

```

**Expected Output:**

```
Syntax OK

```

- **Note**: Validates all loaded .conf files.

## 4. Restart Apache

### Apply the Changes

Reload to activate domain-based routing.

```bash
systemctl restart httpd.service

```

- **Note**: Routes requests by hostname. Test: `curl -H "Host: armour.local" <http://192.168.1.32`> serves site1.

## 5. Confirm Ports and Bindings

### Use Netstat or ss to Check Listening

Verify Apache on port 80.

```bash
netstat -nltup | grep httpd

```

- **Note**: Should show 192.168.1.32:80 or *:80. For multi-port, see prior section.

## 6. Configure Firewall

### Add Ports for HTTP and Custom (if needed)

Allow port 80; add others if port-binding combined.

```bash
firewall-cmd --permanent --add-port=80/tcp

```

```bash
firewall-cmd --permanent --add-port=8080/tcp

```

```bash
firewall-cmd --permanent --add-port=8081/tcp

```

```bash
firewall-cmd --permanent --add-port=8082/tcp

```

### Reload Firewall

Apply rules.

```bash
firewall-cmd --reload

```

- **Note**: Essential for external access. Port 80 for standard HTTP; others for testing. Verify: `firewall-cmd --list-ports`.
