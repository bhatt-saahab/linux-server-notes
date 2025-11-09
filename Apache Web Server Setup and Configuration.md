# Apache Web Server Setup and Configuration

(Imagine a **server in a building** (data center) storing your website, and **Apache/Nginx** is the “messenger” sending your site to visitors’ browsers.)

This document outlines the installation, configuration, and management of the Apache Web Server on a Linux-based system using yum and rpm package managers. It includes essential commands, configuration examples, and troubleshooting steps. All provided data has been reviewed, corrected for syntax errors (e.g., missing pipes in commands, typos like "greb" to "grep", "ps-aux" to "ps aux"), and organized for clarity. Incomplete URLs (e.g., wget command) have been fixed based on standard TemplateMo download patterns. No data has been omitted; duplicates or redundancies have been streamlined while preserving intent.

## 1. Check if Apache is Installed

### Use RPM Query

Query the installed packages to check for Apache (httpd).

```bash
rpm -qa | grep httpd

```

- **Note**: This command lists any installed httpd-related packages. If output is empty, Apache is not installed. The pipe (`|`) redirects output to `grep` for filtering.

## 2. Install Apache Web Server

### Install Core Apache Package

Install the main Apache (httpd) package using yum.

```bash
yum install httpd

```

### Install All Related Apache Packages

Install httpd and all associated packages (e.g., mod_ssl, tools).

```bash
yum install httpd*

```

- **Note**: The wildcard () matches all httpd-prefixed packages. Use `dnf` instead of `yum` on RHEL 8+ or Fedora. Confirm installation with `yum list installed | grep httpd`.
- 
- 
- 
- {It means you installed Apache on your Kali Linux to turn your computer into a web server that can host websites locally or online.}

## 3. Verify Apache Installation

### Check Package Information

Display detailed information about the installed httpd package.

```bash
rpm -qi httpd

```

### List Installed Files

List all files installed by the httpd package.

```bash
rpm -ql httpd

```

### List Configuration Files

List configuration files provided by httpd.

```bash
rpm -qc httpd

```

### List Documentation Files

List documentation files for httpd.

```bash
rpm -qd httpd

```

- **Note**: These `rpm` commands provide insights into package contents. `qi` shows version, size, and description; `ql` enumerates files; `qc` focuses on configs (e.g., `/etc/httpd/conf/httpd.conf`); `qd` lists docs like man pages.

## 4. Apache Configuration

### View Apache Configuration

Display the entire main configuration file.

```bash
cat /etc/httpd/conf/httpd.conf

```

### View Tail of Configuration

Show the last few lines of the main configuration file.

```bash
tail /etc/httpd/conf/httpd.conf

```

### Find conf.modules.d Directive

Search for the modules configuration include directive.

```bash
grep conf.modules.d /etc/httpd/conf/httpd.conf

```

### Find conf.d Directive

Search for the additional configuration includes directive.

```bash
grep conf.d /etc/httpd/conf/httpd.conf

```

- **Note**: The main config (`httpd.conf`) includes dynamic modules from `/etc/httpd/conf.modules.d/` and extra configs from `/etc/httpd/conf.d/`. Use `grep -n` for line numbers if needed. Edits require root privileges; backup before changes.

## 5. Manage Apache Service

### Check Apache Status

Verify if the Apache service is running.

```bash
systemctl status httpd.service

```

### Start Apache Service

Start the Apache service manually.

```bash
systemctl start httpd.service

```

### Enable Apache to Start on Boot

Enable the service to start automatically on system boot.

```bash
systemctl enable httpd.service

```

### Restart Apache Service

Restart the service to apply configuration changes.

```bash
systemctl restart httpd.service

```

- **Note**: `systemctl` manages systemd services. `status` shows PID, uptime, and logs. `start` runs it immediately; `enable` persists across reboots. `restart` stops and starts, useful post-config edit. Use `systemctl stop httpd.service` to halt.

## 6. Network and Port Verification

### Check Open Ports and Services

List all listening TCP/UDP ports and associated processes.

```bash
netstat -nltup

```

### Check if Port 80 is Open

Filter for HTTP port 80.

```bash
netstat -nltup | grep 80

```

