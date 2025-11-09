This guide walks through installing, configuring, and enabling anonymous access on an FTP server using vsftpd on a Red Hat-based system. It also includes firewall setup using firewalld.

## Check for Existing FTP Packages

- Check if any FTP-related packages are already installed:

```
rpm -qa | grep ftp

```

## Install vsftpd and ftp Client

- Install the FTP server and client if they aren't already installed:

```
yum install vsftpd ftp

```

- Verify installation:

```
rpm -qa | grep ftp

```

## Inspecting vsftpd Package Details

- Check detailed package information:

```
rpm -qi vsftpd

```

- List installed files:

```
rpm -ql vsftpd

```

- Check configuration files:

```
rpm -qc vsftpd

```

- Display documentation files:

```
rpm -qd vsftpd

```

## Configure vsftpd

- Edit the main configuration file:

```
vim /etc/vsftpd/vsftpd.conf

```

This configuration enables:
• Anonymous and local user login
• Upload and directory creation for anonymous users
• Passive mode (required for many FTP clients)

**Example configuration:**

```
anonymous_enable=YES
local_enable=YES
write_enable=YES
local_umask=022
anon_upload_enable=YES
anon_mkdir_write_enable=YES
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=NO
listen_ipv6=YES
pam_service_name=vsftpd
userlist_enable=YES
pasv_min_port=55000
pasv_max_port=55999
pasv_enable=YES

```

## Manage vsftpd Service

- Check if the service is running:

```
systemctl status vsftpd.service

```

- Start the service:

```
systemctl start vsftpd.service

```

- Restart the service (e.g., after configuration changes):

```
systemctl restart vsftpd.service

```

- Enable the service at boot:

```
systemctl enable vsftpd.service

```

## Verify FTP Listening Ports

- Check if the FTP service is listening on the correct ports:

```
netstat -nltup | grep ftp

```

## Set Up Anonymous Content

- Navigate to the default FTP root:

```
cd /var/ftp/

```

- Create a directory for anonymous content:

```
mkdir /var/ftp/pub/data

```

- Set ownership:

```
chown -Rv nobody:nobody /var/ftp/pub/data

```

- Set permissions:

```
chmod -Rv 777 /var/ftp/pub/data

```

- Copy content for anonymous access:

```
cp -vr /var/www/html/sites /var/ftp/pub/data

```

## Check Open Files Used by FTP

- Verify FTP-related open files and sockets:

```
lsof | grep ftp

```

## Configuring firewalld for FTP Access

By default, firewalld is the firewall management tool on many modern Linux distributions (like CentOS 7+, RHEL 7+, Fedora, etc.). To support FTP, especially passive FTP, you need to allow the appropriate ports.

### Start and Enable firewalld

- Ensure the service is running:

```
systemctl start firewalld

```

```
systemctl enable firewalld

```

- Check its status:

```
systemctl status firewalld

```

### Allow FTP Service

This opens port 21, used by FTP control connections.

```
firewall-cmd --permanent --add-service=ftp

```

### Allow Passive FTP Ports

If passive mode is configured in `/etc/vsftpd/vsftpd.conf` (e.g., 55000-55999), you must allow this range explicitly:

```
firewall-cmd --permanent --add-port=55000-55999/tcp

```

### Reload firewalld to Apply Changes

```
firewall-cmd --reload

```

### Verify Rules

- List all allowed services and ports:

```
firewall-cmd --list-all

```

### Optional: Allow Access Only in Specific Zones

If you're using a specific zone (e.g., public, internal), specify it like this:

```
firewall-cmd --zone=public --permanent --add-service=ftp

```

```
firewall-cmd --zone=public --permanent --add-port=55000-55999/tcp

```

```
firewall-cmd --reload

```

## SELinux Consideration (If Enforcing)

If SELinux is enabled, you may also need to allow FTP passive mode with these:

```
setsebool -P ftp_home_dir=1

```

```
setsebool -P allow_ftpd_full_access=1

```

- You can confirm settings with:

```
getsebool -a | grep ftp

```

## Summary of Required Ports

| Service | Protocol | Port(s) | Description |
| --- | --- | --- | --- |
| FTP | TCP | 21 | Control connection |
| FTP-PASV | TCP | 55000-55999 | Passive data connections |

## Additional Tips

- If you're running SELinux, make sure to set proper booleans like `allow_ftpd_anon_write` using `setsebool`.
• Ensure the `/var/ftp/pub` directory has correct permissions for anonymous uploads (e.g., `chmod 777` for testing).
• Use an FTP client like FileZilla or the `ftp` command-line client to verify anonymous access.

# FTP Client Usage

This guide provides a step-by-step walkthrough of using the `ftp` command-line client to connect to an FTP server, transfer files, switch modes, and manage remote files.

## Connect to an FTP Server

- To begin an FTP session, use the following command:

```
ftp 192.168.1.35

```

## Login Credentials

When prompted, enter a valid username and password. For anonymous access, try one of the following usernames:

```
ftp

```

```
anonymous

```

```
anon

```

Use an email-style string as the password:

**Username:** ftp

**Password:** [alla.com](http://alla.com/)

## Available FTP Commands

### List All FTP Commands

Displays a list of all FTP commands supported by the client.

```
?

```

### View Current Remote Directory

Shows the present working directory on the remote FTP server.

```
pwd

```

### List Files and Directories

Displays the contents of the current directory on the remote server.

```
ls

```

## Downloading Files From FTP Server

### Download a Single File

Downloads `tuned.log` from the server to your local machine.

```
get about.html

```

### Download Multiple Specific Files

Fetches the listed files from the server.

```
mget contact.html index.html products.html

```

### Download with Wildcard Patterns

Downloads all `.css` or `.conf` files in the current directory.

```
mget *.css

```

```
mget *.conf

```

## Uploading Files to Server

### Before uploading, switch to binary mode to prevent file corruption:

```
binary

```

### Upload a Single File

Sends the specified file to the FTP server.

```
put chisel

```

### Upload Multiple Files

Transfers multiple files or matches based on pattern.

```
mput tuned.log.1 tuned.log hello.py

```

```
mput *.log

```

## Switching Transfer Modes

### Switch to ASCII mode

Used for transferring plain text files.

```
ascii

```

**Response:**

200 Switching to ASCII mode.

### Switch to Binary Mode

Recommended for all non-text files (images, archives, executables, etc.).

```
binary

```

**Response:**

200 Switching to Binary mode.

## Deleting Remote Files

### Delete a Single File

Removes the specified file from the FTP server.

```
delete nc64.exe

```

### Delete Multiple Files

Deletes multiple files in one go.

```
mdelete ScreamingFrogSEOSpider-21.3.exe httrack-3.49.2.exe httrack_x64.3.49.2.exe

```

## Ending the ftp session

Ends the FTP session and closes the connection to the server.

```
bye

```

or

```
quit

```

## Additional Tips

- Always use binary mode for non-text files to avoid corruption.
• Use wildcards (e.g., `.conf`) with `mget`/`mput`/`mdelete` for batch operations.
• Ensure directory permissions allow uploads/downloads on the server side.
• Log out with `bye` or `quit` to end the session.