### Check if Apache is Listening

Filter for httpd processes.

```bash
netstat -nltup | grep httpd

```

- **Note**: `netstat -nltup` shows numeric ports (`n`), TCP/UDP listening (`l`), and PID/program (`up`). Apache listens on port 80 (HTTP) by default. On modern systems, use `ss -nltup` as `netstat` may be deprecated. Expect output like `tcp 0 0 0.0.0.0:80 ... httpd`.

## 7. Firewall Configuration

### Firewalld Configuration for Apache Web Server

Firewalld dynamically manages firewall rules. The following steps configure it for Apache traffic (HTTP/HTTPS).

### Check if Firewalld is Running

Verify the firewalld service status.

```bash
systemctl status firewalld

```

### Start the Firewalld Service

Start firewalld if not running.

```bash
systemctl start firewalld

```

### Enable Firewalld to Start Automatically at Boot

Persist firewalld across reboots.

```bash
systemctl enable firewalld

```

### Check All Active Zones and Their Associated Rules

List zones and rules.

```bash
firewall-cmd --list-all

```

### Allow HTTP (Port 80) and HTTPS (Port 443) Traffic

Add predefined services for HTTP and HTTPS permanently.

```bash
firewall-cmd --permanent --add-service=http

```

```bash
firewall-cmd --permanent --add-service=https

```

### Reload Firewalld

Apply the permanent changes.

```bash
firewall-cmd --reload

```

### Verify Services are Allowed

List allowed services in active zones.

```bash
firewall-cmd --list-services

```

- **Note**: Services like `http` map to port 80/tcp; `https` to 443/tcp. `-permanent` saves rules; `-reload` activates without restart. Default zone is usually `public`. Verify with `firewall-cmd --list-all-zones`.

### 7.1 Open Specific Ports for Apache

### Open Port 80 Manually

Add port 80 if no service is defined.

```bash
firewall-cmd --permanent --add-port=80/tcp

```

### Open Port 443 for HTTPS

Add port 443 for secure traffic.

```bash
firewall-cmd --permanent --add-port=443/tcp

```

### Open Additional Ports (Optional)

Example: Open port 8080 for custom configs.

```bash
firewall-cmd --permanent --add-port=8080/tcp

```

### Reload the Firewall to Apply Changes

Activate port additions.

```bash
firewall-cmd --reload

```

### Verify Open Ports

List allowed ports.

```bash
firewall-cmd --list-ports

```

- **Note**: Use ports over services for non-standard setups. TCP is specified for web traffic; UDP rarely needed.

### 7.2 Remove Rules (Optional)

### Remove HTTP and HTTPS Services

Remove predefined services.

```bash
firewall-cmd --permanent --remove-service=http

```

```bash
firewall-cmd --permanent --remove-service=https

```

### Remove Open Ports

Remove specific ports.

```bash
firewall-cmd --permanent --remove-port=80/tcp

```

```bash
firewall-cmd --permanent --remove-port=443/tcp

```

### Reload Firewalld After Removing Rules

Apply removals.

```bash
firewall-cmd --reload

```

- **Note**: Removals are for cleanup or security hardening. Always reload after changes.

### 7.3 Configure Firewalld Zones (Optional)

### List Available Zones

Display all zones.

```bash
firewall-cmd --get-zones

```

### Assign Apache Traffic to a Specific Zone

Example: Add services to the public zone.

```bash
firewall-cmd --zone=public --add-service=http --permanent

```

```bash
firewall-cmd --zone=public --add-service=https --permanent

```

### Reload to Apply Zone Settings

Activate zone changes.

```bash
firewall-cmd --reload

```

- **Note**: Zones segment rules (e.g., public for external, internal for LAN). Specify `-zone` to target; default is active zone.

### Enable Logging (Optional)

Enable logging for dropped packets.

```bash
firewall-cmd --set-log-denied=all

```

### View Firewall Logs

Monitor firewalld logs in real-time.

```bash
journalctl -f -u firewalld

```

- **Note**: `all` logs all denials (default: off). `journalctl` tails systemd logs; use `grep REJECT` for denials.

## 8. Apache Process and User Management(apache ki services vo kisi perticuler user ki power se run karti he )

### Check Running Processes

### List Apache Processes

Show all httpd processes.

```bash
ps aux | grep httpd

```

### Use lsof to Check Apache

List open files by httpd.

```bash
lsof | grep httpd

```

- **Note**: `ps aux` displays user, PID, CPU/memory; expect root for parent, apache for children. `lsof` shows ports/files; useful for debugging.

### Check Apache User and Group

### Find the Apache User

Search passwd for apache user.

```bash
cat /etit 
```

### Find the Apache Group

Search group file for apache.

```bash
cat /etc/group | grep apache

```

- **Note**: Default user/group is `apache` (UID ~48). Ensures secure file access; never run as root.

## 9. Configure Website Files

vim /var/www/html/index.html

### Create or Edit Web Files

### Navigate to the Document Root

Change to Apache's default web directory.

```bash
cd /var/www/html

```

### Create or Edit an index.html File

Edit the default index file.

```bash
vim /var/www/html/index.html

```

**Example Content:**

```
Test123...

```

- **Note**: `/var/www/html` is the default DocumentRoot. `index.html` serves as the welcome page. Use `echo "Test123..." > /var/www/html/index.html` for simple creation.

### Remove Existing File

Delete the default index if needed.

```bash
rm -f index.html

```

### Set Ownership of Web Files

Assign ownership to Apache user/group recursively.

```bash
chown -Rv apache:apache /var/www/html/*

```

- **Note**: `R` recurses directories; `v` verbose. Ensures Apache can read/execute files without permission errors.

## 10. Load CSS Files

### Download and Extract Template Files

### Download Template

Download the HexaShop template ZIP.

```bash
wget https://templatemo.com/tm-zip-files-2020/templatemo_571_hexashop.zip

```

### Unzip the Template

Extract the ZIP archive.

```bash
unzip templatemo_571_hexashop.zip

```

### Remove the ZIP File

Clean up the downloaded archive.

```bash
rm -rf templatemo_571_hexashop.zip

```

```bash
cd templatemo_571_hexashop

```

```bash

ls -lh
```

### Move the Extracted Folder to a New Directory

Rename for organization.

```bash
mv -v templatemo_571_hexashop site1

```

### Copy Site Files to the Apache Document Root

Copy contents to web root.

```bash
cp -vr /root/site1/* /var/www/html

```

- **Note**: Assumes extraction in `/root`; adjust path if needed. `v` verbose; `r` recursive. The template includes HTML/CSS/JS for e-commerce demo.

```bash
 chown -Rv apache:apache /var/www/html/*

```

- change the ownership to apache

### (if not load the upper one so do this)

Configure Apache to Load CSS Files

Edit httpd.conf to add MIME types and filters (around line 311).

```bash
vim /etc/httpd/conf/httpd.conf

```

**Additions (Example):**

```
AddType text/html .shtml
AddType text/css .css
AddOutputFilter INCLUDES .shtml

```

### Restart Apache

Apply config changes.

```bash
systemctl restart httpd.service

```

### Set Ownership

Re-apply ownership post-copy.

```bash
chown -Rv apache:apache /var/www/html/*

```

- **Note**: `.css` MIME type ensures proper serving; `INCLUDES` for server-side includes. Restart required for config. Verify with browser dev tools (no 404 on CSS).

## 11. Test Apache Setup

### Test Configuration

### Create a Test File

Add a simple test page (as in section 9).

```bash
vim /var/www/html/index.html

```

**Content:** `Test123...`

### Check if Apache is Responding

Use curl to fetch the local server response.

```bash
curl <http://192.168.1.34>

```

- **Note**: Replace `192.168.1.34` with server IP. Expect HTML output (e.g., "Test123..."). Use `curl -I` for headers. Browser test: [http://server-ip](http://server-ip/).

## Apache Web Server Installation and Configuration Completed

You should now have a working Apache Web Server with a sample HTML file and CSS loading correctly. Verify access via browser or curl, monitor logs (`tail -f /var/log/httpd/error_log`), and consider SSL setup for production. For virtual hosts, edit `/etc/httpd/conf.d/` files.

unzip templatemo_571_hexashop.zip
